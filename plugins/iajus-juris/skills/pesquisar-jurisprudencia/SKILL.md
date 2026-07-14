---
name: pesquisar-jurisprudencia
description: Pesquisa e cita jurisprudência brasileira real (STF, STJ, TST, TCU, TSE, STM, TJs, TRFs, TRTs, TREs) pelo MCP IAJUS - 7 modalidades de busca (semântica, híbrida, FTS, regex, CNJ, ontologia OJBU, citações), jurimetria exata (volume/relator/classe/órgão/resultado/lag), qualificadas com vigência (súmula, RG, IRDR) e informativos STF/STJ. Acione para precedente, acórdão, súmula, tema, número CNJ, estatística de julgados ou entendimento de um tribunal. NÃO use para leis.
allowed-tools: mcp__iajus__buscar_semantica, mcp__plugin_iajus-juris_iajus__buscar_semantica, mcp__iajus__buscar_hibrida, mcp__plugin_iajus-juris_iajus__buscar_hibrida, mcp__iajus__buscar_fts, mcp__plugin_iajus-juris_iajus__buscar_fts, mcp__iajus__buscar_regex, mcp__plugin_iajus-juris_iajus__buscar_regex, mcp__iajus__buscar_por_cnj, mcp__plugin_iajus-juris_iajus__buscar_por_cnj, mcp__iajus__buscar_por_ontologia, mcp__plugin_iajus-juris_iajus__buscar_por_ontologia, mcp__iajus__buscar_por_citacoes, mcp__plugin_iajus-juris_iajus__buscar_por_citacoes, mcp__iajus__obter_dispositivos_citados, mcp__plugin_iajus-juris_iajus__obter_dispositivos_citados, mcp__iajus__buscar_citantes_dispositivo, mcp__plugin_iajus-juris_iajus__buscar_citantes_dispositivo, mcp__iajus__jurimetria_volume, mcp__plugin_iajus-juris_iajus__jurimetria_volume, mcp__iajus__jurimetria_relator, mcp__plugin_iajus-juris_iajus__jurimetria_relator, mcp__iajus__jurimetria_classe, mcp__plugin_iajus-juris_iajus__jurimetria_classe, mcp__iajus__jurimetria_orgao_julgador, mcp__plugin_iajus-juris_iajus__jurimetria_orgao_julgador, mcp__iajus__jurimetria_resultado, mcp__plugin_iajus-juris_iajus__jurimetria_resultado, mcp__iajus__jurimetria_lag_publicacao, mcp__plugin_iajus-juris_iajus__jurimetria_lag_publicacao, mcp__iajus__jurimetria_desfecho_cruzado, mcp__plugin_iajus-juris_iajus__jurimetria_desfecho_cruzado, mcp__iajus__buscar_qualificada, mcp__plugin_iajus-juris_iajus__buscar_qualificada, mcp__iajus__obter_versoes_qualificada, mcp__plugin_iajus-juris_iajus__obter_versoes_qualificada, mcp__iajus__buscar_informativos_stf, mcp__plugin_iajus-juris_iajus__buscar_informativos_stf, mcp__iajus__buscar_informativos_stj, mcp__plugin_iajus-juris_iajus__buscar_informativos_stj
---

# Pesquisar jurisprudência brasileira (IAJUS)

Você tem acesso ao servidor MCP `iajus`, que indexa jurisprudência brasileira
(acórdãos colegiados desde 2000 em todos os tribunais - exceções: controle
concentrado do STF desde 1988, TCU desde 1992, TRF6 desde 2022 -, súmulas,
precedentes qualificados, repercussão geral) com
classificação alinhada ao CNJ/TPU (ontologia OJBU: 21 ramos L1 → sub-áreas L2/L3).
**Use o MCP em vez de inventar precedentes - a fonte é a verdade; nunca cite de
memória.**

