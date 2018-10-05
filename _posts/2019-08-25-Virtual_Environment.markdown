---
layout: post
title: "PipEnv e Virtual Enviroment (Python)"
date: 2018-08-25 15:00:00 -0300
tags: [Ambiente, Python]
img: library-1.jpg
output: html_document      
---


Para facilitar o controle de dependências de uma projeto Python podemos isolar um ambiente para executar com um versão controlada do Python e dos seus pacotes<br>
Com um [Ambiente Python](../Ambiente_Python) preparado podemos instalar o pacote do pipenv, nosso gestor de ambientes virtuais, com ele podemos: 

* Controlar os pacotes<br>
* Isolar para execução<br>
* Saber as dependências do projeto<br>
* Faciliitar a duplicação do ambiente para produção e desenvolvimento<br>

Instalando pelo **pip** do repositório do [PyPi](https://pypi.org/)<br>


{% highlight bash %}
pip install pipenv
{% endhighlight %}

## Gerenciar Ambiente Virtual

Para criar um ambiente virtual, usando a versão instalada e configurada como padrão do Python<br>


{% highlight bash %}
pipenv install 
{% endhighlight %}

Ou podemos definir uma versão com a diretiva `--python`


{% highlight bash %}
pipenv --python 3.6
{% endhighlight %}

Após a instalação podemos entrar no ambiente virtual 


{% highlight bash %}
pipenv shell 
{% endhighlight %}

Assim podemos instalar um pacote de maneira muito semelhante ao **pip**


{% highlight bash %}
pipenv install <nome do pacote>
{% endhighlight %}

Com o comando anterior o sistema cria no arquvio **Pipfile** na pasta atual, o arquivo contém todas as configurações do ambiente como versão do Pyhton e pacotes instalados nele<br>
Podemos congelar as versões dos pacotes


{% highlight bash %}
pipenv lock
{% endhighlight %}

Gerando o arquivo **Pipfile.lock** com as versões dos pacotes<br>
Para instalar um ambiente apartir de um já configurado basta executar novamente o comando em uma pasta com os **Pipfile** e o **Pipfile.lock**<br>
No exemplo a seguir vamos usar o pacote flask, que mesmo instalado no ambiente da máquina, precisa estar no *Virtual*, ou somente no virtual


{% highlight bash %}
pipenv install flask
{% endhighlight %}

## Programa

Com biblioteca do *Flask* instalada, basta criar um arquivo **app.py** desta maneira


{% highlight python %}
import os
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Aeeee!!! Funciona'
	
if __name__ == '__main__':
    port = int(os.environ.get("PORT", 5000))
    app.run(host='0.0.0.0', port=port)
{% endhighlight %}

## Execução

Para executar a aplicação em ambiente virtual, podemos executar a chamada `flask run` pelo shell 


{% highlight bash %}
pipenv shell
{% endhighlight %}

Ou executar direto para execução da aplicação 


{% highlight bash %}
pipenv run python app.py
{% endhighlight %}

## Referências

[Flask - Quickstart](http://flask.pocoo.org/docs/1.0/quickstart/)<br>
[How to manage your Python projects with Pipenv](https://robots.thoughtbot.com/how-to-manage-your-python-projects-with-pipenv)<br>
