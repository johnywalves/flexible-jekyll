---
layout: post
title: "Flask Restful API com Token (Python)"
date: 2018-09-10 20:33:15 -0300
tags: [Flask, Python, Web, API]
img: flask-1.jpg
output: html_document      
---



A vantagem de utilizar uma Web API para garantir o acesso e manipulação de dados de uma aplicação possibilita controlar a disponibilidade, segurança e formato dos dados<br>
Fazendo uso de alguns pacotes Python para controlar as Requisições/Respostas, JSON, REST, JWT e Criptografia Hash podemos entregar de maneira fácil e rápida essa API

## Ambiente

Usando um ambiente virtual para facilitar o controle de dependências do projeto, detalhamento disponível em ((Virtual Environment)[../Virtual_Environment])


{% highlight bash %}
pip install pipenv 
{% endhighlight %}

Iniciar o ambiente virtual, onde o desenvolvimento será isolado, para gerenciar as dependências<br>
Dentro da pasta do projeto onde os fontes serão criados  


{% highlight bash %}
pipenv shell
{% endhighlight %}

Dentro do *pipenv*, os comandos ignoram os pacotes no ambiente externo, sendo necessário instalar os que serão utilizados


{% highlight bash %}
pipenv install flask flask-restful flask-jwt-extended passlib
{% endhighlight %}

## Servidor  

O pacote do **Flask** possibilita escutar uma porta para garantir uma aplicação *Web*<br>
Criar um arquivo **app.py** com o conteúdo abaixo, esse nome é um dos padrões do Flask, aconselhavél seu uso


{% highlight python %}
import os
from flask import Flask

app = Flask(__name__)

@app.route('/')
def start():
    return 'Uma resposta flask'
	
if __name__ == '__main__':
    port = int(os.environ.get("PORT", 5000))
    app.run(host='0.0.0.0', port=port)
{% endhighlight %}

Como pode ser visto ele está importando a biblioteca **Flask**, escutando a porta 5000 e retornando o texto 'Uma resposta flask'<br>
Assim podemos executar o aplicativo com o comando


{% highlight bash %}
flask run
{% endhighlight %}

