# Serviço Backend do Projeto ALM

## Visão Geral do Projeto

O Backend da Plataforma ALM é o serviço central do projeto.

## 🚀 Como Rodar o Serviço Rapidamente (Docker)

Este projeto é totalmente containerizado para garantir uma inicialização rápida e um ambiente consistente.

### 1. Pré-requisitos

Você precisa ter o **Docker** e o **Docker Compose** instalados e em execução em sua máquina.

### 2. Inicialização

No diretório raiz do projeto (onde o arquivo `docker-compose.yml` está):

```bash
# Este comando constrói a imagem e inicia o serviço em segundo plano.
sudo docker compose up -d
```

### 3. Acesso

Após a inicialização (aguarde alguns segundos):

- **Endereço do Serviço:** http://localhost:8000
- **Documentação Interativa da API (Swagger UI):** http://localhost:8000/docs

### 4. Parada do Serviço

Para derrubar e remover os contêineres e a rede do projeto:

```bash
sudo docker compose down
```
