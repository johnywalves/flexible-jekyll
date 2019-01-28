---
layout: post
title: "Previsão de Sobreviventes do Titanic (Linguagem R)"
date: 2018-08-19 15:00:00 -0300
tags: ["Machine Learning", R]
img: titanic-1.jpg
output: html_document    
---

Kaggle, um organizador de competições para cientistas de dados, o desafio inicial dele é a previsão dos sobreviventes da viagem inaugural do RMS TItanic. [Titanic: Machine Learning from Disaster](https://www.kaggle.com/c/titanic) 



### Ambiente

Configurei o ambiente como costumo fazer setando o diretório de trabalho, substituindo o *"."* pelo caminho dos arquivos de dados, e limpando as viáveis de ambiente


{% highlight r %}
setwd('.')
rm(list = ls())
{% endhighlight %}

Carregando os dados por leitura de csv simples


{% highlight r %}
test <- read.csv("test.csv")
train <- read.csv("train.csv")
{% endhighlight %}

E unindo as bases de dados para tratar ambas da mesma forma, adicionando a coluna Sobrevivente (Survived) para a test, para poder utilizar o comando *rbind*


{% highlight r %}
test$Survived <- NA
combi <- rbind(train, test)
rm('train','test')
{% endhighlight %}

### Tratamento de dados 

Os atributos de Sobrevivente (Survived), Porto em que embarcou (Embarked) e Gênero (ex) possuem quantidade de variações reduzidas e já informativas, facilitando a manipulação posterior portanto as transformei em fatores


{% highlight r %}
combi$Survived <- as.factor(combi$Survived)
combi$Embarked <- as.factor(combi$Embarked)
combi$Sex <- as.factor(combi$Sex)
{% endhighlight %}




{% highlight r %}
combi$Familia <- combi$SibSp + combi$Parch + 1
combi$TipoFamilia[combi$Familia == 1] <- 'Solteiro/a'
combi$TipoFamilia[combi$Familia < 5 & combi$Familia > 1] <- 'Pequena'
combi$TipoFamilia[combi$Familia > 4] <- 'Grande'
combi$TipoFamilia <- as.factor(combi$TipoFamilia)
{% endhighlight %}


{% highlight r %}
combi$Name <- as.character(combi$Name)
combi$Title <- sapply(combi$Name, FUN=function(x) {trimws(strsplit(x, split = "[.,]")[[1]][2])})
# Organizar títulos 
rareTitle <- c('Dona', 'Lady', 'the Countess', 'Capt', 'Col', 'Don', 'Dr', 'Major', 'Rev', 'Sir', 'Jonkheer')
combi$Title[combi$Title == 'Mlle'] <- 'Miss' 
combi$Title[combi$Title == 'Ms'] <- 'Miss'
combi$Title[combi$Title == 'Mme'] <- 'Mrs' 
combi$Title[combi$Title %in% rareTitle] <- 'Rare Title'
combi$Title <- as.factor(combi$Title)
rm(rareTitle)
{% endhighlight %}




{% highlight r %}
combi$Surname <- sapply(combi$Name, FUN=function(x) {trimws(strsplit(x, split = "[.,]")[[1]][1])})
{% endhighlight %}




{% highlight r %}
combi$Fare[is.na(combi$Fare)] <- median(combi$Fare[combi$Pclass == 3 & combi$Embarked == "S"], na.rm=TRUE)
{% endhighlight %}


{% highlight r %}
library("rpart")
modelo_idade <- rpart(Age ~ Pclass + Sex + SibSp + Parch + Fare + Embarked + Familia + TipoFamilia + Title, data=combi[!is.na(combi$Age),], method="anova")
combi$Age[is.na(combi$Age)] <- predict(modelo_idade, combi[is.na(combi$Age),])
rm(modelo_idade)
{% endhighlight %}


{% highlight r %}
combi$Kid <- ifelse(combi$Age <= 16, 1, 0)
combi$Kid <- as.factor(combi$Kid)
{% endhighlight %}


{% highlight r %}
combi$Mother <- ifelse(combi$Sex == 'female' & combi$Parch > 0 & combi$Age > 18 & combi$Title != 'Miss', 1, 0)
combi$Mother <- as.factor(combi$Mother)
{% endhighlight %}


{% highlight r %}
combi$Embarked[combi$Embarked == ""] <- NA
combi$Embarked[is.na(combi$Embarked)] <- "C"
{% endhighlight %}


{% highlight r %}
combi$Deck <- substr(combi$Cabin, 1, 1)
combi$Deck <- as.factor(combi$Deck)
{% endhighlight %}


{% highlight r %}
combi$NCabin <- sapply(as.character(combi$Cabin), FUN=function(x) {ifelse(x=="",0,length(strsplit(x, split = " ")[[1]]))})
combi$NCabin <- as.factor(combi$NCabin)
{% endhighlight %}




{% highlight r %}
combi$Line <- ifelse(combi$Ticket == "LINE", 1, 0)
combi$Line <- as.factor(combi$Line)
{% endhighlight %}


{% highlight r %}
ticketLimpo <- gsub("[./]", "", toupper(combi$Ticket))

combi$PC <- ifelse(substr(ticketLimpo, 1, 2) == "PC", 1, 0)
combi$PC <- as.factor(combi$PC)

combi$CA <- ifelse(substr(ticketLimpo, 1, 2) == "CA", 1, 0)
combi$CA <- as.factor(combi$CA)

combi$WC <- ifelse(substr(ticketLimpo, 1, 2) == "WC", 1, 0)
combi$WC <- as.factor(combi$WC)

rm(ticketLimpo)

# Encontrar a tripulação
combi$Crew <- ifelse(combi$Fare == 0 & combi$Deck == "", 1, 0)
{% endhighlight %}




{% highlight r %}
names <- colnames(combi)
campos <- names[!names %in% c("Name", "TipoFamilia", "Ticket", "Cabin", "Surname")]
combi <- combi[campos]


formula <- Survived ~ Pclass + Sex + Age + SibSp + Parch + Fare + Embarked + 
                      Familia + Title + Kid + Mother + Deck + NCabin + Line + 
                      PC + CA + WC + Crew


train <- combi[!is.na(combi$Survived),]
test <- combi[is.na(combi$Survived),]
rm(combi)
{% endhighlight %}

### Floresta Aletória

Aplicando Floresta Aletória (*Random Florest*)


{% highlight r %}
library("randomForest")

modelo_rf <- randomForest(formula, data=train)

test$Survived <- predict(modelo_rf, test)
test$Survived <- as.numeric(test$Survived) - 1

write.csv(test[c("PassengerId", "Survived")], file="solution.csv", row.names=FALSE)
{% endhighlight %}

## Visualizar critérios de sobrevivencia


{% highlight r %}
library("tidyverse")

importance <- importance(modelo_rf)

varImportance <- data.frame(Variables = row.names(importance), 
        Importance = round(importance[,'MeanDecreaseGini'],2))

rankImportance <- varImportance %>%
        mutate(Rank = paste0('#',dense_rank(desc(importance))))

ggplot(rankImportance, aes(x = reorder(Variables, Importance), 
                           y = Importance, fill = Importance)) +
  geom_bar(stat='identity') + 
  labs(x = 'Variables') +
  coord_flip() + 
  theme_minimal()
{% endhighlight %}

![plot of chunk importance](/./assets/Rfig/importance-1.svg)


### Visualização 



{% highlight r %}
# Biblioteca geração de relatórios
library("ggplot2")
# Descrição de Sobrevivência
train$Sub = ifelse(train$Survived == 1, "Sim" , "Não") 
# Palheta de Cores
cbPalette <- c("#FF6A00", "#069B87")
{% endhighlight %}

Homens sem títulos nobres ou patentes militares, foram os mais prejudicados


{% highlight r %}
ggplot(train, aes(as.factor(Title), fill=Sub, colour=Sub)) +
  labs(title = "", 
       x="Título", y="Quantidade", 
       fill="Sobreviventes", colour="Sobreviventes") +
  geom_bar(position='dodge', alpha = 0.2) +
  theme_minimal() +
  scale_fill_manual(values=cbPalette) +
  scale_colour_manual(values=cbPalette)
{% endhighlight %}

![plot of chunk unnamed-chunk-19](/./assets/Rfig/unnamed-chunk-19-1.svg)

No gráfico mostra a segunda característica em importância, crianças de até possuem mais possibilidades de adultos de 16 a 35 anos


{% highlight r %}
ggplot(train, aes(Age, fill = Sub, colour = Sub)) +
  labs(x = "Idade", y = "Quantidade", fill = "Sobreviventes", colour = "Sobreviventes") +
  geom_density(position = "identity", alpha = 0.2) +  
  xlim(0, max(train$Age)) + 
  theme_minimal() +
  scale_fill_manual(values=cbPalette) +
  scale_colour_manual(values=cbPalette)
{% endhighlight %}

![plot of chunk unnamed-chunk-20](/./assets/Rfig/unnamed-chunk-20-1.svg)

Infelizmente as pessoas que pagaram menos pela passagem, por estarem mais longes dos botes ou por outros fatores, é o terceiro critério


{% highlight r %}
ggplot(train, aes(Fare, fill = Sub, colour = Sub)) +
  labs(x = "Passagem", y = "Quantidade", fill = "Sobreviventes", colour = "Sobreviventes") +
  geom_density(alpha = 0.3) +  
  xlim(0, 150) + 
  theme_minimal() +
  scale_fill_manual(values=cbPalette) +
  scale_colour_manual(values=cbPalette)
{% endhighlight %}

![plot of chunk unnamed-chunk-21](/./assets/Rfig/unnamed-chunk-21-1.svg)

Embora seja o sétimo o tamanho da família é um dados interessante, proporcionalmente os solteiros tiveram a menor oportunidade de sobrevivência


{% highlight r %}
ggplot(train, aes(as.numeric(Familia), fill=Sub, colour=Sub)) +
  labs(title = "", 
       x="Tamanho da Família", y="Quantidade", 
       fill="Sobreviventes", colour="Sobreviventes") +
  geom_bar(position='dodge', alpha = 0.3) +
  scale_x_continuous(breaks=c(1:11)) + 
  theme_minimal() +
  scale_fill_manual(values=cbPalette) +
  scale_colour_manual(values=cbPalette)
{% endhighlight %}

![plot of chunk unnamed-chunk-22](/./assets/Rfig/unnamed-chunk-22-1.svg)

