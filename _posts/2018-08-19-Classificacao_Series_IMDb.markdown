---
layout: post
title: "Classificação de Séries IMDb (Linguagem R)"
date: 2018-08-19 15:00:00 -0300
tags: [Visualização, R]
img: imdb-1.jpg
output: html_document
---

IMDb é uma maior referência para filmes, série, atores, diretores... podemos entender e visualizar na sua base toda a história de sua indústria cinematografica apartir de *Passage de Venus* de 1874 por *Pierre Jules César Janssen* até os não lançados em pré-produção.  
Atualizada diáriamente [Base de dados](https://datasets.imdbws.com/) possuem uma [Estrutura fácil de entender](https://www.imdb.com/interfaces/)  
  
Esse artigo foi utilizado os arquivos  
* **title.ratings.tsv.gz**: Classificação dos **títulos** feitos para os usuários  
* **title.episode.tsv.gz**: Classificação dos **episódios** das séries feitos para os usuários  
* **title.basics.tsv.gz*`**: Nome dos títulos  



### Ambiente

Para preparar o ambiente informei o diretório de trabalho indicado pelo **'.'** e limpei todas as variáveis de ambiente após pesquisar todas pelo .


{% highlight r %}
setwd('.')
rm(list=ls())
{% endhighlight %}

### Classifição de Episódios 

Carregar a fonte de dados de classificações e epísodios


{% highlight r %}
ratings = read.table(gzfile("title.ratings.tsv.gz"), header = TRUE)
{% endhighlight %}



{% highlight text %}
## Error in open.connection(file, "rt"): não é possível abrir a conexão
{% endhighlight %}



{% highlight r %}
episode = read.table(gzfile("title.episode.tsv.gz"), header = TRUE)
{% endhighlight %}



{% highlight text %}
## Error in open.connection(file, "rt"): não é possível abrir a conexão
{% endhighlight %}

Depois de carregadas foram necessárias algumas manipulações preparar o data.frame


{% highlight r %}
# Unir os data.frames de episódios e classificações
episodeRating <- cbind(episode[match(ratings$tconst, episode$tconst),], ratings)
{% endhighlight %}



{% highlight text %}
## Error in cbind(episode[match(ratings$tconst, episode$tconst), ], ratings): objeto 'episode' não encontrado
{% endhighlight %}



{% highlight r %}
# Remover episódios sem indicação de títulos 
episodeRating <- episodeRating[!is.na(episodeRating$parentTconst),]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}



{% highlight r %}
# Remover episódios sem númeração de temporada
episodeRating <- episodeRating[(episodeRating$seasonNumber != '\\N'),]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}



{% highlight r %}
# Remover episódios com 50 ou menos classificações 
episodeRating <- episodeRating[(episodeRating$numVotes > 50),]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}



{% highlight r %}
# Ordenar por episódios
episodeRating <- episodeRating[order(episodeRating$seasonNumber, episodeRating$episodeNumber),] 
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}



{% highlight r %}
# Converter os episódios e temporadas em números
episodeRating$seasonNumber <- as.integer(as.character(episodeRating$seasonNumber))
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}



{% highlight r %}
episodeRating$episodeNumber <- as.integer(as.character(episodeRating$episodeNumber))
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}

Separando somente as colunas para manipulação 


{% highlight r %}
# Somente as colunas de identificação do título e a classificação média
meanRating <- episodeRating[,c('parentTconst', 'averageRating')]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}



{% highlight r %}
# Agregar as classificações por título aplicação de médias 
meanRating <- aggregate(averageRating ~ parentTconst, meanRating, mean)
{% endhighlight %}



{% highlight text %}
## Error in eval(m$data, parent.frame()): objeto 'meanRating' não encontrado
{% endhighlight %}



{% highlight r %}
# Ordenar de forma decrescente as classificações 
meanRating <- meanRating[order(-meanRating$averageRating),]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'meanRating' não encontrado
{% endhighlight %}

Separando as séries pela classificação pela [Curva ABC](https://pt.wikipedia.org/wiki/Curva_ABC) onde a proporção de 20%, 30%, 50% dos melhores


{% highlight r %}
nrow <- nrow(meanRating)
{% endhighlight %}



{% highlight text %}
## Error in nrow(meanRating): objeto 'meanRating' não encontrado
{% endhighlight %}



{% highlight r %}
limitA <- nrow*0.2
{% endhighlight %}



{% highlight text %}
## Error in nrow * 0.2: argumento não-numérico para operador binário
{% endhighlight %}



{% highlight r %}
limitB <- nrow*0.5
{% endhighlight %}



{% highlight text %}
## Error in nrow * 0.5: argumento não-numérico para operador binário
{% endhighlight %}



{% highlight r %}
mediaCurvaA <- mean(meanRating[1:limitA,]$averageRating)
{% endhighlight %}



{% highlight text %}
## Error in mean(meanRating[1:limitA, ]$averageRating): objeto 'meanRating' não encontrado
{% endhighlight %}



{% highlight r %}
mediaCurvaB <- mean(meanRating[limitA:limitB,]$averageRating)
{% endhighlight %}



{% highlight text %}
## Error in mean(meanRating[limitA:limitB, ]$averageRating): objeto 'meanRating' não encontrado
{% endhighlight %}



{% highlight r %}
mediaCurvaC <- mean(meanRating[limitB:nrow,]$averageRating)
{% endhighlight %}



{% highlight text %}
## Error in mean(meanRating[limitB:nrow, ]$averageRating): objeto 'meanRating' não encontrado
{% endhighlight %}

Para fins de exemplo foi escolhi o [My Little Poney: Friendship is Magic](https://www.imdb.com/title/tt1751105/) com o código **tt1751105**, por quê? Me pareceu divertido


{% highlight r %}
# My Little Poney: Friendship is Magic
codSerie <- 'tt1751105'
# Cálculo da média da série
mediaSerie <- meanRating[meanRating$parentTconst == codSerie,]$averageRating
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'meanRating' não encontrado
{% endhighlight %}



{% highlight r %}
# Filtar os episódios da série e remover atributos duplicados
episodeRatingSerie = episodeRating[episodeRating$parentTconst == codSerie, -5]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}

Tratar os dados organizando gerando um número sequencial de episódios (Ex. "0102" segunda a primeira temporada, "0312" décimo segundo da terceira temporada) e ordenando pela sequência


{% highlight r %}
# Organizar e ordenar episodios por sequência
library(stringr)
episodeRatingSerie$episodeNumber <- str_pad(episodeRatingSerie$episodeNumber, 2, pad = "0")
{% endhighlight %}



{% highlight text %}
## Error in stri_pad_left(string, width, pad = pad): objeto 'episodeRatingSerie' não encontrado
{% endhighlight %}



{% highlight r %}
episodeRatingSerie$seasonNumber <- str_pad(episodeRatingSerie$seasonNumber, 2, pad = "0")
{% endhighlight %}



{% highlight text %}
## Error in stri_pad_left(string, width, pad = pad): objeto 'episodeRatingSerie' não encontrado
{% endhighlight %}



{% highlight r %}
episodeRatingSerie$episode <- paste(episodeRatingSerie$seasonNumber, 
                                    episodeRatingSerie$episodeNumber, sep = "")
{% endhighlight %}



{% highlight text %}
## Error in paste(episodeRatingSerie$seasonNumber, episodeRatingSerie$episodeNumber, : objeto 'episodeRatingSerie' não encontrado
{% endhighlight %}



{% highlight r %}
# Ordenar
episodeRatingSerie <- episodeRatingSerie[order(episodeRatingSerie$episode),]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRatingSerie' não encontrado
{% endhighlight %}



{% highlight r %}
episodeRatingSerie$ordemEpisode <- seq(1,nrow(episodeRatingSerie))
{% endhighlight %}



{% highlight text %}
## Error in nrow(episodeRatingSerie): objeto 'episodeRatingSerie' não encontrado
{% endhighlight %}

Gerar e visualizar as classificações geradas 


{% highlight r %}
# Definir cores as linhas
rainbow <- rainbow(3)
# Geração de gráfico por episódios
library(ggplot2)
ggplot(episodeRatingSerie, aes(x = ordemEpisode, y = averageRating)) +
  labs(x = "Episódio", y = "Média de pontuação") +
  geom_line(color=rainbow[1]) +
  geom_segment(color=rainbow[2], size=0.8, 
               aes(x=1, xend=nrow(episodeRatingSerie), y=mediaSerie, yend=mediaSerie), alpha = 0.5) +  
  theme_minimal()
{% endhighlight %}



{% highlight text %}
## Error in ggplot(episodeRatingSerie, aes(x = ordemEpisode, y = averageRating)): objeto 'episodeRatingSerie' não encontrado
{% endhighlight %}

#### Inclusão de comparações 

Gerar um *data frame* com a média por temporada de todas as séries


{% highlight r %}
mediaTemporada = aggregate(episodeRatingSerie$averageRating,
                           by=list(Category=episodeRatingSerie$seasonNumber), mean)
{% endhighlight %}



{% highlight text %}
## Error in aggregate(episodeRatingSerie$averageRating, by = list(Category = episodeRatingSerie$seasonNumber), : objeto 'episodeRatingSerie' não encontrado
{% endhighlight %}



{% highlight r %}
mediaTemporada$Category = as.integer(mediaTemporada$Category)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'mediaTemporada' não encontrado
{% endhighlight %}



{% highlight r %}
colnames(mediaTemporada) <- c('seasonNumber', 'averageRating')
{% endhighlight %}



{% highlight text %}
## Error in colnames(mediaTemporada) <- c("seasonNumber", "averageRating"): objeto 'mediaTemporada' não encontrado
{% endhighlight %}

Gerar e visualizar as comparações geradas


{% highlight r %}
# Definir cores as linhas
rainbow <- rainbow(5)
# Geração de gráfico comparação de temporadas das séries
ggplot(mediaTemporada, aes(x=seasonNumber, y=averageRating)) +
    labs(x = "Temporada", y = "Média de pontuação") +
    geom_line(color=rainbow[1]) +
    
    geom_segment(color=rainbow[2], size=1, 
                 aes(x=1, xend=nrow(mediaTemporada), y=mediaSerie, yend=mediaSerie), alpha = 0.5) +
    geom_segment(color=rainbow[3], size=1, 
                 aes(x=1, xend=nrow(mediaTemporada), y=mediaCurvaA, yend=mediaCurvaA), alpha=0.5) +
    geom_segment(color=rainbow[4], size=1, 
                 aes(x=1, xend=nrow(mediaTemporada), y=mediaCurvaB, yend=mediaCurvaB), alpha=0.5) +  
    geom_segment(color=rainbow[5], size=1, 
                 aes(x=1, xend=nrow(mediaTemporada), y=mediaCurvaC, yend=mediaCurvaC), alpha=0.5) +    
    
    geom_text(aes(x=1.1, y=mediaSerie, label='Série'), 
              hjust=0, vjust=0.55, size=4, colour='red') +
    geom_text(aes(x=1.1, y=mediaCurvaA, label='A'), 
              hjust=0, vjust=0.55, size=4, colour='red') +
    geom_text(aes(x=1.1, y=mediaCurvaB, label='B'), 
              hjust=0, vjust=0.55, size=4, colour='red') +
    geom_text(aes(x=1.1, y=mediaCurvaC, label='C'), 
              hjust=0, vjust=0.55, size=4, colour='red') + 
    
    theme_minimal()
{% endhighlight %}



{% highlight text %}
## Error in ggplot(mediaTemporada, aes(x = seasonNumber, y = averageRating)): objeto 'mediaTemporada' não encontrado
{% endhighlight %}

### Comparação entre Séries 

Para dar continuidade selecionei minhas séries favoritas
    
Séries para ser comparadas  
* [tt0407362](https://www.imdb.com/title/tt0407362/) : Battlestar Galactica  
* [tt0903747](https://www.imdb.com/title/tt0903747/) : Breaking Bad  
* [tt0944947](https://www.imdb.com/title/tt0944947/) : Game of Thrones  
* [tt0411008](https://www.imdb.com/title/tt0411008/) : Lost  
* [tt0141842](https://www.imdb.com/title/tt0141842/) : The Sopranos  
  
Para realizar a leitura temos de mudar algumas coisas, *quote*, limitador de texto, padrão é o "'" e *comment.char*, reconhecedor de comentários, é o "#", mas alguns nomes possuem eles sendo necessário configurar para ignorar essas entradas
  

{% highlight r %}
# Importar títulos das séries 
titles = read.table(gzfile("title.basics.tsv.gz"), sep="\t", quote="", comment.char="", header=TRUE)
{% endhighlight %}



{% highlight text %}
## Error in open.connection(file, "rt"): não é possível abrir a conexão
{% endhighlight %}



{% highlight r %}
# Listagem das séries
codSeries <- c('tt0407362', 'tt0903747', 'tt0944947', 'tt0411008', 'tt0141842')
{% endhighlight %}




{% highlight r %}
# Carregar pontuação da série
grupoSeries <- episodeRating[episodeRating$parentTconst %in% codSeries,]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'episodeRating' não encontrado
{% endhighlight %}



{% highlight r %}
grupoSeries <- aggregate(grupoSeries$averageRating,
                         by=list(seasonNumber=grupoSeries$seasonNumber,
                                 parentTconst=grupoSeries$parentTconst), mean)
{% endhighlight %}



{% highlight text %}
## Error in aggregate(grupoSeries$averageRating, by = list(seasonNumber = grupoSeries$seasonNumber, : objeto 'grupoSeries' não encontrado
{% endhighlight %}



{% highlight r %}
colnames(grupoSeries)[3] <- 'averageRating'
{% endhighlight %}



{% highlight text %}
## Error in colnames(grupoSeries)[3] <- "averageRating": objeto 'grupoSeries' não encontrado
{% endhighlight %}



{% highlight r %}
# Capturar somente as colunas  títulos das séries 
titlesSeries <- titles[titles$tconst %in% as.factor(codSeries), c('tconst', 'primaryTitle')]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'titles' não encontrado
{% endhighlight %}



{% highlight r %}
grupoSeries <- cbind(titlesSeries[match(grupoSeries$parentTconst, titlesSeries$tconst),], grupoSeries)
{% endhighlight %}



{% highlight text %}
## Error in cbind(titlesSeries[match(grupoSeries$parentTconst, titlesSeries$tconst), : objeto 'titlesSeries' não encontrado
{% endhighlight %}



{% highlight r %}
ggplot(grupoSeries, aes(x=seasonNumber, y=averageRating)) +
    labs(x = "Temporada", y = "Média de pontuação", colour = "Séries") +  
    geom_line(aes(group = primaryTitle, colour = primaryTitle)) +
    theme_minimal()
{% endhighlight %}



{% highlight text %}
## Error in ggplot(grupoSeries, aes(x = seasonNumber, y = averageRating)): objeto 'grupoSeries' não encontrado
{% endhighlight %}


{% highlight r %}
# Quais as melhores de todos os tempos
melhoresSeries <- head(meanRating)
{% endhighlight %}



{% highlight text %}
## Error in head(meanRating): objeto 'meanRating' não encontrado
{% endhighlight %}



{% highlight r %}
melhoresTitles <- titles[titles$tconst %in% as.factor(melhoresSeries$parentTconst), 
                         c('tconst', 'primaryTitle')]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'titles' não encontrado
{% endhighlight %}



{% highlight r %}
melhoresSeries <- cbind(melhoresTitles[match(melhoresSeries$parentTconst, melhoresTitles$tconst),],
                        melhoresSeries)
{% endhighlight %}



{% highlight text %}
## Error in cbind(melhoresTitles[match(melhoresSeries$parentTconst, melhoresTitles$tconst), : objeto 'melhoresTitles' não encontrado
{% endhighlight %}



{% highlight r %}
print(melhoresSeries)
{% endhighlight %}



{% highlight text %}
## Error in print(melhoresSeries): objeto 'melhoresSeries' não encontrado
{% endhighlight %}

Mas tudo isso pode significar nada, por essas classificações esta é a melhor série já produzida
