---
name: consultar-legislacao
description: 'Consulta legislação FEDERAL brasileira (leis, decretos, MPVs, LCs, emendas) pelo MCP IAJUS: texto e referências da fonte oficial quando disponíveis, resolução por tipo/número/ano, busca por tema, vigência (vigente/revogada) e alterações por artigo. Acione quando o usuário pedir o texto de uma lei federal, um artigo ou a redação vigente de um dispositivo, ou perguntar "qual lei federal regula Y", "o que diz o art. X da lei Z". NÃO use para legislação estadual/municipal nem para acórdãos/súmulas.'
---

# Consultar legislação federal brasileira (IAJUS)

Você tem acesso ao servidor MCP `iajus` para **legislação federal** brasileira (leis,
decretos, MPVs, leis complementares, emendas constitucionais, decretos-lei), com **texto
íntegra do Planalto** e rastreamento de alterações artigo por artigo via Senado. Use o
MCP em vez de citar de memória: **o texto da fonte oficial é a verdade.**

> **Corpus VIVO e em crescimento:** a base de legislação é ingerida continuamente -
> normas e dispositivos novos aparecem automaticamente, sem mudança de skill.
> Leia `desfecho` ANTES de qualquer contagem: `erro`/`nao_terminou`/`medida_indisponivel` = a consulta
> não mediu; só `sem_resultado` é zero MEDIDO. Para ver o que a base contém AGORA,
> use a skill **corpus-status** (`obter_estatisticas_base`).

## Envelope de desfecho

Leia a chave `desfecho` ANTES de qualquer contagem. Os cinco valores são mutuamente exclusivos:

- `erro` - a consulta FALHOU; ninguém olhou o acervo. Não é ausência.
- `sem_resultado` - a consulta RODOU e o acervo não tem. Zero MEDIDO.
- `nao_terminou` - timeout ou teto. NÃO-MEDIDO; não afirme que «não existe».
- `parcial` - mediu uma parte; declare o que ficou de fora.
- `medida_indisponivel` - a fonte respondeu e NÃO carrega a medida. NÃO-MEDIDO sem avaria; não é zero.

`total: 0` só é ausência medida quando `desfecho` é `sem_resultado`. Sem `desfecho`, ou com `erro`/`nao_terminou`/`medida_indisponivel`, diga que a consulta não mediu.

## Como consultar - escolha o caminho pela pergunta

| Necessidade | Tool | Como |
|---|---|---|
| Já sei a norma (tipo + número + ano) e quero metadados + link oficial | `buscar_norma_fonte_oficial` | Resolve por `tipo` (LEI, DECRETO, LC, MPV, EC…), `numero`, `ano`. Retorna ementa, data, `link_completo` oficial. |
| Quero o **texto íntegra** de uma norma ou de um artigo | `obter_texto_norma` | Texto pleno; o parâmetro `markdown` traz a estrutura hierárquica (Título/Capítulo/Art./§). |
| Quero a redação de UM dispositivo específico ("art. 5º, II") | `obter_dispositivo_legal` | Isola o dispositivo (artigo, parágrafo, inciso) com a redação vigente. |
| Não sei o número - busca por nome/tema ("CDC", "Lei Geral de Proteção de Dados") | `buscar_norma_fonte_oficial` | Busca por termo/tema na legislação federal. |
| Tenho o **nome/apelido** da norma e quero a norma + **vigência** ("CLT", "Código de Defesa do Consumidor") | `buscar_norma_por_nome` | Resolve a norma pelo nome/apelido e devolve o **`status`** (vigente / revogada). **Para amparo, sirva só `vigente`.** |
| Tenho **tipo + número** e quero a norma + **vigência** ("Lei 8078", "LC 95") | `buscar_norma_por_numero` | Resolve por `tipo` + `numero` (+ `ano`) e devolve o **`status`** (vigente / revogada). |
| Listar normas de um tipo/ano | `listar_normas` | Enumera (ex.: leis de 2023). |
| Esse artigo foi **alterado/revogado**? O que mudou artigo a artigo? | `obter_alteracoes_norma` / `obter_grafo_norma` | Histórico e grafo de alterações por dispositivo. Detalhe: `references/grafo-alteracoes.md`. |
| Busca conceitual/semântica no corpus de legislação | `buscar_semantica` / `buscar_fts` / `buscar_hibrida` | Passe `family="legislacao"`. Semântica casa por significado; FTS por expressão (use `phrase=true` p/ ordem exata). |
| "Quais leis tratam de um ramo do direito" | `buscar_por_ontologia` | `l1_code` TPU + `family="legislacao"`. Ex.: `l1_code=1156` (Consumidor → CDC), `14` (Tributário), `899` (Civil → CC). |
| Taxonomia OJBU de referência / classificar uma norma | `obter_ontologia_juridica` / `obter_classificacao_tipo` | Árvore de ramos (21 L1) e classificação de um texto normativo. |
| Protocolo de decisão CNJ/TPU (como classificar passo a passo) | `obter_protocolo_classificacao` | Devolve o protocolo de classificação CNJ/TPU para o agente seguir antes de atribuir o ramo/assunto. |