> **Corpus VIVO e em crescimento:** a base é ingerida continuamente - órgãos, anos
> e famílias novos aparecem na busca automaticamente, sem mudança de skill. Um
> `total: 0` (ou recall fraco) para um órgão/ano que já está em cobertura significa
> **cobertura em andamento**, não "não existe": avise o usuário e ofereça uma fonte
> alternativa (ex.: tribunal superior). Para conferir o que a base contém AGORA
> (por órgão/ano/família + quanto já está embedado), use a skill **corpus-status**
> (`obter_estatisticas_base`).

> **Cobertura TJ-RJ (atual):** os acórdãos do TJ-RJ no corpus são hoje
> predominantemente **cíveis**; a matéria **criminal** (Câmaras Criminais 1ª a 8ª +
> Seção Criminal) está **apenas parcialmente ingerida** e segue em coleta.
> **Sempre rode a busca primeiro** - não recuse a consulta criminal de antemão.
> Só **se** ela voltar `total: 0` ou recall fraco, trate como **lacuna de cobertura
> em andamento** (não como ausência de precedente): avise o usuário e ofereça os
> tribunais superiores (STJ/STF) para a tese criminal. (Demais tribunais e o TJ-RJ
> cível não têm essa ressalva.)

## Escolha da modalidade (7 tools de busca + jurimetria + qualificadas)

Comece pela modalidade certa para a pergunta. As buscas retornam um envelope
uniforme (`{ modalidade, total, resultados:[…] }`) e são read-only.

| Pergunta do usuário | Tool | Por quê |
|---|---|---|
| Tema/conceito ("o que decidiram sobre dano moral por inscrição indevida") | `buscar_semantica` | Vetorial/densa - casa por significado, não por palavra. **Padrão para perguntas conceituais.** |
| Quer o melhor resultado geral (relevância máxima) | `buscar_hibrida` | Funde semântica + FTS + trigram + CNJ + ontologia via RRF, com boost de classificação. |
| Expressão exata / termo técnico literal ("usucapião extraordinária") | `buscar_fts` | Full-text pt_unaccent, stemming PT, insensível a acento; `phrase=true` exige a ordem. |
| Padrão literal / forma de citação ("Súmula 7", "art. 1.228") | `buscar_regex` | Regex POSIX; **exija ≥3 caracteres literais** no padrão (âncora do índice). |
| Número de processo CNJ | `buscar_por_cnj` | `numero` completo = casamento exato; ou componentes (ano, tribunal, origem). |
| "Todos os acórdãos de um ramo do direito" | `buscar_por_ontologia` | Subárvore ltree por `l1_code` TPU (ou L2/L3, ou `tema_transversal`). Ex.: `l1_code=287` (Penal), `899` (Civil), `9985` (Administrativo). |
| Quem citou uma súmula/tema, ou o que um acórdão cita | `buscar_por_citacoes` | `legal_edges` single-hop; `normalized_ref="Súmula 279"` traz quem aplicou. |
| Quais dispositivos legais um acórdão cita | `obter_dispositivos_citados` | Leitor do grafo de citações (CIT-04): lista os artigos/dispositivos que uma decisão invoca, para conferir o amparo legal aplicado. |
| Quais julgados aplicam um dispositivo legal | `buscar_citantes_dispositivo` | O inverso (CIT-04): dado um dispositivo (ex. art. 1.228 do CC), traz os julgados que o aplicaram, medindo o quanto ele é usado. |
| A redação de uma súmula/tema mudou? Histórico de versões | `obter_versoes_qualificada` | Versões da redação de uma súmula/tema/precedente (o enunciado mudou, quando e por quê), útil ao amparar num precedente cujo texto foi alterado. |
| Pergunta QUANTITATIVA exata ("quantas decisões o TJRJ julgou por ano", "quem mais relata no órgão X", "quais classes/câmaras dominam") | `jurimetria_volume` / `jurimetria_relator` / `jurimetria_classe` / `jurimetria_orgao_julgador` | Contagens EXATAS do read-model agregado, com envelope de honestidade. **Prefira-as para números** (ver seção Jurimetria agregada). |
| TAXA DE DESFECHO ("qual a taxa de provimento do STJ", "o TJRJ provê mais agravos que apelações", "evolução do improvimento no TST") | `jurimetria_resultado` | Taxas de provimento/improvimento do rollup, com **denominador duplo** rotulado (`share_over_known` e `share_over_all`) e `coverage_pct` - nunca uma taxa sem denominador (ver seção Jurimetria agregada). |
| LAG DE PUBLICAÇÃO ("quanto tempo o STJ leva para publicar após julgar", "evolução do lag do TST") | `jurimetria_lag_publicacao` | Intervalo em dias `data_publicacao − data_julgamento` (p50/p90) por órgão × ano. **NÃO é duração do processo** - só o lag publicação−julgamento (ver seção Jurimetria agregada). |
| Entendimento CONSOLIDADO/vinculante de um órgão (súmula, SV, RG, tema repetitivo, IRDR, IRR, IAC, OJ) | `buscar_qualificada` | Lê os precedentes qualificados do órgão. **Prefira-os a um acórdão isolado** quando o usuário quer "o entendimento atual". |
| **Informativos** de jurisprudência do STF ou do STJ (teses recentes destacadas pelo tribunal) | `buscar_informativos_stf` / `buscar_informativos_stj` | Lê os informativos do STF / STJ - a síntese oficial dos julgados de destaque por edição. Use para "o que o STF/STJ decidiu de relevante recentemente" e para localizar a tese pela edição do informativo. |

