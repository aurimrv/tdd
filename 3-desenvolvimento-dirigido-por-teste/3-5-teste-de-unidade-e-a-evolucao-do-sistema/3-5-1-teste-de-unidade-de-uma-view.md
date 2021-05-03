# 3.5.1 Teste de Unidade de uma View

Continuando com o desenvolvimento da aplicação utilizando o TDD, vamos agora criar mais um teste que irá demandar a existência de uma _view_ verdadeira, ou seja, nossa aplicação terá que exibir algo para o usuário, efetivamente.

A alteração no nosso teste para a implementação desse incremento é dada abaixo:

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
		self.assertTrue(html.startswith('<html>'))
		self.assertIn('<title>To-Do lists</title>', html)
		self.assertTrue(html.endswith('</html>'))
```

De forma resumida, o novo teste, listado entre as linhas 13 e 19, faz uma chamada para nossa `home_page` e assegura de que o resultado inicie com a tag `'<html>'` e termine com `'</html>'` e, além disso, encontre `'<title>To-Do lists</title>` na mensagem retornada.

Ao executa o teste, como era de se esperar, obtemos uma falha.

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
TypeError: home_page() takes 0 positional arguments but 1 was given

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (errors=1)
Destroying test database for alias 'default'...
```

Respeitando o ciclo do TDD, vamos iniciar a inclusão de código com o mínimo esforço possível para tentar fazer nosso teste passar. Desse modo, a primeira correção é fazer a função `home_page` receber um argumento, conforme acusado pelo erro no teste \(linha 11 acima\). Assim sendo, a seguir, é aprentada uma sequência de correções/reexecução dos testes, até que o teste passe por completo. Lembre-se que no TDD a intenção é sempre de tentar fazer o mínimo necessário para passar o teste.

#### Alteração mínima no código lists/views.py

```text
(superlists) auri@av:~/tdd/superlists/superlists$ cat lists/views.py 
from django.shortcuts import render

# Create your views here.
def home_page(request):
	pass(superlists) 
```

#### Resultado dos testes lists/tests.py

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
E.
======================================================================
ERROR: test_home_page_returns_correct_html (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 16, in test_home_page_returns_correct_html
    html = response.content.decode('utf-8')
AttributeError: 'NoneType' object has no attribute 'content'

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (errors=1)
Destroying test database for alias 'default'...
```

#### Alteração mínima no código lists/views.py

Cuidando para que um objeto `HttpResponse` seja retornado.

```text
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def home_page(request):
	return HttpResponse()
```

#### Resultado dos testes lists/tests.py

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F.
======================================================================
FAIL: test_home_page_returns_correct_html (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 17, in test_home_page_returns_correct_html
    self.assertTrue(html.startswith('<html>'))
AssertionError: False is not true

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

#### Alteração mínima no código lists/views.py

```text
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def home_page(request):
	return HttpResponse('<html>')
```

#### Resultado dos testes lists/tests.py

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F.
======================================================================
FAIL: test_home_page_returns_correct_html (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 18, in test_home_page_returns_correct_html
    self.assertIn('<title>To-Do lists</title>', html)
AssertionError: '<title>To-Do lists</title>' not found in '<html>'

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

#### Alteração mínima no código lists/views.py

```text
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def home_page(request):
	return HttpResponse('<html><title>To-Do lists</title>')
```

#### Resultado dos testes lists/tests.py

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
Ran 2 tests in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

#### Alteração mínima no código lists/views.py

```text
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def home_page(request):
	return HttpResponse('<html><title>To-Do lists</title></html>')
```

#### Resultado dos testes lists/tests.py

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
..
----------------------------------------------------------------------
Ran 2 tests in 0.001s

OK
Destroying test database for alias 'default'...
```

Nesse ponto tudo parece estar correto, mas será que já finalizamos? Que tal tentarmos executar os testes funcionais para confirmarmos se realmente encerramos um ciclo do TDD?

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python functional_tests.py 
F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (__main__.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "functional_tests.py", line 22, in test_can_start_a_list_and_retrieve_it_later
    self.fail('Finish the test!')
AssertionError: Finish the test!

----------------------------------------------------------------------
Ran 1 test in 3.101s

FAILED (failures=1)
```

O resultado mostra uma falha, mas isso era apenas para lembrar que ainda precisamos continuar com essa história. O início dela, de ter uma página web com um título contendo a palavra To-Do está feito.

Para encerrar essa etapa, nada melhor do que colocarmos nossas mudanças no controle de versão.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git diff
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -am "Basic view now returns minimal HTML"
(superlists) auri@av:~/tdd/superlists/superlists$ git push
```

