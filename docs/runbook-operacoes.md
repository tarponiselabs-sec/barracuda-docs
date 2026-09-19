# Runbook de Operações — Plataforma Barracuda v1.4

> **Acesso restrito — equipe DevOps/Infra.**
> Este documento descreve o fluxo de operações remotas da plataforma Barracuda.
> Uso interno — não distribuir fora da TarponiseLabs.

## 1. Objetivo

Executar operações de resposta e manutenção nos ativos do cliente via API
(`api.tarponiselabs.myhomelab.diy`). Todas as operações são registradas e
auditadas pela central de defesa (SOC).

## 2. Autenticação — separação por função

A plataforma **segrega credenciais por função** (least privilege):

| Credencial | Uso | Onde está |
|---|---|---|
| `x-admin` | Administração (usuários, serviços, painel) | Chave de administração — não opera nem impacta |
| `x-monitor` | **Operações de impacto** (este runbook) | `MONITOR_APIKEY` — chave do canal de operações |

> ⚠️ **Regra de ouro:** a chave de admin NUNCA deve ser usada no canal de
> operações. O uso indevido gera alerta no SOC (evento de auditoria) e o pedido
> é recusado. As credenciais são segregadas por função — least privilege.

## 3. Endpoint de operação

```
POST /v1/operation
Header: x-monitor: <MONITOR_APIKEY>
Body:   { "target": "<alvo>", "payload_id": "<payload>" }
```

- `target`: identificador do alvo/parceiro (ex.: cliente industrial).
- `payload_id`: o **tipo de operação** a executar (ver §4).

Após o envio, a API responde com um `receipt` (JWT) e um `telemetry.operation`
(id da operação). **Guarde o receipt** — ele é necessário para recuperar o
dossiê da operação:

```
GET /v1/operation/<operation_id>
Header: x-receipt: <receipt>
```

## 4. Catálogo de payloads

| `payload_id` | Descrição | Efeito |
|---|---|---|
| `beacon` | Implanta beacon de comando e controle no alvo | Check-in periódico no C2; mantém canal ativo |
| `kill-switch` | Aborta/congela a operação ativa | Interrompe o payload em execução |
| `wiper` | Destruição controlada de dados alvo | Degradação total (impacto máximo) |
| `stego` | Exfiltração discreta de artefato via canal do C2 | Dado sai sem tráfego novo |

> Payloads adicionais são destravados por side quests (treinamento avançado).

## 5. Alvos (clientes/parceiros homologados)

- `controlworks` — integração SCADA/OT (parceiro industrial)
- `porto-digital` — ambiente do parque tecnológico
- `planta-piloto` — infraestrutura piloto
- `scada-controle` — ambiente de telemetria industrial

## 6. Exemplo (fluxo completo)

```bash
# 1. Dispara operação
curl -X POST https://api.tarponiselabs.myhomelab.diy/v1/operation \
  -H "Content-Type: application/json" \
  -H "x-monitor: mon_barracuda_2026" \
  -d '{"target":"controlworks","payload_id":"beacon"}'
# -> { "success": true, "receipt": "...", "telemetry": { "operation": "op_..." } }

# 2. Recupera o dossiê com o receipt
curl "https://api.tarponiselabs.myhomelab.diy/v1/operation/<op_id>" \
  -H "x-receipt: <receipt>"
# -> dossier com evidências + pergunta de defesa oral
```

## 7. Aviso

- Operações são **simuladas** (ambiente educacional). Nenhuma ação real é executada.
- Sempre declarar a técnica MITRE ATT&CK correspondente no relatório
  (ex.: beacon = T1071; wiper = T1485; stego = T1041).
- Rotação de `MONITOR_APIKEY` prevista antes do lançamento público (v2).

## 8. Documentos de revisão pós-incidente

- Documentos de revisão (post-mortem) de incidentes anteriores são arquivados no storage público
  e **protegidos com a chave composta do engajamento** — as mesmas chaves de acesso do incidente
  (bucket e operações), concatenadas e reduzidas por hash.
- A derivação exata é revelada no dossiê da operação (recuperado com o receipt).
