---
task: Coletar KPIs do Gestor
responsavel: "@gestor-fechamento"
responsavel_type: agent
atomic_layer: task
elicit: true
Entrada: |
  - gestor: Nome do gestor
  - mes: Mês de referência
Saida: |
  - kpis: Objeto com os 7 KPIs preenchidos
  - churn_bloqueante: boolean
Checklist:
  - "[ ] Exibir cabeçalho do gestor"
  - "[ ] Coletar NPS (escala)"
  - "[ ] Coletar Churn (binário, verificar regra bloqueante)"
  - "[ ] Coletar ROI ≥ 1 (X de Y)"
  - "[ ] Coletar CPS < R$2,59 (X de Y)"
  - "[ ] Coletar Google CPL ≤ R$25 (X de Y ou N/A)"
  - "[ ] Coletar Contato com Cliente (binário)"
  - "[ ] Coletar Status Report (binário)"
  - "[ ] Retornar objeto kpis"
---

# *coletar-kpis

Coleta os 7 KPIs de um gestor para o mês de referência, um por um, com validação.

## Formato de entrada para percentuais

Para KPIs do tipo "X de Y clientes":
- Aceitar: `7 de 10`, `7/10`, `70%`, `0.7`
- Calcular percentual automaticamente
- Se gestor não tem clientes nessa categoria: aceitar `N/A` → tratar como 0%

## Validações

| KPI | Validação |
|-----|-----------|
| NPS | Número entre 0 e 100 |
| Churn | s/n, sim/não, yes/no |
| ROI, CPS, Google CPL | "X de Y" onde X ≤ Y, ou percentual direto, ou N/A |
| Contato, Status | s/n, sim/não |

## Output

```yaml
kpis:
  gestor: "Amanda"
  mes: "Maio/2026"
  nps_score: 65          # score bruto
  nps_pct: 0.75          # após aplicar escala
  churn_pct: 1.00
  churn_bloqueante: false
  roi_pct: 0.80
  cps_pct: 0.90
  google_cpl_pct: 0.75
  contato_pct: 1.00
  status_report_pct: 1.00
```
