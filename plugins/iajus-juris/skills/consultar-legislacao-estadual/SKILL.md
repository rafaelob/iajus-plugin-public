---
name: consultar-legislacao-estadual
description: Consulta legislação ESTADUAL e MUNICIPAL brasileira ao vivo (texto integral SP/MG/BA, DF resolve+ementa, RJ best-effort; dezenas de municípios) pelo MCP iaJus, na fonte oficial de cada UF/município. Acione quando o usuário pedir uma lei/decreto/norma de um ESTADO ou MUNICÍPIO, o texto íntegra, "lei estadual nº Y de SP", "decreto do RJ número Z", "lei municipal de Porto Alegre". A consulta é por UF (+ município) + tipo + número + ano. NÃO use para legislação FEDERAL nem para acórdãos/súmulas.
allowed-tools: mcp__iajus__consultar_legislacao_estadual, mcp__plugin_iajus-juris_iajus__consultar_legislacao_estadual, mcp__iajus__obter_texto_legislacao_estadual, mcp__plugin_iajus-juris_iajus__obter_texto_legislacao_estadual, mcp__iajus__legislacao_estadual_status, mcp__plugin_iajus-juris_iajus__legislacao_estadual_status, mcp__iajus__consultar_legislacao_municipal, mcp__plugin_iajus-juris_iajus__consultar_legislacao_municipal, mcp__iajus__obter_texto_legislacao_municipal, mcp__plugin_iajus-juris_iajus__obter_texto_legislacao_municipal, mcp__iajus__legislacao_municipal_status, mcp__plugin_iajus-juris_iajus__legislacao_municipal_status, mcp__iajus__cobertura_legislacao_uf, mcp__plugin_iajus-juris_iajus__cobertura_legislacao_uf
---

# Consultar legislação estadual e municipal brasileira ao vivo (iaJus)

Você tem acesso ao servidor MCP `iajus`, que consulta **legislação estadual e municipal**
brasileira **ao vivo** na fonte oficial de cada UF/município (assembleia legislativa,
câmara municipal, diário oficial). A consulta é em tempo real — use o MCP em vez de citar
de memória: **o texto da fonte oficial é a verdade**.

Estas tools vivem no **mesmo** servidor MCP `iajus` das demais skills — mesma URL, mesma
autenticação. Não há credencial nem host novos.

## Comece pela cobertura: o que a UF cobre

Para uma UF que você não conhece, chame **`cobertura_legislacao_uf`** (`uf="SP"`) primeiro:
ela lista, sem fan-out lento, o estado + os municípios cobertos naquela UF e a prontidão de
cada um. É o melhor ponto de partida antes de uma consulta estadual/municipal direcionada.

`legislacao_estadual_status` (estados) e `legislacao_municipal_status` (municípios) dão a
prontidão detalhada por fonte.

## Cobertura estadual por UF (prontidão real)

- **Prontas** (`ready` — verificadas ao vivo: fetch 2xx + texto integral real):
  **SP** (ALESP), **MG** (ALMG API v2 — uma chamada já traz ementa + texto integral) e
  **BA** (LegislaBahia — ementa + texto integral).
- **Resolve + ementa** (`resolve_ementa`): **DF** (CLDF PLE API — resolve a norma + ementa
  + link oficial de forma confiável; o inteiro teor vem via SINJ-DF na maioria dos casos,
  mas é best-effort — o campo `tem_texto_integral` por consulta avisa se o texto veio).
- **Não confirmada** (`unconfirmed`): adaptador existe, mas a resolução ao vivo ainda não
  foi confirmada — sempre confira com `legislacao_estadual_status` e, se falhar, **diga que
  a UF ainda não está confirmada**.
- **Best-effort** (`stub`): **RJ** (a base da ALERJ é Lotus Notes/Domino com busca
  full-text imprecisa — a maioria das consultas por número NÃO resolve; quando resolve, o
  texto vem completo e o número é verificado no cabeçalho). Se não resolver, **diga que a
  norma do RJ não pôde ser localizada na fonte** — nunca invente.