Notas de uso:
- **Filtro de órgão difere por modalidade:** só `buscar_semantica` / `buscar_hibrida`
  aceitam `tribunal` (ex.: `"STF"`). As demais (`buscar_regex`, `buscar_fts`,
  `buscar_por_ontologia`) filtram por **`orgao_code`** - o slug minúsculo do órgão
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
  (nunca stack trace) - leia a mensagem e ajuste.

## Método do pesquisador (execute você mesmo - não delegue)

Uma pesquisa jurídica de verdade quase nunca é UMA chamada: é uma varredura que
escala de modalidade até a cobertura estabilizar. Conduza-a você mesmo, nesta ordem.

1. **Priorize o CONTEÚDO consolidado antes do acórdão comum.** Para "qual o
   entendimento atual / a tese firmada", comece por `buscar_qualificada` (súmula, SV,
   repercussão geral, tema repetitivo, IRDR, IRR, IAC, OJ): o precedente qualificado
   vence um acórdão isolado, e você o cita com a vigência já conferida. Só depois desça
   ao acórdão individual (busca semântica/híbrida) para exemplificar a aplicação da tese.
2. **Escale a modalidade em vez de cair no vazio.** Uma busca fraca (`total: 0`, ou
   hits cujo `trust.trecho` não responde) NÃO é sinal de parar - é sinal de escalar,
   nesta ordem, antes de reportar lacuna:
   - `buscar_semantica` → **`buscar_hibrida`** com a MESMA consulta (a fusão RRF resgata
     o que a densa isolada perdeu);
   - **reformule** com os termos que apareceram nos primeiros hits (relator, tese,
     dispositivo citado, número de tema) - a segunda consulta quase sempre é melhor;
   - **troque de modalidade pela forma da pergunta:** termo técnico literal →
     `buscar_fts`; citação de súmula/artigo → `buscar_regex` (ou a citação numérica
     dispara o lookup exato); ramo inteiro → `buscar_por_ontologia`; precisão por número
     de processo → `buscar_por_cnj`; rede de um precedente → `buscar_por_citacoes`
     (quem aplicou uma súmula/tema) e `obter_dispositivos_citados` (o que um acórdão cita);
   - **suba na hierarquia de autoridade:** se um TJ/TRF vier vazio para a tese, tente o
     tribunal superior (STJ/STF) - a tese firmada costuma estar lá.
3. **Refine até a cobertura estabilizar** (rodadas sem resultado novo relevante) e cruze
   as modalidades: densa/híbrida para o panorama, FTS/regex para termos e dispositivos
   literais, ontologia para esgotar um ramo, citações para a rede do precedente-chave.
