---
layout: post
title: "PipEnv e Virtual Enviroment (Python)"
date: 2018-08-25 15:00:00 -0300
tags: [Ambiente, Python]
img: python-1.jpg
output: html_document      
---



Isolar um ambiente para executar com um versão controlada do Python e dos seus pacotes, são úteis para facilitar a implantação da aplicação, podemos utilizar os <br>
Com um [Ambiente Python](../Ambiente_Python) preparado podemos instalar o pacote do pipenv 

biblioteca de pacotes para o Python [PyPI](https://pypi.org/)

Controlar os pacotes<br>
Isolar ambiente dos pacotes<br>
Saber as dependências do projeto<br>
Faciliitar a duplicação do ambiente para execução e desenvolvimento<br>


{% highlight bash %}
pip install pipenv
{% endhighlight %}

## Gerenciar ambiente virtual

Para criar um ambiente virtual navegue para a pasta de trabalho e execute o comando 'pipenv install' ou 'pipenv shell' usando a versão instalada e configurada como padrão do Python<br>


{% highlight bash %}
pipenv install 
{% endhighlight %}


{% highlight bash %}
pipenv install 
{% endhighlight %}


Com o comando anterior o sistema cria no arquvio **Pipfile** na pasta atual, o arquivo contém todas as configurações do ambiente como versão do Pyhton e pacotes instalados nele<br>
Podemos congelar as versões com o 


{% highlight bash %}
pipenv lock
{% endhighlight %}

Gerando o arquivo **Pipfile.lock** com as versões dos pacotes<br>
Para instalar um ambiente apartir de um já configurado basta executar novamente o comando em uma pasta com os **Pipfile** e o **Pipfile.lock**
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

<https://robots.thoughtbot.com/how-to-manage-your-python-projects-with-pipenv><br>
<http://flask.pocoo.org/docs/1.0/quickstart/>
