---
layout: post
title: "Comandos Básicos Linux para Consulta"
date: 2018-09-24 22:17:00 -0300
tags: [Linux, Bash, Shell]
img: pinguim-1.jpg
output: html_document      
---



#### Tópicos

[Arquivos e Diretórios](#ambiente)<br/>
[Redes](#redes)<br/>
[CRON](#cron)<br/>
[Serviços](#serviços)

## Arquivos e Diretórios

* Listar conteúdo da pasta: `ls`
* Mover ou renomear arquivo: `mv <caminho de origem> <caminho de destino>`
* Excluir arquivo: `rm <nome do arquivo>`
* Excluir diretório: `rm -r <nome do diretório>`
* Ler conteúdo de um arquivo: `cat <nome do arquivo>`

## Redes

* Visualizar IP da máquina: `ip addr show`

## Comandos

* Visualizar pasta de instalação de um comando: `which <nome no comando>`

## CRON

* Ver conteúdo do CRON: `crontab -l`
* Editar conteúdo do CRON: `crontab -e`

Executar as atividades do @reload


{% highlight bash %}
service cron reload
{% endhighlight %}

## Serviços 


systemctl
