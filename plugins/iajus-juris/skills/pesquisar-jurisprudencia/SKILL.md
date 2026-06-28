---
name: pesquisar-jurisprudencia
description: Pesquisa e cita jurisprudência brasileira real (STF, STJ, TST, TCU, TSE, STM, TJs, TRFs, TRTs, TREs) pelo MCP iaJus, com as 8 modalidades de busca (semântica, híbrida, FTS, regex, CNJ, ontologia OJBU, grafo de citações, jurimetria) e a leitura de precedentes qualificados (súmulas, repercussão geral, IRDR, IRR, IAC). Acione sempre que o usuário pedir precedente, acórdão, súmula, repercussão geral, tema repetitivo, número de processo CNJ, estatística de julgados ou o grafo de citações de uma súmula/tema — ou perguntar "o que os tribunais decidiram sobre X", "tem precedente sobre Y", "qual o entendimento atual", "quantos acórdãos sobre Z" — em vez de responder de memória. NÃO use para o texto de leis/decretos (skills consultar-legislacao e consultar-legislacao-estadual).
allowed-tools: mcp__iajus__buscar_semantica, mcp__plugin_iajus-juris_iajus__buscar_semantica, mcp__iajus__buscar_hibrida, mcp__plugin_iajus-juris_iajus__buscar_hibrida, mcp__iajus__buscar_fts, mcp__plugin_iajus-juris_iajus__buscar_fts, mcp__iajus__buscar_regex, mcp__plugin_iajus-juris_iajus__buscar_regex, mcp__iajus__buscar_por_cnj, mcp__plugin_iajus-juris_iajus__buscar_por_cnj, mcp__iajus__buscar_por_ontologia, mcp__plugin_iajus-juris_iajus__buscar_por_ontologia, mcp__iajus__buscar_grafo, mcp__plugin_iajus-juris_iajus__buscar_grafo, mcp__iajus__buscar_jurimetria, mcp__plugin_iajus-juris_iajus__buscar_jurimetria, mcp__iajus__consultar_qualificada, mcp__plugin_iajus-juris_iajus__consultar_qualificada, mcp__iajus__consultar_informativos_stf, mcp__plugin_iajus-juris_iajus__consultar_informativos_stf, mcp__iajus__consultar_informativos_stj, mcp__plugin_iajus-juris_iajus__consultar_informativos_stj
---

# Pesquisar jurisprudência brasileira (iaJus)

Você tem acesso ao servidor MCP `iajus`, que indexa jurisprudência brasileira
(acórdãos colegiados 2013-2026 — TST/TRTs/TREs 2016-2026 —, súmulas, precedentes
qualificados, repercussão geral) com classificação alinhada ao CNJ/TPU (ontologia
OJBU: 21 ramos L1 → sub-áreas L2/L3). **Use o MCP em vez de inventar precedentes —
a fonte é a verdade; nunca cite de memória.**

> **Cobertura TJ-RJ (atual):** os acórdãos do TJ-RJ no corpus são hoje
> predominantemente **cíveis**; as Câmaras Criminais (1ª a 8ª + Seção Criminal)
> ainda estão em ingestão. Para matéria **criminal** do TJ-RJ, trate um `total: 0`
> ou recall fraco como **lacuna de cobertura em andamento**, não como ausência de
> precedente: avise o usuário e ofereça os tribunais superiores (STJ/STF) para a
> tese criminal. (Demais tribunais e o TJ-RJ cível não têm essa ressalva.)

## Escolha da modalidade (8 tools de busca + qualificadas)

Comece pela modalidade certa para a pergunta. As buscas retornam um envelope
uniforme (`{ modalidade, total, resultados:[…] }`) e são read-only.

