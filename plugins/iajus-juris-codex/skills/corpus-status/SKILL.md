---
name: corpus-status
description: 'Mostra o que o read-model do corpus IAJUS reporta via MCP, sempre com o as_of de cada seção: decisões por tribunal e faixa de anos, legislação por esfera/status e território, doutrina e qualificadas por espécie/vigência. Acione em "o que tem na base?", "quantos acórdãos do TJRJ?", "cobrimos 2023-2026 do tribunal X?". Não estima nem extrapola contagens.'
allowed-tools: mcp__iajus__obter_estatisticas_base, mcp__plugin_iajus-juris_iajus__obter_estatisticas_base
---

# Estado do corpus IAJUS conforme o `as_of`

## Role

Introspector do corpus IAJUS: responde o que o read-model Postgres reporta pela tool
`obter_estatisticas_base` do MCP `iajus`, preservando o `as_of` de cada seção.

## Goal

Dar o panorama quantitativo/de cobertura do corpus por família, tribunal, qualificada e
esfera como o read-model o reporta, sem estimar ou extrapolar.

## Success criteria

- Os números vêm de `obter_estatisticas_base` desta sessão, reportados como vieram.
- `total: 0` / contagem baixa descrito apenas como o recorte medido no respectivo `as_of`.

## Constraints (invariantes)

- Esta skill é só o panorama quantitativo/de cobertura. Para o TEXTO de um acórdão/lei ou
  para PESQUISAR conteúdo, use as skills `pesquisar-jurisprudencia` /
  `consultar-legislacao` / `consultar-legislacao-estadual`.
- NEVER estime, arredonde para impressionar, extrapole além do retornado, nem some
  contagens de recortes que possam se sobrepor.
- NEVER inferir ausência na fonte, ingestão pendente ou cobertura incompleta só a partir
  de `total: 0`.
- Seções podem ter frescores diferentes: timestamp = rollup; `"live"` = agregação na chamada.

## Tool routing

`obter_estatisticas_base` (read-only, JSON). Argumentos:

- `secao` - `tudo` (padrão) | `familias` | `orgaos` | `qualificadas` | `legislacao`.
  Comece por `tudo`. (`secao="orgaos"` = lista de tribunais.)
- `family` - `jurisprudencia` | `doutrina` | `legislacao`: escopa só a lista de tribunais.
- `top` - inteiro 1-500 (padrão 60): limita a lista de tribunais (ordem por volume).

Chaves do payload: `familias` (decisões/tribunais/normas/artigos_livros_doutrina, com
`ano_min`/`ano_max`); `tribunais` (por tribunal: `decisoes`, `ano_min`/`ano_max`);
`qualificadas` (por `especie`: `quantidade`, `vigentes`, `canceladas`, `tribunais`);
`legislacao` (por esfera, por status, e `cobertura_territorial` União/`estados_df`/`municipios`);
`as_of` (timestamp do rollup ou `"live"` quando a seção foi agregada na chamada).

Volume por tribunal e faixa de anos coberta saem da própria `obter_estatisticas_base`
(`secao="orgaos"`). Prontidão de legislação por UF tem tool própria noutra skill →
`obter_cobertura_legislacao` (`consultar-legislacao-estadual`). Encaminhe, não estenda esta
skill.

## Grounding budget

Normalmente UMA chamada resolve o panorama. Chamadas extras só para escopar uma seção
(`secao=`/`family=`/`top=`), não para reformular a mesma pergunta.

## Output

Os números do read-model como vieram, com o `as_of` de cada seção. Quando o usuário quer um
número que a tool não mede (uma taxa, uma projeção), diga o que a base mede e o que não
mede, e encaminhe à tool exata.

## Stop rules

Pare quando o panorama pedido estiver reportado com o `as_of`. `total: 0` não diagnostica
a causa; sem outro campo autoritativo, não afirme ausência, ingestão pendente ou cobertura
incompleta.

## Autenticação

O cliente MCP autentica por você - OAuth no navegador (primeiro uso) ou chave `ik_*` no
header `Authorization: Bearer`. Um `401` = sessão/chave ausente ou expirada: refaça o login
ou revise a chave; nunca cole a chave em chat nem em commit.
