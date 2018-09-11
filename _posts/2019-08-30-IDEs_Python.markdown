---
layout: post
title: "IDEs Python (Jupyter e Spyder)"
date: 2018-08-30 13:32:48 -0300
tags: [Ambiente, Python]
img: jupiter-1.jpg
output: html_document      
---



Ambientes de Desenvolvimentos ou IDE (Integrated Development Environment), a definição de um verdadeiro programador é o tema de sua IDE (sempre dark, para evitar cansaços aos olhos), essa ferramenta deve auxliar o desenvolvimento com validação de sintaxe, autopreencimento e facilidade para executar e publicar o objeto devenvolvido.<br>
Uma ótima pedida é [Microsoft Visual Code](https://code.visualstudio.com/) que possui terminal integrado e vários plugins

### Jupyter Notebook


{% highlight bash %}
pip install jupyter
{% endhighlight %}


{% highlight bash %}
jupyter notebook
{% endhighlight %}

### Spyder


{% highlight bash %}
pip install spyder
{% endhighlight %}


{% highlight bash %}
spyder3
{% endhighlight %}

Para alterar o tema \'Ferramentas > Preferências > Sintaxe colorida\' selecionar em **Esquema** o que mais lhe agradar, recomendo o \'Zenbrun\'

### Scripts 

Para executar 


{% highlight python %}
import os
import sys
os.path.dirname(sys.executable)
{% endhighlight %}


