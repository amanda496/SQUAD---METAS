---
task: Fechar Metas do Mês
responsavel: "@gestor-fechamento"
responsavel_type: agent
atomic_layer: task
elicit: true
Entrada: |
  - mes: Mês de referência (ex: Maio/2026)
  - gestores: Lista de gestores a fechar (default: todos)
Saida: |
  - resumo: Tabela consolidada com score e valor variável de cada gestor
  - planilha_atualizada: Confirmação de preenchimento
Checklist:
  - "[ ] Confirmar mês de referência"
  - "[ ] Para cada gestor: coletar KPIs via *coletar-kpis"
  - "[ ] Verificar regra do churn (bloqueante)"
  - "[ ] Calcular score ponderado via *calcular-score"
  - "[ ] Calcular valor variável (Rem. Base × 16% × Score)"
  - "[ ] Exibir resumo consolidado"
  - "[ ] Preencher planilha via *preencher-planilha"
---

# *fechar-mes

Conduz o fechamento completo de metas mensais para todos os gestores de tráfego.

## Uso

```
*fechar-mes
# → Pergunta o mês e fecha todos os gestores

*fechar-mes Maio/2026
# → Fecha diretamente o mês informado

*fechar-gestor Amanda Maio/2026
# → Fecha apenas um gestor
```

## Fluxo Interativo

### Passo 1 — Confirmar mês

```
📅 Qual mês está fechando?
Ex: Maio/2026
>
```

### Passo 2 — Para cada gestor, coletar KPIs

Executa `*coletar-kpis` para cada gestor na sequência:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 GESTOR: Amanda (Sênior II) | Rem. Base: R$5.500,00
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[1/7] NPS — Score NPS deste mês? (0-100)
> 65

[2/7] Churn — Perdeu algum cliente? (s/n)
> n  → ✅ Churn 100%

[3/7] ROI ≥ 1 — Quantos clientes de cirurgia plástica atingiram ROI ≥ 1?
      Formato: X de Y (ex: 7 de 10)
> 8 de 10  → 80%

[4/7] CPS < R$2,59 — Quantos clientes com CPS abaixo da meta?
      Formato: X de Y
> 9 de 10  → 90%

[5/7] Google CPL ≤ R$25 — Quantos clientes dentro da meta de CPL Google?
      Formato: X de Y (deixe em branco se sem clientes Google)
> 6 de 8  → 75%

[6/7] Contato com cliente — Fez contato com todos os clientes? (s/n)
> s  → ✅ 100%

[7/7] Status Report — Entregou status report de todos os clientes? (s/n)
> s  → ✅ 100%
```

### Passo 3 — Calcular e exibir resultado do gestor

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ RESULTADO — Amanda | Maio/2026
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NPS (12,5%)         75%   → 0,094   [≥60 = 75%]
Churn (12,5%)      100%   → 0,125
ROI ≥ 1 (15%)       80%   → 0,120   [≥70% → conta; <70% → zera]
CPS (20%)           90%   → 0,180   [≥70% → conta; <70% → zera]
Google CPL (10%)    75%   → 0,075
Contato (15%)      100%   → 0,150   [100% → conta; <100% → zera]
Status Rep. (15%)  100%   → 0,150   [100% → conta; <100% → zera]
─────────────────────────────
Score Ponderado:   89,4%
Valor Variável:    R$5.500 × 16% × 89,4% = R$787,68
Remuneração Total: R$6.287,68
```

### Passo 4 — Resumo consolidado

Após fechar todos os gestores, exibir tabela completa:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 FECHAMENTO MAIO/2026 — STARK GESTORES DE TRÁFEGO
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Gestor          | Score  | Variável   | Total
─────────────────────────────────────────────────────
Amanda          | 89,4%  | R$ 787,68  | R$ 6.287,68
Vinicius        | ...    | ...        | ...
...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Deseja preencher a planilha agora? (s/n)
```

## Fontes dos KPIs

| KPI | Fonte | Como buscar |
|-----|-------|-------------|
| NPS | Manual | Perguntar à Amanda |
| Churn | Google Sheets | Aba do mês — coluna "% Meta Churn Batida" (tabela META DE CHURN) |
| ROI ≥ 1 | Google Sheets | Aba do mês — coluna "% Meta ROI Batida" |
| CPS < R$2,59 | Google Sheets | Aba do mês — coluna "% Meta CPS Batida" |
| Google CPL | Google Sheets | Aba do mês — coluna "% Meta CPA Batida" |
| Contato Cliente | ClickUp | Lista "Relatórios dos Clientes" — tasks "Relatório ao Cliente Tráfego Pago \| [Cliente]" com status **concluído** |
| Status Report | ClickUp | Lista "Status Report" — task "Status Report \| [Nome Gestor]" com status **concluído** |

**Planilha de Resultados:** `https://docs.google.com/spreadsheets/d/1PATiZf_JBoCgs2x8CIVKiIUdOTKpXz3x`
**ClickUp Relatórios:** `https://app.clickup.com/36998608/v/l/6-901307744658-1`
**ClickUp Status Report:** `https://app.clickup.com/36998608/v/l/6-901300668557-1`

## Regras de Negócio

### Churn Bloqueante
Se o gestor perdeu qualquer cliente (`churn < 100%`):
- Score é calculado normalmente (para registro)
- **Valor Variável = R$0,00** (regra inviolável)
- Exibir aviso: `⚠️ CHURN: Variável zerada por perda de cliente`

### NPS em escala (não binário)
| Score NPS | % do critério |
|-----------|---------------|
| ≥ 70      | 100%          |
| ≥ 60      | 75%           |
| ≥ 50      | 50%           |
| ≥ 40      | 25%           |
| < 40      | 0%            |

### Campos binários
Contato com cliente e Status Report: **apenas 100% conta**.
- 100% (todos feitos) → contribuição plena (peso integral)
- Qualquer valor < 100% → **contribuição = 0%** (zera o KPI)

### ROI e CPS — Threshold 70%
- Percentual ≥ 70% → contribuição plena (percentual × peso)
- Percentual < 70% → **contribuição = 0%** (zera o KPI)

### Campos percentuais (Google CPL)
Inserir como "X de Y" e o agente calcula o percentual.
O percentual informado é usado diretamente (ex: 75% × peso).
