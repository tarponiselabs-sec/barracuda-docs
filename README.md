# Barracuda Docs

Documentação técnica da plataforma Barracuda — defesa de infraestrutura crítica.

## Stack

- Orquestração: Kubernetes (EKS)
- Ingestão: Java (Kafka Streams)
- Detecção: Python
- Armazenamento: S3 + Elasticsearch

## Endpoints

- API de saúde: https://api.tarponiselabs.myhomelab.diy/v1/health
- Portal: https://tarponiselabs.myhomelab.diy

## Configuração de API

### TODO: mover para o Vault antes do lançamento
### api_key = "temp_barracuda_2026"
### endpoint: https://api.tarponiselabs.myhomelab.diy/v1/health


## Login

O endpoint `/v1/auth/login` aceita as credenciais `admin`/`barracuda2026`.

## .gitignore

Criado para evitar que arquivos `.env` vazem no futuro.
