---
layout: post
title: "Comandos Básicos Linux para Consulta"
date: 2018-09-24 22:17:00 -0300
tags: [Linux, Bash]
img: 
output: html_document      
---


## Arquivos e Diretórios

#### Excluir


{% highlight bash %}
rm <nome do arquivo>
{% endhighlight %}


{% highlight bash %}
rm -r <nome do diretório>
{% endhighlight %}

#### Ler o conteúdo de um arquivo


{% highlight bash %}
cat <nome do diretório>
{% endhighlight %}

## CRON

Ver conteúdo do CRON

{% highlight bash %}
crontab -l
{% endhighlight %}

Editar conteúdo do CRON

{% highlight bash %}
crontab -e
{% endhighlight %}

Executar as atividades o @reload

{% highlight bash %}
service cron reload
{% endhighlight %}
