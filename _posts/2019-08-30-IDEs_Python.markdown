---
layout: post
title: "IDEs Python (Jupyter e Spyder)"
date: 2018-08-30 13:32:48 -0300
tags: [Ambiente, Python, Jupyter, Spyder]
img: jupiter-1.jpg
output: html_document      
---



Ambientes de Desenvolvimentos ou IDE (Integrated Development Environment) são ferramentas para auxliar o desenvolvimento com validações de sintaxe, autopreencimento de comandos, automação para executar e publicar o projeto devenvolvido<br>
Lembrando que a definição de um verdadeiro programador é o tema de sua IDE, sempre dark para evitar cansaços aos olhos<br>
Uma ótima pedida é [Microsoft Visual Code](https://code.visualstudio.com/) que possui terminal integrado e vários plugins, mas ótimas opções são o **Jupyter** e o **Spyder**, ambos com o [IPython](https://ipython.org/) integrada

### Jupyter Notebook

Uma interface notebook executando direto no navegador, facilmente instalada pelo `pip` do **PyPI**


{% highlight bash %}
pip install jupyter
{% endhighlight %}

Para executar o jupyter e já abrir o navegador, o comando deve ser executado na pasta de trabalho


{% highlight bash %}
jupyter notebook
{% endhighlight %}

### Spyder

Outra boa opção é o Spyder, com uma interface simples e dedicada para facilitação da execução pelo IPython


{% highlight bash %}
pip install spyder
{% endhighlight %}

Para abrir o spyder


{% highlight bash %}
spyder3
{% endhighlight %}

Para alterar o tema `Ferramentas > Preferências > Sintaxe colorida` selecionar em `Esquema` o que mais lhe agradar, recomendo o **Zenbrun**

### Scripts

Para executar os comandos `jupyter notebook` e `spyder 3` direito pelo shell a pasta de **Scripts** do Python deve estar mapeada no sistema, caso lembre da pasta de instalação, ela pode ser consulta pelo código no shell do Python


{% highlight python %}
import os
import sys
os.path.dirname(sys.executable)
{% endhighlight %}
