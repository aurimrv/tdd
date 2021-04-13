# 1.2 Configuração do Ambiente

Neste livro iremos trabalhar com desenvolvimento de produtos em [Python](https://www.python.org/). Desse modo, precisamos configurar o ambiente para a execução com sucesso de nossos exemplos. O passo a passo abaixo apresenta os passos de instalação para o ambiente Linux, no caso específico do [Linux Mint 20.1](https://www.linuxmint.com/download.php). Entretanto, sendo uma linguagem multi plataforma, os programas desenvolvidos aqui devem funcionar de forma idêntica em ambientes Windows e Mac.

O exemplo empregado neste livro foi extraído de [Percival \(2017\)](https://novatec.com.br/livros/tdd-com-python/) e consiste em um sistema de bloco de notas - versão Web. Ele é implementado em [Python](https://www.python.org/) utilizando-se o [framework Django](https://www.djangoproject.com/). Desse modo, a seguir, apresentamos o passo a passo da instalação do ambiente para execução dos exemplos aqui explorados.

**Instação do Python e Ferramentas de Desenvolvimento**

Inicialmente, faremos a instalação do Python, seu instalador de pacotes \(pip\) e o virtualenv, responsável pro criar um ambiente de execução de programas Python que pode ser personalizável a cada projeto, sem prejudicar a configuração global do sistema. Para isso, basta executar a sequência de comandos abaixo em um terminal Linux. No exemplo, é utilizado o [Linux Mint 20.1](https://www.linuxmint.com/download.php).

```text
sudo apt install python3
sudo apt install build-essential checkinstall
sudo apt install libreadline-gplv2-dev libncursesw5-dev libssl-dev \ 
libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev
sudo apt install python3-setuptools
sudo apt install python3-pip
sudo apt remove python-is-python2
sudo apt install python-is-python3
sudo pip3 install testresources
sudo pip3 install virtualenv
sudo pip3 install virtualenvwrapper
sudo apt install git
pip3 install virtualenv
pip3 install virtualenvwrapper
```

Após a instalação, para realizar o teste, basta consultar as versões dos programas instalados, como ilustrado abaixo:

```text
$ python --version
Python 3.8.5
$ pip3 --version
pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.8)
$ virtualenv --version
16.7.10
$ git --version
git version 2.25.1
```

**Instalação do Framework django e Selenium**

O [django](https://www.djangoproject.com/) é um framework para construçao de sistemas Web em Python e o [Selenium](https://selenium-python.readthedocs.io/) é uma ferramenta de captura e reprodução que permite a execução de testes automatizados via nevegador Web. Para instalar ambas as ferramentas e utiliza-las em nossos projetos, basta executar o comando abaixo:

```text
$ pip3 install "django<=3.2" "selenium<4"

Requirement already satisfied: django<=3.2 in ./.local/lib/python3.8/site-packages (3.2)
Requirement already satisfied: selenium<4 in ./.local/lib/python3.8/site-packages (3.141.0)
Requirement already satisfied: sqlparse>=0.2.2 in ./.local/lib/python3.8/site-packages (from django<=3.2) (0.4.1)
Requirement already satisfied: asgiref<4,>=3.3.2 in ./.local/lib/python3.8/site-packages (from django<=3.2) (3.3.4)
Requirement already satisfied: pytz in /usr/lib/python3/dist-packages (from django<=3.2) (2019.3)
Requirement already satisfied: urllib3 in /usr/lib/python3/dist-packages (from selenium<4) (1.25.8)
```

O Selenium permite a execução dos testes em vários navegadores mas, para cada um deles, é necessário baixar um driver correspondente. No nosso exemplo, faremos o download dos driver para Firefox, denominado de [Gecko Driver](https://github.com/mozilla/geckodriver/releases), e Google Chrome, denominado [Chrome Driver](https://chromedriver.chromium.org/), conforme descrito a seguir. Você deve baixar o driver na versão compatível com a de seu navegador web. No caso abaixo, utilizamos o [Geck Driver 0.29.1](https://github.com/mozilla/geckodriver/releases) e o [Chrome Driver 90.0.4430.24](https://chromedriver.chromium.org/), ambos para sistemas Linux 64 bits.

Primeiro é necessário fazer o download dos arquivos compactados com os drivers acima e, em seguida, a instalação de ambos os driver é feita simplesmente descompactando o conteúdo dos arquivos dentro da pasta `$HOME/.local/bin`, conforme apresentado a seguir:

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

**Variáveis de Ambiente**

Finalmente, para completar a instalação do ambiente, as seguintes variáveis de ambiente devem ser incluídas no final do arquivo `.bashrc`, localizado na raíz da área do usuário Linux.

```text
export PATH=$HOME/.local/bin:$PATH
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
export WORKON_HOME="$HOME/tdd"
source /usr/local/bin/virtualenvwrapper.sh
```

Basicamente, essas variáveis definem configurações necessárias para a execução das ferramentas de desenvolvimento e o nosso diretório de trabalho que, no exemplo acima, está localizado em `$HOME/tdd`.

