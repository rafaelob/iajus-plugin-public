---
name: jurimetria-judicial
description: 'Responde perguntas QUANTITATIVAS sobre a jurisprudência brasileira pelo MCP IAJUS: volume de julgados, ranking de relator/classe/câmara, taxa de desfecho (provimento/improvimento) com denominador duplo, lag de publicação e cruzamentos, sempre com envelope de honestidade e as_of. Acione para "quantas decisões o TJRJ julgou por ano", "quem mais relata no STJ", "qual a taxa de provimento", "quanto leva para publicar". NÃO use para achar/citar um acórdão (use pesquisar-jurisprudencia) nem leis.'
allowed-tools: mcp__iajus__jurimetria_volume, mcp__plugin_iajus-juris_iajus__jurimetria_volume, mcp__iajus__jurimetria_relator, mcp__plugin_iajus-juris_iajus__jurimetria_relator, mcp__iajus__jurimetria_classe, mcp__plugin_iajus-juris_iajus__jurimetria_classe, mcp__iajus__jurimetria_orgao_julgador, mcp__plugin_iajus-juris_iajus__jurimetria_orgao_julgador, mcp__iajus__jurimetria_resultado, mcp__plugin_iajus-juris_iajus__jurimetria_resultado, mcp__iajus__jurimetria_lag_publicacao, mcp__plugin_iajus-juris_iajus__jurimetria_lag_publicacao, mcp__iajus__jurimetria_desfecho_cruzado, mcp__plugin_iajus-juris_iajus__jurimetria_desfecho_cruzado, mcp__iajus__obter_estatisticas_base, mcp__plugin_iajus-juris_iajus__obter_estatisticas_base, mcp__iajus__obter_protocolo_classificacao, mcp__plugin_iajus-juris_iajus__obter_protocolo_classificacao
---

# Jurimetria judicial (números exatos do corpus IAJUS)

Você tem acesso ao servidor MCP `iajus` para responder perguntas **QUANTITATIVAS** sobre a
jurisprudência brasileira - volume, ranking, taxa de desfecho, lag de publicação e
cruzamentos - com **contagens EXATAS** do read-model agregado do Postgres (rollups do
projector, nunca varredura da tabela quente). **Use estas tools para números - nunca
estime, arredonde para impressionar nem infira uma taxa a partir de contagens de volume.**

Acione esta skill para "quantas decisões o TJRJ julgou por ano", "quem mais relata no
STJ", "quais classes/câmaras dominam", "qual a taxa de provimento do STJ", "o TJRJ provê
mais agravos que apelações", "quanto o tribunal leva para publicar após julgar". Para
**achar e citar** um acórdão específico, use `pesquisar-jurisprudencia`; para o **panorama
de cobertura** ("o que tem na base?"), use `corpus-status`.

## Qual tool para qual pergunta

| Pergunta | Tool |
|---|---|
| Volume de julgados por órgão × ano ("quantas decisões o TJRJ julgou em 2024", série anual) | `jurimetria_volume` |
| Quem mais relata num órgão (+ período de atuação) | `jurimetria_relator` |
| Volume por classe processual CNJ de um órgão | `jurimetria_classe` |
| Volume por câmara/turma/seção de um órgão | `jurimetria_orgao_julgador` |
| Taxa de desfecho (provimento/improvimento) por órgão × ano | `jurimetria_resultado` |
| Lag de publicação (dias entre julgar e publicar, p50/p90) | `jurimetria_lag_publicacao` |
| Taxa de desfecho CRUZADA por um segundo eixo (classe, relator, câmara) | `jurimetria_desfecho_cruzado` |
| Panorama geral do corpus (o que existe por acervo/tribunal) antes do recorte fino | `obter_estatisticas_base` |
| Rótulo de um código de classe CNJ que apareceu no resultado | `obter_protocolo_classificacao` |

Todas as tools `jurimetria_*` exigem um **recorte** (`orgao` e/ou `ano` - regra
scan-safety) e devolvem o **envelope de honestidade** `jurimetria` `{snapshot_id, as_of,
denominator_definition, value_kind, coverage_pct, truncado}`. As assinaturas exatas de
cada tool vivem em **`references/jurimetria.md`** - consulte-a para os parâmetros, os
defaults de `top` e o comportamento de cada rollup.

## Método (números honestos)

Chamadas independentes de recortes diferentes rodam em paralelo; feche sempre com o
denominador e o `as_of`.

1. **Comece pelo recorte certo.** Toda tool `jurimetria_*` exige `orgao` e/ou `ano`. Se o
   usuário não deu um recorte, peça (a tool recusa sem ele) - não varra a base inteira.
   Precisa saber que órgãos/anos existem primeiro? `obter_estatisticas_base` dá o panorama.
2. **Escolha a tool pela grandeza** (tabela acima): volume ≠ taxa ≠ lag. Nunca infira uma
   taxa de desfecho a partir de contagens de volume - essa é a `jurimetria_resultado`.
3. **Reporte com o envelope de honestidade.** Toda taxa vai com o **denominador** (a
   `jurimetria_resultado` traz duplo: `share_over_known` e `share_over_all`) e o
   `coverage_pct`. Cobertura baixa não é representativa; diga "baixa cobertura", não uma
   taxa nua. Cite o `as_of` quando o número embasar uma afirmação.
4. **Respeite a supressão LGPD** (figura nominal ou célula cruzada com n<20 é suprimida) -
   não contorne nem estime o valor suprimido.

## Regras de honestidade (obrigatório)

- **Taxa de desfecho SÓ via `jurimetria_resultado` / `jurimetria_desfecho_cruzado`**,
  sempre com denominador duplo e `coverage_pct`. Cobertura baixa = "baixa cobertura", não
  uma taxa nua (o extractor abstém em dispositivo ambíguo; abstenção não é zero).
- **Lag de publicação NÃO é duração do processo** - é só o intervalo publicação−julgamento.
  Sem cobertura / órgão sem `data_publicacao` → "sem cobertura", não estime.
- **`total: 0` ou "sem cobertura" ≠ zero no mundo real** - é cobertura em andamento; avise
  o usuário, não afirme ausência de julgados.
- **Reporte o `as_of`** (data do snapshot) quando o número embasar uma afirmação.
- **LGPD n<20** suprime figura nominal / célula cruzada - respeite a supressão.
- Sem recorte (`orgao`/`ano`) a tool recusa com erro - não insista; peça o recorte.

Detalhe completo (assinaturas + cada regra por tool) em **`references/jurimetria.md`**.

## Subagentes IAJUS (Claude Code)

No **Claude Code**, uma pergunta quantitativa dentro de uma tarefa maior de pesquisa é
conduzida pelo subagente **`pesquisador-juris`**, que usa estas tools `jurimetria_*`
diretamente com o envelope de honestidade. Invoque-o via Task/subagent pelo nome quando a
jurimetria for parte de um dossiê; para uma pergunta quantitativa pontual, execute as
tools você mesmo. Em clientes **sem subagentes** (claude.ai web, ChatGPT, Codex), execute
a jurimetria você mesmo pelo método acima.

**Autenticação:** o cliente MCP autentica por você - via **login OAuth** (claude.ai /
ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
ausente ou expirada - peça ao usuário para refazer o login ou revisar a chave; **nunca**
cole a chave em chat nem em commit.
