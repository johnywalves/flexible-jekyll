---
layout: post
title: "Controle da evolução corporal (Peso)"
date: 2018-10-25 20:33:15 -0300
tags: [Python, Visualização]
img: balance-1.jpg
output: html_document      
---



Sempre tive um barriguinha redonda, na minha guerra constante resolvi usar um pouco de ciência de dados para ajudar em mais uma batalha, me desafiando por 100 dias para alcançar 85Kg  
O projeto completo está no [Repositorio GitHub](https://github.com/johnywalves/PyStudies/tree/master/Controle_Peso_100_dias)

### Carregar os dados

Definindo o foco de **100** dias para alcançar os **85Kg** desejados 


{% highlight python %}
focoDias = 100
focoPeso = 85
{% endhighlight %}

Usando o arquivo de dados em **csv** com o seguinte formato


{% highlight python %}
Data,Peso,Imagem,AtividadeFisica,AlimentacaoTipo,AlimentacaoNivel,AlimentacaoCondicao
2018-09-23,96.7,1.jpg,'[{"Tipo":"Corrida", "Distancia":5.43, "Velocidade":9.92}]','["Laticínios", "Frutas"]',Moderada,
2018-09-24,96.1,,'[{"Tipo":"Corrida", "Distancia":5.40, "Velocidade":9.80}]','["Frutas", "Legumes", "Raízes"]',Moderada,
2018-09-25,94.7,,'[{"Tipo":"Musculação", "Foco":"Pernas"}]','["Frutas", "Legumes", "Raízes"]',Baixa,
2018-09-26,94.4,,,'["Frutas", "Raízes"]',Moderada,
{% endhighlight %}

Carregando os dados as bibliotecas de *pandas*, manipular os dados, e *numpy*, funções matemáticas.<br>
Lendo e carregando o arquivo convertendo os textos em json, para acumular os históricos de atividades físicas e tipo de alimentação ambos objetos com listagens


{% highlight python %}
import pandas as pd
import numpy as np
def LerJSON(data):
    import json
    if (data == ''):
        return json.loads('[]')
    else:
        return json.loads(data)
df_dados = pd.read_csv('./dados.csv', quotechar='\'', converters={'AtividadeFisica':LerJSON, 'AlimentacaoTipo':LerJSON}, header=0)
{% endhighlight %}



{% highlight text %}
## FileNotFoundError: File b'./dados.csv' does not exist
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
##   File "C:\Software\Installs\Python37\lib\site-packages\pandas\io\parsers.py", line 678, in parser_f
##     return _read(filepath_or_buffer, kwds)
##   File "C:\Software\Installs\Python37\lib\site-packages\pandas\io\parsers.py", line 440, in _read
##     parser = TextFileReader(filepath_or_buffer, **kwds)
##   File "C:\Software\Installs\Python37\lib\site-packages\pandas\io\parsers.py", line 787, in __init__
##     self._make_engine(self.engine)
##   File "C:\Software\Installs\Python37\lib\site-packages\pandas\io\parsers.py", line 1014, in _make_engine
##     self._engine = CParserWrapper(self.f, **self.options)
##   File "C:\Software\Installs\Python37\lib\site-packages\pandas\io\parsers.py", line 1708, in __init__
##     self._reader = parsers.TextReader(src, **kwds)
##   File "pandas\_libs\parsers.pyx", line 384, in pandas._libs.parsers.TextReader.__cinit__
##   File "pandas\_libs\parsers.pyx", line 695, in pandas._libs.parsers.TextReader._setup_parser_source
{% endhighlight %}

### Cálculo do limite de peso

Extraindo os dados de contagem e valor máximo das medidas, posteriormente usando extrapolação linear para preencher os dados faltantes


{% highlight python %}
count = df_dados['Peso'].count()
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
max = df_dados['Peso'].max()
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
df_dados['Peso'] = df_dados['Peso'].interpolate()
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}

Com uma projeção linear de peso máximo para o objetivo 


{% highlight python %}
linha_limite = pd.Series(range(0, focoDias))
linha_limite = max - (linha_limite * ((max - focoPeso) / focoDias))
{% endhighlight %}



{% highlight text %}
## TypeError: unsupported operand type(s) for -: 'builtin_function_or_method' and 'int'
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
df_dados['Limite'] = linha_limite
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}

