---
name: consultar-legislacao
description: Consulta legislação FEDERAL brasileira real (leis, decretos, MPVs, leis complementares, emendas) pelo MCP iaJus, com texto íntegra do Planalto, resolução por tipo/número/ano, busca por tema, grafo de normas e histórico de alterações artigo por artigo. Acione sempre que o usuário pedir o texto de uma lei federal, um artigo específico, a redação vigente de um dispositivo, normas por tema/ramo do direito, ou perguntar "qual lei federal regula Y", "o que diz o art. X da lei Z", "esse dispositivo está em vigor", "esse artigo foi alterado" — em vez de citar de memória. NÃO use para legislação ESTADUAL/MUNICIPAL (skill consultar-legislacao-estadual) nem para precedentes/acórdãos/súmulas (skill pesquisar-jurisprudencia).
allowed-tools: mcp__iajus__consultar_legislacao_federal, mcp__plugin_iajus-juris_iajus__consultar_legislacao_federal, mcp__iajus__buscar_legislacao_federal, mcp__plugin_iajus-juris_iajus__buscar_legislacao_federal, mcp__iajus__listar_legislacao_federal, mcp__plugin_iajus-juris_iajus__listar_legislacao_federal, mcp__iajus__obter_texto_legislacao, mcp__plugin_iajus-juris_iajus__obter_texto_legislacao, mcp__iajus__obter_texto_legislacao_markdown, mcp__plugin_iajus-juris_iajus__obter_texto_legislacao_markdown, mcp__iajus__obter_alteracoes_legislacao, mcp__plugin_iajus-juris_iajus__obter_alteracoes_legislacao, mcp__iajus__consultar_grafo_legislacao, mcp__plugin_iajus-juris_iajus__consultar_grafo_legislacao, mcp__iajus__ler_dispositivo_legal, mcp__plugin_iajus-juris_iajus__ler_dispositivo_legal, mcp__iajus__pesquisar_legislacao, mcp__plugin_iajus-juris_iajus__pesquisar_legislacao, mcp__iajus__buscar_semantica, mcp__plugin_iajus-juris_iajus__buscar_semantica, mcp__iajus__buscar_fts, mcp__plugin_iajus-juris_iajus__buscar_fts, mcp__iajus__buscar_por_ontologia, mcp__plugin_iajus-juris_iajus__buscar_por_ontologia, mcp__iajus__buscar_hibrida, mcp__plugin_iajus-juris_iajus__buscar_hibrida, mcp__iajus__consultar_ontologia_juridica, mcp__plugin_iajus-juris_iajus__consultar_ontologia_juridica, mcp__iajus__classificar_norma, mcp__plugin_iajus-juris_iajus__classificar_norma
---

# Consultar legislação federal brasileira (iaJus)

Você tem acesso ao servidor MCP `iajus` para **legislação federal** brasileira (leis,
decretos, MPVs, leis complementares, emendas constitucionais, decretos-lei), com **texto
íntegra do Planalto** e rastreamento de alterações artigo por artigo via Senado. Use o
MCP em vez de citar de memória: **o texto da fonte oficial é a verdade.**

## Como consultar — escolha o caminho pela pergunta

| Necessidade | Tool | Como |
|---|---|---|
| Já sei a norma (tipo + número + ano) e quero metadados + link oficial | `consultar_legislacao_federal` | Resolve por `tipo` (LEI, DECRETO, LC, MPV, EC…), `numero`, `ano`. Retorna ementa, data, `link_completo` oficial. |
| Quero o **texto íntegra** de uma norma ou de um artigo | `obter_texto_legislacao` / `obter_texto_legislacao_markdown` | Texto pleno; a variante `_markdown` traz a estrutura hierárquica (Título/Capítulo/Art./§). |
| Quero a redação de UM dispositivo específico ("art. 5º, II") | `ler_dispositivo_legal` | Isola o dispositivo (artigo, parágrafo, inciso) com a redação vigente. |
| Não sei o número — busca por nome/tema ("CDC", "Lei Geral de Proteção de Dados") | `buscar_legislacao_federal` | Busca por termo/tema na legislação federal. |
| Listar normas de um tipo/ano | `listar_legislacao_federal` | Enumera (ex.: leis de 2023). |
| Esse artigo foi **alterado/revogado**? Por qual norma e quando? | `obter_alteracoes_legislacao` | Histórico de alterações artigo por artigo (norma alteradora + data). |
| Quais normas citam/alteram/são citadas por esta | `consultar_grafo_legislacao` | Relações entre normas (`cita_legislacao`, `altera_norma`). |
| Busca conceitual/semântica no corpus de legislação | `buscar_semantica` / `buscar_fts` / `buscar_hibrida` | Passe `family="legislacao"`. Semântica casa por significado; FTS por expressão (use `phrase=true` p/ ordem exata). |
| "Quais leis tratam de um ramo do direito" | `buscar_por_ontologia` | `l1_code` TPU + `family="legislacao"`. Ex.: `l1_code=1156` (Consumidor → CDC), `14` (Tributário), `899` (Civil → CC). |
| Taxonomia OJBU de referência / classificar uma norma | `consultar_ontologia_juridica` / `classificar_norma` | Árvore de ramos (21 L1) e classificação de um texto normativo. |

Fluxo recomendado: **resolva ou descubra a norma** (`consultar_legislacao_federal` quando
sabe o número; `buscar_legislacao_federal`/`buscar_semantica` quando só sabe o tema) →
**leia o texto** (`obter_texto_legislacao_markdown` ou `ler_dispositivo_legal` para um
dispositivo) → **cheque vigência** (`obter_alteracoes_legislacao`) antes de afirmar que a
redação está em vigor.

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
