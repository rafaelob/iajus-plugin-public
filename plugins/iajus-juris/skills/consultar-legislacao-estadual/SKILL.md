---
name: consultar-legislacao-estadual
description: Consulta legislação ESTADUAL e MUNICIPAL brasileira ao vivo pelo MCP IAJUS, na fonte oficial de cada UF/município. O servidor cobre as 27 UFs (26 estados + DF) com adaptador nativo. Acione quando o usuário pedir uma lei/decreto/norma de um ESTADO ou MUNICÍPIO, o texto íntegra, "lei estadual nº Y de SP", "decreto do RJ número Z", "lei municipal de Porto Alegre". A consulta é por UF (+ município) + tipo + número + ano. NÃO use para legislação FEDERAL nem para acórdãos/súmulas.
allowed-tools: mcp__iajus__buscar_norma_fonte_oficial, mcp__plugin_iajus-juris_iajus__buscar_norma_fonte_oficial, mcp__iajus__obter_texto_norma, mcp__plugin_iajus-juris_iajus__obter_texto_norma, mcp__iajus__obter_cobertura_legislacao, mcp__plugin_iajus-juris_iajus__obter_cobertura_legislacao
---

# Consultar legislação estadual e municipal brasileira ao vivo (IAJUS)

Você tem acesso ao servidor MCP `iajus`, que consulta **legislação estadual e municipal**
brasileira **ao vivo** na fonte oficial de cada UF/município (assembleia legislativa,
câmara municipal, diário oficial). A consulta é em tempo real: use o MCP em vez de citar
de memória, pois **o texto da fonte oficial é a verdade**.

Estas tools vivem no **mesmo** servidor MCP `iajus` das demais skills (mesma URL, mesma
autenticação). Não há credencial nem host novos.

## Regra número zero: NUNCA recuse uma UF por conta própria

O servidor tem **adaptador nativo para as 27 UFs (26 estados + o Distrito Federal)**. Não
existe UF a recusar de antemão. Para QUALQUER UF que o usuário citar,
**chame a tool** (`buscar_norma_fonte_oficial` / `obter_texto_norma`) e **repasse o que o
servidor devolver**:

- Se o servidor retornar a norma, cite-a com o `link_completo` oficial.
- Se o servidor retornar `erro`/`aviso` (norma não localizada na fonte, fonte fora do ar,
  deadline excedido), **repasse esse resultado honesto do servidor** - o vazio vem do
  servidor, nunca de você prejulgar a UF.

Não invente a norma e não afirme "essa UF ainda não é coberta": a decisão de cobertura é do
servidor, medida ao vivo. Confirme com `obter_cobertura_legislacao` quando estiver em dúvida
sobre a prontidão de uma UF.

## Comece pela cobertura: o que a UF cobre

Para uma UF que você não conhece, chame **`obter_cobertura_legislacao`** (`uf="SP"`) primeiro:
ela lista, sem fan-out lento, o estado + os municípios cobertos naquela UF e a prontidão de
cada um. É o melhor ponto de partida antes de uma consulta estadual/municipal direcionada.
Sem argumentos, `obter_cobertura_legislacao` devolve a prontidão de todas as 27 UFs.

## Cobertura estadual por UF (prontidão real das 27 UFs)

Todas têm adaptador nativo e resolvem ao vivo. A prontidão diz **quanto texto vem**, não se
a UF é servida:

- **`ready`** (texto integral verificado ao vivo - 6 UFs):
  **BA** (LegislaBahia), **GO** (Legisla Goiás API v2), **MG** (ALMG API v2), **MS**
  (SECOGE/Domino), **RO** (SAPL REST), **SP** (ALESP). Resolve + ementa + texto integral.
