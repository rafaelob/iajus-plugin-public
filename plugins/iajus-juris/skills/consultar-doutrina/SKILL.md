---
name: consultar-doutrina
description: Consulta e cita DOUTRINA jurídica brasileira real (livros, manuais e tratados de autores) pelo MCP iaJus, com a referência bibliográfica ABNT da obra. Acione sempre que o usuário quiser o ensinamento doutrinário — o que a DOUTRINA/um DOUTRINADOR/AUTOR ensina ou defende sobre um instituto ou conceito, "o que Hely Lopes/Pacelli/a doutrina diz sobre X", "qual livro trata de Y", citar um livro/manual/tratado, ou a referência bibliográfica/ABNT de uma obra do acervo — em vez de um precedente judicial ou o texto de uma norma. NÃO use para precedentes/acórdãos/súmulas (skill pesquisar-jurisprudencia) nem para o texto de leis (skill consultar-legislacao).
allowed-tools: mcp__iajus__buscar_por_ontologia, mcp__iajus__buscar_fts, mcp__iajus__buscar_regex, mcp__iajus__buscar_semantica, mcp__iajus__buscar_hibrida
---

# Consultar doutrina jurídica brasileira (iaJus)

Você tem acesso ao servidor MCP `iajus`, que indexa **doutrina** jurídica brasileira
— livros, manuais e tratados de autores, decompostos na árvore documental real da
obra (livro → parte → título → capítulo → seção, até 6 níveis) e classificados pela
ontologia OJBU alinhada ao CNJ/TPU (21 ramos L1 → sub-áreas L2/L3). **Use o MCP em
vez de inventar o que "a doutrina diz".**

A doutrina vive na família `doutrina` do acervo unificado (`search_units`), ao lado de
`jurisprudencia` e `legislacao`. As mesmas tools de busca alcançam as três famílias; o
que muda é a intenção: **doutrina = ensinamento de autor**, não precedente nem norma.

## Quando NÃO usar esta skill

- Precedente, acórdão, súmula, repercussão geral, número de processo → use
  **pesquisar-jurisprudencia**.
- Texto de lei, artigo de código, decreto, vigência de norma → use
  **consultar-legislacao**.
- Esta skill é para o **conteúdo doutrinário** (o que o autor/manual ensina) e para a
  **referência bibliográfica** das obras do acervo.

## Escolha da modalidade

Todas as tools retornam o mesmo envelope uniforme (`{ modalidade, total, resultados:[…] }`),
são read-only, e aceitam o filtro de família. **Restrinja a `family="doutrina"`** (ou o
parâmetro de família equivalente da tool) para não misturar acórdãos/leis nos resultados.

| Pergunta do usuário | Tool | Por quê |
|---|---|---|
| Conceito/instituto ("o que a doutrina ensina sobre posse vs propriedade") | `buscar_semantica` | Vetorial/densa — casa por significado. **Padrão para perguntas conceituais.** Requer o índice denso da família doutrina (premium gemini-2). Se a família ainda não estiver densa, caia para `buscar_por_ontologia` + `buscar_fts`. |
| Melhor resultado geral | `buscar_hibrida` | Funde semântica + FTS + trigram + ontologia via RRF. |
| Termo técnico literal ("usucapião extraordinária", "tipicidade conglobante") | `buscar_fts` | Full-text pt_unaccent, stemming PT, insensível a acento; `phrase=true` exige a ordem. |
| Forma literal / nº de artigo citado no livro ("art. 1.228", "Súmula 7") | `buscar_regex` | Regex POSIX; **exija ≥3 caracteres literais** (âncora do índice). |
| "Toda a doutrina de um ramo" ("livros de Direito Penal") | `buscar_por_ontologia` | Subárvore ltree por `l1_code` TPU; combine com `family="doutrina"`. Ex.: `l1_code=287` (Penal), `899` (Civil), `9985` (Administrativo). |

Notas de uso:
- **Filtro de órgão NÃO se aplica à doutrina** (livro não tem tribunal). Use o filtro de
  **família** (`family="doutrina"`); `buscar_por_ontologia`/`buscar_fts`/`buscar_regex`
  filtram por família/`l1_code`, não por `tribunal`.
- `buscar_semantica`/`buscar_hibrida` aceitam `space` (`premium` = gemini-2 hierárquico,
  o tier da doutrina) e `k` (1-100). Prefira `premium` para doutrina — o embedding é
  hierárquico (seção + breadcrumb), pensado para texto de manual.
- Se `buscar_semantica` vier fraco, **escale para `buscar_hibrida`** (mesma consulta)
  antes de desistir. Se a família densa ainda não estiver pronta, use `buscar_por_ontologia`
  (recorte por ramo) + `buscar_fts` (termo) como caminho léxico.

## Ontologia OJBU (ramos reais — use estes códigos)

`buscar_por_ontologia` recebe `l1_code` (código TPU do ramo, inteiro) + `family="doutrina"`.
Os ramos com obras no acervo (entre os 21 L1):

| `l1_code` | Ramo | | `l1_code` | Ramo |
|---|---|---|---|---|
| 14 | Tributário | | 1209 | Processual Penal |
| 287 | Penal | | 7724 | Empresarial |
| 899 | Civil | | 8826 | Processual Civil e do Trabalho |
| 9985 | Administrativo e Dir. Público | | | |

(Os demais ramos L1 e os temas transversais existem na ontologia, mas o acervo atual de
doutrina concentra-se nesses; obras meta/transversais — teoria geral, prática — podem não
ter um ramo único e aparecem sem classificação L1, honestamente, em vez de um ramo forçado.)

## Como citar (obrigatório)

- Cite **autor + título da obra**. Quando o registro trouxer `referencia_abnt`, use-a
  como a referência bibliográfica (formato ABNT NBR 6023). **Atenção honesta:** muitas
  obras do acervo têm a referência **incompleta** — quando `referencia_completa` for
  falso, a string traz marcadores ABNT `[S. l.]` (local não informado), `[s. n.]`
  (editora não informada) e/ou `[20--?]` (ano incerto). **Não invente** editora, local ou
  ano para "completar" — apresente a referência como está e sinalize que a fonte não
  informou esses dados.
- Cite o capítulo/seção (`breadcrumb`/`section_title`) e a página (`page_start`) quando
  presentes — a doutrina é citada por trecho, não pela obra inteira.
- Resuma o trecho retornado (`snippet`) em 1-2 frases; ofereça abrir o trecho pelo
  `external_uri` quando houver.
- Se a busca **não** retornar trecho relevante (`total: 0` ou hits fracos), **diga isso
  honestamente** — não preencha a lacuna com uma "posição da doutrina" fabricada nem com
  um autor que não está no acervo.

## Boas práticas

- Comece restrito (conceito + ramo via `l1_code` + `family="doutrina"`) e amplie só se vier vazio.
- Distinga DOUTRINA de JURISPRUDÊNCIA na resposta: o que o autor *defende* (doutrina) não
  é o que o tribunal *decidiu* (jurisprudência). Se o usuário quer os dois, combine esta
  skill com **pesquisar-jurisprudencia** e deixe claro o que é cada um.
- Preserve diacríticos e UTF-8 exatamente como na fonte.
- A chave `ik_*` é injetada pelo cliente MCP no header `Authorization: Bearer`
  (Claude Code: `userConfig`/keychain; Cowork: `managedMcpServers`; Codex:
  `bearer_token_env_var`). Se uma tool responder 401, avise o usuário para revisar a
  chave configurada — nunca cole a chave em chat nem em commit.
