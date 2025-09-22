# Hello World Container

## 🎯 Objetivo

Criar seu primeiro container Docker e entender os conceitos básicos de containerização.

## 📋 Pré-requisitos

- Docker instalado
- Conhecimento básico de linha de comando

## 🚀 Passo a Passo

### 1. Criar o Dockerfile

```dockerfile
# Use uma imagem base oficial
FROM node:18-alpine

# Defina o diretório de trabalho
WORKDIR /app

# Copie os arquivos de dependências
COPY package*.json ./

# Instale as dependências
RUN npm install

# Copie o código da aplicação
COPY . .

# Exponha a porta
EXPOSE 3000

# Comando para executar a aplicação
CMD ["npm", "start"]
```

### 2. Criar a aplicação Node.js

```javascript
// app.js
const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => {
  res.send("Hello World from Docker! 🐳");
});

app.get("/health", (req, res) => {
  res.json({ status: "OK", timestamp: new Date().toISOString() });
});

app.listen(port, "0.0.0.0", () => {
  console.log(`App running on port ${port}`);
});
```

```json
// package.json
{
  "name": "hello-world-docker",
  "version": "1.0.0",
  "description": "Hello World Docker app",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

### 3. Build da imagem

```bash
# Build da imagem
docker build -t hello-world-app .

# Verificar se a imagem foi criada
docker images
```

### 4. Executar o container

```bash
# Executar o container
docker run -d -p 3000:3000 --name hello-world hello-world-app

# Verificar se está rodando
docker ps

# Ver os logs
docker logs hello-world
```

### 5. Testar a aplicação

```bash
# Testar no navegador
curl http://localhost:3000

# Testar health check
curl http://localhost:3000/health
```

### 6. Gerenciar o container

```bash
# Parar o container
docker stop hello-world

# Iniciar o container
docker start hello-world

# Remover o container
docker rm hello-world

# Remover a imagem
docker rmi hello-world-app
```

## 🐳 Docker Compose

Crie um arquivo `docker-compose.yml`:

```yaml
version: "3.8"

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    restart: unless-stopped
```

Execute com:

```bash
# Subir os serviços
docker-compose up -d

# Ver logs
docker-compose logs -f

# Parar os serviços
docker-compose down
```

## 🎯 Desafios

1. **Modifique a aplicação** para mostrar informações do sistema
2. **Adicione uma nova rota** `/info` que retorna informações do container
3. **Configure variáveis de ambiente** para personalizar a mensagem
4. **Implemente logging** estruturado
5. **Adicione health checks** mais robustos

## 📚 Conceitos Aprendidos

- ✅ Dockerfile e build de imagens
- ✅ Execução de containers
- ✅ Mapeamento de portas
- ✅ Docker Compose
- ✅ Gerenciamento de containers
- ✅ Health checks básicos

## 🔗 Próximos Passos

Após completar este projeto, você estará pronto para:

- **Projeto 2**: Aplicação Web Completa
- **Módulo 3**: Docker avançado
- **Módulo 4**: Kubernetes

---

**Dica**: Experimente modificar o código e rebuildar a imagem para ver as mudanças!
