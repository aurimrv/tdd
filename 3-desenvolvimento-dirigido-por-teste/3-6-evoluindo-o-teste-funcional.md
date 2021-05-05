# 3.6 Evoluindo o Teste Funcional

Na seção anterior finalizamos completando toda uma parte da história descrita no teste funcional. É hora de melhorá-lo pois a execução dos testes parou exatamente na mensagem que havíamos deixado nos testes nos lembrando de que o teste ainda não está completo.

Entretanto, antes de complementar o teste funcional, [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) nos apresenta diversos questionamentos que devem estar surgindo sobre a viabilidade do TDD. Algumas das questões que ele argumenta que as pessoas iniciando com TDD fazem são: "Será mesmo que isso vale a pena? Todos esses testes não são excessivos? Será que não estão duplicados? Esses testes não são muito triviais? Isso é mesmo utilizado na prática?". Ele comenta que ele mesmo se fes essas perguntas e que é preciso dedicação e persistência para compreender e colher os benefícios.

> "O TDD é uma disciplina, e isso significa que não é algo que surge naturalmente; como muitas das compensações não são imediatas, mas só aparecem no longo prazo, você precisará se esforçar para segui-lo no momento." [\(Percival, 2017\)](http://www.obeythetestinggoat.com/pages/book.html)

Então vamos em frente e vamos nos esforçar para dar nosso melhor nessa tarefa de usar testes no desenvolvimento de um produto de software.

O próximo passo então é estendermos o nosso teste funcional para avançar na história nele descrita. O trecho de código a seguir ilustra como complementamos algumas frases da história, além do ponto que continha o comando `self.fail('Finish the test!')`. \(linha 22 do arquivo `functional_tests.py`\). Vide resultado da execução no final da [Seção 3.5.1](3-5-teste-de-unidade-e-a-evolucao-do-sistema/3-5-1-teste-de-unidade-de-uma-view.md).

```text
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time
import unittest

class NewVsitorTest(unittest.TestCase):

	def setUp(self):
		self.browser = webdriver.Firefox()

	def tearDown(self):
		self.browser.quit()

	def test_can_start_a_list_and_retrieve_it_later(self):
		# Edith ouviu falar de uma nova aplicação online interessante
		# para lista de tarefas. Ela decide verificar a homepage

		self.browser.get("http://localhost:8000")

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

		table = self.browser.find_element_by_id('id_list_table')
		rows = table.find_elements_by_tag_hame('tr')
		self.assertTrue(
			any(row.text == '1: Buy peacock featers' for row in rows)
		)

		# Ainda continua havendo uma caixa de texto convidando-a a 
		# acrescentar outro item. Ela insere "Use peacock feathers 
		# make a fly" (Usar penas de pavão para fazer um fly - 
		# Edith é bem metódica)

		self.fail('Finish the test!')

		# A página é atualizada novamente e agora mostra os dois
		# itens em sua lista

		# Edith se pergunta se o site lembrará de sua lista. Então
		# ela nota que o site gerou um URL único para ela -- há um 
		# pequeno texto explicativo para isso.

		# Ela acessa essa URL -- sua lista de tarefas continua lá.

		# Satisfeita, ela volta a dormir

if __name__ == '__main__':
	unittest.main()
```

Como pode ser observado acima, incluímos vários comandos novos no código de teste. São, em sua maioria, métodos do Selenium, como os métodos `find_element_by_tag_id` e `find_element_by_tag_name`, responsáveis por localizar elementos na página web, ou `send_keys`, responsável por enviar dados para campos de formulário em páginas web.

A execução do teste acima apresenta, conforme esperado, uma falha, conforme mensagem na linha 17 abaixo, ou seja, a execução do teste não foi capaz de localizar um componente `h1` na página.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python functional_tests.py 
E
======================================================================
ERROR: test_can_start_a_list_and_retrieve_it_later (__main__.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "functional_tests.py", line 24, in test_can_start_a_list_and_retrieve_it_later
    header_text = self.browser.find_element_by_tag_name('h1').text
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/selenium/webdriver/remote/webdriver.py", line 530, in find_element_by_tag_name
    return self.find_element(by=By.TAG_NAME, value=name)
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/selenium/webdriver/remote/webdriver.py", line 976, in find_element
    return self.execute(Command.FIND_ELEMENT, {
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/selenium/webdriver/remote/webdriver.py", line 321, in execute
    self.error_handler.check_response(response)
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/selenium/webdriver/remote/errorhandler.py", line 242, in check_response
    raise exception_class(message, screen, stacktrace)
selenium.common.exceptions.NoSuchElementException: Message: Unable to locate element: h1


----------------------------------------------------------------------
Ran 1 test in 1.923s

FAILED (errors=1)
```

Antes de continuar podemos colocar o código de teste modificado sob controle de versão com os comandos que usamos periodicamente e repetidos abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git diff
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -am "Functional tes now checks we can input a to-do item"
(superlists) auri@av:~/tdd/superlists/superlists$ git push
```

#### Iniciando o Terceiro Passo do Ciclo do TDD \(Refatorar\)

O processo de refatoração envolve melhorar o código atual sem incluir novas funcionalidades. A adição de funcionalidade durante o processo de refatoração fatalmente levará a problemas indesejados. Além disso, jamais refatore o código sem que você tenha testes que permitam assegurar que possível alterações indesejadas sejam barradas.

No nosso exemplo, a refatoração será o uso de templates do Django para exibir o código HTML ao invés de inbutir esse HTML diretamente no código da aplicação. Esse isolamento favorece a manutenção futura de nosso produto. 

No Django, o recurso utilizado para isso são os templates. Para utilizá-lo, basta criar uma subpasta `templates`, abaixo de nosso diretório `lists` \(`lists/templates`\). O Django sai varrendo automaticamente essas pastas na busca por aquivos `.html` que referenciamos no nosso código.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ mkdir lists/templates
(superlists) auri@av:~/tdd/superlists/superlists$ subl lists/templates/home.html
(superlists) auri@av:~/tdd/superlists/superlists$ cat lists/templates/home.html 
<html>
	<title>To-Do lists</title>
</html>
```

No exemplo acima, criamos o arquivo `lists/templates/home.html` com o conteúdo que já estava embutido em nosso código `views.py` e refatoramos `views.py` conforme abaixo.

```text
from django.shortcuts import render

# Create your views here.
def home_page(request):
	return render(request, 'home.html')
```

A função `render` acima, aceita um objeto do tipo requisição como primeiro parâmetro e o segundo é um template que será renderizado para exibição. Conforme comentado, o Django varre o diretório `templates` na busca pelo arquivo `home.html` indicado no segundo parâmetro.

Para saber se nossa refatoração funcionou basta reexecutar os testes:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
E.
======================================================================
ERROR: test_home_page_returns_correct_html (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 15, in test_home_page_returns_correct_html
    response = home_page(request)
  File "/home/auri/insync/tdd/superlists/superlists/lists/views.py", line 5, in home_page
    return render(request, 'home.html')
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/django/shortcuts.py", line 19, in render
    content = loader.render_to_string(template_name, context, request, using=using)
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/django/template/loader.py", line 61, in render_to_string
    template = get_template(template_name, using=using)
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/django/template/loader.py", line 19, in get_template
    raise TemplateDoesNotExist(template_name, chain=chain)
django.template.exceptions.TemplateDoesNotExist: home.html

----------------------------------------------------------------------
Ran 2 tests in 0.002s

FAILED (errors=1)
Destroying test database for alias 'default'...
```

Ops, parece que algo deu errado. Será que nossa refatoração quebrou o nosso código? Na verdade não. O que ocorreu com o erro acima é que ainda não registramos nossa aplicação no Django. Quando rodamos o comando pra criar nossa aplicação \(`python manage.py startapp lists` - [Seção 3.5](3-5-teste-de-unidade-e-a-evolucao-do-sistema/)\). Após criar uma aplicação no Django, ela precisa ser registrada. Isso é feito incluíndo seu nome no arquivo `settings.py`, localizado no diretório `superlists`, um nível abaixo do arquivo `manage.py`.

```text
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'lists',
]
```

Dentro do arquivo `settings.py`, procure pela seção `INSTALLED_APPS` e faça a inclusão de nossa aplicação `lists` ao final da lista, abaixo das aplicações já registradas por padrão pelo Django.

Feita a alteração podemos reexecutar os testes. Observe que ele ainda acusa um erro, mas isso é apenas porque em nosso _template_ teclamos um `ENTER` após o fechamento da tag `'</html>'`. Desse modo, nosso `assertTrue` esta falhando pois a linha não termina apenas com `'</html>'` mas sim com algo como `'</html>\n'`. 

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F.
======================================================================
FAIL: test_home_page_returns_correct_html (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 19, in test_home_page_returns_correct_html
    self.assertTrue(html.endswith('</html>'))
AssertionError: False is not true

----------------------------------------------------------------------
Ran 2 tests in 0.010s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Para evitar que isso ocorra, alteramos o nosso arquivo tests.py e usamos a função `strip()` da classe String do Python que elimina os caracteres em branco antes e após a String. O código dos testes ficaram conforme abaixo:

```text
from django.urls import resolve
from django.test import TestCase
from django.http import HttpRequest

from lists.views import home_page

class HomePageTest(TestCase):

	def test_root_url_resolves_to_home_page_view(self):
		found = resolve('/')
		self.assertEquals(found.func, home_page)

	def test_home_page_returns_correct_html(self):
		request = HttpRequest()
		response = home_page(request)
		html = response.content.decode('utf-8')
		self.assertTrue(html.strip().startswith('<html>'))
		self.assertIn('<title>To-Do lists</title>', html)
		self.assertTrue(html.strip().endswith('</html>'))
```

Com essa alteração\(linhas 17 e19 acima\) nossos testes passam com sucesso, indicando que nossa refatoração foi bem sucedida.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
..
----------------------------------------------------------------------
Ran 2 tests in 0.002s

OK
Destroying test database for alias 'default'...
```

#### Django Test Client

Existem diferentes formas de se testar se o Django está renderizando a página correta fazendo uso de nossos _templates_. Uma delas é fazer a renderização manual, comparando o que a _view_ retorna. Para isso, o Django oferece uma função que auxilia neste processo: `render_to_string`. Veja a seguir, como fica o nosso caso de teste fazendo uso dessa função:

```text
from django.urls import resolve
from django.test import TestCase
from django.http import HttpRequest
from django.template.loader import render_to_string

from lists.views import home_page

class HomePageTest(TestCase):

	def test_root_url_resolves_to_home_page_view(self):
		found = resolve('/')
		self.assertEquals(found.func, home_page)

	def test_home_page_returns_correct_html(self):
		request = HttpRequest()
		response = home_page(request)
		html = response.content.decode('utf-8')
		expected_html = render_to_string('home.html')
		self.assertEquals(html, expected_html)
```

Observe que as mudanças em relação ao teste anterior é o `import` na linha 4 e as linhas 18 e 19 que substituíram as antigas linhas de 17 a 19 \(_asserts_\). Com a chamada da função `render_to_string`, o Django nos devolve todo o conteúdo renderizado e armazena na variável `expected_html` \(linha 18\) que é comparada, na linha 19, com a resposta decodificada obtida da requisição \(linha 17\).

Entretanto, quando usamos _templates_, o Django oferece uma ferramenta ainda mais simples de verificarmos se nossa aplicação respondeu corretamente. Ela se chama [Django Test Clien](https://docs.djangoproject.com/en/3.2/topics/testing/tools/#the-test-client)t. Para mais informações sobre essa ferramenta, o leitor interessado pode consultar a documentação oficial do Django em [https://docs.djangoproject.com/en/3.2/topics/testing/tools/\#the-test-client](https://docs.djangoproject.com/en/3.2/topics/testing/tools/#the-test-client). Nosso caso de teste reescrito para fazer uso do [Django Test Client](https://docs.djangoproject.com/en/3.2/topics/testing/tools/#the-test-client) é apresentado abaixo com os comandos do teste anterior em comentários:

```text
from django.urls import resolve
from django.test import TestCase
from lists.views import home_page

class HomePageTest(TestCase):

	def test_root_url_resolves_to_home_page_view(self):
		found = resolve('/')
		self.assertEquals(found.func, home_page)

	def test_home_page_returns_correct_html(self):
		response = self.client.get('/')
		self.assertTemplateUsed(response, 'home.html')
```

Observe que agora o nosso teste `test_home_page_returns_correct_htm`l ficou muito mais simples. Ao invés de chamarmos manualmente o objeto `HttpRequest` e de chamar a função de _view_, basta fazermos uma chamada a `self.client.get`, passando a URL desejada.

Finalmente, para fazermos o teste se o retorno foi bem sucedido, utilizamos o método `assertTemplateUsed` da classe `TestCase` do Django. Ele nos permite confrontar a resposta do cliente com o conteúdo do _template_ de forma mais simples.

Conforme destaca [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html), o ponto principal ao usarmos o [Django Test Client](https://docs.djangoproject.com/en/3.2/topics/testing/tools/#the-test-client) é que, "ao invés de testarmos constantes, estamos testando nossa implementação", ou seja, eliminamos constantes do nosso código de teste e os deixamos menos sujeitos a manutenções em função das alterações no código da aplicação. Isso é muito importante para minimizarmos os custos de automatização dos testes.

O resultado da execução do teste utilizando o [Django Test Client](https://docs.djangoproject.com/en/3.2/topics/testing/tools/#the-test-client) é dado abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
..
----------------------------------------------------------------------
Ran 2 tests in 0.004s

OK
Destroying test database for alias 'default'...
```

Se ainda não tivermos confiantes de que esse novo modo de testar o retorno da página está funcionando, podemos fazer uma alteração proposital no teste para verificar se o teste ira falhar \(linha 13 - _template_ trocado para `'wrong.html'`\). No exemplo abaixo, ao executar o teste, o resultado indica uma falha pois o _template_ não foi encontrado.

```text
from django.urls import resolve
from django.test import TestCase
from lists.views import home_page

class HomePageTest(TestCase):

	def test_root_url_resolves_to_home_page_view(self):
		found = resolve('/')
		self.assertEquals(found.func, home_page)

	def test_home_page_returns_correct_html(self):
		response = self.client.get('/')
		self.assertTemplateUsed(response, 'wrong.html')
```

Com a alteração, o resultado da execução do teste falha conforme abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F.
======================================================================
FAIL: test_home_page_returns_correct_html (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 13, in test_home_page_returns_correct_html
    self.assertTemplateUsed(response, 'wrong.html')
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/django/test/testcases.py", line 657, in assertTemplateUsed
    self.assertTrue(
AssertionError: False is not true : Template 'wrong.html' was not a template used to render the response. Actual template(s) used: home.html

----------------------------------------------------------------------
Ran 2 tests in 0.005s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Refatorado nosso caso de teste unitário, é hora de colocarmos as modificações sob controle de versão. Utilizamos, em sequência, os comandos: `git status`, `git add .`, `git commit` e `git push`. O resultado é mostrado abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git status
No ramo master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (utilize "git add <arquivo>..." para atualizar o que será submetido)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   lists/tests.py

Arquivos não monitorados:
  (utilize "git add <arquivo>..." para incluir o que será submetido)
	lists/templates/

nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")
(superlists) auri@av:~/tdd/superlists/superlists$ git add .
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -am "Refactor home page view to use template"
[master 97480f8] Refactor home page view to use template
 2 files changed, 5 insertions(+), 8 deletions(-)
 create mode 100644 lists/templates/home.html
(superlists) auri@av:~/tdd/superlists/superlists$ git push
Username for 'https://github.com': aurimrv
Password for 'https://aurimrv@github.com': 
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 12 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 581 bytes | 581.00 KiB/s, done.
Total 6 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 3 local objects.

```

Finalmente, para encerrar esta seção, terminamos com a dica de [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) sobre refatoração e TDD.

> "Ao refatorar, trabalhe no código ou nos testes, mas não em ambos ao mesmo tempo." \([Percival, 2017](http://www.obeythetestinggoat.com/pages/book.html)\)

