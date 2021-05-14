# 1.2 Configuração Inicial do Ambiente

Neste livro iremos trabalhar com desenvolvimento de produtos em [Python](https://www.python.org/). Desse modo, precisamos configurar o ambiente para a execução com sucesso de nossos exemplos. O passo a passo abaixo apresenta os passos de instalação para o ambiente Linux, no caso específico do [Linux Mint 20.1](https://www.linuxmint.com/download.php). Entretanto, sendo uma linguagem multi plataforma, os programas desenvolvidos aqui devem funcionar de forma idêntica em ambientes Windows e Mac.

O exemplo empregado neste livro foi extraído de [Percival \(2017\)](https://novatec.com.br/livros/tdd-com-python/) e consiste em um sistema de bloco de notas - versão Web. Ele é implementado em [Python](https://www.python.org/) utilizando-se o [framework Django](https://www.djangoproject.com/). Desse modo, a seguir, apresentamos o passo a passo da instalação do ambiente para execução dos exemplos aqui explorados.

**Instalação do Python e Ferramentas de Desenvolvimento**

Inicialmente, faremos a instalação do Python, seu instalador de pacotes \(pip\) e o `virtualenv`, responsável pro criar um ambiente de execução de programas Python que pode ser personalizável a cada projeto, sem prejudicar a configuração global do sistema. Para isso, basta executar a sequência de comandos abaixo em um terminal Linux. No exemplo, é utilizado o [Linux Mint 20.1](https://www.linuxmint.com/download.php).

```bash
sudo apt install python3
sudo apt install build-essential checkinstall
sudo apt install libreadline-gplv2-dev libncursesw5-dev libssl-dev \ 
libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev git
sudo apt install python3-setuptools
sudo apt install python3-pip
sudo apt remove python-is-python2
sudo apt install python-is-python3
pip3 install testresources
pip3 install virtualenv
pip3 install virtualenvwrapper
```

Após a instalação, para realizar o teste, basta consultar as versões dos programas instalados, como ilustrado abaixo:

```bash
$ python --version
Python 3.8.5
$ pip3 --version
pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.8)
$ git --version
git version 2.25.1
```

**Variáveis de Ambiente**

Finalmente, para completar a instalação do ambiente, as seguintes variáveis de ambiente devem ser incluídas no final do arquivo `.bashrc`, localizado na raiz da área do usuário Linux.

```bash
export PATH=$HOME/.local/bin:$PATH
export WORKON_HOME="$HOME/tdd"
```

Basicamente, essas variáveis definem configurações necessárias para a execução das ferramentas de desenvolvimento básicas e o nosso diretório de trabalho que, no exemplo acima, está localizado em `$HOME/tdd`.

