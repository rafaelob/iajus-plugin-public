---
name: consultar-legislacao
description: 'Consulta legislação FEDERAL brasileira real (leis, decretos, MPVs, LCs, emendas) pelo MCP IAJUS: texto íntegra do Planalto, resolução por tipo/número/ano, busca por tema, vigência (vigente/revogada) e alterações artigo por artigo. Acione quando o usuário pedir o texto de uma lei federal, um artigo ou a redação vigente de um dispositivo, ou perguntar "qual lei federal regula Y", "o que diz o art. X da lei Z". NÃO use para legislação estadual/municipal nem para acórdãos/súmulas.'
allowed-tools: mcp__iajus__consultar_legislacao_federal, mcp__plugin_iajus-juris_iajus__consultar_legislacao_federal, mcp__iajus__buscar_legislacao_federal, mcp__plugin_iajus-juris_iajus__buscar_legislacao_federal, mcp__iajus__listar_legislacao_federal, mcp__plugin_iajus-juris_iajus__listar_legislacao_federal, mcp__iajus__obter_texto_legislacao, mcp__plugin_iajus-juris_iajus__obter_texto_legislacao, mcp__iajus__obter_texto_legislacao_markdown, mcp__plugin_iajus-juris_iajus__obter_texto_legislacao_markdown, mcp__iajus__obter_alteracoes_legislacao, mcp__plugin_iajus-juris_iajus__obter_alteracoes_legislacao, mcp__iajus__consultar_grafo_legislacao, mcp__plugin_iajus-juris_iajus__consultar_grafo_legislacao, mcp__iajus__ler_dispositivo_legal, mcp__plugin_iajus-juris_iajus__ler_dispositivo_legal, mcp__iajus__pesquisar_legislacao, mcp__plugin_iajus-juris_iajus__pesquisar_legislacao, mcp__iajus__buscar_semantica, mcp__plugin_iajus-juris_iajus__buscar_semantica, mcp__iajus__buscar_fts, mcp__plugin_iajus-juris_iajus__buscar_fts, mcp__iajus__buscar_por_ontologia, mcp__plugin_iajus-juris_iajus__buscar_por_ontologia, mcp__iajus__buscar_hibrida, mcp__plugin_iajus-juris_iajus__buscar_hibrida, mcp__iajus__consultar_ontologia_juridica, mcp__plugin_iajus-juris_iajus__consultar_ontologia_juridica, mcp__iajus__classificar_norma, mcp__plugin_iajus-juris_iajus__classificar_norma, mcp__iajus__buscar_norma_por_nome, mcp__plugin_iajus-juris_iajus__buscar_norma_por_nome, mcp__iajus__buscar_norma_por_numero, mcp__plugin_iajus-juris_iajus__buscar_norma_por_numero, mcp__iajus__consultar_protocolo_classificacao, mcp__plugin_iajus-juris_iajus__consultar_protocolo_classificacao
---

# Consultar legislação federal brasileira (IAJUS)

Você tem acesso ao servidor MCP `iajus` para **legislação federal** brasileira (leis,
decretos, MPVs, leis complementares, emendas constitucionais, decretos-lei), com **texto
íntegra do Planalto** e rastreamento de alterações artigo por artigo via Senado. Use o
MCP em vez de citar de memória: **o texto da fonte oficial é a verdade.**

> **Corpus VIVO e em crescimento:** a base de legislação é ingerida continuamente —
> normas e dispositivos novos aparecem automaticamente, sem mudança de skill. Um
> `total: 0` para uma norma que deveria existir pode ser **cobertura em andamento**,
> não inexistência — diga isso honestamente e não invente a norma. Para ver o que a
> base de legislação contém AGORA (por esfera/status + cobertura de indexação), use a
> skill **corpus-status** (`estatisticas_da_base`).

## Como consultar — escolha o caminho pela pergunta