4. **Envelope de honestidade.** Reporte o vazio como vazio, com o motivo: um `total: 0`
   de órgão/ano em cobertura é **cobertura em andamento**, não "o precedente não existe"
   (diga isso e ofereça a fonte superior); um filtro rejeitado (`tribunal` numa modalidade
   que só aceita `orgao_code`) pede reenvio, não descarte do resultado; um
   `{ "erro": … }` pede ajuste do argumento. Nunca preencha a lacuna com um precedente
   plausível porém fabricado.
5. **Passada de conferência anti-alucinação (obrigatória antes de entregar).** Toda
   citação que você entregar precisa ter vindo de uma chamada REAL nesta sessão, com o
   `link_completo` que a fonte retornou. Antes de fechar a resposta, confira cada citação:
   - **existe?** o precedente/norma com aquele número/tema/tipo voltou de uma chamada;
   - **é fiel?** o enunciado, o órgão, o redator, a data que você afirma batem com a fonte;
   - **está vigente?** cheque `status_vigencia` (no `trust` ou em `buscar_qualificada`) -
     súmula cancelada/superada e artigo revogado saem MARCADOS, nunca como amparo vigente.
   Um número, ementa, relator ou link só entra na resposta se veio da tool. Sem retorno,
   é NÃO LOCALIZADA (possível alucinação) - reporte assim, nunca "provavelmente existe".

## Jurimetria agregada (6 tools dedicadas - contagens/taxas exatas)

Para perguntas de VOLUME/ranking use estas tools (servidas do read-model agregado do
Postgres, mantido por projector - nunca varrem a tabela quente; respostas exatas e
rápidas). Todas exigem um **recorte** (regra scan-safety) e devolvem o **envelope de
honestidade** `jurimetria` `{snapshot_id, as_of, denominator_definition, value_kind,
coverage_pct, truncado}`:

| Tool | Assinatura | Para quê |
|---|---|---|
| `jurimetria_volume` | `orgao?` e/ou `ano?` (ou `ano_de`/`ano_ate`); `top` 1-200 (padrão 50). **Pelo menos um recorte é obrigatório.** | Volume por órgão × ano. Ex.: `jurimetria_volume(orgao="tjrj")` → série anual do TJRJ; `jurimetria_volume(ano=2024, top=20)` → maiores órgãos em 2024. |
| `jurimetria_relator` | `orgao` (OBRIGATÓRIO), `relator?` (substring, case-insensitive), `top` 1-50 (padrão 25) | Quem mais relata num órgão + período de atuação (`ano_min`/`ano_max`). Rollup top-50 por volume. **LGPD:** figura nominal com n<20 é suprimida ("amostra insuficiente"). |
| `jurimetria_classe` | `orgao` (OBRIGATÓRIO), `ano?` ou `ano_de`/`ano_ate`, `top` 1-100 (padrão 25) | Volume por classe processual CNJ. O bucket `classe_cnj_code=-1` é a massa SEM classe resolvida; o envelope traz `cobertura_classe_pct`. Rótulos de código via `obter_protocolo_classificacao`. |
| `jurimetria_orgao_julgador` | `orgao` (OBRIGATÓRIO), `filtro?` (substring), `top` 1-100 (padrão 25) | Volume por câmara/turma/seção (unidade organizacional - sem supressão LGPD) + período de atuação. Ex.: "quais câmaras do TJRJ mais julgam?". |
| `jurimetria_resultado` | `orgao?` e/ou recorte de ano (`ano?` ou `ano_de`/`ano_ate`). **Pelo menos um recorte é obrigatório.** | Taxas de DESFECHO (provimento/improvimento etc.) por órgão × ano, dos rollups `agg_decisions_resultado(_cov)`. Cada desfecho vem com **denominador duplo** rotulado: `share_over_known` (n/decididas - a fração ENTRE as decisões cujo desfecho o extractor determinou) E `share_over_all` (n/total). **LGPD:** recortes com <20 decisões conhecidas são suprimidos ("amostra insuficiente"). |
| `jurimetria_lag_publicacao` | `orgao?` OU `ano?`. **Pelo menos um recorte é obrigatório.** | Lag de publicação (dias `data_publicacao − data_julgamento`, p50/p90) por órgão × ano, do rollup `agg_decisions_lag_pub`. É a ÚNICA métrica temporal servida hoje - **NÃO é duração do processo** (ajuizamento→trânsito). Órgãos sem `data_publicacao` (vários TJs) saem sem lag. |
| `jurimetria_desfecho_cruzado` | `orgao` (OBRIGATÓRIO) + um eixo de cruzamento (ex. `por="classe"` ou `por="relator"`), com recorte de ano opcional | Cruza a taxa de desfecho (provimento/improvimento) por um segundo eixo (classe, relator, câmara), numa janela limitada por dia. Mantém a supressão LGPD (célula com n<20 é suprimida). Use para "o TJRJ provê mais apelações ou agravos", sempre reportando o denominador. |

