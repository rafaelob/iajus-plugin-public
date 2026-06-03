---
name: consultar-legislacao
description: Consulta legislação federal brasileira real (leis, decretos, MPVs, leis complementares, emendas) e localiza dispositivos pelo MCP iaJus, com texto íntegra do Planalto. Acione sempre que o usuário pedir o texto de uma lei, um artigo específico, a redação vigente de um dispositivo, normas por tema/ramo do direito, ou perguntar "qual lei regula Y", "o que diz o art. X da lei Z", "esse dispositivo está em vigor" — em vez de citar de memória. Localiza por nome/termo, conceito, expressão literal ou ramo OJBU. NÃO use para precedentes/acórdãos/súmulas (skill pesquisar-jurisprudencia) nem para doutrina de autor (skill consultar-doutrina).
allowed-tools: mcp__iajus__buscar_fts, mcp__iajus__buscar_semantica, mcp__iajus__buscar_regex, mcp__iajus__buscar_por_ontologia, mcp__iajus__buscar_grafo, mcp__iajus__buscar_hibrida
---

# Consultar legislação federal brasileira (iaJus)

Você tem acesso ao servidor MCP `iajus` para legislação federal brasileira (leis,
decretos, MPVs, leis complementares, emendas). As mesmas modalidades de busca da
jurisprudência cobrem a família `legislacao` — passe `family="legislacao"` para
restringir os resultados à legislação. Use o MCP em vez de citar de memória: **o
texto da fonte é a verdade.**

## Como consultar (modalidades, com `family="legislacao"`)

| Necessidade | Tool | Como |
|---|---|---|
| Localizar a norma/dispositivo por nome ou termo ("CDC", "Lei Geral de Proteção de Dados", "boa-fé objetiva") | `buscar_fts` | `consulta=<termo>`, `family="legislacao"`. Stemming PT, insensível a acento. `phrase=true` para expressão exata. |
| Buscar por conceito/intenção quando o usuário não sabe a redação | `buscar_semantica` | `consulta=<descrição>`, `space=…`. Vetorial. |
| Citação ou redação literal ("art. 5º", "§ 2º do art. 18") | `buscar_regex` | Padrão POSIX com **≥3 caracteres literais** (âncora do índice). |
| "Quais leis tratam de um ramo do direito" | `buscar_por_ontologia` | `l1_code` TPU (ou L2/L3), `family="legislacao"`. Subárvore OJBU. Ex.: `l1_code=1156` (Consumidor → CDC), `14` (Tributário), `899` (Civil → CC). |
| Normas que se citam/alteram entre si | `buscar_grafo` | `normalized_ref`/`entity_id`; tipos incluem `cita_legislacao`, `altera_norma`. |
| Melhor resultado geral | `buscar_hibrida` | Funde os sinais via RRF. |

Cada resultado de legislação traz a estrutura da norma em `family_meta`
(obra/norma, artigo/dispositivo, path, `unit_kind`, `title`, `external_uri`) e o
deep-link canônico em `link_completo` (`url_oficial`). Filtros comuns: `family`,
`orgao_code`, `ano_min`/`ano_max`, `unit_kind` (`artigo`, `secao`, …), `k` (1-100).

## Regras de citação (obrigatório)

- Cite o número da norma, o artigo e a redação como retornada pela fonte. Sempre
  cite o `link_completo` (URL oficial) e **nunca invente** número de lei, redação
  de artigo ou link.
- Se o registro indicar dispositivo **revogado/alterado**, diga isso
  explicitamente — não apresente texto revogado como vigente. Quando a relação
  alteradora aparecer no grafo (`altera_norma`), aponte a norma e a data.
- Preserve a grafia e os diacríticos exatamente como na fonte (UTF-8).
- Se a norma não estiver na base (ex.: norma estadual fora de escopo, ou `total: 0`),
  **diga isso** em vez de improvisar.
- A cobertura é de **legislação federal**; jurisprudência tem skill própria
  (`pesquisar-jurisprudencia`).

## Boas práticas

- Comece pelo `buscar_fts` com `family="legislacao"` e o nome/termo da norma;
  refine com `unit_kind="artigo"` para isolar dispositivos.
- Para "qual lei regula X" sem saber o nome, use `buscar_semantica` ou
  `buscar_por_ontologia` pelo ramo do direito.
- A chave `ik_*` é injetada pelo cliente MCP no header `Authorization: Bearer`
  (Claude Code: `userConfig`/keychain; Cowork: `managedMcpServers`; Codex:
  `bearer_token_env_var`); um 401 indica chave ausente/inválida — peça para revisar
  a chave configurada, nunca cole a chave em chat.
