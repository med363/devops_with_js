### nestjs est un fremwork plus avacee que nodejs base sur expressjs(par defaut) ou fastify .
### create nestjs project
### 1.globally
```bash
npm i -g @nestjs/cli
```
### 2.A travers l'outils nest on peut cree un projet nestjs
```bash
nest new api
```
### 3.dans le meme directory qu'on cree le projet nest on cree un project rectjs
```bash
npx create-react-app app
```
### cree un dosier www a l'interieur on a cree un autre dossier certs à l'interieur de ce dernier un fichier nginx.conf et un fichier Dockerfile
### 1.Dockerfile de nginx
```bash
FROM nginx:alpine
COPY certs /certs
#path of nginx officially
COPY nginx.conf /etc/nginx/conf.d/default.conf
```
### 2.nginx.conf
```bash
server {
  listen 443 ssl http2;
  server_name localhost;
  #path private and public keys
  ssl_certfificate /certs/localhost.pem;
  ssl_certfificate_key /certs/localhost-key.pem;
location / {
#serv in docker-compose==>no de service c'est le hostname de chaque container
   proxy_pass http://api:3000;
  }
  
  location /api {
   proxy_pass http://app:3000;
  }
}
  
  
```
### pour que l'env soit simullaire à l'env de production on dont on doit utilise virtualisation d'application
### 4.dans le meme directory qu'on cree le projet nest on cree un fichier docker-compose.yml et Docker pour conterize les deux projets nest et react
### 4.2. docker-compose.yml
```bash
version: "3.8"
networks:
   amine: null
   
volumes:
#utilise db postgres
  postgres: null 
  
services:
# container api utilise l'image du Dockerfile externe 4.1 dans le meme chemin
  api:
    build:
# path of build
     context: api
#chemin de dockerfile
#     dockerfie: ./Dockerfile
     
# nom du container
    container_name: amine/api
# synchrone data volumes la 1ere /src c'est le workdir que nous avons annoce dans le dockerfile
    volumes: ["./api/src:/src/src"]
    networks: ["amine"]
    
    
# container api utilise l'image du Dockerfile externe 4.1 dans le meme chemin  
  app:
    build:
# path of build
     context: app
#chemin de dockerfile
#     dockerfie: ./Dockerfile
     
# path of build
    container_name: amine/app
# synchrone data volumes la 1ere /src c'est le workdir que nous avons annoce dans le dockerfile
    volumes: ["./app/src:/src/src"]
    networks: ["amine"]
    
### serv web nginx pour transmettre mon traffic à mon application
  www:
    build:
# path of build
     context: www
#chemin de dockerfile
#     dockerfie: ./Dockerfile
     
# nom du container
    container_name: amine/www
    networks: ["amine"]
    ports: ["80:80","443:443"]
```
### 4.1.Dckerfile in src of project api et app
```bash
FROM node:4-alpine
WORKDIR /src
COPY package.json .
RUN npm install 
COPY . .
CMD ["npm","run", "start:dev"]
```

### 4.3. .dockerignore pour que Dockerfile ne prend pas on consideration
```bash
**/node_modules
**/build
**/dist
```


## in terminal type et utilise l'option --build to reload docker-compose
```bash
docker-compose up
```
### or cree un fichie Makefile 
```bash
start:
  @docker-compose up --build -d
```
et on tappe 
```bash
make
```


## genere certificat in terminal in certs
```bash
mkcert localhost
```
### voir log in network nginx
```bash
docker logs amine-www
```