Fluxo recomendado: **resolva ou descubra a norma** (`buscar_norma_fonte_oficial` quando
sabe o número; `buscar_norma_fonte_oficial`/`buscar_semantica` quando só sabe o tema) →
**leia o texto** (`obter_texto_norma` ou `obter_dispositivo_legal` para um
dispositivo) → **cheque vigência** (`obter_alteracoes_norma`) antes de afirmar que a
redação está em vigor.

Para a pergunta **"qual a norma chamada X / a Lei nº N, e está em vigor?"** prefira
`buscar_norma_por_nome` (por nome/apelido) e `buscar_norma_por_numero` (por tipo + número):
elas retornam a **vigência (`status`)** junto com a norma e **substituem** os antigos
`pesquisar_*` de lookup. **Para amparo jurídico, sirva apenas o que vier `status=vigente`** e
sinalize explicitamente quando a norma estiver `revogada`.

Vigência e amparo nas buscas (síntese - detalhe em `references/grafo-alteracoes.md`):

- **`buscar_hibrida` com `family="legislacao"` serve por padrão só normas em vigor**
  (ou de vigência incerta/parcialmente alterada) e OCULTA as comprovadamente sem
  vigência (revogada, não recepcionada, inconstitucional, vigência esgotada, suspensa).
  Para pesquisa **histórica** (trazer também revogadas), passe `incluir_historico=true`
  - e sinalize o status ao usuário. As demais modalidades não aplicam esse corte:
  cheque `status_vigencia` no hit.
- Cada hit de legislação traz o envelope `trust` `{authority_tier, status_vigencia,
  trecho}` e o flag **`is_amending_only`** (norma que só existe para alterar/revogar
  outra - não a cite como fonte substantiva do direito; cite a norma alterada
  consolidada). Cheque-os antes de citar como amparo.

## Referência detalhada

- **`references/grafo-alteracoes.md`** - a modelagem de alterações artigo por artigo
  (§11): `obter_alteracoes_norma`, o grafo completo de `obter_grafo_norma`
  (`cadeia_alteracoes`, `dead_ends`, conversão MPV→LEI, `citacoes`,
  `alteracoes_dispositivo`) e a regra dura de vigência/amparo (`is_amending_only`,
  redação vigente por `obter_dispositivo_legal`). Consulte para "esse artigo foi
  alterado/revogado?" e "o que mudou dentro da norma?".

## Método do especialista em legislação

Legislação **não tem piso temporal**: toda norma recepcionada pela CF-1988 e vigente está
em escopo, de qualquer ano (um decreto-lei dos anos 1950 ainda em vigor está em escopo). A
vigência decide-se pelo **status da fonte** (`status`/`status_vigencia`), NUNCA pelo ano.
Não presuma que uma norma antiga "não existe na base" por ser antiga. No Claude Code,
numa tarefa grande (dossiê normativo, parecer que amarra várias normas), delegue ao
subagente `legislacao-juris` (ver Subagentes); nos demais clientes, execute você mesmo:

