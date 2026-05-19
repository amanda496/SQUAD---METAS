# Guia de Manutenção — Squad Fechamento de Metas

Use este guia sempre que algo mudar nas planilhas ou nas regras de negócio.
Cada seção indica exatamente quais arquivos atualizar.

---

## 1. Mudou o salário de um gestor

**Arquivo:** `data/gestores.yaml`

```yaml
- nome: Amanda
  rem_base: 5500.00      # ← alterar aqui
  bonus_semestral: 1080.00
```

---

## 2. Entrou um novo gestor

**Arquivo:** `data/gestores.yaml` — adicionar bloco:

```yaml
- nome: Nome do Gestor
  cargo: Gestor de Tráfego [Nível]
  rem_base: 0000.00
  bonus_semestral: 000.00
```

**Arquivo:** `README.md` — adicionar nome na seção `## Gestores`

---

## 3. Saiu um gestor

**Arquivo:** `data/gestores.yaml` — remover o bloco do gestor

**Arquivo:** `README.md` — remover nome da seção `## Gestores`

---

## 4. Mudou o threshold do ROI ou CPS

Exemplo: ROI passa de 70% para 80%.

| Arquivo | O que alterar |
|---------|--------------|
| `data/kpis.yaml` | Campo `threshold` e `threshold_rule` do KPI |
| `tasks/calcular-score.md` | Pseudocódigo: `roi_pct >= 0.70` → novo valor |
| `tasks/calcular-score.md` | Tabela de pesos e exemplos |
| `tasks/fechar-mes.md` | Fórmula IF() documentada na seção Passo 3 |
| `agents/gestor-fechamento.md` | Seção "Regras que o agente conhece" |
| `README.md` | Coluna "Regra" na tabela de KPIs |

> **Planilha Google Sheets:** atualizar também a fórmula em J3:J11 da aba do mês.
> Exemplo novo threshold 80%: `IF(E3>=0.8,E3,0)*0.15`

---

## 5. Mudou o valor-limite do CPS (R$2,59)

| Arquivo | O que alterar |
|---------|--------------|
| `data/kpis.yaml` | Campo `meta` do KPI CPS |
| `README.md` | Nome do KPI na tabela (`CPS < R$X,XX`) |
| `agents/gestor-fechamento.md` | Nome do KPI e regra |
| `tasks/calcular-score.md` | Tabela de pesos (nome do KPI) |
| `tasks/fechar-mes.md` | Referências ao valor limite |

> O cálculo em si não muda — só o critério de coleta na planilha.

---

## 6. Mudou o valor-limite do Google CPL (R$25)

| Arquivo | O que alterar |
|---------|--------------|
| `data/kpis.yaml` | Campo `meta` do KPI Google CPL |
| `README.md` | Nome do KPI na tabela |
| `agents/gestor-fechamento.md` | Nome do KPI |
| `tasks/calcular-score.md` | Tabela de pesos (nome do KPI) |

---

## 7. Mudou a escala do NPS

Exemplo: ≥70→100%, ≥60→75% etc. passa a ter outros valores.

| Arquivo | O que alterar |
|---------|--------------|
| `data/kpis.yaml` | Campo `escala` do KPI NPS |
| `tasks/calcular-score.md` | Tabela de pesos, pseudocódigo e exemplos |
| `tasks/fechar-mes.md` | Seção de coleta do NPS |
| `agents/gestor-fechamento.md` | Regra do NPS |
| `README.md` | Coluna "Regra" do NPS |

---

## 8. Mudou o peso de um KPI

Exemplo: NPS passa de 12,5% para 10%.

> ⚠️ Os pesos devem somar 100%. Verifique antes de alterar.

| Arquivo | O que alterar |
|---------|--------------|
| `data/kpis.yaml` | Campo `peso` do KPI |
| `tasks/calcular-score.md` | Tabela de pesos + pseudocódigo (ex: `× 0.125` → `× 0.10`) + exemplos |
| `tasks/fechar-mes.md` | Fórmula IF() e referências ao peso |
| `agents/gestor-fechamento.md` | Seção "Regras que o agente conhece" |
| `README.md` | Tabela de KPIs, coluna Peso |

> **Planilha Google Sheets:** atualizar o coeficiente correspondente na fórmula J3:J11.

---

## 9. Mudou o percentual do variável (atualmente 16%)

| Arquivo | O que alterar |
|---------|--------------|
| `tasks/calcular-score.md` | Fórmula: `rem_base × 0.16 × score` |
| `tasks/fechar-mes.md` | Fórmula e exemplo numérico |
| `agents/gestor-fechamento.md` | Fórmula na seção "Regras" |
| `README.md` | Seção `## Fórmula` |

---

## 10. Mudou a URL de uma planilha

| Arquivo | O que alterar |
|---------|--------------|
| `README.md` | Tabela `## Planilhas` |
| `agents/gestor-fechamento.md` | Seção "Regras que o agente conhece" |

---

## 11. Mudou o bônus semestral de um gestor

**Arquivo:** `data/gestores.yaml` — campo `bonus_semestral`

---

## Checklist rápido após qualquer alteração

- [ ] `data/kpis.yaml` atualizado (se regra/peso/meta mudou)
- [ ] `data/gestores.yaml` atualizado (se gestor/salário mudou)
- [ ] `tasks/calcular-score.md` consistente com kpis.yaml
- [ ] `tasks/fechar-mes.md` consistente com calcular-score.md
- [ ] `agents/gestor-fechamento.md` consistente com as regras
- [ ] `README.md` reflete o estado atual
- [ ] Planilha Google Sheets atualizada (fórmula J3:J11 da aba do mês)
- [ ] Commit feito com mensagem descritiva

---

## Arquivos do squad e o que cada um controla

| Arquivo | Controla |
|---------|----------|
| `data/gestores.yaml` | Salários, bônus e cargos |
| `data/kpis.yaml` | Definição oficial dos KPIs, pesos e regras |
| `tasks/calcular-score.md` | Lógica de cálculo do score |
| `tasks/fechar-mes.md` | Workflow interativo mensal |
| `tasks/coletar-kpis.md` | Como coletar cada KPI do gestor |
| `agents/gestor-fechamento.md` | Persona e comandos do agente |
| `README.md` | Visão geral do squad |