- **`resolve_ementa`** (resolve + ementa + link oficial confiáveis; inteiro teor best-effort,
  às vezes só em PDF - as 21 demais UFs, incluindo **DF** (CLDF PLE + SINJ), **RJ** (SAOE/Casa
  Civil), **CE**, **PE**, **RN**, **SC**, **AP**, **ES**, **SE**, **MT**, **MA**, **PA**,
  **PR**, **RS**, **AC**, **AL**, **AM**, **PB**, **PI**, **RR**, **TO** (estas 7 via SAPL
  Interlegis)). O campo `tem_texto_integral` por consulta avisa se o texto integral veio.

> Regra REAL: nunca afirme que uma norma específica existe se a tool não a retornou. `ready`
> é sobre a robustez do **texto integral**, não sobre existência de cobertura: mesmo uma UF
> `resolve_ementa` resolve a norma + ementa + link oficial. Se uma consulta pontual não
> resolver, repasse o `erro`/`aviso` do servidor e diga honestamente que a norma não foi
> localizada na fonte - sem prejulgar a UF inteira.

## Como consultar

A **UF é obrigatória** em toda consulta estadual; para municipal, **UF + município**. Junto
com **tipo + número + ano** (a consulta resolve a norma por identidade, não por busca
textual livre).

| Necessidade | Tool | Argumentos |
|---|---|---|
| Mapa de cobertura de uma UF (estado + municípios) | `obter_cobertura_legislacao` | `uf` |
| Prontidão de todas as UFs (estadual) | `obter_cobertura_legislacao` | (sem argumentos) |
| Metadados de uma norma ESTADUAL (link oficial, ementa, data) | `buscar_norma_fonte_oficial` | `uf`, `tipo`, `numero`, `ano` |
| Texto íntegra de uma norma ESTADUAL | `obter_texto_norma` | `uf`, `tipo`, `numero`, `ano` |
| Metadados de uma norma MUNICIPAL | `buscar_norma_fonte_oficial` | `uf`, `municipio`, `tipo`, `numero`, `ano` |
| Texto íntegra de uma norma MUNICIPAL | `obter_texto_norma` | `uf`, `municipio`, `tipo`, `numero`, `ano` |

Notas de uso:
- **`uf` é sempre obrigatória; para municipal, `municipio` também.** Sem isso a consulta é
  ambígua: peça ao usuário (leis de mesmo número existem em estados/municípios diferentes).
- `tipo` é a espécie normativa (`LEI`, `DECRETO`, `LEI COMPLEMENTAR`, …); `numero` e `ano`
  identificam a norma. Esta consulta **não** faz busca por tema/assunto: se o usuário só
  descreve o assunto, peça (ou ajude a descobrir) tipo/número/ano.
- A consulta é **ao vivo**: pode ser mais lenta e depende da fonte oficial. Se a fonte
  estiver fora do ar ou não retornar a norma, o campo `erro`/`aviso` diz isso -
  **repasse ao usuário**, não invente.

## Regras de citação (obrigatório)

- Cite a **UF** (e o **município**, quando municipal), o tipo, o número/ano e a redação
  como retornada pela fonte. Sempre inclua o **`link_completo`** (URL oficial deep-per-norma)
  e **nunca invente** número, redação ou link.
- Deixe claro que a fonte é **estadual** ou **municipal** e de **qual UF/município**.
- Preserve grafia e diacríticos exatamente como na fonte (UTF-8).
- Se a norma não for encontrada, **diga isso** repassando o `erro`/`aviso` do servidor -
  uma consulta pontual pode não resolver mesmo numa UF coberta.

## Boas práticas

- Comece por `obter_cobertura_legislacao` para a UF quando estiver em dúvida; ajuste a
  expectativa (`ready` = texto integral robusto vs `resolve_ementa` = ementa + link + texto
  best-effort) antes de prometer o inteiro teor.
- Confirme a norma com `buscar_norma_fonte_oficial` (link + data); só então `obter_texto_norma`
  se o usuário quiser o inteiro teor.
- **Autenticação:** o cliente MCP autentica por você - via **login OAuth** (claude.ai /
  ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
  header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
  ausente ou expirada - peça ao usuário para refazer o login ou revisar a chave; **nunca**
  cole a chave em chat nem em commit.