Regras de honestidade (obrigatório):

- **Taxa de desfecho SÓ via `jurimetria_resultado`** - nunca infira provimento/improvimento
  a partir de contagens de volume. A tool traz **denominador duplo** (`share_over_known` vs
  `share_over_all`): reporte a taxa SEMPRE com o denominador, e o `coverage_pct`
  (=100·conhecidas/total). Cobertura baixa = **não** é representativa (o extractor abstém em
  dispositivo ambíguo - abstenção não é zero); diga "baixa cobertura", não uma taxa nua.
- **Lag de publicação (`jurimetria_lag_publicacao`) NÃO é duração do processo** - é só o
  intervalo publicação−julgamento. Quando `sem_cobertura` (coverage baixo) ou o órgão não
  expõe `data_publicacao`, reporte "sem cobertura" e NÃO estime.
- `aviso: "sem cobertura para o recorte"` = lacuna de ingestão/rollup, **não** volume
  zero no mundo real - reporte assim.
- Reporte `as_of` (data do snapshot) quando o número embasar uma afirmação.
- Sem recorte (`orgao`/`ano`) a tool recusa com erro explicando - não insista; peça o
  recorte ao usuário.

## Campos canônicos novos (taxonomia + matéria + autoria)

- **`buscar_qualificada` devolve a taxonomia CANÔNICA do precedente:** `tipo_canonico`
  (o tipo padronizado), `tipo_label` (rótulo PT-BR pronto para exibição) e `tipo_familia`
  (a família qualitativa: vinculante / editorial / …). Use-os para agrupar e rotular os
  precedentes por tipo, em vez de inferir do texto da ementa.
