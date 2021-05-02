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



