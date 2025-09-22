# GitHub Actions

## O que é GitHub Actions?

GitHub Actions é uma plataforma de CI/CD integrada ao GitHub que permite automatizar workflows de software diretamente no repositório. É uma ferramenta poderosa para integração contínua e entrega contínua.

## 🎯 Conceitos Básicos

### Workflow

Um workflow é um processo automatizado que você define no seu repositório. É composto por jobs e steps.

### Job

Um job é um conjunto de steps que são executados no mesmo runner.

### Step

Um step é uma tarefa individual que pode executar comandos ou usar actions.

### Action

Uma action é uma unidade reutilizável de código que pode ser executada em um workflow.

## 📁 Estrutura de Arquivos

```
.github/
└── workflows/
    ├── ci.yml
    ├── deploy.yml
    └── security.yml
```

## 🚀 Primeiro Workflow

### Exemplo Básico

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "18"

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build
```

## 🔧 Triggers (Gatilhos)

### Push e Pull Request

```yaml
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
```

### Schedule (Cron)

```yaml
on:
  schedule:
    - cron: "0 2 * * *" # Todo dia às 2h
```

### Workflow Dispatch

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Environment to deploy"
        required: true
        default: "staging"
```

### Webhook

```yaml
on:
  repository_dispatch:
    types: [deploy]
```

## 🏃‍♂️ Runners

### GitHub-hosted Runners

- **ubuntu-latest**: Ubuntu 22.04
- **windows-latest**: Windows Server 2022
- **macos-latest**: macOS 12

### Self-hosted Runners

```yaml
jobs:
  build:
    runs-on: [self-hosted, linux]
```

## 📦 Actions Populares

### Checkout

```yaml
- uses: actions/checkout@v3
  with:
    fetch-depth: 0 # Fetch all history
```

### Setup Node.js

```yaml
- uses: actions/setup-node@v3
  with:
    node-version: "18"
    cache: "npm"
```

### Setup Python

```yaml
- uses: actions/setup-python@v4
  with:
    python-version: "3.11"
    cache: "pip"
```

### Setup Java

```yaml
- uses: actions/setup-java@v3
  with:
    java-version: "17"
    distribution: "temurin"
```

### Docker

```yaml
- name: Build Docker image
  uses: docker/build-push-action@v4
  with:
    context: .
    push: true
    tags: ${{ github.repository }}:${{ github.sha }}
```

## 🔐 Secrets e Variáveis

### Secrets

```yaml
- name: Deploy
  run: |
    echo "Deploying to ${{ secrets.ENVIRONMENT }}"
    echo "API Key: ${{ secrets.API_KEY }}"
```

### Variáveis de Ambiente

```yaml
env:
  NODE_ENV: production
  API_URL: https://api.example.com
```

### Variáveis do Contexto

```yaml
- name: Show context
  run: |
    echo "Repository: ${{ github.repository }}"
    echo "Branch: ${{ github.ref_name }}"
    echo "Commit: ${{ github.sha }}"
    echo "Actor: ${{ github.actor }}"
```

## 🏗️ Jobs e Matrizes

### Job Simples

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm test
```

### Matriz de Builds

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16, 18, 20]
        os: [ubuntu-latest, windows-latest, macos-latest]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm test
```

### Jobs Dependentes

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: npm test

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: npm run build

  deploy:
    needs: [test, build]
    runs-on: ubuntu-latest
    steps:
      - run: npm run deploy
```

## 🚀 Deploy com GitHub Actions

### Deploy para Vercel

```yaml
name: Deploy to Vercel

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
          vercel-args: "--prod"
```

### Deploy para AWS S3

```yaml
name: Deploy to S3

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Build
        run: npm run build

      - name: Deploy to S3
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Upload to S3
        run: aws s3 sync dist/ s3://${{ secrets.S3_BUCKET }} --delete
```

### Deploy para Docker Hub

```yaml
name: Build and Push Docker Image

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: |
            ${{ secrets.DOCKER_USERNAME }}/myapp:latest
            ${{ secrets.DOCKER_USERNAME }}/myapp:${{ github.sha }}
```

## 🔒 Segurança

### Secrets

- Nunca commite secrets no código
- Use GitHub Secrets para dados sensíveis
- Rotacione secrets regularmente

### Permissões

```yaml
permissions:
  contents: read
  packages: write
  issues: write
  pull-requests: write
```

### GITHUB_TOKEN

```yaml
- name: Create Release
  uses: actions/create-release@v1
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## 📊 Notificações

### Slack

```yaml
- name: Notify Slack
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    channel: "#deployments"
    webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

### Email

```yaml
- name: Send Email
  uses: dawidd6/action-send-mail@v3
  with:
    server_address: smtp.gmail.com
    server_port: 587
    username: ${{ secrets.EMAIL_USERNAME }}
    password: ${{ secrets.EMAIL_PASSWORD }}
    subject: "Deploy completed"
    to: ${{ github.actor }}@example.com
    from: GitHub Actions
    body: "Deploy completed successfully!"
```

## 🛠️ Boas Práticas

### 1. Use Actions Oficiais

```yaml
# ✅ Bom
- uses: actions/checkout@v3

# ❌ Evite
- uses: some-random-user/checkout@v1
```

### 2. Fixe Versões

```yaml
# ✅ Bom
- uses: actions/checkout@v3.1.0

# ❌ Evite
- uses: actions/checkout@v3
```

### 3. Use Cache

```yaml
- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

### 4. Fail Fast

```yaml
- name: Lint
  run: npm run lint
  continue-on-error: false
```

### 5. Cleanup

```yaml
- name: Cleanup
  if: always()
  run: |
    echo "Cleaning up..."
    # Cleanup code here
```

## 🐛 Troubleshooting

### Debug

```yaml
- name: Debug
  run: |
    echo "Debug information:"
    echo "OS: ${{ runner.os }}"
    echo "Node version: $(node --version)"
    echo "NPM version: $(npm --version)"
```

### Logs

```yaml
- name: Show logs
  run: |
    echo "Build logs:"
    cat build.log
```

### Artifacts

```yaml
- name: Upload artifacts
  uses: actions/upload-artifact@v3
  with:
    name: build-files
    path: dist/
```

## 🎯 Exemplo Completo

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: "18"
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Run linting
        run: npm run lint

      - name: Run tests
        run: npm test

      - name: Run security audit
        run: npm audit --audit-level moderate

  build:
    needs: test
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npm run build

      - name: Build Docker image
        run: docker build -t ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }} .

      - name: Login to Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Push Docker image
        run: docker push ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}

  deploy:
    needs: [test, build]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Deploy to production
        run: |
          echo "Deploying to production..."
          # Deploy logic here
```

---

**Dica**: Comece com workflows simples e evolua gradualmente. Use o GitHub Actions Marketplace para encontrar actions úteis!
