# 3.5 Teste de unidade e a evolução do sistema

Na seção anterior, iniciamos a escrita de um teste funcional que buscava um site que apresentasse título "To-Do" que não foi encontrado e, desse modo, o teste falhou.

Nesta seção, daremos início à construção da aplicação de forma mais acelerada e, para isso, iremos combinar dois tipos de testes, conforme sugerido por [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) na implantação do TDD. Mais detalhes do ciclo TDD sugerido por  [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) será apresentado mais adiante. O primeiro passo, entretanto, é configurar a aplicação no [Django](https://www.djangoproject.com/).

#### Primeira aplicação [Django](https://www.djangoproject.com/)

Considerando o nosso diretório de trabalho, conforme ilustrado abaixo, estando no diretório contendo o arquivo `manage.py`.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ tree .
.
├── db.sqlite3
├── functional_tests.py
├── geckodriver.log
├── manage.py
├── README.md
└── superlists
    ├── asgi.py
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-38.pyc
    │   ├── settings.cpython-38.pyc
    │   ├── urls.cpython-38.pyc
    │   └── wsgi.cpython-38.pyc
    ├── settings.py
    ├── urls.py
    └── wsgi.py

2 directories, 14 files
```

Estando nessa pasta de trabalho, vamos executar o comando para a criação de um projeto em Django. O Djando enfatiza a organização do código dos projetos em **aplicações**. Ao executar o comando da linha 1, `python manage.py startapp lists`, será criada a aplicação `superlists/lists`, ao lado da pasta `superlists/superlists`, conforme ilustrado abaixo.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py startapp lists
(superlists) auri@av:~/tdd/superlists/superlists$ tree .
.
├── db.sqlite3
├── functional_tests.py
├── geckodriver.log
├── lists
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
├── README.md
└── superlists
    ├── asgi.py
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-38.pyc
    │   ├── settings.cpython-38.pyc
    │   ├── urls.cpython-38.pyc
    │   └── wsgi.cpython-38.pyc
    ├── settings.py
    ├── urls.py
    └── wsgi.py

4 directories, 21 files
```

Como pode ser observado na estrutura de diretórios acima,  Django estrutura a aplicação segundo o padrão MVC \(_Model-View-Controller_\), e gerou arquivos que representam tal padrão. No nosso caso, o arquivo de maior interesse nesse momento é o arquivo `tests.py` \(linha 14\).

#### **Combinando Testes Funcionais e Testes Unitários no TDD**

A abordagem de TDD adotada por [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) segue a seguinte filosofia: testes funcionais testam a aplicação a partir do lado externo, do ponto de vista do usuário; testes unitários testam a aplicação do ponto de vista do programador. O ciclo do TDD preconizado por [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) é detalhado abaixo:

1. Iniciamos escrevendo um **teste funcional** que descreve uma nova funcionalidade do ponto de vista do usuário;
2. Em posse do teste funcional que falhe, começamos a pensar em como devemos escrever o código da aplicação para fazê-lo passar ou pelo menos chegar mais próximo da solução para fazê-lo passar. Para isso, usaremos nesse momento,um ou mais **testes de unidade** para definir como queremos o código das partes que irão compor nossa solução. A intenção é que cada linha do código de produção seja executada por ao menos um teste de unidade;
3. Tendo o teste de unidade que falhe escrevemos a menor quantidade possível de código da aplicação para fazer o teste passar. Pode ser necessário que os passos 2 e 3 se repitam algumas vezes até que ocorra um avanço em relação ao teste funcional criado no passo 1;
4. Ao final do ciclo dos passos 2 e 3 é possível executar novamente o teste funcional e verificar se eles passam, ou se avançam um pouco. Isso pode exigir que escrevamos mais testes de unidade e mais código da aplicação, e assim sucessivamente.

Como comentado por Percival \(2017\), esse ciclo e combinação de testes funcionais e testes de unidade podem parecer redundante mas o objetivo de cada tipo de teste é bem definido e são distintos.

> "Os testes funcionais devem ajudar você a construir uma aplicação com as funcionalidades corretas e garantir que você não causará falhas acidentalmente. Os testes de unidade deveriam ajudá-lo a escrever um código que seja limpo e livre de defeitos." \([Percival, 2017](http://www.obeythetestinggoat.com/pages/book.html)\)

#### Teste de Unidade no Django

Nessa seção veremos como escrever um teste unitário para view criada pelo Django. O _template_ do teste unitário criado pelo Django é ilustrado abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ cat lists/tests.py 
from django.test import TestCase

# Create your tests here.
```

Como observado na linha 2, o Django utiliza uma classe `TestCase` que é uma versão estendida da classe `unittest.TestCase`. Essa classe possui alguns recursos adicionais específicos do Django que faremos uso no restante deste capítulo.

Para iniciarmos, vamos redigir um teste que falhe propositalmente para verificar se o ambiente de execução está funcionando corretamente. Desse modo, o arquivo `lists/tests.py` foi alterado conforme abaixo:

```text
from django.test import TestCase

class SmokeTest(TestCase):
	def test_bad_maths(self):
		self.assertEquals( 1 + 1, 3)
```

A execução desse teste é feita conforme abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_bad_maths (lists.tests.SmokeTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 5, in test_bad_maths
    self.assertEquals( 1 + 1, 3)
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Observe que diferentemente do `functional_tests.py` que era executado diretamente, o teste do Django é executado de forma diferente, com o comando `python manage.py test`. Com o resultado acima o executor parece funcionar normalmente e esse é um bom momento para colocarmos nosso projeto sob controle de versão. Os passos para isso, adaptados de [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html), são dados abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git status         # Exibe que lists/ não está sob controle de versão
(superlists) auri@av:~/tdd/superlists/superlists$ git add lists
(superlists) auri@av:~/tdd/superlists/superlists$ git diff --staged  # Mostra as diferenças que serão confirmadas
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -m "Add app for lists, with deliberately failing unit test"
(superlists) auri@av:~/tdd/superlists/superlists$ git push           # Envia alterações para o GitHub
```

#### Explicação Básica sobre Django

Conforme mencionado anteriormente, o Django está estruturado conforme o padrão MVC. Desse modo, ele tem modelos mas, conforme comentado por [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html), suas _views_ estão mais para controladores, e são os _templates_ que, na verdade, compõem a visão. De modo geral, como qualquer servidor Web, o papel principal do Django é decidir o que fazer ao receber uma requisição via URL.

O fluxo do Django pode ser resumido a \([Percival, 2017](http://www.obeythetestinggoat.com/pages/book.html)\):

1. Uma requisição HTTP chega a um URL específico;
2. O Django utiliza algumas regras para decidir qual função de _view_ deve lidar com a requisição \(resolver a URL\);
3. A função _view_ processa a requisição e devolve uma resposta HTTP.

Desse modo, pensando no nosso teste, dois pontos precisam ser testados \([Percival, 2017](http://www.obeythetestinggoat.com/pages/book.html)\):

* Podemos resolver o URL da raiz do site \("/"\) para uma determinada função de view que criamos?
* Podemos fazer essa função de _view_ devolver um pouco de HTML que fará o teste funcional passar?

Para resolver o primeiro ponto, alteramos o nosso teste `lists/tests.py` conforme abaixo:

```text
from django.urls import resolve
from django.test import TestCase
from lists.views import home_page

class HomePageTest(TestCase):

	def test_root_url_resolves_to_home_page_view(self):
		found = resolve('/')
		self.assertEquals(found.func, home_page)
```

Em relação ao código acima, a chamada da linha 8, `resolve('/')`, é uma função interna do Django que resolve um URL para descobrir qual função de view deve tratar a requisição. No exemplo, quando chamada com `'/'`, que é a raiz do site, esperamos que tal URL seja resolvido para a função `home_page`.

Como podemos observar ao executar esse teste, a função `home_page` ainda não está implementada \(mensagem da linha 15\) e, portanto, o teste ainda falha.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
System check identified no issues (0 silenced).
E
======================================================================
ERROR: lists.tests (unittest.loader._FailedTest)
----------------------------------------------------------------------
ImportError: Failed to import test module: lists.tests
Traceback (most recent call last):
  File "/usr/lib/python3.8/unittest/loader.py", line 436, in _find_test_path
    module = self._get_module_from_name(name)
  File "/usr/lib/python3.8/unittest/loader.py", line 377, in _get_module_from_name
    __import__(name)
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 3, in <module>
    from lists.views import home_page
ImportError: cannot import name 'home_page' from 'lists.views' (/home/auri/insync/tdd/superlists/superlists/lists/views.py)


----------------------------------------------------------------------
Ran 1 test in 0.000s

FAILED (errors=1)
```

#### Iniciando a Escrita de Código da Aplicação

Como observado no exemplo acima, o teste ainda falha pois não existe uma função de _views_ responsável por tratar determinada URL. Para fornecer uma implementação para isso devemos editar o arquivo `lists/views.py`. Tentando fazer o mínimo necessário para fazer o teste passar, o código abaixo é uma tentativa de solução.

```text
from django.shortcuts import render

# Create your views here.
home_page = None
```

Ao executar o teste novamente, o resultado é exibido abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
E
======================================================================
ERROR: test_root_url_resolves_to_home_page_view (lists.tests.HomePageTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 8, in test_root_url_resolves_to_home_page_view
    found = resolve('/')
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/django/urls/base.py", line 24, in resolve
    return get_resolver(urlconf).resolve(path)
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/django/urls/resolvers.py", line 585, in resolve
    raise Resolver404({'tried': tried, 'path': new_path})
django.urls.exceptions.Resolver404: {'tried': [[<URLResolver <URLPattern list> (admin:admin) 'admin/'>]], 'path': ''}

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (errors=1)
Destroying test database for alias 'default'...
```

Ainda não foi dessa fez que nosso teste passou e [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) nos alerta que temos que aprender a ler as mensagens de erro para nos ajudar a compreender o que ocorreu e tentar corrigir o código da aplicação para que o nosso teste passe.

O primeiro ponto a ser observado é o erro que ocorreu \(linha 15\). Em geral, ele deveria ser o suficiente para compreendermos o problema e fazermos as devidas correções mas, nesse caso, ele parece meio complicado.

O segundo ponto a ser verificado é qual teste está falhando \(linha 6\). Nesse caso é o que esperávamos.

O terceiro ponto é verificar o que está cursando o erro e, no exemplo, é a chamada ao `resolve('/')` \(linha 8\). Nesse caso, ao tentar resolver o URL fornecido, o Django está devolvendo um erro 404.

#### urls.py

Conforme observamos no erro acima, os testes indicam que precisamos de um mapeamento de URL. Isso é feito em Django no arquivo `urls.py`, que organiza os mapeamentos entre URLs e funções de view.

Há um arquivo `urls.py` para todo site e ele está localizado na pasta `superlists/superlists`.  Para resolver o problema momentaneamente vamos alterá-lo conforme abaixo:

```text
"""superlists URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/3.2/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""

from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
```

Além dos comentários que nos ajudam a compreender como alterar o código, o código acima apresenta como o Django faz os mapeamentos. Basicamente, na linha 5, o primeiro parâmetro é uma string utilizada para indicar quais URLs atendem ao padrão desejado e, os demais, indicam quais funções de view são chamada.

Para atender as nossas necessidades, vamos utilizar as instruções presentes nas linhas 7 e 8, mapeando para o nosso exemplo. 

```text
from django.urls import path
from lists import views

urlpatterns = [
    path('', views.home_page, name='home'),
]
```

Ao executar os testes agora temos o seguinte resultado:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
Destroying test database for alias 'default'...
Traceback (most recent call last):
  File "manage.py", line 22, in <module>
    main()
 ...
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/django/urls/conf.py", line 73, in _path
    raise TypeError('view must be a callable or a list/tuple in the case of include().')
TypeError: view must be a callable or a list/tuple in the case of include().
```

O trace do erro é longo e a maior parte foi omitida do resultado acima. Observe, entretanto, que não temos mais um erro 404. O problema agora é que, ao resolver o URL deveríamos encontrar uma função de _view_ e o que temos até o momento é apenas uma variável. Vamos corrigir isso alterando o arquivo `lists/views.py` com o conteúdo abaixo:

```text
from django.shortcuts import render

# Create your views here.
def home_page():
	pass
```

Finalmente, o resultado da execução do teste passou, conforme apresentado abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
Destroying test database for alias 'default'...
```

Como chegamos a um resultado satisfatório até aqui é hora de confirmarmos nossas e colocar o código alterado sob controle de versão.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -am "First unit test and url mapping, dummy view"
(superlists) auri@av:~/tdd/superlists/superlists$ git push
```

O primeiro comando é uma forma rápida de adicionar as alterações e já confirmar. O segundo envia as alterações para o GitHub.