- **Federado** (`federated`): UFs sem adaptador nativo → tentativa via índice LexML
  federado (só link, sem texto; pode não resolver). Se não resolver, **diga honestamente**
  que a UF ainda não tem cobertura nativa — não improvise a norma.

> Regra REAL: nunca afirme que uma norma existe se a tool não a retornou. `ready` = só as
> UFs/municípios com prova ao vivo de texto integral. Para `resolve_ementa`, `unconfirmed`,
> `stub` e `federated`, modere a expectativa e seja honesto sobre lacunas — a cobertura ao
> vivo subnacional ainda está em expansão.

## Como consultar

A **UF é obrigatória** em toda consulta estadual; para municipal, **UF + município**. Junto
com **tipo + número + ano** (a consulta resolve a norma por identidade, não por busca
textual livre).

| Necessidade | Tool | Argumentos |
|---|---|---|
| Mapa de cobertura de uma UF (estado + municípios) | `cobertura_legislacao_uf` | `uf` |
| Metadados de uma norma ESTADUAL (link oficial, ementa, data) | `consultar_legislacao_estadual` | `uf`, `tipo`, `numero`, `ano` |
| Texto íntegra de uma norma ESTADUAL | `obter_texto_legislacao_estadual` | `uf`, `tipo`, `numero`, `ano` |
| Prontidão estadual por UF | `legislacao_estadual_status` | (sem argumentos) |
| Metadados de uma norma MUNICIPAL | `consultar_legislacao_municipal` | `uf`, `municipio`, `tipo`, `numero`, `ano` |
| Texto íntegra de uma norma MUNICIPAL | `obter_texto_legislacao_municipal` | `uf`, `municipio`, `tipo`, `numero`, `ano` |
| Prontidão municipal | `legislacao_municipal_status` | (sem argumentos) |

Notas de uso:
- **`uf` é sempre obrigatória; para municipal, `municipio` também.** Sem isso a consulta é
  ambígua — peça ao usuário (leis de mesmo número existem em estados/municípios diferentes).
- `tipo` é a espécie normativa (`LEI`, `DECRETO`, `LEI COMPLEMENTAR`, …); `numero` e `ano`
  identificam a norma. Esta consulta **não** faz busca por tema/assunto — se o usuário só
  descreve o assunto, peça (ou ajude a descobrir) tipo/número/ano.
- A consulta é **ao vivo**: pode ser mais lenta e depende da fonte oficial. Se a fonte
  estiver fora do ar ou não retornar a norma, o campo `erro`/`aviso` diz isso —
  **repasse ao usuário**, não invente.

## Regras de citação (obrigatório)

- Cite a **UF** (e o **município**, quando municipal), o tipo, o número/ano e a redação
  como retornada pela fonte. Sempre inclua o **`link_completo`** (URL oficial deep-per-norma)
  e **nunca invente** número, redação ou link.
- Deixe claro que a fonte é **estadual** ou **municipal** e de **qual UF/município**.
- Preserve grafia e diacríticos exatamente como na fonte (UTF-8).
- Se a norma não for encontrada (ou a fonte for federada/best-effort e falhar), **diga
  isso** — legislação subnacional ao vivo tem lacunas de cobertura conhecidas.

## Boas práticas

- Comece por `cobertura_legislacao_uf` para a UF; ajuste a expectativa (ready vs best-effort
  vs federated) antes de prometer um resultado.
- Confirme a norma com `consultar_legislacao_*` (link + data); só então `obter_texto_*` se o
  usuário quiser o inteiro teor.
- **Autenticação:** o cliente MCP autentica por você — via **login OAuth** (claude.ai /
  ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
  header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
  ausente ou expirada — peça ao usuário para refazer o login ou revisar a chave; **nunca**
  cole a chave em chat nem em commit.
