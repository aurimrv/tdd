# 3.4 Teste Funcional com UnitTest

O [Selenium](https://www.selenium.dev/) é uma ferramenta capaz de simular as ações dos usuários em aplicações Web. Desse modo, ele pode e está sendo utilizado por nós na escrita dos chamados Testes Funcionais, ou seja, testes que executam as funcionalidades do sistema.

Considerando a Pirâmide de Teste apresentada na [Seção 2.3](../2-teste-de-software/2-3-fases-de-teste.md), classificamos os testes funcionais como sendo testes do topo da piramide, ou seja, testes funcionais são considerados aqui testes de sistema ou testes fim-a-fim. 

[Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) prega que os testes funcionais devem, na verdade, descrever histórias do usuário, sendo, os testes, um artefato a mais para se compreender as funcionalidades que o sistema deve possuir. Ele defende que as histórias sejam incluídas como comentários no teste funcional. Por exemplo, considerando o TDD e as metodologias ágeis, é comum se falar em produto mínimo viável \(do Inglês - MVP - _Minimal Viable Product_\). O MVP corresponde ao artefato mais simples que possamos construir e que seja viável.

A aplicação que será utilizada para isso sera uma lista de tarefas. Nesse sentido, uma lista de tarefas mínima, conforme descrito por [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html), deve permitir ao usuário inserir itens referentes a tarefas e posteriormente, ser lembrado das tarefas a serem executadas.

Considerando o teste funcional feito anteriormente \(`functional_tests.py`\), poderíamos editá-lo para representar uma história inicial, conforme abaixo.

```text
from selenium import webdriver

browser = webdriver.Firefox()

# Edith ouviu falar de uma nova aplicação online interessante
# para lista de tarefas. Ela decide verificar a homepage

browser.get("http://localhost:8000")

# Ela percebe que o título da página e o cabeçalho mencionam
# listas de tarefas (to-do)

assert 'To-Do' in browser.title

# Ela é convidada a inserir um item de tarefa imediatamente

# Ela digita "Buy peacock featers" (Comprar penas de pavão)
# em uma nova caixa de texto (o hobby de Edith é fazer iscas
# para pesca com fly)

# Quando ela tecla enter, a página é atualizada, e agora
# a página lista "1 - Buy peacock feathers" como um item em 
# uma lista de tarefas

# Ainda continua havendo uma caixa de texto convidando-a a 
# acrescentar outro item. Ela insere "Use peacock feathers 
# make a fly" (Usar penas de pavão para fazer um fly - 
# Edith é bem metódica)

# A página é atualizada novamente e agora mostra os dois
# itens em sua lista

# Edith se pergunta se o site lembrará de sua lista. Então
# ela nota que o site gerou um URL único para ela -- há um 
# pequeno texto explicativo para isso.

# Ela acessa essa URL -- sua lista de tarefas continua lá.

# Satisfeita, ela volta a dormir

browser.quit()
```

Observa-se que os comentários são utilizados para descrever a história e, algumas partes deles já são implementadas como teste.

Vamos executar esse teste? Já sabemos antecipadamente o resultado do mesmo, correto? Ele irá falar. Isso é o que [Percival \(2017\)](http://www.obeythetestinggoat.com/pages/book.html) classifica como uma **falha esperada**, ou seja, como o sistema ainda não implementa a funcionalidade desejara, o teste deveria falar.

#### Executando o Teste

Para executar esse teste, primeiramente precisamos inicializar o django para, em seguida, executar o teste. Assumindo que não temos terminais abertos, executaremos, inicialmente os comandos abaixo para inicializar o django.

```text
auri@av:~$ workon superlists
(superlists) auri@av:~$ cd tdd/superlists/superlists/
(superlists) auri@av:~/tdd/superlists/superlists$ python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
April 29, 2021 - 01:51:54
Django version 3.2, using settings 'superlists.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Em seguida, para executar os testes, devemos abrir um novo terminar e executar os comandos abaixo:

```text
auri@av:~/insync/tdd/superlists/superlists$ cd
auri@av:~$ workon superlists
(superlists) auri@av:~$ cd tdd/superlists/superlists/
(superlists) auri@av:~/tdd/superlists/superlists$ python functional_tests.py 
Traceback (most recent call last):
  File "functional_tests.py", line 13, in <module>
    assert 'To-Do' in browser.title
AssertionError
(superlists) auri@av:~/tdd/superlists/superlists$
```

Como será observado, o teste faz com que a janela do Firefox abra com o conteúdo abaixo e, desse modo, como o título não contém a palavra To-Do, o teste apresenta uma mensagem de erro no _prompt_, conforme mostrado nas linhas de 5 a 8 dos comandos acima.

![Tela do django com a execu&#xE7;&#xE3;o do caso de teste](../.gitbook/assets/django-02.png)

Antes de continuar, como fizamos alterações importantes no nosso arquivo `functional_tests.py`, tais alterações merecem ser confirmadas no nosso Git. Para isso, podemos executar os comandos abaixo:

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git status
No ramo master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (utilize "git add <arquivo>..." para atualizar o que será submetido)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   functional_tests.py

nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")
(superlists) auri@av:~/tdd/superlists/superlists$ git add functional_tests.py 
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -m "Improve history of functional test"
[master cd22f11] Improve history of functional test
 1 file changed, 36 insertions(+), 1 deletion(-)
```

Para enviar as alterações para o Github basta usar os comandos abaixo, considerando seu login e token de usuários do GitHub.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git push
Username for 'https://github.com': aurimrv
Password for 'https://aurimrv@github.com': 
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 12 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 925 bytes | 925.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/aurimrv/superlists.git
   2e3fda5..cd22f11  master -> master
```

#### Módulo unittest

Alguns aspectos com o nosso caso de teste chamam atenção e merecem ser melhorados. Um deles é o fato da janela do navegador ficar aberta após o término do caso de teste. O ideal seria que a mesma fosse fechada. Para nos auxiliar nessa e em outras tarefas, vamos utilizar um dos diversos frameworks para a execução automática de testes em Python. Nesse caso, faremos uso do módulo [unittest](https://docs.python.org/3/library/unittest.html).

O trecho de código abaixo, ilustra as alterações feitas no nosso caso de teste para torná-lo aderente ao framework unittest. Os detalhes das alterações são explicados a seguir.

```text
from selenium import webdriver
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
		self.fail('Finish the test!')

		# Ela é convidada a inserir um item de tarefa imediatamente

		# Ela digita "Buy peacock featers" (Comprar penas de pavão)
		# em uma nova caixa de texto (o hobby de Edith é fazer iscas
		# para pesca com fly)

		# Quando ela tecla enter, a página é atualizada, e agora
		# a página lista "1 - Buy peacock feathers" como um item em 
		# uma lista de tarefas

		# Ainda continua havendo uma caixa de texto convidando-a a 
		# acrescentar outro item. Ela insere "Use peacock feathers 
		# make a fly" (Usar penas de pavão para fazer um fly - 
		# Edith é bem metódica)

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

O primeiro ponto a ser observado é que no `unittest`, os testes são organizados dentro de uma classe que herda de `unittest.TestCase` \(linha 4\).

Um teste propriamente dito, é um método dentro dessa classe cujo nome se inicia por `test_`, como ocorre em `test_can_start_a_list_and_retrieve_it_later` \(linha 12\). Podemos ter vários métodos iniciando com `test_`dentro da mesma classe e é sempre bom dar nomes significativos para os testes para auxiliar na identificação de seus objetivos.

Existem dois métodos especiais presentes na nossa classe de test: `setUp` e `tearDown`. Eles servem para executar ações sempre antes e após a execução de cada teste, respectivamente. No caso, dentro do `setUp` nós estamos inicializando o navegador \(linhas 6 e 7\) e no `tearDown` encerramos sua execução \(linhas 9 e 10\). Recordando a terminologia de casos de teste apresentada durante o curso, ao utilizar tais métodos, a intenção é tornar cada caso de teste independente pois iremos reiniciar o estado do sistema sempre antes da execução de cada novo teste.

Para verificar se o texto desejado aparece no título do navegador estamos utilizando o método `self.assertIn`, disponível no framework \(linha 21\). Além dele, o framework também disponibiliza outros métodos de verificação como `assertEquals`, `assertTrue`, dentre outros. Uma lista completa dos métodos de verificação pode ser encontrada na página do framework, disponível em [https://docs.python.org/3/library/unittest.html\#assert-methods](https://docs.python.org/3/library/unittest.html#assert-methods).

O método `self.fail` faz o teste falhar \(linha 22\), independentemente de qualquer outras verificação de sucesso realizada. Está sendo usado apenas como um lembrete de que o teste ainda não está finalizado e, portanto, deve falhar.

Finalmente, o código `if __name__ == '__main__'` \(linha 50\) é a forma que o Python utiliza para identificar que um determinado programa é invocado via linha de comando para, então, executar o método principal do `unittest.main`.

Ao executar novamente o caso de teste veremos a diferença de comportamento, principalmente com a janela do navegador abrindo e fechando rapidamente, e o resultado da execução do teste aparecendo no prompt, conforme apresentado a seguir.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ python functional_tests.py 
F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (__main__.NewVsitorTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "functional_tests.py", line 21, in test_can_start_a_list_and_retrieve_it_later
    self.assertIn('To-Do', self.browser.title)
AssertionError: 'To-Do' not found in 'The install worked successfully! Congratulations!'

----------------------------------------------------------------------
Ran 1 test in 2.323s

FAILED (failures=1)
```

No exemplo acima podemos observar que o teste falhou pois a string "To-Do" não foi encontrada no título do navegador, o qual apresenta a string padrão da execução do django; "The install worked successfully! Congratulations!"

Novamente, devido as mudanças no arquivo de casos de testes é interessante fazermos um novo commit para confirmar as alterações. No exemplo abaixo, utilizamos um `git commit -a` que adiciona e confirma automaticamente todas as mudanças realizadas no repositório.

```text
(superlists) auri@av:~/tdd/superlists/superlists$ git commit -a -m "First unittest test case"
[master a241448] First unittest test case
 1 file changed, 51 insertions(+), 41 deletions(-)
 rewrite functional_tests.py (96%)
 
(superlists) auri@av:~/tdd/superlists/superlists$ git push
Username for 'https://github.com': aurimrv
Password for 'https://aurimrv@github.com': 
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 12 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 641 bytes | 641.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/aurimrv/superlists.git
   cd22f11..a241448  master -> master
```

