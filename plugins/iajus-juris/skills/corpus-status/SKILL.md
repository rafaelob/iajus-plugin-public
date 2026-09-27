---
name: corpus-status
description: 'Mostra o que o read-model do corpus IAJUS reporta via MCP, sempre com o as_of de cada seção: decisões por tribunal e faixa de anos, legislação por esfera/status e território, doutrina e qualificadas por espécie/vigência. Acione em "o que tem na base?", "quantos acórdãos do TJRJ?", "cobrimos 2023-2026 do tribunal X?". Não estima nem extrapola contagens.'
---

# Estado do corpus IAJUS conforme o `as_of`

Você tem acesso ao servidor MCP `iajus`, que serve a tool **`obter_estatisticas_base`**:
uma introspecção do **read-model Postgres** que as buscas consultam. Cada seção informa
`as_of`: um timestamp quando vem de rollup, ou `"live"` quando foi agregada na chamada.
Use esse campo para dizer de quando é cada número.

> **O corpus muda continuamente.** Leia `desfecho` ANTES de qualquer contagem:
> `erro`/`nao_terminou`/`medida_indisponivel` = a consulta não mediu; só `sem_resultado` (ou um `total`
> sem `desfecho` de falha) descreve o recorte no `as_of`. Sem outro campo
> autoritativo, não conclua ausência na fonte, ingestão pendente ou cobertura incompleta.

## Envelope de desfecho

Leia a chave `desfecho` ANTES de qualquer contagem. Os cinco valores são mutuamente exclusivos:

- `erro` - a consulta FALHOU; ninguém olhou o acervo. Não é ausência.
- `sem_resultado` - a consulta RODOU e o acervo não tem. Zero MEDIDO.
- `nao_terminou` - timeout ou teto. NÃO-MEDIDO; não afirme que «não existe».
- `parcial` - mediu uma parte; declare o que ficou de fora.
- `medida_indisponivel` - a fonte respondeu e NÃO carrega a medida. NÃO-MEDIDO sem avaria; não é zero.

`total: 0` só é ausência medida quando `desfecho` é `sem_resultado`. Sem `desfecho`, ou com `erro`/`nao_terminou`/`medida_indisponivel`, diga que a consulta não mediu.

## Quando usar

Acione `obter_estatisticas_base` quando o usuário perguntar coisas como:

- "**o que tem na base?**", "qual a cobertura atual do IAJUS?";
- "**quantos acórdãos do TJRJ** (ou de qualquer tribunal) existem?";
- "**cobrimos 2023-2026** do tribunal X?", "de que ano a que ano vai o STJ?";
- "quantas **súmulas / temas de repercussão geral / IRDR** existem, e quantos estão
  vigentes?";
- "quantas normas de **legislação** por esfera (federal/estadual/municipal) e por
  status (vigente/revogada)?", "**quantos estados e municípios** a base cobre?".

Para o **texto** de um acórdão/lei ou para **pesquisar** conteúdo, use as outras skills
(`pesquisar-jurisprudencia`, `consultar-legislacao`, `consultar-legislacao-estadual`).
Esta skill é só para o **panorama quantitativo / de cobertura** do corpus.

## Qual tool de estatística para qual pergunta

`obter_estatisticas_base` é a porta de entrada - o panorama geral do read-model. Há também
uma tool irmã, exata, para um recorte específico (servida pelo mesmo MCP; a skill que a
detalha está entre parênteses):

| Pergunta | Tool | Onde detalhada |
|---|---|---|
| "o que tem na base?", cobertura por acervo/tribunal/qualificada/legislação, **volume por tribunal e faixa de anos** (contagem do read-model, com `as_of`) | `obter_estatisticas_base` | esta skill |
| mapa de cobertura de legislação estadual/municipal por UF (estado + municípios + prontidão) | `obter_cobertura_legislacao` | `consultar-legislacao-estadual` |

Regra prática: `obter_estatisticas_base` responde "o que existe na base, por acervo" e
também o **volume por tribunal e a faixa de anos coberta** (`secao="orgaos"`); para
**prontidão de legislação por UF**, `obter_cobertura_legislacao`. Não estenda esta skill
para juris fina ou legislação por UF - aponte para a skill irmã.