| Pergunta do usuário | Tool | Por quê |
|---|---|---|
| Tema/conceito ("o que decidiram sobre dano moral por inscrição indevida") | `buscar_semantica` | Vetorial/densa — casa por significado, não por palavra. **Padrão para perguntas conceituais.** |
| Quer o melhor resultado geral (relevância máxima) | `buscar_hibrida` | Funde semântica + FTS + trigram + CNJ + ontologia via RRF, com boost de classificação. |
| Expressão exata / termo técnico literal ("usucapião extraordinária") | `buscar_fts` | Full-text pt_unaccent, stemming PT, insensível a acento; `phrase=true` exige a ordem. |
| Padrão literal / forma de citação ("Súmula 7", "art. 1.228") | `buscar_regex` | Regex POSIX; **exija ≥3 caracteres literais** no padrão (âncora do índice). |
| Número de processo CNJ | `buscar_por_cnj` | `numero` completo = casamento exato; ou componentes (ano, tribunal, origem). |
| "Todos os acórdãos de um ramo do direito" | `buscar_por_ontologia` | Subárvore ltree por `l1_code` TPU (ou L2/L3, ou `tema_transversal`). Ex.: `l1_code=287` (Penal), `899` (Civil), `9985` (Administrativo). |
| Quem citou uma súmula/tema, ou o que um acórdão cita | `buscar_grafo` | `legal_edges` single-hop; `normalized_ref="Súmula 279"` traz quem aplicou. |
| Pergunta QUANTITATIVA ("quantos acórdãos sobre X", "distribuição por ano/tribunal", "tendência") | `buscar_jurimetria` | Agrega contagens/estatísticas do corpus (por tribunal, ano, ramo). Use para números, não para o texto de um acórdão. |
| Entendimento CONSOLIDADO/vinculante de um órgão (súmula, SV, RG, tema repetitivo, IRDR, IRR, IAC, OJ) | `consultar_qualificada` | Lê os precedentes qualificados do órgão. **Prefira-os a um acórdão isolado** quando o usuário quer "o entendimento atual". |
| **Informativos** de jurisprudência do STF ou do STJ (teses recentes destacadas pelo tribunal) | `consultar_informativos_stf` / `consultar_informativos_stj` | Lê os informativos do STF / STJ — a síntese oficial dos julgados de destaque por edição. Use para "o que o STF/STJ decidiu de relevante recentemente" e para localizar a tese pela edição do informativo. |

Notas de uso:
- **Filtro de órgão difere por modalidade:** só `buscar_semantica` / `buscar_hibrida`
  aceitam `tribunal` (ex.: `"STF"`). As demais (`buscar_regex`, `buscar_fts`,
  `buscar_por_ontologia`) filtram por **`orgao_code`** — o slug minúsculo do órgão
  (ex.: `"stf"`, `"stj"`). **Não** passe `tribunal` para essas: a tool rejeita o
  argumento. (`buscar_por_cnj` recebe `tribunal` como componente CNJ.)
- `buscar_semantica` / `buscar_hibrida` aceitam `tribunal`, `ano_min`/`ano_max`,
  `space` (`default` = text-embedding-3-small; `premium` = gemini) e `k` (1-100,
  padrão 20). `buscar_hibrida` aceita `ramo_l1` para ativar o sinal de ontologia.
- `buscar_semantica` para a intenção temática; **se vier fraco, escale para
  `buscar_hibrida`** (mesma consulta) antes de desistir.
- `buscar_regex` recusa padrões só de metacaracteres (ex.: `^[A-Z]+$`); inclua um
  trecho literal ≥3 chars. Em erro, a tool devolve `{ "erro": "…", "resultados": [] }`
  (nunca stack trace) — leia a mensagem e ajuste.

## Campos canônicos novos (taxonomia + matéria + autoria)

- **`consultar_qualificada` devolve a taxonomia CANÔNICA do precedente:** `tipo_canonico`
  (o tipo padronizado), `tipo_label` (rótulo PT-BR pronto para exibição) e `tipo_familia`
  (a família qualitativa: vinculante / editorial / …). Use-os para agrupar e rotular os
  precedentes por tipo, em vez de inferir do texto da ementa.
