---
name: pesquisar-jurisprudencia
description: Pesquisa e cita jurisprudência brasileira real (STF, STJ, TST, TCU, TSE, STM, TJs, TRFs, TRTs, TREs) pelo MCP iaJus — 8 modalidades de busca (semântica, híbrida, FTS, regex, CNJ, ontologia OJBU, grafo, jurimetria), jurimetria agregada (jurimetria_volume/relator/classe/orgao_julgador), qualificadas com vigência (súmula, RG, IRDR) e informativos STF/STJ. Acione para precedente, acórdão, súmula, tema, número CNJ, estatística de julgados ou entendimento atual de um tribunal. NÃO use para leis.
allowed-tools: mcp__iajus__buscar_semantica, mcp__plugin_iajus-juris_iajus__buscar_semantica, mcp__iajus__buscar_hibrida, mcp__plugin_iajus-juris_iajus__buscar_hibrida, mcp__iajus__buscar_fts, mcp__plugin_iajus-juris_iajus__buscar_fts, mcp__iajus__buscar_regex, mcp__plugin_iajus-juris_iajus__buscar_regex, mcp__iajus__buscar_por_cnj, mcp__plugin_iajus-juris_iajus__buscar_por_cnj, mcp__iajus__buscar_por_ontologia, mcp__plugin_iajus-juris_iajus__buscar_por_ontologia, mcp__iajus__buscar_grafo, mcp__plugin_iajus-juris_iajus__buscar_grafo, mcp__iajus__buscar_jurimetria, mcp__plugin_iajus-juris_iajus__buscar_jurimetria, mcp__iajus__jurimetria_volume, mcp__plugin_iajus-juris_iajus__jurimetria_volume, mcp__iajus__jurimetria_relator, mcp__plugin_iajus-juris_iajus__jurimetria_relator, mcp__iajus__jurimetria_classe, mcp__plugin_iajus-juris_iajus__jurimetria_classe, mcp__iajus__jurimetria_orgao_julgador, mcp__plugin_iajus-juris_iajus__jurimetria_orgao_julgador, mcp__iajus__consultar_qualificada, mcp__plugin_iajus-juris_iajus__consultar_qualificada, mcp__iajus__consultar_informativos_stf, mcp__plugin_iajus-juris_iajus__consultar_informativos_stf, mcp__iajus__consultar_informativos_stj, mcp__plugin_iajus-juris_iajus__consultar_informativos_stj
---

# Pesquisar jurisprudência brasileira (iaJus)

Você tem acesso ao servidor MCP `iajus`, que indexa jurisprudência brasileira
(acórdãos colegiados desde 2000 em todos os tribunais — exceções: controle
concentrado do STF desde 1988, TCU desde 1992, TRF6 desde 2022 —, súmulas,
precedentes qualificados, repercussão geral) com
classificação alinhada ao CNJ/TPU (ontologia OJBU: 21 ramos L1 → sub-áreas L2/L3).
**Use o MCP em vez de inventar precedentes — a fonte é a verdade; nunca cite de
memória.**

> **Corpus VIVO e em crescimento:** a base é ingerida continuamente — órgãos, anos
> e famílias novos aparecem na busca automaticamente, sem mudança de skill. Um
> `total: 0` (ou recall fraco) para um órgão/ano que já está em cobertura significa
> **cobertura em andamento**, não "não existe": avise o usuário e ofereça uma fonte
> alternativa (ex.: tribunal superior). Para conferir o que a base contém AGORA
> (por órgão/ano/família + quanto já está embedado), use a skill **corpus-status**
> (`estatisticas_corpus_pg`).

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
| Pergunta QUANTITATIVA exata ("quantas decisões o TJRJ julgou por ano", "quem mais relata no órgão X", "quais classes/câmaras dominam") | `jurimetria_volume` / `jurimetria_relator` / `jurimetria_classe` / `jurimetria_orgao_julgador` | Contagens EXATAS do read-model agregado, com envelope de honestidade. **Prefira-as para números** (ver seção Jurimetria agregada). |
| Navegação/facet por colunas estruturadas (listar acórdãos por relator/classe/UF; buckets ad-hoc com `group_by`) | `buscar_jurimetria` | Filtros tipados + `group_by` por bucket. Os buckets contam o campo PREENCHIDO, não o universo — nunca derive taxa de campo esparso. |
| Entendimento CONSOLIDADO/vinculante de um órgão (súmula, SV, RG, tema repetitivo, IRDR, IRR, IAC, OJ) | `consultar_qualificada` | Lê os precedentes qualificados do órgão. **Prefira-os a um acórdão isolado** quando o usuário quer "o entendimento atual". |
| **Informativos** de jurisprudência do STF ou do STJ (teses recentes destacadas pelo tribunal) | `consultar_informativos_stf` / `consultar_informativos_stj` | Lê os informativos do STF / STJ — a síntese oficial dos julgados de destaque por edição. Use para "o que o STF/STJ decidiu de relevante recentemente" e para localizar a tese pela edição do informativo. |

Notas de uso:
- **Filtro de órgão difere por modalidade:** só `buscar_semantica` / `buscar_hibrida`
  aceitam `tribunal` (ex.: `"STF"`). As demais (`buscar_regex`, `buscar_fts`,
  `buscar_por_ontologia`) filtram por **`orgao_code`** — o slug minúsculo do órgão
  (ex.: `"stf"`, `"stj"`). **Não** passe `tribunal` para essas: a tool rejeita o
  argumento. (`buscar_por_cnj` recebe `tribunal` como componente CNJ.)
