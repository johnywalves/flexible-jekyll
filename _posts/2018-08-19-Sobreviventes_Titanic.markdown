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
setwd(".")
rm(list = ls())
{% endhighlight %}

Carregando os dados por leitura de csv simples


{% highlight r %}
test <- read.csv("test.csv")
{% endhighlight %}



{% highlight text %}
## Error in file(file, "rt"): não é possível abrir a conexão
{% endhighlight %}



{% highlight r %}
train <- read.csv("train.csv")
{% endhighlight %}



{% highlight text %}
## Error in file(file, "rt"): não é possível abrir a conexão
{% endhighlight %}

E unindo as bases de dados para tratar ambas da mesma forma, adicionando a coluna Sobrevivente (Survived) para a test, para poder utilizar o comando *rbind*


{% highlight r %}
test$Survived <- NA
{% endhighlight %}



{% highlight text %}
## Error in test$Survived <- NA: objeto 'test' não encontrado
{% endhighlight %}



{% highlight r %}
combi <- rbind(train, test)
{% endhighlight %}



{% highlight text %}
## Error in rbind(train, test): objeto 'train' não encontrado
{% endhighlight %}



{% highlight r %}
rm('train','test')
{% endhighlight %}

### Tratamento de dados 

Os atributos de Sobrevivente (Survived), Porto em que embarcou (Embarked) e Gênero (ex) possuem quantidade de variações reduzidas e já informativas, facilitando a manipulação posterior portanto as transformei em fatores


{% highlight r %}
combi$Survived <- as.factor(combi$Survived)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Embarked <- as.factor(combi$Embarked)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Sex <- as.factor(combi$Sex)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}




{% highlight r %}
combi$Familia <- combi$SibSp + combi$Parch + 1
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$TipoFamilia[combi$Familia == 1] <- 'Solteiro/a'
{% endhighlight %}



{% highlight text %}
## Error in combi$TipoFamilia[combi$Familia == 1] <- "Solteiro/a": objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$TipoFamilia[combi$Familia < 5 & combi$Familia > 1] <- 'Pequena'
{% endhighlight %}



{% highlight text %}
## Error in combi$TipoFamilia[combi$Familia < 5 & combi$Familia > 1] <- "Pequena": objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$TipoFamilia[combi$Familia > 4] <- 'Grande'
{% endhighlight %}



{% highlight text %}
## Error in combi$TipoFamilia[combi$Familia > 4] <- "Grande": objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$TipoFamilia <- as.factor(combi$TipoFamilia)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}


{% highlight r %}
combi$Name <- as.character(combi$Name)
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Title <- sapply(combi$Name, FUN=function(x) {trimws(strsplit(x, split = "[.,]")[[1]][2])})
{% endhighlight %}



{% highlight text %}
## Error in lapply(X = X, FUN = FUN, ...): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
# Organizar títulos 
rareTitle <- c('Dona', 'Lady', 'the Countess', 'Capt', 'Col', 'Don', 'Dr', 'Major', 'Rev', 'Sir', 'Jonkheer')
combi$Title[combi$Title == 'Mlle'] <- 'Miss' 
{% endhighlight %}



{% highlight text %}
## Error in combi$Title[combi$Title == "Mlle"] <- "Miss": objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Title[combi$Title == 'Ms'] <- 'Miss'
{% endhighlight %}



{% highlight text %}
## Error in combi$Title[combi$Title == "Ms"] <- "Miss": objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Title[combi$Title == 'Mme'] <- 'Mrs' 
{% endhighlight %}



{% highlight text %}
## Error in combi$Title[combi$Title == "Mme"] <- "Mrs": objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Title[combi$Title %in% rareTitle] <- 'Rare Title'
{% endhighlight %}



{% highlight text %}
## Error in combi$Title[combi$Title %in% rareTitle] <- "Rare Title": objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Title <- as.factor(combi$Title)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
rm(rareTitle)
{% endhighlight %}




{% highlight r %}
combi$Surname <- sapply(combi$Name, FUN=function(x) {trimws(strsplit(x, split = "[.,]")[[1]][1])})
{% endhighlight %}



