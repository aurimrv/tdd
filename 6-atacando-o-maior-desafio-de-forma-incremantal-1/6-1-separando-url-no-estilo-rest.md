# 6.1 Separando URL no Estilo REST

O maior desafio de nossa história de usuário é lidar com as URLs distintas para cada novo usuário do nosso sistema. A necessidade é que tenhamos URLs únicas para cada usuário consultar a sua própria lista de tarefas.

Utilizaremos para isso a inspiração de APIs REST \(_Representational State Transfer_ ou Transferência de Estado Representativo\). Em REST, diferentes URLs oferecem diferentes serviços aos usuários e a estrutura das URLs corresponde a estrutura de dados desejada. 

Para o nosso sistema de lista de tarefas, [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) define a folha de rascunho abaixo de itens que precisam ser resolvidos nesse capítulo de modo a implementarmos a história de usuário por completa. 

* [ ] Ajustar o modelo para que itens sejam associados a listas distintas
* [ ] Adicionar URLs exclusivos para cada lista, por exemplo, como `/lists/<identificador da lista>/`
* [ ] Adicionar um URL para criar uma lista via POST, por exemplo, como `/lists/new`
* [ ] Adicionar URL para acrescentar um novo item em uma lista existente, por exemplo,  como `/lists/<identificador da lista>/add_item`

#### Garantindo um Teste de Regressão

Antes de iniciarmos com as implementações, o primeiro passo é modificarmos nosso teste funcional, garantindo que tenhamos um mecanismo para detectar possíveis quebras no código.

Desse modo, o nosso teste funcional ficará conforme abaixo. Basicamente, alteramos o nome do método de teste anterior de `test_can_start_a_list_and_retrieve_it_later` para `test_can_start_a_list_for_one_user`. \(linhas 32 a 87\) e incluímos um novo método de teste para avaliar a característica de múltiplos usuários \(linhas 89 a 129\).

```python
from django.test import LiveServerTestCase
from selenium import webdriver
from selenium.common.exceptions import WebDriverException
from selenium.webdriver.common.keys import Keys
import time

MAX_WAIT = 10

class NewVsitorTest(LiveServerTestCase):

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


	def test_can_start_a_list_for_one_user(self):
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

		# Ela digita "Buy peacock feathers" (Comprar penas de pavão)
		# em uma nova caixa de texto (o hobby de Edith é fazer iscas
		# para pesca com fly)

		inputbox.send_keys('Buy peacock feathers')

		# Quando ela tecla enter, a página é atualizada, e agora
		# a página lista "1 - Buy peacock feathers" como um item em 
		# uma lista de tarefas

		inputbox.send_keys(Keys.ENTER)
		self.wait_for_row_in_list_table('1: Buy peacock feathers')

		# Ainda continua havendo uma caixa de texto convidando-a a 
		# acrescentar outro item. Ela insere "Use peacock feathers 
		# to make a fly" (Usar penas de pavão para fazer um fly - 
		# Edith é bem metódica)
		inputbox = self.browser.find_element_by_id('id_new_item')
		inputbox.send_keys("Use peacock feathers to make a fly")
		inputbox.send_keys(Keys.ENTER)

		# A página é atualizada novamente e agora mostra os dois
		# itens em sua lista
		self.wait_for_row_in_list_table('1: Buy peacock feathers')
		self.wait_for_row_in_list_table('2: Use peacock feathers to make a fly')

		# Edith se pergunta se o site lembrará de sua lista. Então
		# ela nota que o site gerou um URL único para ela -- há um 
		# pequeno texto explicativo para isso.

		# Ela acessa essa URL -- sua lista de tarefas continua lá.

		# Satisfeita, ela volta a dormir

	def test_multiple_users_can_start_lists_at_different_urls(self):
		# Edith inicia uma nova lista de tarefas
		self.browser.get(self.live_server_url)
		inputbox = self.browser.find_element_by_id('id_new_item')
		inputbox.send_keys('Buy peacock feathers')
		inputbox.send_keys(Keys.ENTER)
		self.wait_for_row_in_list_table('1: Buy peacock feathers')

		#Ela percebe que sua lista te um URL único
		edith_list_url = self.browser.current_url
		self.assertRegex(edith_list_url, '/lists/.+')

		#Agora um novo usuário, Francis, chega ao site

		## Usamos uma nova versão do nagegador para garantir que nenhuma 
		## informação de Edith está vindo de cookies, etc
		
		self.browser.quit()
		self.browser = webdriver.Firefox()

		# Francis acessa a página inicial. Não há sinal da lista de Edith
		self.browser.get(self.live_server_url)
		page_text = self.browser.find_element_by_tag_name('body').text
		self.assertNotIn('Buy peacock feathers', page_text)
		self.assertNotIn('make a fly', page_text)

		# Francis inicia uma nova lista inserindo um novo item.
		inputbox = self.browser.find_element_by_id('id_new_item')
		inputbox.send_keys('Buy milk')
		inputbox.send_keys(Keys.ENTER)
		self.wait_for_row_in_list_table('1: Buy milk')

		# Francis obtém seu próprio URL exclusivo
		francis_list_url = self.browser.current_url
		self.assertRegex(edith_list_url, '/lists/.+')
		self.assertNotEqual( francis_list_url, edith_list_url)

		# Novamente não há sinal algum da lista de Edith
		page_text = self.browser.find_element_by_tag_name('body').text
		self.assertNotIn('Buy peacock feathers', page_text)
		self.assertIn('Buy milk', page_text)

		# Fim
```

Com a execução dos testes observamos que o teste anterior continua passando e o novo teste falha no ponto que esperávamos.

```bash
(superlists) auri@av:~/insync/tdd/superlists/superlists$ python manage.py test functional_tests
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.F
======================================================================
FAIL: test_multiple_users_can_start_lists_at_different_urls (functional_tests.tests.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/functional_tests/tests.py", line 99, in test_multiple_users_can_start_lists_at_different_urls
    self.assertRegex(edith_list_url, '/lists/.+')
AssertionError: Regex didn't match: '/lists/.+' not found in 'http://localhost:50315/'

----------------------------------------------------------------------
Ran 2 tests in 9.043s

FAILED (failures=1)
Destroying test database for alias 'default'...

```

Ótimo momento para confirmar as alterações e colocar o código sob controle de versões.

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -am "Regression testing for two different lists"
(superlists) auri@av:~/tdd/superlists/superlists$ git push
```





