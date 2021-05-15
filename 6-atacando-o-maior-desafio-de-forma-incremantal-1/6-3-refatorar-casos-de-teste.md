# 6.3 Refatorar Casos de Teste

Recordando o nosso ciclo de TDD \(vermelho, verde, refatorar\), quando chegamos ao estado verde, sempre devemos ponderar se cabe alguma refatoração em nosso código, seja código de aplicação ou código de teste.

Agora, nossa aplicação está com duas _views_: uma responsável por lidar com a página principal e outra para lidar com a lista de itens individual. Ambas usam o mesmo _template_, passando ao mesmo todos os itens disponíveis no banco de dados.

Se avaliarmos os testes unitários que temos disponível pode ser que exita algum que possa ser eliminado decorrente da evolução da aplicação. O comando abaixo percorre nosso arquivo `lists/tests.py` e apresenta o nome de todos os métodos de teste.

```python
(superlists) auri@av:~/tdd/superlists/superlists$ grep -E "class|def" lists/tests.py 
class HomePageTest(TestCase):
	def test_root_url_resolves_to_home_page_view(self):
	def test_home_page_returns_correct_html(self):
	def test_only_saves_items_when_necessary(self):
	def test_can_save_a_POST_request(self):
	def test_redirects_after_POST(self):
	def test_displays_all_list_itens(self):
class ListViewTest(TestCase):
	def test_displays_all_list_itens(self):
class ItemModelTest(TestCase):
	def test_saving_and_retriving_items(self):
```

O último método de nossa classe de teste `HomePage`, `test_displays_all_list_itens`, pode ser eliminado, pois, criamos a classe `ListViewTest` e o método de teste `test_displays_all_list_itens` mais adequado para a evolução de nossa aplicação. Desse modo, o conjunto de testes unitários ficará conforme abaixo.

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

A execução do mesmo, sendo uma refatoração, deveria continuar sendo de sucesso, conforme ilustrado abaixo:

```bash
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py test lists
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.......
----------------------------------------------------------------------
Ran 7 tests in 0.014s

OK
Destroying test database for alias 'default'...
```

Desse modo, temos um teste melhorado após a refatoração. Vamos retomar a evolução de nossa aplicação.