## A tool

`obter_estatisticas_base` retorna JSON e aceita:

| Argumento | Valores | Efeito |
|---|---|---|
| `secao` | `tudo` (padrão), `familias`, `orgaos`, `qualificadas`, `legislacao` | Que parte do panorama retornar. Comece por `tudo` para uma visão geral. (`secao="orgaos"` retorna a lista de **tribunais**.) |
| `family` | `jurisprudencia`, `doutrina`, `legislacao` | Escopa **só a lista de tribunais** a um acervo. |
| `top` | inteiro 1-500 (padrão 60) | Limita a lista de **tribunais** (ordenada por volume decrescente). |

O que cada seção traz (chaves do payload):

- **`familias`** - por acervo: jurisprudência traz **`decisoes`** e **`tribunais`**;
  legislação traz **`normas`**; doutrina traz **`artigos_livros_doutrina`**. Todos com
  `ano_min`/`ano_max`.
- **`tribunais`** (resposta de `secao="orgaos"`) - por tribunal (top N por volume):
  `tribunal`, `decisoes`, `ano_min`/`ano_max` (a **faixa de anos coberta** daquele
  tribunal). É a seção para "quantos acórdãos do tribunal X" e "cobrimos 2023-2026?".
- **`qualificadas`** - por **`especie`** (súmula, SV, tema RG, IRDR, IAC…):
  `quantidade`, **`vigentes`** e **`canceladas`**, e nº de `tribunais`.
- **`legislacao`** - normas **por esfera** (federal/estadual/municipal/…), **por
  status de vigência** (vigente/revogada/desconhecida) e a **`cobertura_territorial`**:
  União (normas federais), **`estados_df`** e **`municipios`** cobertos.
- **`as_of`** - timestamp do rollup ou `"live"` quando a seção foi agregada na chamada.

## Como interpretar (honestidade > preencher lacuna)

- **`total: 0` só é recorte medido se `desfecho` for `sem_resultado`.** `erro`, `nao_terminou`
  ou `medida_indisponivel` não diagnostica fonte nem pipeline. Não transforme o número em
  ausência sem evidência adicional.
- **Reporte o frescor por seção.** Não chame um timestamp de rollup de "ao vivo" e não
  omita que seções diferentes podem ter `as_of` diferentes.
- **Contrato de honestidade: reporte os números do read-model como vieram.** Não estime,
  não arredonde para impressionar, não extrapole além do que a tool devolveu, não some
  contagens de recortes que possam se sobrepor. Quando o usuário quiser um número que a
  tool não mede (ex.: uma taxa, uma projeção), diga o que a base mede e o que não mede -
  o volume por tribunal e a faixa de anos coberta vêm de `obter_estatisticas_base`
  (`secao="orgaos"`).

## Exemplos de chamada

- Panorama geral: `obter_estatisticas_base()` (= `secao="tudo"`).
- Só os acervos (decisões / normas / doutrina): `obter_estatisticas_base(secao="familias")`.
- Maiores tribunais por volume de decisões:
  `obter_estatisticas_base(secao="orgaos", family="jurisprudencia", top=30)`.
- Espécies de qualificada (vigentes/canceladas):
  `obter_estatisticas_base(secao="qualificadas")`.
- Legislação por esfera, status e território: `obter_estatisticas_base(secao="legislacao")`.

## Subagentes IAJUS (Claude Code)

Esta skill é uma introspecção direta - normalmente você chama `obter_estatisticas_base`
você mesmo e reporta os números. No **Claude Code**, quando o panorama do corpus é o
primeiro passo de uma tarefa maior, os subagentes de pesquisa consomem esta skill: o
**`iajus-juris:pesquisador-juris`** confere a cobertura antes de escalar a busca, e o
**`iajus-juris:legislacao-juris`** confere a prontidão de legislação por esfera/UF. Nos
demais clientes (claude.ai, Cowork, ChatGPT, Codex), execute a introspecção você mesmo.

**Autenticação:** o cliente MCP autentica por você - via **login OAuth** (claude.ai /
ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
ausente ou expirada - peça ao usuário para refazer o login ou revisar a chave; **nunca**
cole a chave em chat nem em commit.
