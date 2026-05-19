---
task: Calcular Score Ponderado
responsavel: "@gestor-fechamento"
responsavel_type: agent
atomic_layer: task
elicit: false
Entrada: |
  - kpis: Objeto com os 7 KPIs (output de coletar-kpis)
  - rem_base: Remuneração base do gestor
Saida: |
  - score_ponderado: Número entre 0 e 1
  - valor_variavel: Valor em reais
  - rem_total: Remuneração base + variável
  - detalhamento: Breakdown por KPI
  - churn_aplicado: boolean (se regra de churn zerou o variável)
---

# *calcular-score

Calcula o score ponderado e o valor variável com base nos KPIs coletados.

## Fórmula

```
Score Ponderado = Σ (contribuição_kpi × peso)

Valor Variável = rem_base × 0.16 × score_ponderado

Exceção: Se churn_bloqueante = true → Valor Variável = R$0,00
```

## Pesos e Regras de Contribuição

| KPI               | Peso   | Regra de Contribuição |
|-------------------|--------|-----------------------|
| NPS               | 12,5%  | Escala: ≥70→100%, ≥60→75%, ≥50→50%, ≥40→25%, <40→0% |
| Churn             | 12,5%  | Percentual direto. Se <100% → Valor Variável = R$0,00 (bloqueante) |
| ROI ≥ 1           | 15,0%  | **Threshold 70%:** se % clientes ≥ 70% → usa o percentual; se < 70% → contribuição = 0% |
| CPS < R$2,59      | 20,0%  | **Threshold 70%:** se % clientes ≥ 70% → usa o percentual; se < 70% → contribuição = 0% |
| Google CPL ≤ R$25 | 10,0%  | Percentual direto (proporcional) |
| Contato Cliente   | 15,0%  | **Binário:** apenas 100% conta; qualquer valor < 100% → contribuição = 0% |
| Status Report     | 15,0%  | **Binário:** apenas 100% conta; qualquer valor < 100% → contribuição = 0% |
| **Total**         | **100%** | |

## Lógica de Cálculo por KPI

```
contrib_nps     = nps_pct × 0.125          # nps_pct já convertido pela escala

contrib_churn   = churn_pct × 0.125        # direto

contrib_roi     = (roi_pct >= 0.70 ? roi_pct : 0) × 0.15

contrib_cps     = (cps_pct >= 0.70 ? cps_pct : 0) × 0.20

contrib_gcpl    = gcpl_pct × 0.10          # direto (proporcional)

contrib_contato = (contato_pct == 1.00 ? 1.00 : 0) × 0.15

contrib_sr      = (sr_pct == 1.00 ? 1.00 : 0) × 0.15

score_ponderado = contrib_nps + contrib_churn + contrib_roi + contrib_cps
                + contrib_gcpl + contrib_contato + contrib_sr
```

## Exemplos de Cálculo

### Exemplo 1 — Gestor com bom desempenho

```
NPS 65 → escala 75% × 12,5%           = 9,375%
Churn 100%    × 12,5%                  = 12,500%
ROI 80%  (≥70% ✅) × 15%              = 12,000%
CPS 90%  (≥70% ✅) × 20%              = 18,000%
Google 75%    × 10%                    = 7,500%
Contato 100% (=100% ✅) × 15%         = 15,000%
Status 100%  (=100% ✅) × 15%         = 15,000%
──────────────────────────────────────────────
Score Ponderado = 89,375%

Variável = R$5.500 × 16% × 89,375% = R$786,50
Total    = R$5.500 + R$786,50 = R$6.286,50
```

### Exemplo 2 — Gestor com ROI abaixo do threshold e Contato incompleto

```
NPS 70 → escala 100% × 12,5%          = 12,500%
Churn 100%    × 12,5%                  = 12,500%
ROI 60%  (<70% ❌) → 0% × 15%         = 0,000%
CPS 75%  (≥70% ✅) × 20%              = 15,000%
Google 80%    × 10%                    = 8,000%
Contato 80%  (<100% ❌) → 0% × 15%    = 0,000%
Status 100%  (=100% ✅) × 15%         = 15,000%
──────────────────────────────────────────────
Score Ponderado = 63,000%

Variável = R$4.000 × 16% × 63,000% = R$403,20
Total    = R$4.000 + R$403,20 = R$4.403,20
```

### Exemplo 3 — Churn bloqueante

```
[qualquer KPI]
Churn: perdeu cliente → churn_bloqueante = true

Score calculado: 75% (para registro)
Valor Variável:  R$0,00  ← BLOQUEADO
Rem. Total:      R$4.000 (apenas base)
```

## Output de Exibição

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ SCORE — {gestor} | {mes}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
NPS (12,5%)         {nps_score} → {nps_pct}%  → {contrib}%
Churn (12,5%)       {churn}%                   → {contrib}%
ROI ≥ 1 (15%)       {roi}% [{threshold_tag}]   → {contrib}%
CPS (20%)           {cps}% [{threshold_tag}]   → {contrib}%
Google CPL (10%)    {gcpl}%                    → {contrib}%
Contato (15%)       {cont}% [{binario_tag}]    → {contrib}%
Status Rep. (15%)   {sr}% [{binario_tag}]      → {contrib}%
────────────────────────────────────────────
Score Ponderado:    {score}%
Variável:          R$ {variavel}
Rem. Total:        R$ {total}
```

Tags de exibição:
- `{threshold_tag}`: `≥70% ✅` ou `<70% ❌ zerado`
- `{binario_tag}`: `100% ✅` ou `<100% ❌ zerado`

Se churn_bloqueante:
```
⚠️ CHURN BLOQUEANTE: Gestor perdeu cliente.
   Score calculado: {score}% (apenas para registro)
   Valor Variável:  R$ 0,00
   Remuneração Total: R$ {rem_base} (apenas base)
```