### Nível de atividades físicas

Para o resultado do fluxo ser negativo é necessário ter saída, aumentar esse fluxo com muita atividade física


{% highlight python %}
def scoreAtividade(data):
    score = 0
    for atividade in data:
        score = score + {
            'Corrida': lambda: atividade['Distancia'] * atividade['Velocidade'],
            'Caminhada': lambda: atividade['Distancia'] * atividade['Velocidade'] * 0.5,
            'Funcional': lambda: 30,
            'Musculação': lambda: 15,
        }.get(atividade['Tipo'], lambda: 0)()        
    return score
df_dados['ScoreAtividade'] = df_dados['AtividadeFisica'].apply(scoreAtividade)
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
df_dados['ScoreAtividade'] = 15 * df_dados['ScoreAtividade']
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}

Usando elevação de 2, para impedir que as somas deve possuir um valor igual com os outros 


{% highlight python %}
def tipoAtividade(data):
    tipo = 0
    for atividade in data:
        tipo = tipo + {
            'Corrida': lambda: 1,
            'Musculação': lambda: 2,
            'Funcional': lambda: 4
        }.get(atividade['Tipo'], lambda: 0)()        
    return tipo
df_dados['Tipo'] = df_dados['AtividadeFisica'].apply(tipoAtividade)
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}

### Nível de alimentação

Usando as regras de 


{% highlight python %}
def tipoAlimentacao(data):
    tipo = 0        
    for alimentacao in data:
        tipo = tipo + {
            'Raízes': lambda: 1,
            'Legumes': lambda: 2,
            'Frutas': lambda: 4,
            'Laticínios': lambda: 8,
            'Farinários': lambda: 16,
            'Proteína Animal': lambda: 32,
            'Açúcar': lambda: 64
        }.get(alimentacao, lambda: 0)()
    return tipo
def nivelAlimentacao(data):
    return {
        'Baixa': lambda: 1,
        'Moderada': lambda: 2,
        'Alta': lambda: 4,
        'Exagerada': lambda: 8,
    }.get(data, lambda: 0)()
alimentacaoTipo = df_dados['AlimentacaoTipo'].apply(tipoAlimentacao)
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
alimentacaoVolume = df_dados['AlimentacaoNivel'].apply(nivelAlimentacao)
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
df_dados["ScoreAlimentacao"] = 0.025 * np.sqrt(alimentacaoTipo * alimentacaoVolume)
{% endhighlight %}



{% highlight text %}
## NameError: name 'alimentacaoTipo' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}

### Número de dias



{% highlight python %}
df_dados["NumeroDias"] = df_dados.index + 1
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}

### Geração do gráfico



{% highlight python %}
import matplotlib.pyplot as plt
plt.plot(df_dados["NumeroDias"], df_dados["Peso"])
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
plt.plot(df_dados["NumeroDias"], df_dados["Limite"])
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
plt.scatter(df_dados["NumeroDias"], df_dados["Peso"], s=df_dados["ScoreAtividade"], c=df_dados["Tipo"], alpha=0.5)
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
plt.errorbar(df_dados["NumeroDias"], df_dados["Peso"], yerr=df_dados['ScoreAlimentacao'], ecolor="grey", elinewidth=4, alpha=0.75, fmt='none')
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
plt.xlabel("Dias")
plt.ylabel("Medida")
plt.legend(['Evolução', 'Objetivo'])
plt.savefig('evolution.png', dpi=300)
plt.show()
{% endhighlight %}

![plot of chunk MedidasPeso](/./assets/Rfig/MedidasPeso-1.svg)

### Visualização da Tabela


{% highlight python %}
tabela = df_dados[['Data', 'NumeroDias', 'Peso', 'Limite', 'ScoreAtividade', 'ScoreAlimentacao', 'AlimentacaoCondicao']]
{% endhighlight %}



{% highlight text %}
## NameError: name 'df_dados' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
tabela.to_csv('dadosOrganizados.csv')
{% endhighlight %}



{% highlight text %}
## NameError: name 'tabela' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}



{% highlight python %}
tabela.head(15)
{% endhighlight %}



{% highlight text %}
## NameError: name 'tabela' is not defined
## 
## Detailed traceback: 
##   File "<string>", line 1, in <module>
{% endhighlight %}

