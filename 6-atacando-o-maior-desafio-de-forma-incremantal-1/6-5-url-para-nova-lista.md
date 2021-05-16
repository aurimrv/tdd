# 6.5 URL para Nova Lista

Retomando nossa lista de tarefas proposta para o capítulo e reapresentada abaixo, observamos que tivemos algum progresso no segundo item embora ainda não o resolvemos por completo. O primeiro item da lista certamente é o mais desafiador. Nesta seção tentaremos avançar em relação ao item 3 para a criação de uma URL para a adição de novos itens na lista.

* [ ] Ajustar o modelo para que itens sejam associados a listas distintas
* [ ] Adicionar URLs exclusivos para cada lista, por exemplo, como `/lists/<identificador da lista>/`
* [ ] Adicionar um URL para criar uma lista via POST, por exemplo, como `/lists/new`
* [ ] Adicionar URL para acrescentar um novo item em uma lista existente, por exemplo,  como `/lists/<identificador da lista>/add_item`

#### Classe de Teste para Criação de Nova Lista

Como sempre, iniciamos o desenvolvimento de um caso de teste para guiar a implementação da nova funcionalidade. Nesse caso, na verdade, iremos modificar testes já existentes para fazer isso. Vamos iniciar movendo dois casos de teste da classe `HomePageTest` \(`test_can_save_a_POST_request` e `test_redirects_after_POST`\) para uma nova classe de teste denominada `NewListTest` e, alteraremos a URL para o qual o `POST` é enviado para `/lists/new`. As alterações podem ser vistas no código abaixo. A nova classe está entre as linhas 21 e 32.

```python
from django.urls import resolve
from django.test import TestCase
from lists.views import home_page
from lists.models import Item

class HomePageTest(TestCase):

	def test_root_url_resolves_to_home_page_view(self):
		found = resolve('/')
		self.assertEquals(found.func, home_page)

	def test_home_page_returns_correct_html(self):
		response = self.client.get('/')
		self.assertTemplateUsed(response, 'home.html')

	def test_only_saves_items_when_necessary(self):
		self.client.get('/')
		self.assertEquals(Item.objects.count(), 0)


class NewListTest(TestCase):

	def test_can_save_a_POST_request(self):
		self.client.post('/lists/new', data={'item_text': 'A new list item'})
		self.assertEquals(Item.objects.count(), 1)
		new_item = Item.objects.first()
		self.assertEquals(new_item.text, 'A new list item')

	def test_redirects_after_POST(self):
		response = self.client.post('/lists/new', data={'item_text': 'A new list item'})
		self.assertEquals(response.status_code, 302)
		self.assertEquals(response['location'], '/lists/the-only-list-in-the-world/')


class ListViewTest(TestCase):

	def test_uses_list_template(self):
		response = self.client.get('/lists/the-only-list-in-the-world/')
		self.assertTemplateUsed(response, 'list.html')


	def test_displays_all_list_itens(self):
		Item.objects.create(text='itemey 1')
		Item.objects.create(text='itemey 2')

		response = self.client.get('/lists/the-only-list-in-the-world/')

		self.assertContains(response, 'itemey 1')
		self.assertContains(response, 'itemey 2')


class ItemModelTest(TestCase):

	def test_saving_and_retriving_items(self):
		first_item = Item()
		first_item.text = 'The first (ever) list item'
		first_item.save()

		second_item = Item()
		second_item.text = 'Item the second'
		second_item.save()

		saved_items = Item.objects.all()
		self.assertEquals(saved_items.count(),2)

		first_saved_item = saved_items[0]
		second_saved_item = saved_items[1]

		self.assertEquals(first_saved_item.text, 'The first (ever) list item')
		self.assertEquals(second_saved_item.text, 'Item the second')
```

Antes de executar os testes, podemos ainda fazer uma alteração no método `test_redirects_after_POST`e usar um novo _assert_ do Django Test Cliete, denominado `assertRedirect` \(linha 31\). Ele faz exatamente o papel dos dois _asserts_ anteriores e simplifica o nosso teste. O código final completo da classe de teste é dado abaixo:

