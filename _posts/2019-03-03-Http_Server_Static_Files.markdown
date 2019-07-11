---
layout: post
title: "Http Server para arquivos estáticos (Python e JavaScript)"
date: 2019-03-03 10:13:13 -0300
tags: [Web, Python, JavaScript]
img: gift-1.jpg
output: html_document
---



Disponilizar arquivos estáticos para projetos em desenvolvimento, não usar em produção, ou somente para disponilizar arquivos de forma simples, quando o index.html não estiver disponível

## JavaScript

Fazendo uso do Node, NPM e data biblioteca [Serve](https://www.npmjs.com/package/serve), instalando de forma simples com a diretiva `-g` para disponilizar para todo o sistema


{% highlight shell %}
npm i serve -g
{% endhighlight %}

Com o shell localizado na pasta 


{% highlight shell %}
serve
{% endhighlight %}

Ou em uma pasta pai, para disponilizar uma pasta filha com:


{% highlight shell %}
serve -s <nome da pasta>
{% endhighlight %}

Disponilizando o acesso em [127.0.0.1:5050](127.0.0.1:5050)

## Python

Usando a biblioteca HTTP que faz parte do core do Python, com o ambiente configurado e com o shell localizado na pasta 


{% highlight shell %}
python -m http.server
{% endhighlight %}

Disponilizando o acesso em [127.0.0.1:8000](127.0.0.1:8000)
