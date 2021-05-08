# 5.1 Isolamento dos Testes Funcionais

Ao encerrarmos o capítulo anterior, nossa aplicação apresentava um pequeno problema que era o acúmulo de itens na lista em execuções sucessivas dos casos de teste. Um outro inconveniente que temos em nosso código de testes são os time.sleeps. Durante este capítulo iremos deixar um pouco de lado no desenvolvimento do código da aplicação em si para nos concentrarmos na melhoria do nosso conjunto de teste funcional.

Nesta seção abordaremos o isolamento dos testes e como eliminar as repetições de inclusão de itens na lista durante execuções sucessivas dos testes. Na Seção 5.2 lidaremos com a questão de sincronização.

O framework unittest oferece métodos que nos permitem executar ações antes ou após cada caso de teste, são os métodos `setUp` e `tearDown`, respectivamente. Poderíamos utilizá-los para fazer, antes de cada teste, colocar o sistema em um estado conhecido válido e, posteriormente, limpar os dados produzidos durante a execução de um teste. Entretanto, o Django oferece uma classe de teste, denominada `LiveServerTestCase` que nos proporciona recursos semelhantes aqueles utilizados nos testes unitários, ou seja, do isolamento dos testes em execuções sucessivas. Essa classe é responsável por criar, automaticamente, um banco de dados de teste.

Testes derivados de `LiveServerTestCase`  esperam ser executadas pelo executor de testes do Django e, para isso precisamos reorganizar os arquivos contendo os testes para indicar ao Djando qual desejamos executar em dado momento. Desse modo, vamos criar uma pasta para acomodar nossos testes funcionais um nível abaixo de nosso diretório de trabalho. Tudo que o Django necessita é uma pasta com um arquivo **`__init__.py`**dentro dela.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ mkdir functional_tests
(superlists) auri@av:~/tdd/superlists/superlists$ touch functional_tests/__init__.py
```

Em seguida, como nosso código está sob controle de versão, podemos utilizar os comandos do Git para movimentação de arquivos conforme abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git mv functional_tests.py functional_tests/tests.py

(superlists) auri@av:~/tdd/superlists/superlists$ git status
No ramo master
Your branch is up to date with 'origin/master'.

Mudanças a serem submetidas:
  (use "git restore --staged <file>..." to unstage)
	renamed:    functional_tests.py -> functional_tests/tests.py

Arquivos não monitorados:
  (utilize "git add <arquivo>..." para incluir o que será submetido)
	functional_tests/__init__.py
```

Com essa alteração, `functional_tests.py` passou a ser `functional_tests/tests.py`. O conteúdo do novo arquivo também precisa ser alterado para fazer uso da classe `LiveServerTestCase`, conforme abaixo.

```text
from django.test import LiveServerTestCase
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time

class NewVsitorTest(LiveServerTestCase):

	def setUp(self):
		self.browser = webdriver.Firefox()

	def tearDown(self):
		self.browser.quit()

	# Auxiliary method 
	def check_for_row_in_list_table(self, row_text):
		table = self.browser.find_element_by_id('id_list_table')
		rows = table.find_elements_by_tag_name('tr')
		self.assertIn(row_text, [row.text for row in rows])

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
		self.check_for_row_in_list_table('1: Buy peacock featers')

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
		self.check_for_row_in_list_table('1: Buy peacock featers')
		self.check_for_row_in_list_table('2: Use peacock feathers to make a fly')

		# Edith se pergunta se o site lembrará de sua lista. Então
		# ela nota que o site gerou um URL único para ela -- há um 
		# pequeno texto explicativo para isso.

		self.fail('Finish the test!')

		# Ela acessa essa URL -- sua lista de tarefas continua lá.

		# Satisfeita, ela volta a dormir
```

Basicamente, incluímos a dependência de LiveServerTestCase \(linha 1\), modificamos a URL utilizada na linha 24 de `self.browser.get(http://localhost:8000/)` para `self.browser.get(self.live_server_url)`, que identifica corretamente a URL e a porta relacionada com o servidor de teste. Além disso, as linhas de código abaixo podem ser removidas do servidor de teste pois a execução dos mesmos se dará via `manage.py`.

```text
if __name__ == '__main__':
	unittest.main()
```

Com essas alterações, a execução dos testes funcionais passa a ser realizadas agora da seguinte forma:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test functional_tests
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (functional_tests.tests.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/functional_tests/tests.py", line 73, in test_can_start_a_list_and_retrieve_it_later
    self.fail('Finish the test!')
AssertionError: Finish the test!

----------------------------------------------------------------------
Ran 1 test in 5.956s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Observe que agora, ao executa os testes, o Django cria uma base auxiliar apenas para a execução dos testes, que é destruída no encerramento dos testes, conforme mensagens nas linhas 2 e 17, respectivamente.

Antes de continuarmos, vamos colocar nosso código e suas alterações no controle de verão:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git status 
No ramo master
Your branch is up to date with 'origin/master'.

Mudanças a serem submetidas:
  (use "git restore --staged <file>..." to unstage)
	renamed:    functional_tests.py -> functional_tests/tests.py

Changes not staged for commit:
  (utilize "git add <arquivo>..." para atualizar o que será submetido)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   functional_tests/tests.py

Arquivos não monitorados:
  (utilize "git add <arquivo>..." para incluir o que será submetido)
	functional_tests/__init__.py

(superlists) auri@av:~/tdd/superlists/superlists$ git add functional_tests

(superlists) auri@av:~/tdd/superlists/superlists$ git commit -am "Make functional_tests an app using LiveServerTestCase."
[master 04657aa] Make functional_tests an app using LiveServerTestCase.
 2 files changed, 4 insertions(+), 7 deletions(-)
 create mode 100644 functional_tests/__init__.py
 rename functional_tests.py => functional_tests/tests.py (93%)
(superlists) auri@av:~/tdd/superlists/superlists$ git push
Username for 'https://github.com': aurimrv
Password for 'https://aurimrv@github.com': 
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 12 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 1.44 KiB | 1.44 MiB/s, done.
Total 4 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/aurimrv/superlists.git
   06e3205..04657aa  master -> master
```