```python
from django.urls import resolve
from django.test import TestCase
from lists.views import home_page
from lists.models import Item

class HomePageTest(TestCase):

	def test_root_url_resolves_to_home_page_view(self):
		found = resolve('/')
		self.assertEquals(found.func, home_page)

	def test_home_page_returns_correct_html(self):
		response = self.client.get('/')
		self.assertTemplateUsed(response, 'home.html')

	def test_only_saves_items_when_necessary(self):
		self.client.get('/')
		self.assertEquals(Item.objects.count(), 0)


class NewListTest(TestCase):

	def test_can_save_a_POST_request(self):
		self.client.post('/lists/new', data={'item_text': 'A new list item'})
		self.assertEquals(Item.objects.count(), 1)
		new_item = Item.objects.first()
		self.assertEquals(new_item.text, 'A new list item')

	def test_redirects_after_POST(self):
		response = self.client.post('/lists/new', data={'item_text': 'A new list item'})
		self.assertRedirects(response, '/lists/the-only-list-in-the-world/')


class ListViewTest(TestCase):

	def test_uses_list_template(self):
		response = self.client.get('/lists/the-only-list-in-the-world/')
		self.assertTemplateUsed(response, 'list.html')


	def test_displays_all_list_itens(self):
		Item.objects.create(text='itemey 1')
		Item.objects.create(text='itemey 2')

		response = self.client.get('/lists/the-only-list-in-the-world/')

		self.assertContains(response, 'itemey 1')
		self.assertContains(response, 'itemey 2')


class ItemModelTest(TestCase):

	def test_saving_and_retriving_items(self):
		first_item = Item()
		first_item.text = 'The first (ever) list item'
		first_item.save()

		second_item = Item()
		second_item.text = 'Item the second'
		second_item.save()

		saved_items = Item.objects.all()
		self.assertEquals(saved_items.count(),2)

		first_saved_item = saved_items[0]
		second_saved_item = saved_items[1]

		self.assertEquals(first_saved_item.text, 'The first (ever) list item')
		self.assertEquals(second_saved_item.text, 'Item the second')
```