Acessando pelo navegador o endereço [http://localhost:5000/](http://localhost:5000/) vai aparecer a mensagem "Uma resposta flask" mostrando que o ambiente está completamente funcional

## REST

De forma simplicitada uma REST faz referência a um CRUD através de requisições HTTP, onde o servidor e cliente possui uma comunicação completa sem a necessidade de armazenar informações

|	Operações	|	Descrição	|
|---|---|
|	POST		|	(CREATE) Inserir novas instâncias	|
|	GET			| (READ) Consultar instâncias  	|
|	PUT			|	(UPDATE) Atualizar as informações de uma ou várias instâncias	|
|	DELETE		|	(DELETE) Deletar uma instância 	|

Vamos adicionar um nível de detalhamento com os métodos de requisição HTTP  


{% highlight python %}
from flask_restful import Resource, Api

api = Api(app)

class response(Resource):
	def post(self):
		return {'post':'Resposta de POST'}

	def get(self):
		return {'get':'Resposta de GET'}
		
	def put(self):
		return {'put':'Resposta de PUT'}				
		
	def delete(self):
		return {'delete':'Resposta de DELETE'}

api.add_resource(response, '/response')
{% endhighlight %}

Para testar a execução podemos utiliza o [PostMan](https://www.getpostman.com/) ou pelo pacote **request** do Python<br>
Vamos instalar o **requests** pelo PyPi 


{% highlight bash %}
pip install requests
{% endhighlight %}

Através do shell do Python, acessado pelo comando `python` no shell do sistema, carregamos os objetos para cada tipo de requisição


{% highlight python %}
from requests import put, get, post, delete
{% endhighlight %}

De maneira simples podemos fazer as requisições, com a função `.json()` forçando uma resposta em json


{% highlight python %}
post('http://localhost:5000/response').json()
# {'post':'Resposta de POST'}

put('http://localhost:5000/response').json()
# {'post':'Resposta de PUT'}

get('http://localhost:5000/response').json()
# {'get':'Resposta de GET'}

delete('http://localhost:5000/response').json()
# {'delete':'Resposta de DELETE'}
{% endhighlight %}

## JSON




{% highlight js %}
{
	'name':'cake', 
	'categories':[
		{'name':'sweet'}, 
		{'name':'wheat'}
	], 
	'steps':[
		{'ingredient':'sugar'}, 
		{'ingredient':'flour'}
	]
}
{% endhighlight %}



{% highlight python %}
from flask import request

class readJSON(Resource):
	def post(self):
		data = request.json
		
		print('Name: ' + data['name'])
		print('First Category: ' + data['categories'][0]['name'])
		
		print('Steps') 
		for step in data['steps']:
			print('Ingredient: ' + step['ingredient']) 

api.add_resource(readJSON, '/readjson')
{% endhighlight %}


{% highlight python %}
post('http://localhost:5000/readjson', json={'name':'cake', 'categories':[{'name':'sweet'}, {'name':'wheat'}], 'steps':[{'ingredient':'sugar'}, {'ingredient':'flour'}]}).json()
# Print do lado da aplicação 
# Name: cake
# First Category: sweet
# Steps
# Ingredient: sugar
# Ingredient: flour
{% endhighlight %}

## Token


{% highlight python %}
from flask_jwt_extended import jwt_required

class response(Resource):
	@jwt_required
	def post(self):
		return {'post':'Resposta de POST'}
{% endhighlight %}


{% highlight python %}
post('http://localhost:5000/v1.0/posts').json()
# {'message': 'Internal Server Error'} 
{% endhighlight %}

`Missing Authorization Header`


{% highlight python %}
from flask_jwt_extended import JWTManager, jwt_required, get_jwt_identity, create_access_token

app.config['JWT_SECRET_KEY'] = 'Chave-Secreta-JWT'
jwt = JWTManager(app)

class token(Resource):
	def get(self):
		data = request.json

		if (('admin' == data['user']) & ('123' == data['pass'])):
			current_user = get_jwt_identity()
			access_token = create_access_token(identity = current_user)
			return {'token': access_token}
		else:
			return {}
{% endhighlight %}



{% highlight python %}
get('http://localhost:5000/token', json={'user':'admin', 'pass':'123'}).json()
# {'token': '<token gerado>'}
{% endhighlight %}


{% highlight python %}
headers = {"Authorization":"Bearer <token gerado>"}
post('http://localhost:5000/v1.0/posts', headers=headers).json()
# {'post':'Resposta de POST'}
{% endhighlight %}

### Criptografia Hash 

Usando como exemplo um cadastro com um usuário "admin" e senha "123", devemos criptografar a senha 


{% highlight python %}
from passlib.hash import pbkdf2_sha256 as sha256
sha256.hash('123')
# $pbkdf2-sha256$29000$mzNmjJHSWuudE8KYU8q59w$HLyrrchxVmTINur6OJ5LxQP8Rma2lWpDveHCzVa7iKQ
{% endhighlight %}



{% highlight python %}
if (('admin' == data['user']) & sha256.verify(data['pass'], '$pbkdf2-sha256$29000$mzNmjJHSWuudE8KYU8q59w$HLyrrchxVmTINur6OJ5LxQP8Rma2lWpDveHCzVa7iKQ')):
{% endhighlight %}

Claro que este trecho funciona melhor com uma base de dados consultando o nome do usuário e a senha hash armazenada

### Compilando

Para facilitar o código completo gerado nas etapas anteriores que devem conter no *app.py* para rodar com o \'flask run\'


{% highlight python %}
import os
from flask import Flask, request
from flask_restful import Resource, Api
from flask_jwt_extended import JWTManager, jwt_required, get_jwt_identity, create_access_token
from passlib.hash import pbkdf2_sha256 as sha256

app = Flask(__name__)
api = Api(app)

app.config['JWT_SECRET_KEY'] = 'Chave-Secreta-JWT'
jwt = JWTManager(app)

@app.route('/')
def start():
    return 'Uma resposta do flask'

class response(Resource):
	@jwt_required
	def post(self):
		return {'post':'Resposta de POST'}

	@jwt_required
	def put(self):
		return {'put':'Resposta de PUT'}				

	@jwt_required	
	def get(self):
		return {'get':'Resposta de GET'}

	@jwt_required	
	def delete(self):
		return {'delete':'Resposta de DELETE'}

class readJSON(Resource):
	def post(self):
		data = request.json
		
		print('Name: ' + data['name'])
		print('First Category: ' + data['categories'][0]['name'])
		
		print('steps') 
		for step in data['steps']:
			print(step['ingredient']) 

class token(Resource):
	def post(self):
		data = request.json

		if (('admin' == data['user']) & sha256.verify(data['pass'], '$pbkdf2-sha256$29000$mzNmjJHSWuudE8KYU8q59w$HLyrrchxVmTINur6OJ5LxQP8Rma2lWpDveHCzVa7iKQ')):		
			current_user = get_jwt_identity()
			access_token = create_access_token(identity = current_user)
			return {'token': access_token}
		else:
			return {}

api.add_resource(token, '/token')
api.add_resource(readJSON, '/readjson')
api.add_resource(response, '/response')

if __name__ == '__main__':
    port = int(os.environ.get("PORT", 5000))
    app.run(host='0.0.0.0', port=port)
{% endhighlight %}

### Referências

[Flask - Quickstart](http://flask.pocoo.org/docs/1.0/quickstart/)<br>
[Flask - Configuration Handling](http://flask.pocoo.org/docs/1.0/config/)<br>
[Flask-RESTful - Quickstart](https://flask-restful.readthedocs.io/en/latest/quickstart.html)<br>
[JWT authorization in Flask](https://codeburst.io/jwt-authorization-in-flask-c63c1acf4eeb)

