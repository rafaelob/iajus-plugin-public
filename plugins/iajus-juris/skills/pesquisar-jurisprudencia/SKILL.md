---
name: pesquisar-jurisprudencia
description: Pesquisa e cita jurisprudência brasileira real (STF, STJ, TST, TCU, TSE, STM, TJs, TRFs, TRTs, TREs) pelo MCP IAJUS - 7 modalidades de busca (semântica, híbrida, FTS, regex, CNJ, ontologia OJBU, citações), qualificadas com vigência (súmula, RG, IRDR) e informativos STF/STJ. Acione para precedente, acórdão, súmula, tema, número CNJ ou entendimento de um tribunal. NÃO use para leis.
allowed-tools: mcp__iajus__buscar_semantica, mcp__plugin_iajus-juris_iajus__buscar_semantica, mcp__iajus__buscar_hibrida, mcp__plugin_iajus-juris_iajus__buscar_hibrida, mcp__iajus__buscar_fts, mcp__plugin_iajus-juris_iajus__buscar_fts, mcp__iajus__buscar_regex, mcp__plugin_iajus-juris_iajus__buscar_regex, mcp__iajus__buscar_por_cnj, mcp__plugin_iajus-juris_iajus__buscar_por_cnj, mcp__iajus__buscar_por_ontologia, mcp__plugin_iajus-juris_iajus__buscar_por_ontologia, mcp__iajus__buscar_por_citacoes, mcp__plugin_iajus-juris_iajus__buscar_por_citacoes, mcp__iajus__obter_dispositivos_citados, mcp__plugin_iajus-juris_iajus__obter_dispositivos_citados, mcp__iajus__buscar_citantes_dispositivo, mcp__plugin_iajus-juris_iajus__buscar_citantes_dispositivo, mcp__iajus__buscar_qualificada, mcp__plugin_iajus-juris_iajus__buscar_qualificada, mcp__iajus__obter_versoes_qualificada, mcp__plugin_iajus-juris_iajus__obter_versoes_qualificada, mcp__iajus__buscar_informativos_stf, mcp__plugin_iajus-juris_iajus__buscar_informativos_stf, mcp__iajus__buscar_informativos_stj, mcp__plugin_iajus-juris_iajus__buscar_informativos_stj
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

## Escolha da modalidade (7 tools de busca + qualificadas)

Comece pela modalidade certa para a pergunta. As buscas retornam um envelope
uniforme (`{ modalidade, total, resultados:[…] }`) e são read-only.

| Pergunta do usuário | Tool | Por quê |
|---|---|---|
| Tema/conceito ("o que decidiram sobre dano moral por inscrição indevida") | `buscar_semantica` | Vetorial/densa - casa por significado. **Padrão para perguntas conceituais.** |
| Quer o melhor resultado geral (relevância máxima) | `buscar_hibrida` | Funde semântica + FTS + trigram + CNJ + ontologia via RRF, com boost de classificação. |
| Expressão exata / termo técnico literal ("usucapião extraordinária") | `buscar_fts` | Full-text pt_unaccent, stemming PT, insensível a acento; `phrase=true` exige a ordem. |
| Padrão literal / forma de citação ("Súmula 7", "art. 1.228") | `buscar_regex` | Regex POSIX; **exija ≥3 caracteres literais** no padrão (âncora do índice). |
| Número de processo CNJ | `buscar_por_cnj` | `numero` completo = casamento exato; ou componentes (ano, tribunal, origem). |
| "Todos os acórdãos de um ramo do direito" | `buscar_por_ontologia` | Subárvore ltree por `l1_code` TPU (ou L2/L3, ou `tema_transversal`). Códigos em `references/ontologia-ojbu.md`. |
| Quem citou uma súmula/tema, ou o que um acórdão cita | `buscar_por_citacoes` | `legal_edges` single-hop; `normalized_ref="Súmula 279"` traz quem aplicou. |
| Quais dispositivos legais um acórdão cita | `obter_dispositivos_citados` | Leitor do grafo de citações (CIT-04): lista os artigos que a decisão invoca. |
| Quais julgados aplicam um dispositivo legal | `buscar_citantes_dispositivo` | O inverso (CIT-04): dado um dispositivo, traz os julgados que o aplicaram. |
| A redação de uma súmula/tema mudou? Histórico de versões | `obter_versoes_qualificada` | Versões da redação de uma súmula/tema/precedente (mudou, quando e por quê). |
| Entendimento CONSOLIDADO/vinculante de um órgão (súmula, SV, RG, repetitivo, IRDR, IRR, IAC, OJ) | `buscar_qualificada` | Lê os precedentes qualificados do órgão. **Prefira-os a um acórdão isolado** para "o entendimento atual". |
| **Informativos** de jurisprudência do STF ou do STJ (teses recentes destacadas) | `buscar_informativos_stf` / `buscar_informativos_stj` | A síntese oficial dos julgados de destaque por edição. |

Notas de uso:
- **Filtro de órgão difere por modalidade:** só `buscar_semantica` / `buscar_hibrida`
  aceitam `tribunal` (ex.: `"STF"`). As demais (`buscar_regex`, `buscar_fts`,
  `buscar_por_ontologia`) filtram por **`orgao_code`** - o slug minúsculo (ex.: `"stf"`).
  **Não** passe `tribunal` para essas: a tool rejeita o argumento. (`buscar_por_cnj`
  recebe `tribunal` como componente CNJ.)
