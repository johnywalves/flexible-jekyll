---
layout: post
title: "Comandos Básicos Linux para Consulta"
date: 2018-09-24 22:17:00 -0300
tags: [Linux, Bash, Shell]
img: pinguim-1.jpg
output: html_document      
---



#### Tópicos

[Arquivos e Diretórios](#ambiente)
[Redes](#redes)
[CRON](#cron)
[Serviços](#serviços)

## Arquivos e Diretórios

* Listar conteúdo da pasta \`ls\`
* Mover ou renomear arquivo \`mv <caminho de origem> <caminho de destino>\`
* Excluir arquivo \`rm <nome do arquivo>\`
* Excluir diretório \`rm -r <nome do diretório>\`
* Ler conteúdo de um arquivo \`cat <nome do arquivo>\`

## Redes

* Visualizar IP da máquina \`ip addr show\`

## Comandos 

\`which <nome no comando>\`

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

## Serviços 


systemctl
