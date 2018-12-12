---
layout: post
title: "Render Web Template (JavaScript Express)"
date: 2018-12-11 16:22:15 -0200
tags: [Web, JavaScript, Express]
topics: [Montagem do Modelo, Preparação, Interpretação, Arquivos Estáticos, Favicon, Execução]
img: python-1.jpg
output: html_document
---



Entregar páginas Web intepretando como se fossem estáticas, reduz a carga de processo pelo cliente, executando o processamento pelo servidor onde pode estar posicionado a base de dados e outros recursos que não precisam disponibilidade fora dele

## Montagem do Modelo

Fazendo uso do [Jinja](http://jinja.pocoo.org/docs/2.10/templates/) para a montagem do HTML os delimitadores para expressões entre `{{'{{'}}` e `}}` e controles de fluxo entre `{{'{%'}}` e `%}`  
Inserir um valor direto no corpo do arquivo, usando a extensão **twig**


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

ou por uso de if ternário, seguindo o padrão do JavaScript


{% highlight html %}
<!doctype html>
	<h1>Hello, {{'{{'}} (nome ? nome : 'World') }}!</h1>	
{% endhighlight %}

Através da iteração de uma lista de objetos, salvando na pasta de **view** com o nome de **lista.twig**


{% highlight html %}
<!doctype html>
  <ul id="navigation">
  {{'{%'}} for item in navigation %}
      <li><a href="{{'{{'}} item.href }}">{{'{{'}} item.caption }}</a></li>
  {{'{%'}} endfor %}
  </ul>	
{% endhighlight %}

Com o uso um modelo padrão, salvando na pasta de **view** com o nome de **base.twig**


{% highlight html %}
<!doctype html>
  <h1>{{'{%'}} block titulo %}{{'{%'}} endblock %}</h1>

  <div class="wrapper">
    {{'{%'}} block corpo %}{{'{%'}} endblock %}
  </div>	
{% endhighlight %}

As definições de bloco `{{'{%'}} block titulo %}` e  `{{'{%'}} block corpo %}` podem ser preenchidas por qualquer página ao estender o documento de base, vamos fazer isso no **lista.twig**


{% highlight html %}
{{'{%'}} extends 'base.twig' %}

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
/server.js
/view
	/hello.twig
	/lista.twig
	/base.twig
{% endhighlight %}

## Preparação

Instalar o pelo gerenciador de pacotes


{% highlight bash %}
npm i express path twig serve-favicon
{% endhighlight %}

Iniciando o arquivo **server.js** com a declaração do Express e do Twig


{% highlight python %}
var Twig = require('twig'),
	express = require('express'),
	app = express();
{% endhighlight %}

## Interpretação

Adicionando no arquivo **server.js** a opção de rotas para ouvir o caminho **/hello/** com e sem parametros


{% highlight python %}
app.get('/hello/:param', function (req, res) {
	res.render('hello.twig', { name: req.params.param });
});
{% endhighlight %}

Também adicionando no arquivo **server.js** a **/lista/** passando a listagem em navigation como fixa para ilustração


{% highlight python %}
app.get('/lista/', function (req, res) {
    list = [
        {"href":"#1", "caption":"primeiro"},
        {"href":"#2", "caption":"segundo"}
    ]
	res.render('lista.twig', { navigation: list });
});
{% endhighlight %}

## Arquivos Estáticos

Folhas de estilo não costumam sofre variação na execução do sistema, não sendo necessária a intepreção da mesma
Para entregar devemos adicionar na pasta **static** na raiz do projeto, esse nome pode ser mudado


{% highlight html %}
/server.js
/view
/static
    /style.css
{% endhighlight %}

Disponibilizando todos os arquivos disponibilizados na pasta


{% highlight js %}
var path = require('path');
app.use(express.static(path.join(__dirname, './static')));
{% endhighlight %}

E adiciona a referência sem a pasta */static*


{% highlight html %}
<link rel="stylesheet" href="/style.css">
{% endhighlight %}

## Favicon

Para adicionar o *favicon.ico* na página basta colocar o arquivo na pasta **static** e adicionar o trecho no **server.js**


{% highlight js %}
var favicon = require('serve-favicon');
app.use(favicon(path.join(__dirname, 'static', 'favicon.ico')))
{% endhighlight %}

## Execução

Com os pacotes instalados e arquivo nomeado como **server.js**


{% highlight bash %}
node server.js
{% endhighlight %}

Uma mensagem no shell apresenta que o site está disponível no [localhost:3000](localhost:3000), acesse os endereços para visualizar os resultados
* [localhost:3000/hello](localhost:3000/hello)
* [localhost:3000/hello/Teste](localhost:3000/hello/Teste)
* [localhost:3000/lista](localhost:3000/lista)

### Referências

[Express - API 4.x](http://expressjs.com/pt-br/api.html)  
[NPM - Twig](https://www.npmjs.com/package/twig)  
