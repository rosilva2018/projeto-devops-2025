Simple Node App - Kubernetes & Helm Deployment

Projeto

Este projeto é uma aplicação simples em Node.js utilizando o framework Express.js, empacotada em um container Docker e implantada no Kubernetes usando Helm.

Tecnologias Utilizadas

Node.js (Framework Express)

Docker (Containerização)

Helm (Gerenciamento de deploys no Kubernetes)

Kubernetes (Orquestração de containers)

Passo a Passo para Configuração e Implantação

1. Configurar o Ambiente

Certifique-se de ter as seguintes ferramentas instaladas:

Node.js e npm

Docker

Kubernetes

Helm

Verifique se os comandos abaixo retornam as versões corretamente:

node -v
npm -v
docker -v
kubectl version --client
helm version

2. Criar e Configurar o Projeto Node.js

mkdir simple-node-app
cd simple-node-app
npm init -y

Crie o arquivo server.js:

const express = require("express");
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/", (req, res) => {
    res.send("Hello, Kubernetes with Helm!");
});

app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});

Instale as dependências:

npm install express

3. Criar o Dockerfile

Crie o arquivo Dockerfile no diretório raiz:

FROM node:18
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]

Construa a imagem Docker:

docker build -t rosilva2018/simple-node-app:latest .

Faça login no Docker Hub:

docker login

Envie a imagem para o repositório Docker Hub:

docker push rosilva2018/simple-node-app:latest

4. Criar o Helm Chart

Navegue até o diretório do projeto e crie um Helm Chart:

mkdir helm
cd helm
helm create simple-node-app
mv simple-node-app/* .
rm -rf simple-node-app

Edite helm/values.yaml para definir os valores personalizados:

replicaCount: 2

image:
  repository: "rosilva2018/simple-node-app"
  tag: "latest"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 3000

ingress:
  enabled: false

serviceAccount:
  create: false

autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 5
  targetCPUUtilizationPercentage: 80

Remova helm/templates/hpa.yaml caso não utilize autoscaling:

rm helm/templates/hpa.yaml

5. Implantar no Kubernetes com Helm

No diretório do projeto, execute:

helm install simple-node-app ./helm

Verifique se os pods estão rodando:

kubectl get pods

Se tudo estiver correto, o output será algo como:

NAME                               READY   STATUS    RESTARTS   AGE
simple-node-app-7fb9494757-9s7r9   1/1     Running   0          3m
simple-node-app-7fb9494757-cf5zb   1/1     Running   0          3m

6. Acessar a Aplicação

Como o serviço está usando ClusterIP, use port-forward para acessar localmente:

kubectl port-forward simple-node-app-7fb9494757-9s7r9 8080:3000 -n default

Agora acesse no navegador:

http://127.0.0.1:8080

Se quiser expor a aplicação externamente, edite values.yaml:

service:
  type: LoadBalancer

E aplique a atualização:

helm upgrade simple-node-app ./helm

Verifique o IP externo:

kubectl get svc

7. Atualizar a Aplicação

Se fizer alterações no código, siga os passos abaixo:

Crie uma nova versão da imagem Docker:

docker build -t rosilva2018/simple-node-app:v2 .
docker push rosilva2018/simple-node-app:v2

Atualize o values.yaml:

image:
  repository: "rosilva2018/simple-node-app"
  tag: "v2"

Atualize o Kubernetes com Helm:

helm upgrade simple-node-app ./helm

Verifique os pods após o upgrade:

kubectl get pods

8. Remover a Aplicação

Para remover completamente o deploy do Kubernetes:

helm uninstall simple-node-app

Para remover a imagem localmente:

docker rmi rosilva2018/simple-node-app:latest

Conclusão

Agora sua aplicação Node.js está totalmente containerizada, gerenciada com Helm e rodando no Kubernetes. Se precisar de mais ajustes ou melhorias, basta modificar os arquivos e seguir os passos de atualização!

Autor: rosilva2018Licença: MITContato: rosilva2018@example.com


