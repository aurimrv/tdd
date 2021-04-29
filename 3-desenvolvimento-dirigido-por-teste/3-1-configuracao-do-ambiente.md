# 3.1 Configuração do Ambiente

Considerando a aplicação que será resenvolvida, ela fará uso do framework django e para realizarmos o TDD, também precisaremos do Selenium. Segue abaixo a instrução para a instalação de ambos.Após a instalação das ferramentas básicas, conforme apresentado na [Seção 1.2](../1-introducao/1-2-configuracao-inicial-do-ambiente.md), a seguir são apresentadas as demais configurações para dar início ao desenvolvimento da aplicação exemplo com TDD. 

O primeiro passo é verificarmos as versões das ferramentas já instaladas. Os comandos a seguir permitem fazer tal verificação.

```text
$ python --version
Python 3.8.5
$ pip3 --version
pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.8)
$ git --version
git version 2.25.1
```

Com essas ferramentas instaladas e as variáveis de ambiente definidas, é possível agora iniciarmos a instalação das ferramentas do Python que permitem a criação do chamado ambiente virtual de desenvolvimento. Ou seja, o `virtualenv` permite que sejam configurados diferentes ambientes de programação, cada um deles com diferentes requisitos de pacotes e bibliotecas, evitando assim que tenhamos que fazer isso sempre em todo o sistema.

O primeiro passo para isso é instalar as ferramentas abaixo:

```text
pip3 install testresources
pip3 install virtualenv
pip3 install --user virtualenvwrapper
```

Finalizada a instalação das ferramentas, é necessário incluir mais algumas linhas no arquivo `.bashrc` conforme descrito a seguir.

**Atualizando as Variáveis de Ambiente**

Finalmente, para completar a instalação do ambiente, as seguintes variáveis de ambiente devem ser incluídas no final do arquivo `.bashrc`, localizado na raiz da área do usuário Linux. Observa-se que as linhas 1 e 2 abaixo já devem estar presentes no seu arquivo de configuração se você seguiu os passos da [Seção 1.2](../1-introducao/1-2-configuracao-inicial-do-ambiente.md).

```text
export PATH=$HOME/.local/bin:$PATH
export WORKON_HOME="$HOME/tdd"
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
source /usr/local/bin/virtualenvwrapper.sh
```

Basicamente, essas variáveis definem configurações necessárias para a execução das ferramentas de desenvolvimento e o nosso diretório de trabalho que, no exemplo acima, está localizado em `$HOME/tdd`.

#### Configurando o Ambiente Virtual

O primeiro passo para dar início ao desenvolvimento da nossa aplicação é a criação do ambiente virtual. Para isso, vamos executar os comandos abaixo:

```text
$ cd $HOME/tdd
$ mkvirtualenv superlists
Using base prefix '/usr'
New python executable in /home/auri/insync/tdd/superlists/bin/python3
Also creating executable in /home/auri/insync/tdd/superlists/bin/python
Installing setuptools, pip, wheel...
done.
virtualenvwrapper.user_scripts creating /home/auri/tdd/superlists/bin/predeactivate
virtualenvwrapper.user_scripts creating /home/auri/tdd/superlists/bin/postdeactivate
virtualenvwrapper.user_scripts creating /home/auri/tdd/superlists/bin/preactivate
virtualenvwrapper.user_scripts creating /home/auri/tdd/superlists/bin/postactivate
virtualenvwrapper.user_scripts creating /home/auri/tdd/superlists/bin/get_env_details
```

Ao final da execução do comando `mkvirtualenv`, observa-se uma pequena mudança no _prompt_ de comando, ao invés de aparecer apenas um sinal de `$`, o _prompt_ passa a ser precedido por `(superlists)$` , indicando que você está conectado ao ambiente virtual. A estrutura de diretório do ambiente virtual, considerando apenas dois níveis, é dada abaixo:

```text
$ tree -L 2 superlists
superlists
├── bin
│   ├── activate
│   ├── activate.csh
│   ├── activate.fish
│   ├── activate.ps1
│   ├── activate_this.py
│   ├── activate.xsh
│   ├── get_env_details
│   ├── pip
│   ├── pip3
│   ├── pip3.8
│   ├── postactivate
│   ├── postdeactivate
│   ├── preactivate
│   ├── predeactivate
│   ├── python -> python3
│   ├── python3
│   ├── python3.8 -> python3
│   ├── python-config
│   └── wheel
├── include
│   └── python3.8 -> /usr/include/python3.8
└── lib
    └── python3.8
```

