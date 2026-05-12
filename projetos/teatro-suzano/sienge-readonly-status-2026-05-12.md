# Teatro Suzano — status read-only Sienge (2026-05-12)

## Escopo
Registro compartilhado para qualquer agente consultar o estado validado da API PCS/Sienge sem inventar valores.

## Ambiente validado
- Empresa: **PCS OBRAS E LOCACOES LTDA**
- API pública: `https://api.sienge.com.br/pcsservices/public/api/v1`
- Autenticação validada: **Basic Auth**
- Obra: **TEATRO MUNICÍPIO DE SUZANO**
- `buildingId`: `1354`
- `sheetId`: `1`

## Probe executado
Teste read-only executado em `2026-05-12` via script:
- `/root/.openclaw/workspace/scripts/sienge_readonly_probe.py`

Artefato bruto gerado:
- `/root/.openclaw/workspace/runtime/sienge/probes/sienge-pcs-readonly-probe-20260512T131635Z.json`

## Resultado objetivo
- `GET /building-cost-estimations/1354/sheets` → `200`, `count=1`
- `GET /building-cost-estimations/1354/sheets/1/items` → `200`, `count=196`
- `GET /building-cost-estimations/1354/resources` → `200`, `count=5242`
- `GET /building-cost-estimations/1354/cost-estimate-resources` → `200`, `count=0`

## O que pode ser tratado como verdade hoje
- O endpoint `/items` já traz **quantidade** e **`unitPrice`** em múltiplos itens.
- O probe encontrou:
  - **177 itens com `unitPrice` diferente de zero**
  - **4 itens com `unitPrice = 0`**
- Amostras verificadas:
  - item `8` → `quantity=6.0`, `unitPrice=212.96`
  - item `9` → `quantity=230.4`, `unitPrice=9.835`
  - item `10` → `quantity=158.4`, `unitPrice=10.115`

## O que NÃO pode ser inventado
- `totalValue` apareceu `null` nas amostras verificadas; portanto, não usar esse campo como total consolidado canônico.
- `cost-estimate-resources` continua zerado (`count=0`); portanto, não usar esse endpoint para concluir que o orçamento total está vazio nem para inferir valor consolidado real.
- Se precisar de total de orçamento, calcular explicitamente a partir da base item a item ou buscar endpoint/fonte adicional que devolva esse total de forma fechada.

## Regra operacional curta
Se algum agente falar de valor no Teatro Suzano/Sienge:
1. usar `/items` como fonte factual por item;
2. citar `quantity` + `unitPrice`;
3. não inventar total consolidado enquanto `totalValue` seguir `null` e `/cost-estimate-resources` seguir zerado.