Ao executar os testes unitários, temos duas falhas conforme ilustrado abaixo:

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
......FF
======================================================================
FAIL: test_can_save_a_POST_request (lists.tests.NewListTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 25, in test_can_save_a_POST_request
    self.assertEquals(Item.objects.count(), 1)
AssertionError: 0 != 1

======================================================================
FAIL: test_redirects_after_POST (lists.tests.NewListTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 31, in test_redirects_after_POST
...
    self.assertEqual(
AssertionError: 404 != 302 : Response didn't redirect as expected: Response code was 404 (expected 302)

----------------------------------------------------------------------
Ran 8 tests in 0.014s

FAILED (failures=2)
Destroying test database for alias 'default'...
```

A primeira, na linha 11, indica que não estamos salvando o item no banco de dados e, desse modo, não conseguimos recuperar o item salvo. O segundo, linha 20, acusa que nosso URL `/lists/new` ainda não existe e, portanto, obtemos um 404 e não um 302 conforme desejado.

#### URL e View para Nova Lista

Vamos atacar esse problema da URL primeiro. Para isso iremos alterar os arquivos `superlists/urls.py` e `lists/views.py`, conforme abaixo:

```python
from django.urls import path
from lists import views

urlpatterns = [
    path('', views.home_page, name='home'),
    path('lists/new', views.new_list, name='new_list'),
    path('lists/the-only-list-in-the-world/', views.view_list, name='view_list'),
]
```

```python
from django.shortcuts import redirect, render
from lists.models import Item

# Create your views here.
def home_page(request):
	if request.method == 'POST':
		Item.objects.create(text=request.POST['item_text'])
		return redirect('/lists/the-only-list-in-the-world/')

	return render(request, 'home.html')

def view_list(request):
	items = Item.objects.all()
	return render(request, 'list.html', {'items': items})

def new_list(request):
	pass
```

Ao reexecutar os testes o erro muda para a falta de um retorno do tipo `HttpResponse` por parte da função `new_list`.

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
......EE
======================================================================
ERROR: test_can_save_a_POST_request (lists.tests.NewListTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 24, in test_can_save_a_POST_request
    self.client.post('/lists/new', data={'item_text': 'A new list item'})
 ...
ValueError: The view lists.views.new_list didn't return an 
HttpResponse object. It returned None instead.

======================================================================
ERROR: test_redirects_after_POST (lists.tests.NewListTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 30, in test_redirects_after_POST
    response = self.client.post('/lists/new', data={'item_text': 'A new list item'})
...
ValueError: The view lists.views.new_list didn't return an 
HttpResponse object. It returned None instead.

----------------------------------------------------------------------
Ran 8 tests in 0.027s

FAILED (errors=2)
Destroying test database for alias 'default'...
```

Vamos corrigir isso alterando o código de nossa função em `lists/views.py`, conforme abaixo:

```python
from django.shortcuts import redirect, render
from lists.models import Item

# Create your views here.
def home_page(request):
	if request.method == 'POST':
		Item.objects.create(text=request.POST['item_text'])
		return redirect('/lists/the-only-list-in-the-world/')

	return render(request, 'home.html')

def view_list(request):
	items = Item.objects.all()
	return render(request, 'list.html', {'items': items})

def new_list(request):
	return redirect('/lists/the-only-list-in-the-world/')
```

O resultado dos testes passa então a indicar o não salvamento do item, conforme abaixo:

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
......F.
======================================================================
FAIL: test_can_save_a_POST_request (lists.tests.NewListTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 25, in test_can_save_a_POST_request
    self.assertEquals(Item.objects.count(), 1)
AssertionError: 0 != 1

----------------------------------------------------------------------
Ran 8 tests in 0.015s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

O que pode ser resolvido utilizando o mesmo comando que usamos na função `home_page`, conforme abaixo:

```python
from django.shortcuts import redirect, render
from lists.models import Item

# Create your views here.
def home_page(request):
	if request.method == 'POST':
		Item.objects.create(text=request.POST['item_text'])
		return redirect('/lists/the-only-list-in-the-world/')

	return render(request, 'home.html')

def view_list(request):
	items = Item.objects.all()
	return render(request, 'list.html', {'items': items})

def new_list(request):
	Item.objects.create(text=request.POST['item_text'])
	return redirect('/lists/the-only-list-in-the-world/')
```

E nossos testes unitários passam a funcionar.

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
........
----------------------------------------------------------------------
Ran 8 tests in 0.015s

OK
Destroying test database for alias 'default'...
```

Os testes funcionais também indicam que estamos no mesmo estágio inicial que estávamos, conforme mostrado abaixo:

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test functional_tests
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.F
======================================================================
FAIL: test_multiple_users_can_start_lists_at_different_urls (tests.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/functional_tests/tests.py", line 117, in test_multiple_users_can_start_lists_at_different_urls
    self.wait_for_row_in_list_table('1: Buy milk')
...
AssertionError: '1: Buy milk' not found in ['1: Buy peacock feathers', 
'2: Buy milk']

----------------------------------------------------------------------
Ran 2 tests in 20.071s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Antes de prosseguirmos, como alteramos as URLs para fazerem o trabalho que antes estava na função `home_page`, a mesma pode ser simplificada conforme abaixo:

```python
from django.shortcuts import redirect, render
from lists.models import Item

# Create your views here.
def home_page(request):
	return render(request, 'home.html')

def view_list(request):
	items = Item.objects.all()
	return render(request, 'list.html', {'items': items})

def new_list(request):
	Item.objects.create(text=request.POST['item_text'])
	return redirect('/lists/the-only-list-in-the-world/')
```

Será que isso não quebrou os testes unitários? Não, felizmente. Vejamos o resultado da execução abaixo. Testes unitários ok.

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
........
----------------------------------------------------------------------
Ran 8 tests in 0.017s

OK
Destroying test database for alias 'default'...
```

E os testes funcionais, qual o resultado da execução dos mesmos com a correção da função `home_page`?

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test functional_tests
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
EE
======================================================================
ERROR: test_can_start_a_list_for_one_user (tests.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/functional_tests/tests.py", line 64, in test_can_start_a_list_for_one_user
    self.wait_for_row_in_list_table('1: Buy peacock feathers')
...
selenium.common.exceptions.NoSuchElementException: 
Message: Unable to locate element: [id="id_list_table"]


======================================================================
ERROR: test_multiple_users_can_start_lists_at_different_urls (tests.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/functional_tests/tests.py", line 93, in test_multiple_users_can_start_lists_at_different_urls
    self.wait_for_row_in_list_table('1: Buy peacock feathers')
...
selenium.common.exceptions.NoSuchElementException: 
Message: Unable to locate element: [id="id_list_table"]


----------------------------------------------------------------------
Ran 2 tests in 25.673s

FAILED (errors=2)
Destroying test database for alias 'default'...
```

No caso dos testes funcionais, como pode ser observado, tivemos uma regressão, pois agora, com a correção de `home_page`, precisamos fazer as correções de nossas URLs nos formulários.

#### Corrigindo URLs

Para lidar como erro acima, precisamos corrigir o atributo `action` no nosso `form` dos _templates_ `home.html` e`lists.html` \(linhas 7 em ambos os códigos, respectivamente\). A correção fica conforme abaixo:

```markup
<html>
	<head>
		<title>To-Do lists</title>
	</head>
	<body>
		<h1>Your To-Do list</h1>
		<form method="POST" action="/lists/new">
			<input name="item_text" id="id_new_item" placeholder="Enter a to-do item" />
			{% csrf_token %}
		</form>
	</body>
</html>
```

```markup
<html>
	<head>
		<title>To-Do lists</title>
	</head>
	<body>
		<h1>Your To-Do list</h1>
		<form method="POST" action="/lists/new">
			<input name="item_text" id="id_new_item" placeholder="Enter a to-do item" />
			{% csrf_token %}
		</form>
		<table id="id_list_table">
			{% for item in items %}
			<tr><td>{{ forloop.counter }}: {{ item.text }}</td></tr>
			{% endfor %}
		</table>
	</body>
</html>
```

Com essa correção, atingimos novamente o estado funcional que tínhamos anteriormente, conforme mostrado abaixo:

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test functional_tests
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.F
======================================================================
FAIL: test_multiple_users_can_start_lists_at_different_urls (tests.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/functional_tests/tests.py", line 117, in test_multiple_users_can_start_lists_at_different_urls
    self.wait_for_row_in_list_table('1: Buy milk')
...
AssertionError: '1: Buy milk' not found in ['1: Buy peacock feathers', '2: Buy milk']

----------------------------------------------------------------------
Ran 2 tests in 19.229s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Com isso, um dos itens de nossa lista pode ser marcado como feito.

* [ ] Ajustar o modelo para que itens sejam associados a listas distintas
* [ ] Adicionar URLs exclusivos para cada lista, por exemplo, como `/lists/<identificador da lista>/`
* [x] ~~Adicionar um URL para criar uma lista via POST, por exemplo, como `/lists/new`~~
* [ ] Adicionar URL para acrescentar um novo item em uma lista existente, por exemplo,  como `/lists/<identificador da lista>/add_item`

Ótimo momento para colocarmos as alterações sob controle de versão.

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -am "Test case for new URL added successfully. URL for new list added. View updated." 
[master 89328a3] Test case for new URL added successfully. URL for new list added. View updated.
 5 files changed, 17 insertions(+), 15 deletions(-)

(superlists) auri@av:~/tdd/superlists/superlists$ git push
Username for 'https://github.com': aurimrv
Password for 'https://aurimrv@github.com': 
Enumerating objects: 19, done.
Counting objects: 100% (19/19), done.
Delta compression using up to 12 threads
Compressing objects: 100% (10/10), done.
Writing objects: 100% (10/10), 952 bytes | 952.00 KiB/s, done.
Total 10 (delta 8), reused 0 (delta 0)
remote: Resolving deltas: 100% (8/8), completed with 8 local objects.
To https://github.com/aurimrv/superlists.git
   65607e1..89328a3  master -> master
```

