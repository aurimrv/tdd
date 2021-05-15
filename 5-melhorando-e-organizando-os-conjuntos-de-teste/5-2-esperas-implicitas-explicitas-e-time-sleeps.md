# 5.2 Esperas Implícitas, Explícitas e Time Sleeps

Um dos maiores problemas com a execução de testes simulando ações do usuário diz respeito ao sincronismo para a execução correta dos testes. Para que um elemento seja clicado ou pesquisado em uma página web, primeiro ele precisa estar renderizado e, nem sempre, a velocidade de renderização bate com a velocidade da verificação. Por isso, em nosso código de teste, incluímos anteriormente alguns comandos `time.sleep(1)` que provoca uma espera de um segundo na execução dos testes. Essa categoria de espera é chamada de **espera explícita**.

O problema é que, quando executamos esse teste localmente, um segundo costuma ser um tempo demasiadamente grande e, quando executarmos esses testes em um servidor remoto pode ser que não seja. A verdade é que, por nunca termos certeza do tempo que será necessário para garantir o sincronismo acabamos colocando um tempo superior ao que efetivamente seria necessário causando um atraso desnecessário na execução de nossos testes.

O Selenium oferece várias outras formas de esperam, tanto explícitas quanto implícitas, mas os seus próprios desenvolvedores desaconselham o uso das esperas implícitas, entretanto, vale a pena verificar a documentação sobre as possibilidades de esperas oferecidas pela ferramenta. Mais informações sobre elas podem ser encontradas em [https://www.selenium.dev/documentation/en/webdriver/waits/](https://www.selenium.dev/documentation/en/webdriver/waits/).

No nosso caso, vamos implementar nosso próprio mecanismo de espera e, para isso, vamos reimplementar o método `check_for_row_in_list_table` e renomeá-lo para `wait_for_row_in_list_table` \(linhas 18 a 29\) e a implementação fica conforme abaixo:

```python
from django.test import LiveServerTestCase
from selenium import webdriver
from selenium.common.exceptions import WebDriverException
from selenium.webdriver.common.keys import Keys
import time

MAX_WAIT = 10

class NewVisitorTest(LiveServerTestCase):

	def setUp(self):
		self.browser = webdriver.Firefox()

	def tearDown(self):
		self.browser.quit()

	# Auxiliary method 
	def wait_for_row_in_list_table(self, row_text):
		start_time = time.time()
		while True:
			try:
				table = self.browser.find_element_by_id('id_list_table')
				rows = table.find_elements_by_tag_name('tr')
				self.assertIn(row_text, [row.text for row in rows])
				return
			except(AssertionError, WebDriverException) as e:
				if time.time() - start_time > MAX_WAIT:
					raise e
				time.sleep(0.5)


	def test_can_start_a_list_and_retrieve_it_later(self):
		# Edith ouviu falar de uma nova aplicação online interessante
		# para lista de tarefas. Ela decide verificar a homepage

		self.browser.get(self.live_server_url)

		# Ela percebe que o título da página e o cabeçalho mencionam
		# listas de tarefas (to-do)

		self.assertIn('To-Do', self.browser.title)
		header_text = self.browser.find_element_by_tag_name('h1').text
		self.assertIn('To-Do', header_text)
		
		# Ela é convidada a inserir um item de tarefa imediatamente

		inputbox = self.browser.find_element_by_id('id_new_item')
		self.assertEqual(
			inputbox.get_attribute('placeholder'),
			'Enter a to-do item'
		)

		# Ela digita "Buy peacock featers" (Comprar penas de pavão)
		# em uma nova caixa de texto (o hobby de Edith é fazer iscas
		# para pesca com fly)

		inputbox.send_keys('Buy peacock featers')

		# Quando ela tecla enter, a página é atualizada, e agora
		# a página lista "1 - Buy peacock feathers" como um item em 
		# uma lista de tarefas

		inputbox.send_keys(Keys.ENTER)
		time.sleep(1)
		self.wait_for_row_in_list_table('1: Buy peacock featers')

		# Ainda continua havendo uma caixa de texto convidando-a a 
		# acrescentar outro item. Ela insere "Use peacock feathers 
		# to make a fly" (Usar penas de pavão para fazer um fly - 
		# Edith é bem metódica)
		inputbox = self.browser.find_element_by_id('id_new_item')
		inputbox.send_keys("Use peacock feathers to make a fly")
		inputbox.send_keys(Keys.ENTER)
		time.sleep(1)

		# A página é atualizada novamente e agora mostra os dois
		# itens em sua lista
		self.wait_for_row_in_list_table('1: Buy peacock featers')
		self.wait_for_row_in_list_table('2: Use peacock feathers to make a fly')

		# Edith se pergunta se o site lembrará de sua lista. Então
		# ela nota que o site gerou um URL único para ela -- há um 
		# pequeno texto explicativo para isso.

		self.fail('Finish the test!')

		# Ela acessa essa URL -- sua lista de tarefas continua lá.

		# Satisfeita, ela volta a dormir
```

Se desejarmos, para ter certeza de que os testes estão funcionando, podemos incluir algumas falhas propositais no mesmo e verificar os resultados. Por hora, podemos confirmar as alterações já que os resultados dos testes funcionais param no `self.fail`.

```bash
(superlists) auri@av:~/insync/tdd/superlists/superlists$ python manage.py test functional_tests
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (functional_tests.tests.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/functional_tests/tests.py", line 85, in test_can_start_a_list_and_retrieve_it_later
    self.fail('Finish the test!')
AssertionError: Finish the test!

----------------------------------------------------------------------
Ran 1 test in 4.667s

FAILED (failures=1)
Destroying test database for alias 'default'...
```



