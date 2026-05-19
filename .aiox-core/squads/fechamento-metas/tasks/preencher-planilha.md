---
task: Preencher Planilha de Metas
responsavel: "@gestor-fechamento"
responsavel_type: agent
atomic_layer: task
elicit: true
Entrada: |
  - resultados: Array com scores e valores de todos os gestores
  - mes: Mês de referência
Saida: |
  - confirmacao: Dados prontos para preenchimento
  - instrucoes: Guia de onde preencher na planilha
---

# *preencher-planilha

Gera as instruções de preenchimento e/ou preenche automaticamente a planilha Google Sheets
com os resultados do fechamento do mês.

## Planilha

URL: https://docs.google.com/spreadsheets/d/1UQv-2uo3pGEN4OBLC6roykEfFyjtObV3K30C2WE7ElU

## Modos de operação

### Modo 1: Preenchimento automático (via MCP Google Drive)
O agente tenta preencher diretamente via API.

### Modo 2: Guia manual (fallback)
Se não for possível preencher automaticamente, exibe os valores prontos para cópia.

## Output — Guia de Preenchimento Manual

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 VALORES PARA PREENCHER — {mes}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Planilha: [Fechamento de Metas]
Aba: [mês correspondente]

Gestor         | NPS    | Churn  | ROI    | CPS    | G.CPL  | Contato | Status | Score
─────────────────────────────────────────────────────────────────────────────────────
Amanda         | 75%    | 100%   | 80%    | 90%    | 75%    | 100%    | 100%   | 89,4%
Vinicius       | ...
...

VALORES VARIÁVEIS:
Amanda:        R$ 786,50
Vinicius:      R$ ...
...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Estrutura da planilha (referência)

A planilha segue o formato:

| Coluna | Conteúdo |
|--------|----------|
| A | Nome do Gestor |
| B | Rem. Base |
| C | NPS (%) |
| D | Churn (%) |
| E | ROI ≥ 1 (%) |
| F | CPS < R$2,59 (%) |
| G | Google CPL (%) |
| H | Contato Cliente (%) |
| I | Status Report (%) |
| J | Score Meta % |
| K | Valor Variável |
| L | Rem. Total |
| M | Obs. |

## Bônus Semestral

Após preencher o mês, atualizar também a aba de bônus semestral com o score mensal calculado.
O score do mês vai para a coluna do mês correspondente (Dezembro → Maio).
