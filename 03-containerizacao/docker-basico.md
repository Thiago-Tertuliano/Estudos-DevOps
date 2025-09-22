# Docker Básico

## Instalação do Docker

### Windows

1. Baixe o Docker Desktop do [site oficial](https://www.docker.com/products/docker-desktop)
2. Execute o instalador
3. Reinicie o computador se necessário
4. Verifique a instalação: `docker --version`

### Linux (Ubuntu/Debian)

```bash
# Atualizar pacotes
sudo apt update

# Instalar dependências
sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release

# Adicionar chave GPG oficial do Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Adicionar repositório
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

# Adicionar usuário ao grupo docker
sudo usermod -aG docker $USER
```

### macOS

1. Baixe o Docker Desktop do [site oficial](https://www.docker.com/products/docker-desktop)
2. Instale o arquivo .dmg
3. Inicie o Docker Desktop
4. Verifique a instalação: `docker --version`

## Comandos Básicos do Docker

### Gerenciamento de Imagens

```bash
# Listar imagens locais
docker images

# Baixar uma imagem
docker pull nginx:latest

# Remover uma imagem
docker rmi nginx:latest

# Remover imagens não utilizadas
docker image prune

# Inspecionar uma imagem
docker inspect nginx:latest
```

### Gerenciamento de Containers

```bash
# Listar containers em execução
docker ps

# Listar todos os containers
docker ps -a

# Executar um container
docker run nginx

# Executar um container em background
docker run -d nginx

# Executar um container com nome
docker run -d --name meu-nginx nginx

# Parar um container
docker stop meu-nginx

# Iniciar um container parado
docker start meu-nginx

# Remover um container
docker rm meu-nginx

# Remover containers parados
docker container prune
```

### Executar Comandos em Containers

```bash
# Executar comando em container em execução
docker exec -it meu-nginx bash

# Executar comando específico
docker exec meu-nginx ls /usr/share/nginx/html

# Acessar logs do container
docker logs meu-nginx

# Acompanhar logs em tempo real
docker logs -f meu-nginx
```

## Portas e Networking

### Mapeamento de Portas

```bash
# Mapear porta do host para container
docker run -d -p 8080:80 nginx

# Mapear porta específica do host
docker run -d -p 127.0.0.1:8080:80 nginx

# Mapear porta aleatória
docker run -d -P nginx
```

### Networking

```bash
# Listar redes
docker network ls

# Criar uma rede
docker network create minha-rede

# Conectar container à rede
docker run -d --network minha-rede --name web nginx

# Inspecionar rede
docker network inspect minha-rede
```

## Volumes

### Volumes Nomeados

```bash
# Criar um volume
docker volume create meu-volume

# Listar volumes
docker volume ls

# Usar volume em container
docker run -d -v meu-volume:/data nginx

# Inspecionar volume
docker volume inspect meu-volume
```

### Bind Mounts

```bash
# Montar diretório do host
docker run -d -v /caminho/do/host:/caminho/do/container nginx

# Exemplo com Windows
docker run -d -v C:\meu-projeto:/app nginx
```

## Variáveis de Ambiente

```bash
# Definir variável de ambiente
docker run -d -e MYSQL_ROOT_PASSWORD=minhasenha mysql

# Usar arquivo de variáveis
docker run -d --env-file .env nginx

# Exemplo de arquivo .env
# MYSQL_ROOT_PASSWORD=minhasenha
# MYSQL_DATABASE=meudb
# MYSQL_USER=usuario
```

## Exemplos Práticos

### 1. Servidor Web Simples

```bash
# Executar nginx
docker run -d --name web-server -p 8080:80 nginx

# Acessar no navegador: http://localhost:8080
```

### 2. Banco de Dados MySQL

```bash
# Executar MySQL
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=minhasenha \
  -e MYSQL_DATABASE=meudb \
  -p 3306:3306 \
  mysql:8.0

# Conectar ao MySQL
docker exec -it mysql-db mysql -u root -p
```

### 3. Aplicação Node.js

```bash
# Executar aplicação Node.js
docker run -d \
  --name node-app \
  -p 3000:3000 \
  -v $(pwd):/app \
  -w /app \
  node:16 \
  npm start
```

## Limpeza e Manutenção

### Limpeza de Recursos

```bash
# Parar todos os containers
docker stop $(docker ps -q)

# Remover todos os containers parados
docker container prune

# Remover todas as imagens não utilizadas
docker image prune -a

# Remover todos os volumes não utilizados
docker volume prune

# Limpeza completa (cuidado!)
docker system prune -a --volumes
```

### Monitoramento

```bash
# Estatísticas de uso
docker stats

# Estatísticas de um container específico
docker stats meu-nginx

# Informações do sistema Docker
docker system df

# Informações detalhadas
docker system info
```

## Troubleshooting

### Problemas Comuns

1. **Container não inicia**

   ```bash
   # Verificar logs
   docker logs nome-do-container

   # Verificar status
   docker ps -a
   ```

2. **Porta já em uso**

   ```bash
   # Verificar qual processo usa a porta
   netstat -tulpn | grep :8080

   # Usar porta diferente
   docker run -p 8081:80 nginx
   ```

3. **Permissões no Linux**

   ```bash
   # Adicionar usuário ao grupo docker
   sudo usermod -aG docker $USER

   # Fazer logout e login novamente
   ```

### Comandos de Debug

```bash
# Inspecionar container
docker inspect nome-do-container

# Executar shell no container
docker exec -it nome-do-container sh

# Ver processos no container
docker exec nome-do-container ps aux

# Copiar arquivos do/para container
docker cp arquivo.txt nome-do-container:/caminho/
docker cp nome-do-container:/caminho/arquivo.txt ./
```

## Próximos Passos

Agora que você domina os comandos básicos do Docker, você está pronto para:

1. **Criar Dockerfiles** personalizados
2. **Usar Docker Compose** para orquestrar múltiplos containers
3. **Implementar boas práticas** de containerização
4. **Integrar Docker** com CI/CD

---

**Dica**: Pratique criando containers para diferentes tipos de aplicações e experimente com diferentes configurações de rede e volumes!