- **Recorte de ano difere:** `buscar_semantica` aceita `ano` (UM ano exato);
  `buscar_hibrida` (e regex/FTS/ontologia) aceitam `ano_min`/`ano_max` (faixa).
  Ambas aceitam `tribunal`, `ramo_l1` (código OJBU L1), `space` (`default` =
  text-embedding-3-small; `premium` = gemini) e `k` (1-100, padrão 20).
- `buscar_regex` recusa padrões só de metacaracteres (ex.: `^[A-Z]+$`); inclua um
  trecho literal ≥3 chars. Em erro, a tool devolve `{ "erro": "…", "resultados": [] }`
  (nunca stack trace) - leia a mensagem e ajuste.

## Referências detalhadas (consulte quando precisar do detalhe)

- **`references/ontologia-ojbu.md`** - os 21 códigos `l1_code` TPU e os 5 temas
  transversais. Consulte ao usar `buscar_por_ontologia`.
- **`references/campos-e-trust.md`** - taxonomia canônica das qualificadas
  (`tipo_canonico`/`tipo_label`/`materia`), autoria do acórdão (`redator_acordao`), o
  envelope `trust` e a vigência (`status_vigencia`). Consulte ao rotular/citar.

## Método do pesquisador (5 passos)

Uma pesquisa jurídica de verdade quase nunca é UMA chamada: é uma varredura que
escala de modalidade até a cobertura estabilizar. Numa tarefa grande (dossiê,
multi-busca), delegue ao subagente `pesquisador-juris` (ver seção Subagentes); num
cliente sem subagentes, conduza-a você mesmo, nesta ordem.

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
   Num lote grande de citações, delegue essa passada ao subagente `conferente-citacoes`.

## Como citar (obrigatório)

- **Sempre** cite o campo `link_completo` do registro retornado - é a URL estável,
  deep-per-record, do acórdão na fonte oficial. **Nunca invente** número de processo,
  ementa ou link.
- Cite `tribunal`, `numero_processo` (ou `numero_processo_cnj`), `relator` e
  `data_julgamento` quando presentes. Resuma a `ementa_snippet` em 1-2 frases.
- Quando útil, mostre a classificação OJBU (`classificacao.l1`, `ramo_l1_codes`) e
  ofereça abrir o inteiro teor pelo `inteiro_teor_url`.
- Em **acórdãos**, atribua a autoria ao **`redator_acordao`** (o redator - autor do
  acórdão pelo art. 941 do CPC) e ao `revisor` quando vier; nas **qualificadas** apresente
  `tipo_label` (o tipo) e `materia` (a matéria/ramo). Detalhe em `references/campos-e-trust.md`.
- **Confira a vigência antes de amparar:** ao citar súmula/tema/qualificada, verifique
  `status_vigencia` (no hit `trust` ou em `buscar_qualificada`) e sinalize
  explicitamente quando o ato estiver cancelado/superado - nunca o apresente como vigente.
- Se a busca **não** retornar resultado relevante (`total: 0` ou hits fracos),
  **diga isso honestamente** - não preencha a lacuna com um precedente fabricado.

## Subagentes IAJUS (Claude Code)

No **Claude Code**, uma tarefa grande de pesquisa ganha em delegar a subagentes
especializados (invoque cada um via Task/subagent pelo nome). Em clientes **sem
subagentes** (claude.ai web, ChatGPT, Codex), **execute você mesmo o método acima** -
não delegue.

- **`pesquisador-juris`** - tarefa multi-busca ou dossiê: conduz a varredura completa
  (escalonamento de modalidade + envelope de honestidade) e devolve os julgados citáveis.
- **`elaborador-tese`** - construir uma tese jurídica (enunciado + cadeia de fundamentos
  com qualificada vigente, norma e precedentes, com solidez honesta).
- **`refutador-tese`** - stress-test / contraditório de uma tese (jurisprudência contrária
  real, distinguishing, sinais de superação).
- **`precedentes-vinculantes`** - mapear o precedente qualificado aplicável (súmula, SV,
  RG, repetitivo, IRDR/IRR/IAC, OJ) com vigência conferida.
- **`processo-juris`** - pesquisa amarrada a um processo (dossiê a partir do número CNJ).
- **`legislacao-juris`** - norma aplicável (federal, estadual ou municipal), vigência e
  grafo de alterações.
- **`memorialista-juris`** - montar memorial de pesquisa, parecer ou peça com citação
  verificável de cada tese.
- **`conferente-citacoes`** - **SEMPRE feche uma entrega citável com ele** (anti-alucinação):
  confere cada citação de acórdão/norma contra a fonte antes do texto final.

## Boas práticas

- Comece restrito (tema + tribunal + faixa de ano) e amplie só se vier vazio.
- Para "qual o entendimento atual", prefira **precedentes qualificados** (súmula /
  repercussão geral / tema repetitivo) a um acórdão isolado: use `buscar_qualificada`
  e/ou `buscar_por_citacoes` para ver quem o aplicou.
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
  `buscar_regex`, `buscar_por_ontologia`…) são **refinamento**: acione-as
  quando a busca direta vier fraca ou a pergunta pedir facet/forma literal.
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