- **Recorte de ano difere:** `buscar_semantica` aceita `ano` (UM ano exato);
  `buscar_hibrida` (e regex/FTS/ontologia) aceitam `ano_min`/`ano_max` (faixa).
  Ambas aceitam `tribunal`, `ramo_l1` (código OJBU L1 para escopar o ramo),
  `space` (`default` = text-embedding-3-small; `premium` = gemini) e `k` (1-100,
  padrão 20).
- `buscar_semantica` para a intenção temática; **se vier fraco, escale para
  `buscar_hibrida`** (mesma consulta) antes de desistir.
- `buscar_regex` recusa padrões só de metacaracteres (ex.: `^[A-Z]+$`); inclua um
  trecho literal ≥3 chars. Em erro, a tool devolve `{ "erro": "…", "resultados": [] }`
  (nunca stack trace) — leia a mensagem e ajuste.

## Jurimetria agregada (4 tools dedicadas — contagens exatas)

Para perguntas de VOLUME/ranking use estas tools (servidas do read-model agregado do
Postgres, mantido por projector — nunca varrem a tabela quente; respostas exatas e
rápidas). Todas exigem um **recorte** (regra scan-safety) e devolvem o **envelope de
honestidade** `jurimetria` `{snapshot_id, as_of, denominator_definition, value_kind,
coverage_pct, truncado}`:

| Tool | Assinatura | Para quê |
|---|---|---|
| `jurimetria_volume` | `orgao?` e/ou `ano?` (ou `ano_de`/`ano_ate`); `top` 1-200 (padrão 50). **Pelo menos um recorte é obrigatório.** | Volume por órgão × ano. Ex.: `jurimetria_volume(orgao="tjrj")` → série anual do TJRJ; `jurimetria_volume(ano=2024, top=20)` → maiores órgãos em 2024. |
| `jurimetria_relator` | `orgao` (OBRIGATÓRIO), `relator?` (substring, case-insensitive), `top` 1-50 (padrão 25) | Quem mais relata num órgão + período de atuação (`ano_min`/`ano_max`). Rollup top-50 por volume. **LGPD:** figura nominal com n<20 é suprimida ("amostra insuficiente"). |
| `jurimetria_classe` | `orgao` (OBRIGATÓRIO), `ano?` ou `ano_de`/`ano_ate`, `top` 1-100 (padrão 25) | Volume por classe processual CNJ. O bucket `classe_cnj_code=-1` é a massa SEM classe resolvida; o envelope traz `cobertura_classe_pct`. Rótulos de código via `consultar_protocolo_classificacao`. |
| `jurimetria_orgao_julgador` | `orgao` (OBRIGATÓRIO), `filtro?` (substring), `top` 1-100 (padrão 25) | Volume por câmara/turma/seção (unidade organizacional — sem supressão LGPD) + período de atuação. Ex.: "quais câmaras do TJRJ mais julgam?". |

Regras de honestidade (obrigatório):

- **Taxas de resultado (provimento etc.) ainda não são servidas** — o envelope diz
  `taxas_resultado: "sem cobertura"`. NUNCA infira taxa a partir de contagens.
- `aviso: "sem cobertura para o recorte"` = lacuna de ingestão/rollup, **não** volume
  zero no mundo real — reporte assim.
- Reporte `as_of` (data do snapshot) quando o número embasar uma afirmação.
- Sem recorte (`orgao`/`ano`) a tool recusa com erro explicando — não insista; peça o
  recorte ao usuário.

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
- **Envelope de confiança `trust`** — os hits de busca (`buscar_regex`, `buscar_fts`,
  `buscar_por_ontologia`, `buscar_por_cnj`, `buscar_hibrida`) trazem
  `trust: {authority_tier, status_vigencia, trecho}`: `authority_tier` gradua a autoridade
  do órgão/tipo; **cheque `status_vigencia` antes de citar qualificada/legislação como
  amparo** — ato não-vigente vem sinalizado, nunca oculto.
- **Vigência nas qualificadas (`consultar_qualificada`)** — cada resultado traz
  `status_vigencia` (`vigente` / `cancelada` / …); canceladas/superadas saem **MARCADAS**
  (vigentes primeiro, com `aviso` quando nada vigente casa). `incluir_canceladas=false`
  oculta-as. Além do lookup por número, há o modo **navegar por matéria**:
  `consultar_qualificada(materia="Direito Tributário", tipo="sumula")`.
- **Citação numérica dispara lookup exato** — em `buscar_fts`/`buscar_semantica`, uma
  consulta que cita "súmula 145 do STF", "súmula vinculante 11", "OJ 191" ou "tema 1234"
  prependa o casamento EXATO por número (sinalizado em `signals.qualificada_numero`),
  antes dos hits textuais.

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
- **Confira a vigência antes de amparar:** ao citar súmula/tema/qualificada, verifique
  `status_vigencia` (no hit `trust` ou em `consultar_qualificada`) e sinalize
  explicitamente quando o ato estiver cancelado/superado — nunca o apresente como vigente.
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