1. **Nome ou número → texto vigente.** Se o usuário dá o **apelido** ("CLT", "CDC", "Lei
   Maria da Penha"), resolva por `buscar_norma_por_nome`; se dá **tipo + número**
   ("Lei 8.078", "LC 95"), por `buscar_norma_por_numero`. Ambas devolvem a norma com o
   **`status`** (vigente / revogada). Não sabe o número, só o tema? Comece por
   `buscar_norma_fonte_oficial` (por termo) ou `buscar_semantica`/`buscar_hibrida`
   (`family="legislacao"`). Depois leia o texto vigente consolidado com `obter_texto_norma`.
2. **Dispositivo específico.** Para "o que diz o art. X" vá direto a
   `obter_dispositivo_legal` (isola artigo/parágrafo/inciso com a redação vigente) - é o
   caminho mais preciso para um único dispositivo. Para varrer os dispositivos de uma norma
   por tema/termo, use `buscar_dispositivos` (grão dispositivo).
3. **Vigência e cadeia de alterações (o coração do trabalho).** Antes de afirmar que uma
   redação está em vigor, rode `obter_alteracoes_norma` e, para o mapa completo,
   `obter_grafo_norma` (bloco `alteracoes_dispositivo`). **Para amparo, sirva só
   `status=vigente`**; sinalize `revogada`/`nao_recepcionada`; norma `is_amending_only`
   não é fonte substantiva. Detalhe completo em `references/grafo-alteracoes.md`.
4. **Verificação na fonte oficial quando load-bearing.** Quando o número/data/ementa de uma
   norma sustentam a resposta, confirme os metadados com `buscar_norma_fonte_oficial`
   (resolve por `tipo`/`numero`/`ano`, devolve ementa, data e `link_completo` oficial do
   Planalto) antes de citar - assim você ancora a citação na fonte, não na memória.

## Regras de citação (obrigatório)

- Cite o número da norma, o artigo e a redação **como retornada pela fonte**. Sempre
  cite o `link_completo` (URL oficial do Planalto) e **nunca invente** número de lei,
  redação de artigo ou link.
- Se o dispositivo estiver **revogado/alterado** (visto em `obter_alteracoes_norma`
  ou no grafo `altera_norma`), **diga isso explicitamente** - não apresente texto
  revogado como vigente; aponte a norma alteradora e a data.
- Preserve a grafia e os diacríticos exatamente como na fonte (UTF-8).
- Se `desfecho` for `sem_resultado` (consulta rodou e não achou), ou a norma não for
  federal / MPV convertida que não resolve, **diga isso**. Se for `erro` ou
  `nao_terminou`, reporte a falha - não «não está na base».
- A cobertura aqui é **federal**; legislação estadual/municipal tem skill própria
  (`consultar-legislacao-estadual`) e jurisprudência também (`pesquisar-jurisprudencia`).

## Subagentes IAJUS (Claude Code)

No **Claude Code**, delegue uma tarefa normativa grande a subagentes especializados.
Cada um se chama `iajus-juris:<nome>` e também atende por `@agent-iajus-juris:<nome>`.
Nos demais clientes (claude.ai, Cowork, ChatGPT, Codex), **execute você mesmo o método
acima**, sem delegar.

- **`legislacao-juris`** - norma aplicável (federal, estadual ou municipal), vigência
  (`status_vigencia`) e grafo de alterações por dispositivo, num só dossiê normativo.
- **`memorialista-juris`** - montar parecer ou peça que amarra várias normas + jurisprudência,
  com citação verificável de cada fundamento.
- **`conferente-citacoes`** - **feche a entrega com ele** (anti-alucinação): confere cada
  artigo/norma citado contra a fonte, incluindo se a redação bate e se o dispositivo está
  vigente, antes do texto final.

## Boas práticas

- Para "qual o texto do art. X da lei Y", vá direto a `obter_dispositivo_legal` - é o
  caminho mais preciso para um único dispositivo.
- Para "qual lei regula X" sem saber o nome, comece por `buscar_norma_fonte_oficial` ou
  `buscar_semantica` (`family="legislacao"`); confirme com `buscar_norma_fonte_oficial`.
- Antes de afirmar vigência, rode `obter_alteracoes_norma` - leis antigas mudam de
  redação artigo a artigo.
- **Autenticação:** o cliente MCP autentica por você - via **login OAuth** (claude.ai /
  ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
  header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
  ausente ou expirada - peça ao usuário para refazer o login ou revisar a chave; **nunca**
  cole a chave em chat nem em commit.
