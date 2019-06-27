---
layout: post
title: "Configurar Jupyter para Servidor"
date: 2018-09-24 22:17:00 -0300
tags: [Python, Jupyter]
img: jupiter-1.jpg
output: html_document      
---



Para usar o notebook em um servidor é necessário alterar o arquivo de configuração, que pode ser gerado pelo comando


{% highlight bash %}
jupyter notebook --generate-config
{% endhighlight %}

### Acesso externo

Descomentar e configurar o IP Externo que pode ser um `*` para todos os caminhos de acesso ao servidor


{% highlight python %}
c.NotebookApp.ip = '<IP Externo>'
{% endhighlight %}

### Referências 

[Running a notebook server - Jupyter Notebook](https://jupyter-notebook.readthedocs.io/en/stable/public_server.html)
