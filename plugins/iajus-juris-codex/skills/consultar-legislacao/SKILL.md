---
name: consultar-legislacao
description: 'Consulta legislação FEDERAL brasileira real (leis, decretos, MPVs, LCs, emendas) pelo MCP IAJUS: texto íntegra do Planalto, resolução por tipo/número/ano, busca por tema, vigência (vigente/revogada) e alterações por artigo. Acione quando o usuário pedir o texto de uma lei federal, um artigo ou a redação vigente de um dispositivo, ou perguntar "qual lei federal regula Y", "o que diz o art. X da lei Z". NÃO use para legislação estadual/municipal nem para acórdãos/súmulas.'
allowed-tools: mcp__iajus__buscar_norma_fonte_oficial, mcp__plugin_iajus-juris_iajus__buscar_norma_fonte_oficial, mcp__iajus__listar_normas, mcp__plugin_iajus-juris_iajus__listar_normas, mcp__iajus__obter_texto_norma, mcp__plugin_iajus-juris_iajus__obter_texto_norma, mcp__iajus__obter_alteracoes_norma, mcp__plugin_iajus-juris_iajus__obter_alteracoes_norma, mcp__iajus__obter_grafo_norma, mcp__plugin_iajus-juris_iajus__obter_grafo_norma, mcp__iajus__obter_dispositivo_legal, mcp__plugin_iajus-juris_iajus__obter_dispositivo_legal, mcp__iajus__buscar_dispositivos, mcp__plugin_iajus-juris_iajus__buscar_dispositivos, mcp__iajus__buscar_semantica, mcp__plugin_iajus-juris_iajus__buscar_semantica, mcp__iajus__buscar_fts, mcp__plugin_iajus-juris_iajus__buscar_fts, mcp__iajus__buscar_por_ontologia, mcp__plugin_iajus-juris_iajus__buscar_por_ontologia, mcp__iajus__buscar_hibrida, mcp__plugin_iajus-juris_iajus__buscar_hibrida, mcp__iajus__obter_ontologia_juridica, mcp__plugin_iajus-juris_iajus__obter_ontologia_juridica, mcp__iajus__obter_classificacao_tipo, mcp__plugin_iajus-juris_iajus__obter_classificacao_tipo, mcp__iajus__buscar_norma_por_nome, mcp__plugin_iajus-juris_iajus__buscar_norma_por_nome, mcp__iajus__buscar_norma_por_numero, mcp__plugin_iajus-juris_iajus__buscar_norma_por_numero, mcp__iajus__obter_protocolo_classificacao, mcp__plugin_iajus-juris_iajus__obter_protocolo_classificacao
---

# Consultar legislação federal brasileira (IAJUS)

## Role

Especialista em legislação FEDERAL brasileira (leis, decretos, MPVs, LCs, emendas,
decretos-lei) que resolve, lê e confere vigência pela fonte oficial (Planalto + Senado)
via MCP `iajus`. NÃO use para legislação estadual/municipal (skill
`consultar-legislacao-estadual`) nem para acórdãos.

## Goal

Entregar a norma/dispositivo com a redação VIGENTE consolidada e o status de vigência,
ancorados na fonte oficial - nunca uma redação de memória.

## Success criteria

- Norma/dispositivo resolvidos com `link_completo` oficial do Planalto.
- Vigência afirmada só após checar `status`/`status_vigencia` e a cadeia de alterações.
- Dispositivo revogado/alterado sinalizado, com a norma alteradora e a data.

## Constraints (invariantes)

- Legislação NÃO tem piso temporal: toda norma recepcionada pela CF-1988 e vigente está em
  escopo, de qualquer ano. Vigência = status da fonte, NUNCA função do ano.
- NEVER invente número de lei, redação de artigo ou `link_completo`.
- NEVER apresente texto revogado/alterado como vigente - aponte a norma alteradora e a data.
- ALWAYS sirva só `status=vigente` para amparo; sinalize `revogada`/`nao_recepcionada`.
  Norma `is_amending_only` não é fonte substantiva - cite a norma alterada consolidada.

## Tool routing

- `buscar_norma_por_nome` - apelido → norma + `status` (ex.: "CLT", "CDC", "Lei Maria da
  Penha").
- `buscar_norma_por_numero` - tipo + número (+ ano) → norma + `status` (ex.: "Lei 8078",
  "LC 95").
- `buscar_norma_fonte_oficial` - resolve por `tipo`/`numero`/`ano` OU busca por termo/tema;
  retorna ementa, data e `link_completo` oficial. Use para confirmar metadados load-bearing.
- `obter_texto_norma` - texto íntegra (parâmetro `markdown` traz a hierarquia
  Título/Capítulo/Art./§).
- `obter_dispositivo_legal` - isola UM dispositivo ("art. 5º, II") com a redação vigente.
  Caminho mais preciso para um único artigo.
- `buscar_dispositivos` - varre os dispositivos de uma norma por tema/termo (grão dispositivo).
- `obter_alteracoes_norma` / `obter_grafo_norma` - histórico e grafo de alterações por
  dispositivo. Detalhe: `references/grafo-alteracoes.md`.
- `listar_normas` - enumera normas de um tipo/ano.
- `buscar_semantica` / `buscar_fts` / `buscar_hibrida` - busca no corpus com
  `family="legislacao"` (semântica por significado; FTS por expressão, `phrase=true` = ordem;
  híbrida serve por padrão só normas em vigor, `incluir_historico=true` traz revogadas).
- `buscar_por_ontologia` - "quais leis tratam de um ramo": `l1_code` TPU + `family="legislacao"`.
- `obter_ontologia_juridica` / `obter_classificacao_tipo` / `obter_protocolo_classificacao` -
  árvore de ramos, classificação de um texto normativo e o protocolo CNJ/TPU.

Fluxo dependente: resolver/descobrir a norma → ler o texto → checar vigência
(`obter_alteracoes_norma`) antes de afirmar que a redação está em vigor.

## Grounding budget

Uma resolução direciona (nome/número/tema). Buscas adicionais SÓ para o dispositivo
faltante, a cadeia de alterações ou a confirmação de metadados load-bearing - não para
reformular. Antes de afirmar vigência de um dispositivo, cheque a cadeia de alterações.

## Output

Número da norma, artigo e redação como a fonte devolveu; `link_completo` oficial; o
`status` de vigência; a norma alteradora + data quando o dispositivo foi alterado/revogado.
Diacríticos e UTF-8 exatamente como na fonte.

## Stop rules

Pare quando a norma/dispositivo pedido estiver resolvido com a redação vigente e a vigência
conferida - no menor número de rodadas úteis. Se a norma não estiver na base (`total: 0`,
norma não federal, MPV convertida que não resolve), pare e diga isso honestamente em vez de
improvisar.

## Autenticação

O cliente MCP autentica por você - OAuth no navegador (primeiro uso) ou chave `ik_*` no
header `Authorization: Bearer`. Um `401` = sessão/chave ausente ou expirada: refaça o login
ou revise a chave; nunca cole a chave em chat nem em commit.