- **`materia`** acompanha cada qualificada (= `ramo_hint`, presente em ~100% delas): é a
  matéria/ramo canônico do precedente. Use como **facet de recorte** ("súmulas de
  Tributário") e em perguntas de **jurimetria** (distribuição por matéria).
- **`redator_acordao`** vem nos acórdãos: é o magistrado **redator** do acórdão (autoria
  pelo art. 941 do CPC), com `revisor` quando houver - o autor do acórdão a citar, distinto
  do relator sorteado nos casos de relator vencido.
- **Envelope de confiança `trust`** - os hits de busca (`buscar_regex`, `buscar_fts`,
  `buscar_por_ontologia`, `buscar_por_cnj`, `buscar_hibrida`) trazem
  `trust: {authority_tier, status_vigencia, trecho}`: `authority_tier` gradua a autoridade
  do órgão/tipo; **cheque `status_vigencia` antes de citar qualificada/legislação como
  amparo** - ato não-vigente vem sinalizado, nunca oculto.
- **Vigência nas qualificadas (`buscar_qualificada`)** - cada resultado traz
  `status_vigencia` (`vigente` / `cancelada` / …); canceladas/superadas saem **MARCADAS**
  (vigentes primeiro, com `aviso` quando nada vigente casa). `incluir_canceladas=false`
  oculta-as. Além do lookup por número, há o modo **navegar por matéria**:
  `buscar_qualificada(materia="Direito Tributário", tipo="sumula")`.
- **Citação numérica dispara lookup exato** - em `buscar_fts`/`buscar_semantica`, uma
  consulta que cita "súmula 145 do STF", "súmula vinculante 11", "OJ 191" ou "tema 1234"
  prependa o casamento EXATO por número (sinalizado em `signals.qualificada_numero`),
  antes dos hits textuais.

## Ontologia OJBU (ramos reais - use estes códigos)

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

- **Sempre** cite o campo `link_completo` do registro retornado - é a URL estável,
  deep-per-record, do acórdão na fonte oficial. **Nunca invente** número de processo,
  ementa ou link.
- Cite `tribunal`, `numero_processo` (ou `numero_processo_cnj`), `relator` e
  `data_julgamento` quando presentes. Resuma a `ementa_snippet` em 1-2 frases.
- Quando útil, mostre a classificação OJBU do registro (`classificacao.l1`,
  `ramo_l1_codes`) e ofereça abrir o inteiro teor pelo `inteiro_teor_url`.
- Em **acórdãos**, atribua a autoria ao **`redator_acordao`** (o redator - autor do
  acórdão pelo art. 941 do CPC) e ao `revisor` quando vier; nas **qualificadas** apresente
  `tipo_label` (o tipo) e `materia` (a matéria/ramo) para situar o precedente.
- **Confira a vigência antes de amparar:** ao citar súmula/tema/qualificada, verifique
  `status_vigencia` (no hit `trust` ou em `buscar_qualificada`) e sinalize
  explicitamente quando o ato estiver cancelado/superado - nunca o apresente como vigente.
- Se a busca **não** retornar resultado relevante (`total: 0` ou hits fracos),
  **diga isso honestamente** - não preencha a lacuna com um precedente fabricado.

## Boas práticas

- Comece restrito (tema + tribunal + faixa de ano) e amplie só se vier vazio.
- Para "qual o entendimento atual", prefira **precedentes qualificados** (súmula /
  repercussão geral / tema repetitivo) a um acórdão isolado: use `buscar_qualificada`
  (entendimento consolidado do órgão) e/ou `buscar_por_citacoes` para ver quem o aplicou.
- Preserve diacríticos e UTF-8 exatamente como na fonte.
- **Autenticação:** o cliente MCP autentica por você - via **login OAuth** (claude.ai /
  ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
  header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
  ausente ou expirada - peça ao usuário para refazer o login ou revisar a chave
  configurada; **nunca** cole a chave em chat nem em commit.

## Aprovação de ferramentas (sem fricção)

Todas as tools do IAJUS são **somente-leitura** (marcadas `readOnlyHint`) - não escrevem
nada, só pesquisam e citam. Ainda assim, alguns clientes pedem **uma aprovação por tool**
na primeira chamada:

- **Prefira a busca direta.** Para responder e citar, `buscar_hibrida` (melhor relevância
  geral) e `buscar_semantica` (perguntas conceituais) já entregam ementa + `link_completo`
  oficial de cada julgado - comece por elas. As demais modalidades (`buscar_fts`,
  `buscar_regex`, `buscar_por_ontologia`, jurimetria…) são **refinamento**: acione-as
  quando a busca direta vier fraca ou a pergunta pedir contagem/facet/forma literal.
- **Abrir a íntegra:** cada resultado da busca já traz o `link_completo` (URL estável do
  acórdão na fonte) e, quando disponível, o `inteiro_teor_url` (PDF/HTML da íntegra) - cite
  esses links; NÃO é preciso outra tool para "abrir" o julgado. A leitura do documento é o
  próprio hit da busca (ementa + trecho + links); não há tool separada de "abrir".
- **Se o cliente pedir aprovação por chamada:** oriente o usuário a **autorizar uma vez**
  e marcar **"sempre permitir" / "lembrar nesta conversa"** - como as tools são
  somente-leitura, é seguro liberar em bloco, e as buscas seguintes deixam de perguntar.
  (No Claude Code, `/permissions` permite pré-aprovar as tools `mcp__iajus__*`; no ChatGPT,
  a caixa de confirmação do conector oferece lembrar a escolha na conversa.) Se a caixa de
  aprovação **não renderizar/travar**, é limitação da interface do cliente, não do IAJUS:
  reabra a conversa/sessão ou reautentique em `/mcp`, e prefira a busca direta acima.
