# Usar uma imagem base do Node.js
FROM node:18

# Definir o diretório de trabalho
WORKDIR /app

# Copiar arquivos necessários
COPY package.json package-lock.json ./
RUN npm install

# Copiar o restante da aplicação
COPY . .

# Expor a porta
EXPOSE 3000

# Comando para rodar a aplicação
CMD ["npm", "start"]

