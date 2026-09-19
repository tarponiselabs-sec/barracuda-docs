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

## Login (console administrativo)

### Endpoint: POST /v1/auth/login
### user: admin
### password: B4rr4cud@2026!
### TODO: esta credencial será girada antes do lançamento — não commitar senhas em texto plano.
## Processo de release

- Desenvolvimento e revisao de mudancas acontecem em branches de staging (ex.: `staging/plantao-15-09`).
- Nada de staging sobe para a `main` sem revisao aprovada — verificar branches antes de copiar arquivos.
- Artefatos temporarios (configs de teste, notas de plantao) pertencem a branch, nunca a `main`.

## Operações de resposta

- Execução de operações de resposta a incidentes via API: `POST /v1/operation` (autenticação por header de monitor — ver projeto Barracuda).