Os comandos a seguir mostram como desativar e ativar o `virtualenv`. Ou seja, estando no ambiente virtual, basta usar o comando `deactivate` que você deixa o ambiente. Se necessitar retornar, basta executar o comando `workon superlists`.

```text
(superlists) $ deactivate 
$ workon superlists
(superlists) $ 
```

#### Instalação do Django e Selenium dentro do Ambiente Virtual

O [django](https://www.djangoproject.com/) é um framework para construção de sistemas Web em Python e o [Selenium](https://selenium-python.readthedocs.io/) é uma ferramenta de captura e reprodução que permite a execução de testes automatizados via navegador Web. Para instalar ambas as ferramentas e utilizá-las em nossos projetos, basta executar o comando abaixo:

```text
(superlists) auri@av:~/tdd$ pip3 install "django<=3.2" "selenium<4"
Collecting django<=3.2
  Using cached Django-3.2-py3-none-any.whl (7.9 MB)
Collecting selenium<4
  Using cached selenium-3.141.0-py2.py3-none-any.whl (904 kB)
Collecting sqlparse>=0.2.2
  Using cached sqlparse-0.4.1-py3-none-any.whl (42 kB)
Collecting pytz
  Using cached pytz-2021.1-py2.py3-none-any.whl (510 kB)
Collecting asgiref<4,>=3.3.2
  Using cached asgiref-3.3.4-py3-none-any.whl (22 kB)
Collecting urllib3
  Downloading urllib3-1.26.4-py2.py3-none-any.whl (153 kB)
     |████████████████████████████████| 153 kB 3.8 MB/s 
Installing collected packages: urllib3, sqlparse, pytz, asgiref, selenium, django
Successfully installed asgiref-3.3.4 django-3.2 pytz-2021.1 selenium-3.141.0 sqlparse-0.4.1 urllib3-1.26.4
```

O Selenium permite a execução dos testes em vários navegadores mas, para cada um deles, é necessário baixar um driver correspondente. No nosso exemplo, faremos o download dos drivers para Firefox, denominado de [Gecko Driver](https://github.com/mozilla/geckodriver/releases), e Google Chrome, denominado [Chrome Driver](https://chromedriver.chromium.org/), conforme descrito a seguir. Você deve baixar o driver na versão compatível com a de seu navegador web. No caso abaixo, utilizamos o [Geck Driver 0.29.1](https://github.com/mozilla/geckodriver/releases) e o [Chrome Driver 90.0.4430.24](https://chromedriver.chromium.org/), ambos para sistemas Linux 64 bits.

Primeiro é necessário fazer o download dos arquivos compactados com os drivers acima e, em seguida, a instalação de ambos os drivers é feita simplesmente descompactando o conteúdo dos arquivos dentro da pasta `$HOME/.local/bin`, conforme apresentado a seguir:

```text
$ cd $HOME/.local/bin
$ wget https://github.com/mozilla/geckodriver/releases/download/v0.29.1/geckodriver-v0.29.1-linux64.tar.gz
$ tar zxvf geckodriver-v0.29.1-linux64.tar.gz 

$ wget https://chromedriver.storage.googleapis.com/90.0.4430.24/chromedriver_linux64.zip
$ unzip chromedriver_linux64.zip 

$ rm geckodriver-v0.29.1-linux64.tar.gz chromedriver_linux64.zip
```

Feita as instalações, é possível conferir as versões dos drivers com os comandos abaixo:

```text
$ ./geckodriver --version
geckodriver 0.29.1 (970ef713fe58 2021-04-08 23:34 +0200)

The source code of this program is available from
testing/geckodriver in https://hg.mozilla.org/mozilla-central.

This program is subject to the terms of the Mozilla Public License 2.0.
You can obtain a copy of the license at https://mozilla.org/MPL/2.0/.
```

```text
./chromedriver --version
ChromeDriver 90.0.4430.24 (4c6d850f087da467d926e8eddb76550aed655991-refs/branch-heads/4430@{#429})
```

Pronto. Ao final de todos esses passos estamos prontos para dar início ao desenvolvimento de nossa aplicação com base no TDD. Vamos lá!?