- **`materia`** acompanha cada qualificada (= `ramo_hint`, presente em ~100% delas): é a
  matéria/ramo canônico do precedente. Use como **facet de recorte** ("súmulas de
  Tributário") e em perguntas de **jurimetria** (distribuição por matéria).
- **`redator_acordao`** vem nos acórdãos: é o magistrado **redator** do acórdão (autoria
  pelo art. 941 do CPC), com `revisor` quando houver — o autor do acórdão a citar, distinto
  do relator sorteado nos casos de relator vencido.

## Ontologia OJBU (ramos reais — use estes códigos)

`buscar_por_ontologia` recebe `l1_code` (código TPU do ramo, inteiro), opcionalmente
`l2_code`/`l3_code` (a sub-área exige o `l1_code` do seu ramo) **ou** `tema_transversal`.
Os 21 ramos L1 (código TPU → ramo) são os nós reais da ontologia:

| `l1_code` | Ramo | | `l1_code` | Ramo |
|---|---|---|---|---|
| 14 | Tributário | | 9633 | Criança e Adolescente |
| 195 | Previdenciário | | 9985 | Administrativo e Dir. Público |
| 287 | Penal | | 10110 | Ambiental |
| 864 | Trabalho | | 11068 | Penal Militar |
| 899 | Civil | | 11428 | Eleitoral |
| 1156 | Consumidor | | 12480 | Saúde |
| 1209 | Processual Penal | | 12734 | Assistencial |
| 6191 | Internacional | | 12775 | Educação |
| 7724 | Registros Públicos | | 1146 | Marítimo |
| 8826 | Processual Civil e do Trabalho | | 11049 | Processual Penal Militar |

Temas transversais (`tema_transversal=`): `DDG` (Digital e Proteção de Dados),
`DER` (Econômico e Regulação), `DFN` (Financeiro e Orçamentário), `DAG` (Agrário e
Fundiário Rural), `DID` (Pessoa Idosa e Envelhecimento). Use o tema transversal para
recortes que cruzam ramos (ex.: LGPD → `DDG`), e o `l1_code` para o ramo em si.

## Como citar (obrigatório)

- **Sempre** cite o campo `link_completo` do registro retornado — é a URL estável,
  deep-per-record, do acórdão na fonte oficial. **Nunca invente** número de processo,
  ementa ou link.
- Cite `tribunal`, `numero_processo` (ou `numero_processo_cnj`), `relator` e
  `data_julgamento` quando presentes. Resuma a `ementa_snippet` em 1-2 frases.
- Quando útil, mostre a classificação OJBU do registro (`classificacao.l1`,
  `ramo_l1_codes`) e ofereça abrir o inteiro teor pelo `inteiro_teor_url`.
- Em **acórdãos**, atribua a autoria ao **`redator_acordao`** (o redator — autor do
  acórdão pelo art. 941 do CPC) e ao `revisor` quando vier; nas **qualificadas** apresente
  `tipo_label` (o tipo) e `materia` (a matéria/ramo) para situar o precedente.
- Se a busca **não** retornar resultado relevante (`total: 0` ou hits fracos),
  **diga isso honestamente** — não preencha a lacuna com um precedente fabricado.

## Boas práticas

- Comece restrito (tema + tribunal + faixa de ano) e amplie só se vier vazio.
- Para "qual o entendimento atual", prefira **precedentes qualificados** (súmula /
  repercussão geral / tema repetitivo) a um acórdão isolado: use `consultar_qualificada`
  (entendimento consolidado do órgão) e/ou `buscar_grafo` para ver quem o aplicou.
- Preserve diacríticos e UTF-8 exatamente como na fonte.
- **Autenticação:** o cliente MCP autentica por você — via **login OAuth** (claude.ai /
  ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
  header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
  ausente ou expirada — peça ao usuário para refazer o login ou revisar a chave
  configurada; **nunca** cole a chave em chat nem em commit.
