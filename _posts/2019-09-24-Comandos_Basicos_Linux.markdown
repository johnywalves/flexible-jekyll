---
layout: post
title: "Comandos Básicos Linux para Consulta"
date: 2018-09-24 22:17:00 -0300
tags: [Linux, Bash, Shell]
topics: [Arquivos e Diretórios, Redes, CRON, Serviços]
img: pinguim-1.jpg
output: html_document      
---



# Comandos Básicos Linux 

Recentemente me deparei com a necessidade de aprender a lidar com o linux, sem uma interface gráfica, amei<br>
Dentro do uso com o shell do Linux para mim foi a dificuldade de memorizar os comandos, então criei um guia pequeno para consultar constantemente

## Arquivos e Diretórios

Listar conteúdo da pasta


{% highlight bash %}
ls
{% endhighlight %}

Mover ou renomear arquivo


{% highlight bash %}
mv <caminho de origem> <caminho de destino>
{% endhighlight %}

Excluir arquivo


{% highlight bash %}
rm <nome do arquivo>
{% endhighlight %}

Excluir diretório


{% highlight bash %}
rm -r <nome do diretório>
{% endhighlight %}

Ler conteúdo de um arquivo


{% highlight bash %}
cat <nome do arquivo>
{% endhighlight %}

## Redes

Visualizar IP da máquina


{% highlight bash %}
ip addr show
{% endhighlight %}

## Compreeender Ambiente

Visualizar pasta de instalação de um comando


{% highlight bash %}
which <nome no comando>
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

Executar as atividades do @reload


{% highlight bash %}
service cron reload
{% endhighlight %}

## Serviços 

Verificar o status de um serviço


{% highlight bash %}
systemctl status application.service
{% endhighlight %}
