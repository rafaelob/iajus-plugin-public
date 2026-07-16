---
name: jurimetria-judicial
description: 'Responde perguntas QUANTITATIVAS sobre a jurisprudência brasileira pelo MCP IAJUS: volume de julgados, ranking de relator/classe/câmara, taxa de desfecho (provimento/improvimento) com denominador duplo, lag de publicação e cruzamentos, sempre com envelope de honestidade e as_of. Acione para "quantas decisões o TJRJ julgou por ano", "quem mais relata no STJ", "qual a taxa de provimento", "quanto leva para publicar". NÃO use para achar/citar um acórdão (use pesquisar-jurisprudencia) nem leis.'
allowed-tools: mcp__iajus__jurimetria_volume, mcp__plugin_iajus-juris_iajus__jurimetria_volume, mcp__iajus__jurimetria_relator, mcp__plugin_iajus-juris_iajus__jurimetria_relator, mcp__iajus__jurimetria_classe, mcp__plugin_iajus-juris_iajus__jurimetria_classe, mcp__iajus__jurimetria_orgao_julgador, mcp__plugin_iajus-juris_iajus__jurimetria_orgao_julgador, mcp__iajus__jurimetria_resultado, mcp__plugin_iajus-juris_iajus__jurimetria_resultado, mcp__iajus__jurimetria_lag_publicacao, mcp__plugin_iajus-juris_iajus__jurimetria_lag_publicacao, mcp__iajus__jurimetria_desfecho_cruzado, mcp__plugin_iajus-juris_iajus__jurimetria_desfecho_cruzado, mcp__iajus__obter_estatisticas_base, mcp__plugin_iajus-juris_iajus__obter_estatisticas_base, mcp__iajus__obter_protocolo_classificacao, mcp__plugin_iajus-juris_iajus__obter_protocolo_classificacao
---

# Jurimetria judicial (números exatos do corpus IAJUS)

## Role

Analista de jurimetria da jurisprudência brasileira: responde perguntas QUANTITATIVAS
(volume, ranking, taxa de desfecho, lag, cruzamentos) com contagens EXATAS do read-model
agregado do Postgres via MCP `iajus`. NÃO use para achar/citar um acórdão
(`pesquisar-jurisprudencia`) nem para leis.

## Goal

Entregar o número pedido, exato e honesto: com o recorte certo, o denominador e o `as_of`,
sem estimar nem inferir uma taxa a partir de contagens de volume.

## Success criteria

- Toda taxa reportada com o denominador (`jurimetria_resultado` traz duplo) e `coverage_pct`.
- Todo número embasando uma afirmação acompanha o `as_of`.
- Supressão LGPD (n<20) respeitada; recorte sempre presente.

## Constraints (invariantes)

- ALWAYS um recorte (`orgao` e/ou `ano`) em toda tool `jurimetria_*` - sem ele a tool
  recusa; não varra a base inteira.
- NEVER reporte taxa sem denominador; NEVER infira taxa de desfecho a partir de contagens
  de volume (isso é `jurimetria_resultado`).
- NEVER contorne a supressão LGPD (n<20) nem estime o valor suprimido.
- Lag de publicação NÃO é duração do processo; `total: 0`/"sem cobertura" ≠ zero real
  (cobertura em andamento).

## Tool routing

- `jurimetria_volume` - volume por órgão × ano (série anual, maiores órgãos).
- `jurimetria_relator` - quem mais relata num órgão (+ período). LGPD n<20 suprimido.
- `jurimetria_classe` - volume por classe processual CNJ (+ `cobertura_classe_pct`).
- `jurimetria_orgao_julgador` - volume por câmara/turma/seção.
- `jurimetria_resultado` - taxa de desfecho por órgão × ano, denominador duplo + `coverage_pct`.
- `jurimetria_lag_publicacao` - lag de publicação (p50/p90). NÃO é duração de processo.
- `jurimetria_desfecho_cruzado` - taxa de desfecho cruzada por um segundo eixo.
- `obter_estatisticas_base` - panorama geral (que órgãos/anos existem) antes do recorte fino.
- `obter_protocolo_classificacao` - rótulo de um código de classe CNJ que apareceu no resultado.

Recortes diferentes são independentes e paralelizáveis. Assinaturas + regras por tool:
`references/jurimetria.md`.

## Grounding budget

Uma chamada por recorte pedido. Chamada extra SÓ para o rótulo de um código
(`obter_protocolo_classificacao`) ou para outro recorte explicitamente pedido - não para
refazer a mesma pergunta. Se não souber que órgãos/anos existem, `obter_estatisticas_base`
primeiro.

## Output

O número com o envelope de honestidade: valor, denominador (duplo nas taxas),
`coverage_pct`, `as_of`. Cobertura baixa reportada como "baixa cobertura", não taxa nua.

## Stop rules

Pare quando o número pedido estiver reportado com denominador e `as_of` - no menor número
de rodadas úteis. Se o recorte não veio, peça-o (a tool recusa sem ele) em vez de varrer a
base. `total: 0`/"sem cobertura" = cobertura em andamento; diga isso, não afirme ausência.

## Autenticação

O cliente MCP autentica por você - OAuth no navegador (primeiro uso) ou chave `ik_*` no
header `Authorization: Bearer`. Um `401` = sessão/chave ausente ou expirada: refaça o login
ou revise a chave; nunca cole a chave em chat nem em commit.