| Necessidade | Tool | Como |
|---|---|---|
| Já sei a norma (tipo + número + ano) e quero metadados + link oficial | `consultar_legislacao_federal` | Resolve por `tipo` (LEI, DECRETO, LC, MPV, EC…), `numero`, `ano`. Retorna ementa, data, `link_completo` oficial. |
| Quero o **texto íntegra** de uma norma ou de um artigo | `obter_texto_legislacao` / `obter_texto_legislacao_markdown` | Texto pleno; a variante `_markdown` traz a estrutura hierárquica (Título/Capítulo/Art./§). |
| Quero a redação de UM dispositivo específico ("art. 5º, II") | `ler_dispositivo_legal` | Isola o dispositivo (artigo, parágrafo, inciso) com a redação vigente. |
| Não sei o número — busca por nome/tema ("CDC", "Lei Geral de Proteção de Dados") | `buscar_legislacao_federal` | Busca por termo/tema na legislação federal. |
| Tenho o **nome/apelido** da norma e quero a norma + **vigência** ("CLT", "Código de Defesa do Consumidor") | `buscar_norma_por_nome` | Resolve a norma pelo nome/apelido e devolve o **`status`** (vigente / revogada). **Para amparo, sirva só `vigente`.** |
| Tenho **tipo + número** e quero a norma + **vigência** ("Lei 8078", "LC 95") | `buscar_norma_por_numero` | Resolve por `tipo` + `numero` (+ `ano`) e devolve o **`status`** (vigente / revogada). |
| Listar normas de um tipo/ano | `listar_legislacao_federal` | Enumera (ex.: leis de 2023). |
| Esse artigo foi **alterado/revogado**? Por qual norma e quando? | `obter_alteracoes_legislacao` | Histórico de alterações artigo por artigo (norma alteradora + data). |
| Quais normas citam/alteram/são citadas por esta; o que mudou **artigo a artigo** | `consultar_grafo_legislacao` | `norma_ref` canônico (ex. `LEI_8112_1990`) + `max_depth` (1-20, padrão 8). Retorna `cadeia_alteracoes` (quem alterou, recursivo), `dead_ends` (revogadas/caducadas alcançáveis), conversão MPV→LEI, `citacoes` (`cita` / `citada_por`) e **`alteracoes_dispositivo`** — eventos por dispositivo ("redação dada por", "revogado por", "regulamentado por") com `dispositivo_ref` + a norma alteradora. |
| Busca conceitual/semântica no corpus de legislação | `buscar_semantica` / `buscar_fts` / `buscar_hibrida` | Passe `family="legislacao"`. Semântica casa por significado; FTS por expressão (use `phrase=true` p/ ordem exata). |
| "Quais leis tratam de um ramo do direito" | `buscar_por_ontologia` | `l1_code` TPU + `family="legislacao"`. Ex.: `l1_code=1156` (Consumidor → CDC), `14` (Tributário), `899` (Civil → CC). |
| Taxonomia OJBU de referência / classificar uma norma | `consultar_ontologia_juridica` / `classificar_norma` | Árvore de ramos (21 L1) e classificação de um texto normativo. |
| Protocolo de decisão CNJ/TPU (como classificar passo a passo) | `consultar_protocolo_classificacao` | Devolve o protocolo de classificação CNJ/TPU para o agente seguir antes de atribuir o ramo/assunto. |

Fluxo recomendado: **resolva ou descubra a norma** (`consultar_legislacao_federal` quando
sabe o número; `buscar_legislacao_federal`/`buscar_semantica` quando só sabe o tema) →
**leia o texto** (`obter_texto_legislacao_markdown` ou `ler_dispositivo_legal` para um
dispositivo) → **cheque vigência** (`obter_alteracoes_legislacao`) antes de afirmar que a
redação está em vigor.

Para a pergunta **"qual a norma chamada X / a Lei nº N, e está em vigor?"** prefira
`buscar_norma_por_nome` (por nome/apelido) e `buscar_norma_por_numero` (por tipo + número):
elas retornam a **vigência (`status`)** junto com a norma e **substituem** os antigos
`pesquisar_*` de lookup. **Para amparo jurídico, sirva apenas o que vier `status=vigente`** e
sinalize explicitamente quando a norma estiver `revogada`.

Vigência e amparo nas buscas (regras do motor):

- **`buscar_hibrida` com `family="legislacao"` serve por padrão só normas em vigor**
  (ou de vigência incerta/parcialmente alterada) e OCULTA as comprovadamente sem
  vigência (revogada, não recepcionada, inconstitucional, vigência esgotada, suspensa).
  Para pesquisa **histórica** (trazer também revogadas), passe `incluir_historico=true`
  — e sinalize o status ao usuário. As demais modalidades não aplicam esse corte:
  cheque `status_vigencia` no hit.
- Cada hit de legislação traz o envelope `trust` `{authority_tier, status_vigencia,
  trecho}` e o flag **`is_amending_only`** (norma que só existe para alterar/revogar
  outra — não a cite como fonte substantiva do direito; cite a norma alterada
  consolidada). Cheque-os antes de citar como amparo.
- Para saber **o que mudou dentro da norma, artigo a artigo**, prefira
  `consultar_grafo_legislacao` (bloco `alteracoes_dispositivo`) e
  `obter_alteracoes_legislacao` — a redação vigente de um dispositivo específico vem de
  `ler_dispositivo_legal`.

## Regras de citação (obrigatório)

- Cite o número da norma, o artigo e a redação **como retornada pela fonte**. Sempre
  cite o `link_completo` (URL oficial do Planalto) e **nunca invente** número de lei,
  redação de artigo ou link.
- Se o dispositivo estiver **revogado/alterado** (visto em `obter_alteracoes_legislacao`
  ou no grafo `altera_norma`), **diga isso explicitamente** — não apresente texto
  revogado como vigente; aponte a norma alteradora e a data.
- Preserve a grafia e os diacríticos exatamente como na fonte (UTF-8).
- Se a norma não estiver na base (ex.: `total: 0`, norma não federal, MPV convertida que
  não resolve), **diga isso** em vez de improvisar.
- A cobertura aqui é **federal**; legislação estadual/municipal tem skill própria
  (`consultar-legislacao-estadual`) e jurisprudência também (`pesquisar-jurisprudencia`).

## Boas práticas

- Para "qual o texto do art. X da lei Y", vá direto a `ler_dispositivo_legal` — é o
  caminho mais preciso para um único dispositivo.
- Para "qual lei regula X" sem saber o nome, comece por `buscar_legislacao_federal` ou
  `buscar_semantica` (`family="legislacao"`); confirme com `consultar_legislacao_federal`.
- Antes de afirmar vigência, rode `obter_alteracoes_legislacao` — leis antigas mudam de
  redação artigo a artigo.
- **Autenticação:** o cliente MCP autentica por você — via **login OAuth** (claude.ai /
  ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
  header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
  ausente ou expirada — peça ao usuário para refazer o login ou revisar a chave; **nunca**
  cole a chave em chat nem em commit.
