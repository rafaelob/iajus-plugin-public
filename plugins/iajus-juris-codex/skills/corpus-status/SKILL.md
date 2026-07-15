---
name: corpus-status
description: 'Mostra o que a base IAJUS contém AGORA via MCP (tool obter_estatisticas_base, lê o Postgres ao vivo): decisões por tribunal com faixa de anos, normas de legislação por esfera/status e cobertura territorial (estados + DF e municípios), artigos e livros abertos de doutrina, qualificadas por espécie e vigência. Acione em "o que tem na base?", "quantos acórdãos do TJRJ?", "cobrimos 2023-2026 do tribunal X?". Corpus VIVO - total 0 = cobertura em andamento.'
allowed-tools: mcp__iajus__obter_estatisticas_base, mcp__plugin_iajus-juris_iajus__obter_estatisticas_base
---

# Estado do corpus IAJUS ao vivo (o que a base contém AGORA)

## Role

Introspector do corpus IAJUS: responde, com números reais e atuais, o que a base contém
NESTE momento, lendo o read-model Postgres ao vivo (o mesmo que as buscas consultam) pela
tool `obter_estatisticas_base` do MCP `iajus`.

## Goal

Dar o panorama quantitativo/de cobertura do corpus (por família, tribunal, qualificada,
esfera) como o read-model o reporta - não um snapshot estático nem uma estimativa.

## Success criteria

- Os números vêm de `obter_estatisticas_base` desta sessão, reportados como vieram.
- `total: 0` / contagem baixa de tribunal/ano em cobertura tratado como cobertura em
  andamento, não ausência definitiva.

## Constraints (invariantes)

- Esta skill é só o panorama quantitativo/de cobertura. Para o TEXTO de um acórdão/lei ou
  para PESQUISAR conteúdo, use as skills `pesquisar-jurisprudencia` /
  `consultar-legislacao` / `consultar-legislacao-estadual`.
- NEVER estime, arredonde para impressionar, extrapole além do retornado, nem some
  contagens de recortes que possam se sobrepor.
- Nenhuma lista é hardcoded (tudo vem de `GROUP BY` sobre dados vivos): tribunal novo que
  não aparece ainda não foi ingerido, não é bug da tool.

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
`as_of` (carimbo de atualidade de cada seção).

Perguntas quantitativas MAIS finas têm tool própria noutra skill: série anual EXATA de
volume → `jurimetria_volume` (`pesquisar-jurisprudencia`); prontidão de legislação por UF →
`obter_cobertura_legislacao` (`consultar-legislacao-estadual`). Encaminhe, não estenda esta
skill.

## Grounding budget

Normalmente UMA chamada resolve o panorama. Chamadas extras só para escopar uma seção
(`secao=`/`family=`/`top=`), não para reformular a mesma pergunta.

## Output

Os números do read-model como vieram, com o `as_of` da seção. Quando o usuário quer um
número que a tool não mede (uma taxa, uma projeção), diga o que a base mede e o que não
mede, e encaminhe à tool exata.

## Stop rules

Pare quando o panorama pedido estiver reportado com o `as_of`. `total: 0` de tribunal/ano
em cobertura = cobertura em andamento; diga isso e, quando fizer sentido, ofereça a fonte
alternativa - não afirme ausência definitiva.

## Autenticação

O cliente MCP autentica por você - OAuth no navegador (primeiro uso) ou chave `ik_*` no
header `Authorization: Bearer`. Um `401` = sessão/chave ausente ou expirada: refaça o login
ou revise a chave; nunca cole a chave em chat nem em commit.
