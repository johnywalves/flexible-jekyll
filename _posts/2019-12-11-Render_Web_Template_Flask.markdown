---
layout: post
title: "Render Web Template (Python Flask)"
date: 2018-12-11 16:22:15 -0200
tags: [Web, Python, Flask]
topics: [Montagem do Modelo, Preparação, Interpretação, Arquivos Estáticos, Favicon, Execução]
img: gearspy-1.jpg
output: html_document
---



Entregar páginas Web intepretando como se fossem estáticas, reduz a carga de processamento pelo cliente, executando pelo servidor onde pode estar posicionado a base de dados e outros recursos que não precisam disponibilidade fora dele

## Montagem do Modelo

Fazendo uso do [Jinja](http://jinja.pocoo.org/docs/2.10/templates/) para a montagem do HTML os delimitadores para expressões entre `{{'{{'}}` e `}}` e controles de fluxo entre `{{'{%'}}` e `%}`  
Inserir um valor direto no corpo do arquivo 


{% highlight html %}
<!doctype html>
	<h1>Hello {{'{{'}} nome }}</h1>
{% endhighlight %}

Uso de if para mudar a opção de treços de código


{% highlight html %}
<!doctype html>
	{{'{%'}} if nome %}
	  <h1>Hello {{'{{'}} nome }}!</h1>
	{{'{%'}} else %}
	  <h1>Hello, World!</h1>
	{{'{%'}} endif %}
{% endhighlight %}

ou por uso de if ternário, seguindo o padrão do Python


{% highlight html %}
<!doctype html>
	<h1>Hello, {{'{{'}} nome if nome else 'World'}}!</h1>	
{% endhighlight %}

Através da iteração de uma lista de objetos, salvando na pasta de **template** com o nome de **lista.html**


{% highlight html %}
<!doctype html>
  <ul id="navigation">
  {{'{%'}} for item in navigation %}
      <li><a href="{{'{{'}} item.href }}">{{'{{'}} item.caption }}</a></li>
  {{'{%'}} endfor %}
  </ul>	
{% endhighlight %}

Com o uso um modelo padrão, salvando na pasta de **template** com o nome de **base.html**


{% highlight html %}
<!doctype html>
  <h1>{{'{%'}} block titulo %}{{'{%'}} endblock %}</h1>

  <div class="wrapper">
    {{'{%'}} block corpo %}{{'{%'}} endblock %}
  </div>	
{% endhighlight %}

As definições de bloco `{{'{%'}} block titulo %}` e  `{{'{%'}} block corpo %}` podem ser preenchidas por qualquer página ao estender o documento de base, vamos fazer isso no **lista.html**


{% highlight html %}
{{'{%'}} extends 'base.html' %}

{{'{%'}} block titulo %}
Página Teste 
{{'{%'}} endblock %}

{{'{%'}} block corpo %}
  <ul id="navigation">
  {{'{%'}} for item in navigation %}
      <li><a href="{{'{{'}} item.href }}">{{'{{'}} item.caption }}</a></li>
  {{'{%'}} endfor %}
  </ul>	
{{'{%'}} endblock %}
{% endhighlight %}

A estrutura de arquivos deve ficar assim: 


{% highlight html %}
/app.py
/templates
	/hello.html
	/lista.html
	/base.html
{% endhighlight %}

## Preparação 

Instalar o pelo gerenciador de pacotes, recomendo o uso de [Ambiente Virtual](/Virtual_Environment)


{% highlight bash %}
pip install flask
{% endhighlight %}

Iniciando o arquivo **app.py** com a declaração do flask


{% highlight python %}
from flask import Flask
app = Flask(__name__)
{% endhighlight %}

## Interpretação 

Adicionando no arquivo **app.py** a opção de rotas para ouvir o caminho **/hello/** com e sem parametros


{% highlight python %}
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<param>')
def hello(param=None):
    return render_template('hello.html', nome=param)
{% endhighlight %}

Também adicionando no arquivo **app.py** a **/lista/** passando a listagem em navigation como fixa para ilustração


{% highlight python %}
from flask import render_template

@app.route('/lista/')
def lista():
    list = [
        {"href":"#1", "caption":"primeiro"},
        {"href":"#2", "caption":"segundo"}
    ]
    return render_template('lista.html', navigation=list)    
{% endhighlight %}

## Arquivos Estáticos

Folhas de estilo não costumam sofre variação na execução do sistema, não sendo necessária a intepreção da mesma
Para entregar devemos adicionar na pasta **static** na raiz do projeto


{% highlight html %}
/app.py
/templates
/static
    /style.css
{% endhighlight %}

E adiciona o trecho na página HTML


{% highlight html %}
<link rel="stylesheet" href="{{'{{'}} url_for('static', filename='style.css') }}">
{% endhighlight %}

ou direto no intepretador, e informando o caminho */static/style.css*, mas desta maneira é necessário definir um contexto como no exemplo abaixo


{% highlight python %}
from flask import url_for
app.config['SERVER_NAME'] = "127.0.0.1:5000"

with app.app_context():
	url_for('static', filename='style.css')
{% endhighlight %}

## Favicon

Para adicionar o *favicon.ico* na página basta colocar o arquivo na pasta **static** e adicionar o trecho no texto do HTML


{% highlight html %}
<link rel="shortcut icon" href="{{'{{'}} url_for('static', filename='favicon.ico') }}">
{% endhighlight %}

## Execução

Se o pacote do flask estiver instalado e arquivo nomeado como **app.py**, basta executar o comando abaixo ou rodar `python <nome do arquivo>` para outro nome de arquivo


{% highlight bash %}
flask run
{% endhighlight %}

Uma mensagem no shell apresenta que o site está disponível no [localhost:5000](localhost:5000), acesse os endereços para visualizar os resultados
* [localhost:5000/hello](localhost:5000/hello)
* [localhost:5000/hello/Teste](localhost:5000/hello/Teste)
* [localhost:5000/lista](localhost:5000/lista)

### Referências

[Flask - Quickstart](http://flask.pocoo.org/docs/1.0/quickstart/)  
[Flask - Templates](http://flask.pocoo.org/docs/1.0/tutorial/templates/)  
[Flask - Static Files](http://flask.pocoo.org/docs/1.0/tutorial/static/)  
[Jinja - Template Designer Documentation](http://jinja.pocoo.org/docs/2.10/templates/)  