{% highlight text %}
## Error in lapply(X = X, FUN = FUN, ...): objeto 'combi' não encontrado
{% endhighlight %}




{% highlight r %}
combi$Fare[is.na(combi$Fare)] <- median(combi$Fare[combi$Pclass == 3 & combi$Embarked == "S"], na.rm=TRUE)
{% endhighlight %}



{% highlight text %}
## Error in median(combi$Fare[combi$Pclass == 3 & combi$Embarked == "S"], : objeto 'combi' não encontrado
{% endhighlight %}




{% highlight r %}
library("rpart")
modelo_idade <- rpart(Age ~ Pclass + Sex + SibSp + Parch + Fare + Embarked + Familia + TipoFamilia + Title, data=combi[!is.na(combi$Age),], method="anova")
{% endhighlight %}



{% highlight text %}
## Error in is.data.frame(data): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Age[is.na(combi$Age)] <- predict(modelo_idade, combi[is.na(combi$Age),])
{% endhighlight %}



{% highlight text %}
## Error in predict(modelo_idade, combi[is.na(combi$Age), ]): objeto 'modelo_idade' não encontrado
{% endhighlight %}



{% highlight r %}
rm(modelo_idade)
{% endhighlight %}


{% highlight r %}
combi$Kid <- ifelse(combi$Age <= 16, 1, 0)
{% endhighlight %}



{% highlight text %}
## Error in ifelse(combi$Age <= 16, 1, 0): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Kid <- as.factor(combi$Kid)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}


{% highlight r %}
combi$Mother <- ifelse(combi$Sex == 'female' & combi$Parch > 0 & combi$Age > 18 & combi$Title != 'Miss', 1, 0)
{% endhighlight %}



{% highlight text %}
## Error in ifelse(combi$Sex == "female" & combi$Parch > 0 & combi$Age > : objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Mother <- as.factor(combi$Mother)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}


{% highlight r %}
combi$Embarked[combi$Embarked == ""] <- NA
{% endhighlight %}



{% highlight text %}
## Error in combi$Embarked[combi$Embarked == ""] <- NA: objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Embarked[is.na(combi$Embarked)] <- "C"
{% endhighlight %}



{% highlight text %}
## Error in combi$Embarked[is.na(combi$Embarked)] <- "C": objeto 'combi' não encontrado
{% endhighlight %}


{% highlight r %}
combi$Deck <- substr(combi$Cabin, 1, 1)
{% endhighlight %}



{% highlight text %}
## Error in substr(combi$Cabin, 1, 1): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Deck <- as.factor(combi$Deck)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}


{% highlight r %}
combi$NCabin <- sapply(as.character(combi$Cabin), FUN=function(x) {ifelse(x=="",0,length(strsplit(x, split = " ")[[1]]))})
{% endhighlight %}



{% highlight text %}
## Error in lapply(X = X, FUN = FUN, ...): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$NCabin <- as.factor(combi$NCabin)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}




{% highlight r %}
combi$Line <- ifelse(combi$Ticket == "LINE", 1, 0)
{% endhighlight %}



{% highlight text %}
## Error in ifelse(combi$Ticket == "LINE", 1, 0): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$Line <- as.factor(combi$Line)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}


{% highlight r %}
ticketLimpo <- gsub("[./]", "", toupper(combi$Ticket))
{% endhighlight %}



{% highlight text %}
## Error in toupper(combi$Ticket): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$PC <- ifelse(substr(ticketLimpo, 1, 2) == "PC", 1, 0)
{% endhighlight %}



{% highlight text %}
## Error in substr(ticketLimpo, 1, 2): objeto 'ticketLimpo' não encontrado
{% endhighlight %}



{% highlight r %}
combi$PC <- as.factor(combi$PC)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$CA <- ifelse(substr(ticketLimpo, 1, 2) == "CA", 1, 0)
{% endhighlight %}



{% highlight text %}
## Error in substr(ticketLimpo, 1, 2): objeto 'ticketLimpo' não encontrado
{% endhighlight %}



