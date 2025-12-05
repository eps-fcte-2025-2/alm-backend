# ALM - xLSTM Service

Serviço de inferência para Asset Liability Management (ALM) utilizando modelos xLSTM (extended Long Short-Term Memory) para previsão de preços de ativos. O Backend da Plataforma ALM é o serviço central do projeto. Por fim, o trabalho deste repostório foi realizado durante a disciplina EPS (Engenharia de Produto de Software) do semestre 25.2 (UnB - FCTE) e corresponde ao trabalho do Grupo 11, orientado pelo professor Ricardo Matos Chaim.

## Equipe

| Nome                              | Matrícula   | Papel                              |
| --------------------------------- | ----------- | ---------------------------------- |
| Ricardo Matos Chaim               | 39742059187 | Product Owner e Orientador         |
| Thomas Queiroz Souza Alves        | 211062526   | Gerente de projeto e Desenvolvedor |
| André Emanuel Bispo da Silva      | 221007813   | Desenvolvedor                      |
| Artur Henrique Holz Bartz         | 221007869   | Desenvolvedor                      |
| Eduardo Matheus dos Santos Sandes | 221008024   | Desenvolvedor                      |

## Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Instalação e Execução](#instalação-e-execução)
- [Endpoints da API](#endpoints-da-api)
- [Exemplos de Uso](#exemplos-de-uso)
- [Formato dos Dados](#formato-dos-dados)
- [Desenvolvimento](#desenvolvimento)

## Sobre o Projeto

Este serviço fornece uma API REST para realizar inferências com modelos xLSTM treinados. O treinamento de novos modelos deve ser feito através da biblioteca PyxLSTM que foi importada como submódulo desta API. O sistema processa arquivos CSV contendo embeddings de séries temporais financeiras e retorna previsões de preços futuros.

### Características Principais

- **Processamento Assíncrono**: Jobs são enfileirados e processados em background
- **Múltiplos Modelos**: Suporte para carregar e utilizar diferentes modelos treinados
- **Detecção Automática de GPU**: Utiliza CUDA, MPS ou CPU automaticamente
- **API RESTful**: Interface padronizada e documentada
- **Containerização**: Totalmente dockerizado para fácil deployment

## Tecnologias Utilizadas

- **FastAPI**: Framework web moderno e rápido
- **PyTorch**: Framework de deep learning
- **xLSTM**: Arquitetura de rede neural recorrente avançada
- **Docker & Docker Compose**: Containerização
- **Uvicorn**: Servidor ASGI de alta performance
- **Pydantic**: Validação de dados
- **Pandas & NumPy**: Processamento de dados

## Estrutura do Projeto

```
src/
├── app/
│   ├── __init__.py
│   ├── main.py                      # Aplicação principal FastAPI
│   ├── routers/
│   │   └── inference.py             # Endpoints de inferência
│   ├── schemas/
│   │   └── inference.py             # Modelos Pydantic (request/response)
│   └── services/
│       └── inference_service.py     # Lógica de negócio e processamento
├── models/                          # Diretório para modelos PyTorch (.pt)
├── submodules/
│   └── PyxLSTM/                     # Submódulo com implementação xLSTM
├── tests/
│   ├── __init__.py
│   ├── conftest.py
│   └── test_itens.py
├── .env.dev                         # Variáveis de ambiente
├── .gitignore
├── Dockerfile
├── docker-compose.yml
├── pyproject.toml                   # Configurações de formatação
└── requirements.txt                 # Dependências Python
```

## Pré-requisitos

- Docker 20.10+
- Docker Compose 2.0+
- Submódulo PyxLSTM importado
- (Opcional) NVIDIA Docker para suporte GPU

## Instalação e Execução

### 1. Clone o Repositório

```bash
git clone <url-do-repositorio>
cd <nome-do-repositorio>
```

### 2. Importe o Submódulo PyxLSTM

```bash
git submodule update --init --recursive
```

### 3. Prepare os Modelos

Coloque seus modelos PyTorch treinados (arquivos `.pt`) no diretório `src/models/`:

```bash
mkdir -p src/models
cp seu_modelo.pt src/models/
```

**Formato esperado do modelo:**
- Arquivo `.pt` contendo um checkpoint com:
  - `model_state_dict`: pesos do modelo
  - `config`: configuração do modelo (input_size, hidden_size, etc.)

### 4. Configure as Variáveis de Ambiente

O arquivo `.env.dev` já está configurado com valores padrão. Ajuste se necessário:

```env
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=fastapi_db
POSTGRES_HOST=db
POSTGRES_PORT=5432
```

### 5. Inicie os Containers

```bash
cd src
docker-compose up --build
```

O serviço estará disponível em: `http://localhost:8000`

### 6. Verifique o Status

```bash
curl http://localhost:8000/health
```

Resposta esperada:
```json
{"status": "healthy"}
```

## Endpoints da API

### 1. Health Check

**GET** `/health`

Verifica se o serviço está funcionando.

**Resposta:**
```json
{
  "status": "healthy"
}
```

### 2. Listar Modelos Disponíveis

**GET** `/api/v1/models`

Lista todos os modelos PyTorch disponíveis no diretório `models/`.

**Resposta:**
```json
{
  "models": [
    {
      "name": "petr_4_xlstm_embedding_128",
      "path": "models/petr_4_xlstm_embedding_128.pt",
      "config": {
        "input_size": 128,
        "hidden_size": 256,
        "num_layers": 3,
        "num_blocks": 7,
        "output_size": 1,
        "dropout": 0.1,
        "prediction_horizon": 5
      },
      "loaded": false
    }
  ]
}
```

### 3. Submeter Job de Inferência

**POST** `/api/v1/inference/{model_name}`

Submete um arquivo CSV para processamento assíncrono.

**Parâmetros:**
- `model_name` (path): Nome do modelo a ser utilizado
- `file` (form-data): Arquivo CSV com os dados

**Exemplo:**
```bash
curl -X POST "http://localhost:8000/api/v1/inference/petr_4_xlstm_embedding_128" \
     -F "file=@dados.csv"
```

**Resposta (202 Accepted):**
```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "pending",
  "model": "petr_4_xlstm_embedding_128",
  "message": "CSV file 'dados.csv' uploaded and queued for processing. Use job_id to retrieve results."
}
```

### 4. Consultar Resultado

**GET** `/api/v1/result/{job_id}`

Recupera o resultado de um job de inferência.

**Exemplo:**
```bash
curl "http://localhost:8000/api/v1/result/550e8400-e29b-41d4-a716-446655440000"
```

**Resposta (Job Pendente):**
```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "pending",
  "submitted_at": "2024-12-04T10:30:00.000Z",
  "completed_at": null,
  "model": "petr_4_xlstm_embedding_128",
  "result": null,
  "error": null
}
```

**Resposta (Job Completo):**
```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "completed",
  "submitted_at": "2024-12-04T10:30:00.000Z",
  "completed_at": "2024-12-04T10:30:15.000Z",
  "model": "petr_4_xlstm_embedding_128",
  "result": {
    "predicted_prices": [25.34, 25.67, 25.89, 26.12, 26.45],
    "prediction_horizon": 5,
    "current_price": 25.10
  },
  "error": null
}
```

**Resposta (Job com Erro):**
```json
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "failed",
  "submitted_at": "2024-12-04T10:30:00.000Z",
  "completed_at": "2024-12-04T10:30:10.000Z",
  "model": "petr_4_xlstm_embedding_128",
  "result": null,
  "error": "Not enough data points. Need at least 20, got 15"
}
```

## Formato dos Dados

### Arquivo CSV de Entrada

O arquivo CSV deve conter as seguintes colunas:

- `emb_0` até `emb_127`: 128 colunas de embeddings (valores float)
- `last_price`: Preço atual do ativo (valor float)

**Exemplo:**

```csv
emb_0,emb_1,emb_2,...,emb_127,last_price
0.123,0.456,0.789,...,0.321,25.10
0.234,0.567,0.890,...,0.432,25.15
...
```

**Requisitos:**
- Mínimo de 20 linhas (tamanho da sequência)
- Todas as colunas de embedding devem estar presentes
- Valores numéricos válidos
- Codificação UTF-8

## Desenvolvimento

### Executar Testes

```bash
docker-compose exec web pytest
```

### Verificar Cobertura de Testes

```bash
docker-compose exec web pytest --cov=app --cov-report=html
```

### Formatação de Código

O projeto utiliza Black e Ruff para formatação e linting.

```bash
# Formatar código
docker-compose exec web black .

# Verificar linting
docker-compose exec web ruff check .
```

### Logs do Container

```bash
docker-compose logs -f web
```

### Acessar Container

```bash
docker-compose exec web bash
```

### Parar os Serviços

```bash
docker-compose down
```

### Parar e Remover Volumes

```bash
docker-compose down -v
```

## Configurações Avançadas

### Variáveis de Ambiente

| Variável | Descrição | Padrão |
|----------|-----------|--------|
| `POSTGRES_USER` | Usuário do PostgreSQL | `postgres` |
| `POSTGRES_PASSWORD` | Senha do PostgreSQL | `postgres` |
| `POSTGRES_DB` | Nome do banco de dados | `fastapi_db` |
| `POSTGRES_HOST` | Host do PostgreSQL | `db` |
| `POSTGRES_PORT` | Porta do PostgreSQL | `5432` |

### Configuração do Modelo

Os modelos devem ter a seguinte configuração no checkpoint:

```python
config = {
    "input_size": 128,          # Tamanho do embedding de entrada
    "hidden_size": 256,         # Tamanho da camada oculta
    "num_layers": 3,            # Número de camadas xLSTM
    "num_blocks": 7,            # Número de blocos por camada
    "output_size": 1,           # Tamanho da saída
    "dropout": 0.1,             # Taxa de dropout
    "prediction_horizon": 5     # Horizonte de previsão
}
```

## Documentação Interativa

Após iniciar o serviço, acesse a documentação interativa:

- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

## Licença

Este projeto está sob a licença especificada no arquivo LICENSE.

---

**Nota**: Este é um serviço de inferência. Para treinar novos modelos, consulte a documentação do PyxLSTM.
