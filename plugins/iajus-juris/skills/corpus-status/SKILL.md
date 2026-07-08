---
name: corpus-status
description: 'Mostra o que a base IAJUS contém AGORA via MCP (tool estatisticas_da_base, lê o Postgres ao vivo): por família (jurisprudência/doutrina/legislação) total de unidades, nº de órgãos, faixa de anos e cobertura de embedding/FTS; por órgão contagem + anos; por qualificada e legislação por status. Acione em "o que tem na base?", "quantos acórdãos do TJRJ?", "cobrimos 2023-2026 do órgão X?", "quanto já está embedado?". Corpus VIVO — total 0 = cobertura em andamento.'
allowed-tools: mcp__iajus__estatisticas_da_base, mcp__plugin_iajus-juris_iajus__estatisticas_da_base
---

# Estado do corpus IAJUS AO VIVO (o que a base contém AGORA)

Você tem acesso ao servidor MCP `iajus`, que serve a tool **`estatisticas_da_base`**
— uma introspecção **ao vivo** do corpus, lida diretamente do **read-model Postgres**
que as buscas consultam (não um snapshot estático). Use-a para responder, com números
reais e atuais, **o que a base contém neste momento**.

> **O corpus é VIVO e cresce continuamente.** A base é ingerida o tempo todo: órgãos,
> anos, famílias e espécies de qualificada **novos aparecem aqui (e na busca)
> automaticamente, sem mudança de ferramenta ou de skill**. Por isso um `total: 0`
> para um órgão/ano que já está em cobertura significa **cobertura em andamento**, não
> "não existe" — diga isso ao usuário em vez de afirmar ausência de dados.

## Quando usar

Acione `estatisticas_da_base` quando o usuário perguntar coisas como:

- "**o que tem na base?**", "qual a cobertura atual do IAJUS?";
- "**quantos acórdãos do TJRJ** (ou de qualquer órgão) existem?";
- "**cobrimos 2023-2026** do órgão X?", "de que ano a que ano vai o STJ?";
- "**quanto já está embedado** / indexado (`_3s` / FTS)?";
- "quantas **súmulas / temas de repercussão geral / IRDR** existem, e quantos estão
  vigentes?";
- "quantas normas de **legislação** por esfera (federal/estadual/municipal) e por
  status (vigente/revogada)?".

Para o **texto** de um acórdão/lei ou para **pesquisar** conteúdo, use as outras skills
(`pesquisar-jurisprudencia`, `consultar-legislacao`, `consultar-legislacao-estadual`).
Esta skill é só para o **panorama quantitativo / de cobertura** do corpus.

## A tool

`estatisticas_da_base` é **read-only**, retorna JSON e aceita:

| Argumento | Valores | Efeito |
|---|---|---|
| `secao` | `tudo` (padrão), `familias`, `orgaos`, `qualificadas`, `legislacao` | Que parte do panorama retornar. Comece por `tudo` para uma visão geral. |
| `family` | `jurisprudencia`, `doutrina`, `legislacao` | Escopa **só a seção `orgaos`** a uma família (ex.: órgãos de jurisprudência). |
| `top` | inteiro 1-500 (padrão 60) | Limita a lista de **órgãos** (ordenada por volume decrescente). |

O que cada seção traz:

- **`familias`** — por família (`jurisprudencia` / `doutrina` / `legislacao`):
  `n_unidades`, `n_orgaos`, `ano_min`/`ano_max` e a **cobertura de embedding `_3s`**
  (`n_com_embedding_3s`, `cobertura_embedding_pct`) e de FTS (`n_com_fts`).
- **`orgaos`** — por órgão (top N por volume): `orgao_code`, `n_unidades`,
  `ano_min`/`ano_max` (a **faixa de anos coberta** daquele órgão) e a cobertura de
  embedding. É a seção para "quantos acórdãos do órgão X" e "cobrimos 2023-2026 dele?".
- **`qualificadas`** — por espécie (`precedent_kind`: súmula, SV, tema RG, IRDR, IAC…):
  contagem total, **`n_vigente`** e **`n_cancelada`**, e nº de órgãos.
- **`legislacao`** — normas **por esfera** (federal/estadual/municipal/…) e **por
  status de vigência** (vigente/revogada/desconhecida).

## Como interpretar (honestidade > preencher lacuna)

- **`total: 0` ou contagem baixa para um órgão/ano em cobertura = cobertura em
  andamento**, não ausência definitiva. Avise o usuário e, quando fizer sentido,
  ofereça uma fonte alternativa (ex.: tribunal superior para a tese).
- **Cobertura de embedding < 100%** é normal e esperado: a indexação `_3s` corre
  atrás da ingestão. Um órgão pode ter acórdãos buscáveis por FTS/regex mesmo antes de
  estar 100% embedado para busca semântica.
- **Nenhuma lista é hardcoded** — todo órgão / família / espécie vem de um `GROUP BY`
  sobre os dados vivos. Se um órgão novo não aparece, ele ainda não foi ingerido (não é
  bug da tool).
- Reporte os números **como vieram** da tool; não estime nem arredonde para impressionar.

## Exemplos de chamada

- Panorama geral: `estatisticas_da_base()` (= `secao="tudo"`).
- Só as famílias e a cobertura de embedding: `estatisticas_da_base(secao="familias")`.
- Maiores órgãos de jurisprudência por volume:
  `estatisticas_da_base(secao="orgaos", family="jurisprudencia", top=30)`.
- Espécies de qualificada (vigentes/canceladas):
  `estatisticas_da_base(secao="qualificadas")`.
- Legislação por esfera e status: `estatisticas_da_base(secao="legislacao")`.

**Autenticação:** o cliente MCP autentica por você — via **login OAuth** (claude.ai /
ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
ausente ou expirada — peça ao usuário para refazer o login ou revisar a chave; **nunca**
cole a chave em chat nem em commit.
