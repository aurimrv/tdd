# 6.4 Separando Templates

Pretendemos que a _view_ da página inicial e da lista sejam páginas bem distintas, portanto, merecem que sejam renderizadas por diferentes _templates_.

Na página principal, a intenção é que tenhamos uma página com um campo para a entrada de itens para uma nova lista. Já a página que exige a lista deve apresentar todos os itens disponíveis, já cadastrados.

Para iniciarmos essa separação dos _templates_, vamos, primeiro, criar um teste para guiar nossa implementação.

A nossa classe de teste `ListViewTest` ganhará o teste `test_uses_list_template`, conforme apresentado abaixo entre as linhas 36 a 38.

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

	def test_can_save_a_POST_request(self):
		self.client.post('/', data={'item_text': 'A new list item'})

		self.assertEquals(Item.objects.count(), 1)
		new_item = Item.objects.first()
		self.assertEquals(new_item.text, 'A new list item')

	def test_redirects_after_POST(self):
		response = self.client.post('/', data={'item_text': 'A new list item'})

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

Ao executar os testes, temos a mensagem de erro abaixo:

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.......F
======================================================================
FAIL: test_uses_list_template (lists.tests.ListViewTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", 
  line 38, in test_uses_list_template
    self.assertTemplateUsed(response, 'list.html')
...
AssertionError: False is not true : Template 'list.html' was not a 
template used to render the response. Actual template(s) used: home.html

----------------------------------------------------------------------
Ran 8 tests in 0.016s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Assim sendo, vamos ditar nossa _view_ para dar início a solução desse erro. Inicialmente vamos editar o arquivo `lists/views.py` para ficar conforme abaixo. Na linha 15, `'home.html'` foi substituído por `'list.html'`.

```python
from django.shortcuts import redirect, render
from lists.models import Item

# Create your views here.
def home_page(request):
	if request.method == 'POST':
		Item.objects.create(text=request.POST['item_text'])
		return redirect('/lists/the-only-list-in-the-world/')

	items = Item.objects.all()
	return render(request, 'home.html', {'items': items})

def view_list(request):
	items = Item.objects.all()
	return render(request, 'list.html', {'items': items})
```

Após essa alteração, a execução dos testes apresenta a saída abaixo:

```python
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
......EE
======================================================================
ERROR: test_displays_all_list_itens (lists.tests.ListViewTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 45, in test_displays_all_list_itens
    response = self.client.get('/lists/the-only-list-in-the-world/')
...
django.template.exceptions.TemplateDoesNotExist: list.html

======================================================================
ERROR: test_uses_list_template (lists.tests.ListViewTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 37, in test_uses_list_template
    response = self.client.get('/lists/the-only-list-in-the-world/')
...
django.template.exceptions.TemplateDoesNotExist: list.html

----------------------------------------------------------------------
Ran 8 tests in 0.035s

FAILED (errors=2)
Destroying test database for alias 'default'...
```

O próximo passo é criarmos o _template_ `list.html`. Como sempre, faremos sempre pelo menor esforço. O comando abaixo cria um arquivo vazio.

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ touch lists/templates/list.html
```

Após a execução do comando acima, os testes passa a apresentar uma nova mensagem de erro:

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
......F.
======================================================================
FAIL: test_displays_all_list_itens (lists.tests.ListViewTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/auri/insync/tdd/superlists/superlists/lists/tests.py", line 47, in test_displays_all_list_itens
    self.assertContains(response, 'itemey 1')
  File "/home/auri/insync/tdd/superlists/lib/python3.8/site-packages/django/test/testcases.py", line 471, in assertContains
    self.assertTrue(real_count != 0, msg_prefix + "Couldn't find %s in response" % text_repr)
AssertionError: False is not true : Couldn't find 'itemey 1' in response

----------------------------------------------------------------------
Ran 8 tests in 0.014s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Vamos então criar um _template_ mais elaborado. Como muito do que precisamos está em home.html, vamos copiar o conteúdo de `home.html` para `list.html` e, em seguida, alterar o `home.html` para manter apenas o que é necessário.

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ cp lists/templates/home.html lists/templates/list.html 
```

O conteúdo de `home.html` após as alterações fica conforme abaixo:

```markup
<html>
	<head>
		<title>To-Do lists</title>
	</head>
	<body>
		<h1>Your To-Do list</h1>
		<form method="POST" action="/">
			<input name="item_text" id="id_new_item" placeholder="Enter a to-do item" />
			{% csrf_token %}
		</form>
	</body>
</html>
```

Após as alterações, os testes passam e nos mostram que, após as alterações, não causamos efeitos colaterais no código.

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
........
----------------------------------------------------------------------
Ran 8 tests in 0.015s

OK
Destroying test database for alias 'default'...
```

Na nossa implementação, não há mais a necessidade de passar todos os itens para o _template_ home.html e, desse modo, podemos simplificar a função da _view_ para se adequar a isso. A nova função `home_page` - linhas 5 a 10 de `lists/views.py` - fica conforme abaixo:

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
```

Conforme observado abaixo, os testes unitários continuam passando.

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
........
----------------------------------------------------------------------
Ran 8 tests in 0.015s

OK
Destroying test database for alias 'default'...
```

E ao executar os testes funcionais observamos que os primeiros testes continuam passando e nosso novo teste avançou na sua execução. 

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
Ran 2 tests in 19.612s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Ótimo momento para colocarmos essas alterações sob controle de versão.

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ git status
No ramo master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (utilize "git add <arquivo>..." para atualizar o que será submetido)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   lists/templates/home.html
	modified:   lists/tests.py
	modified:   lists/views.py

Arquivos não monitorados:
  (utilize "git add <arquivo>..." para incluir o que será submetido)
	lists/templates/list.html

nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")

(superlists) auri@av:~/tdd/superlists/superlists$ git add lists/templates/list.html

(superlists) auri@av:~/tdd/superlists/superlists$ git commit -am "New URL, view and template to display lists" 
[master 65607e1] New URL, view and template to display lists
 4 files changed, 26 insertions(+), 22 deletions(-)
 create mode 100644 lists/templates/list.html

(superlists) auri@av:~/tdd/superlists/superlists$ git push
Username for 'https://github.com': aurimrv
Password for 'https://aurimrv@github.com': 
Enumerating objects: 13, done.
Counting objects: 100% (13/13), done.
Delta compression using up to 12 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 702 bytes | 702.00 KiB/s, done.
Total 7 (delta 5), reused 0 (delta 0)
remote: Resolving deltas: 100% (5/5), completed with 5 local objects.
To https://github.com/aurimrv/superlists.git
   8e35b4a..65607e1  master -> master

```

