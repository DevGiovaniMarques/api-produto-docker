# api-produto-docker
Projeto de uma API de produtos, onde vamos utilizar um container para a aplicação em Node e outro container em Mongo, trabalhar a conexão entre os containers.

Primeiro passo foi criar uma network para conexão entre os containers executando o comando:
```
docker network create produto_net
```
Nesse projeto optei por criar um volume para o container do mongodb para que os dados sejam persistidos, criei o volume com o comando:
```
docker volume create produto_vol
```
Agora vamos criar o container com o Mongodb, para construção do container vamos passar alguns parametros como: variável de usuário e de senha, o volume que criamos acima, em qual porta vamos executar, já passamos também a network para conexão e criamos um nome para o container que será usado posteriormente.
```
docker container run -d -e MONGO_INITDB_ROOT_USERNAME=mongouser -e MONGO_INITDB_ROOT_PASSWORD=mongopwd -v produto_vol:/data/db -p 27017:27017 --network produto_net --name mongodb mongo:4.4.3
```
Agora já temos o container do Mongodb executando, vamos criar a imagem para criar o container Node para armazenar a aplicação.
Criei um Dockerfile para criar a imagem do container.
```
FROM node:14.15.4
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8080
CMD ["node","app.js"]
```
Depois de criar o arquivo vamos executar o comando para criar a imagem, precisamos navegar até  pasta onde está o arquivo Dockerfile:
```
docker build -t ggarruch/api-produto:v1 .
```
Agora a imagem está criada, pode verificar executando o comando:
```
docker image ls
```
Agora vamos criar o container Node onde ficará a aplicação e vamos passar alguns parametros: a porta em qual será executada a aplicação, a network para conexão, e uma variável para conexão com o Mongodb e a imagem:
```
 docker container run -d -p 8080:8080 --network produto_net -e MONGODB_URI=mongodb://mongouser:mongopwd@mongodb:27017/admin ggarruch/api-produto:v1
```
Agora estamos com os dois containers rodando para testar é só acessar o endereço:
```
http://localhost:8080/api-docs/
```
Para testar a API vamos criar um produto
![alt text](https://github.com/DevGiovaniMarques/api-produto-docker/blob/4834f02bdb272bd0bdcac3953f0aea87cc9f37f1/2023-09-24%2014_02_16-Swagger%20UI%20e%20mais%2015%20p%C3%A1ginas%20-%20Pessoa%201%20%E2%80%94%20Microsoft%E2%80%8B%20Edge.png)

Em seguida podemos ver os produtos cadastrados e a API está funcionando corretamente.

![alt text](https://github.com/DevGiovaniMarques/api-produto-docker/blob/4834f02bdb272bd0bdcac3953f0aea87cc9f37f1/2023-09-24%2014_03_01-Swagger%20UI%20e%20mais%2015%20p%C3%A1ginas%20-%20Pessoa%201%20%E2%80%94%20Microsoft%E2%80%8B%20Edge.png)

Finalizado o projeto , um projeto simples , mas muito interessante para trabalhar a conexão entre containers e simular uma aplicaçao na prática.
