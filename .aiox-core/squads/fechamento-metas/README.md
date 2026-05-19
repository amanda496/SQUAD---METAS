# Squad: fechamento-metas

Squad para fechamento de metas mensais e semestrais dos gestores de tráfego da Stark.

## O que faz

Conduz o processo completo de fechamento de metas mensais: coleta os KPIs de cada gestor,
aplica as regras de pontuação, calcula o score ponderado, o valor variável e prepara
os dados para preenchimento na planilha.

## Ativar

```
@gestor-fechamento
```

## Comando principal

```
*fechar-mes Maio/2026
```

## KPIs avaliados

| KPI | Peso | Tipo | Regra |
|-----|------|------|-------|
| NPS | 12,5% | Escala | ≥70→100%, ≥60→75%, ≥50→50%, ≥40→25%, <40→0% |
| Churn | 12,5% | Percentual | Se perdeu cliente → Variável = R$0,00 (bloqueante) |
| ROI ≥ 1 | 15% | Threshold | % clientes com ROI ≥ 1. Se < 70% → contribuição = 0% |
| CPS < R$2,59 | 20% | Threshold | % clientes dentro do CPS. Se < 70% → contribuição = 0% |
| Google CPL ≤ R$25 | 10% | Proporcional | % clientes dentro do CPL Google (direto) |
| Contato com cliente | 15% | Binário | 100% = conta integral; qualquer valor < 100% → 0% |
| Status Report | 15% | Binário | 100% = conta integral; qualquer valor < 100% → 0% |

## Fórmula

```
Valor Variável = Rem. Base × 16% × Score Ponderado
```

> **Regra do Churn (inviolável):** Se perdeu qualquer cliente → Variável = R$0,00

## Planilhas

| Planilha | URL | Uso |
|----------|-----|-----|
| Resultados Mensais (KPIs) | https://docs.google.com/spreadsheets/d/1PATiZf_JBoCgs2x8CIVKiIUdOTKpXz3x | Fonte do Churn, ROI, CPS, Google CPL |
| Semestral (Scores) | https://docs.google.com/spreadsheets/d/1UQv-2uo3pGEN4OBLC6roykEfFyjtObV3K30C2WE7ElU | Registro mensal de scores + bônus semestral |

## Gestores

Amanda · Vinicius · Luiz Eduardo · Thiago Manoel · Mateus · Andreyves · Wallison · Gustavo Radler · Richard