{% highlight r %}
combi$CA <- as.factor(combi$CA)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
combi$WC <- ifelse(substr(ticketLimpo, 1, 2) == "WC", 1, 0)
{% endhighlight %}



{% highlight text %}
## Error in substr(ticketLimpo, 1, 2): objeto 'ticketLimpo' não encontrado
{% endhighlight %}



{% highlight r %}
combi$WC <- as.factor(combi$WC)
{% endhighlight %}



{% highlight text %}
## Error in is.factor(x): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
rm(ticketLimpo)

# Encontrar a tripulação
combi$Crew <- ifelse(combi$Fare == 0 & combi$Deck == "", 1, 0)
{% endhighlight %}



{% highlight text %}
## Error in ifelse(combi$Fare == 0 & combi$Deck == "", 1, 0): objeto 'combi' não encontrado
{% endhighlight %}




{% highlight r %}
names <- colnames(combi)
{% endhighlight %}



{% highlight text %}
## Error in is.data.frame(x): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
campos <- names[!names %in% c("Name", "TipoFamilia", "Ticket", "Cabin", "Surname")]
{% endhighlight %}



{% highlight text %}
## Error in match(x, table, nomatch = 0L): 'match' requer argumentos vetoriais
{% endhighlight %}



{% highlight r %}
combi <- combi[campos]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
formula <- Survived ~ Pclass + Sex + Age + SibSp + Parch + Fare + Embarked + 
                      Familia + Title + Kid + Mother + Deck + NCabin + Line + 
                      PC + CA + WC + Crew


train <- combi[!is.na(combi$Survived),]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
test <- combi[is.na(combi$Survived),]
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'combi' não encontrado
{% endhighlight %}



{% highlight r %}
rm(combi)
{% endhighlight %}

### Floresta Aletória

Aplicando Floresta Aletória (*Random Florest*)


{% highlight r %}
library("randomForest")

modelo_rf <- randomForest(formula, data=train)
{% endhighlight %}



{% highlight text %}
## Error in eval(m$data, parent.frame()): objeto 'train' não encontrado
{% endhighlight %}



{% highlight r %}
test$Survived <- predict(modelo_rf, test)
{% endhighlight %}



{% highlight text %}
## Error in predict(modelo_rf, test): objeto 'modelo_rf' não encontrado
{% endhighlight %}



{% highlight r %}
test$Survived <- as.numeric(test$Survived) - 1
{% endhighlight %}



{% highlight text %}
## Error in eval(expr, envir, enclos): objeto 'test' não encontrado
{% endhighlight %}



{% highlight r %}
write.csv(test[c("PassengerId", "Survived")], file="solution.csv", row.names=FALSE)
{% endhighlight %}



{% highlight text %}
## Error in is.data.frame(x): objeto 'test' não encontrado
{% endhighlight %}

### Visualizar 


{% highlight r %}
library("tidyverse")

importance <- importance(modelo_rf)
{% endhighlight %}



{% highlight text %}
## Error in importance(modelo_rf): objeto 'modelo_rf' não encontrado
{% endhighlight %}



{% highlight r %}
varImportance <- data.frame(Variables = row.names(importance), 
        Importance = round(importance[,'MeanDecreaseGini'],2))
{% endhighlight %}



{% highlight text %}
## Error in importance[, "MeanDecreaseGini"]: objeto de tipo 'closure' não possível dividir em subconjuntos
{% endhighlight %}



{% highlight r %}
rankImportance <- varImportance %>%
        mutate(Rank = paste0('#',dense_rank(desc(importance))))
{% endhighlight %}



{% highlight text %}
## Error in eval(lhs, parent, parent): objeto 'varImportance' não encontrado
{% endhighlight %}



{% highlight r %}
ggplot(rankImportance, aes(x = reorder(Variables, Importance), 
                           y = Importance, fill = Importance)) +
  geom_bar(stat='identity') + 
  labs(x = 'Variables') +
  coord_flip() + 
  theme_minimal()
{% endhighlight %}



{% highlight text %}
## Error in ggplot(rankImportance, aes(x = reorder(Variables, Importance), : objeto 'rankImportance' não encontrado
{% endhighlight %}
