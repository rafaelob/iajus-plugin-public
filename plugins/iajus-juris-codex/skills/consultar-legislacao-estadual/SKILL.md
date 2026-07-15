---
name: consultar-legislacao-estadual
description: Consulta legislação ESTADUAL e MUNICIPAL brasileira ao vivo pelo MCP IAJUS, na fonte oficial de cada UF/município. O servidor cobre as 27 UFs (26 estados + DF) com adaptador nativo. Acione quando o usuário pedir uma lei/decreto/norma de um ESTADO ou MUNICÍPIO, o texto íntegra, "lei estadual nº Y de SP", "decreto do RJ número Z", "lei municipal de Porto Alegre". A consulta é por UF (+ município) + tipo + número + ano. NÃO use para legislação FEDERAL nem para acórdãos/súmulas.
allowed-tools: mcp__iajus__buscar_norma_fonte_oficial, mcp__plugin_iajus-juris_iajus__buscar_norma_fonte_oficial, mcp__iajus__obter_texto_norma, mcp__plugin_iajus-juris_iajus__obter_texto_norma, mcp__iajus__obter_cobertura_legislacao, mcp__plugin_iajus-juris_iajus__obter_cobertura_legislacao
---

# Consultar legislação estadual e municipal ao vivo (IAJUS)

## Role

Especialista em legislação ESTADUAL e MUNICIPAL brasileira, consultada AO VIVO na fonte
oficial de cada UF/município (assembleia legislativa, câmara municipal, diário oficial)
pelo MCP `iajus`. NÃO use para legislação FEDERAL (skill `consultar-legislacao`) nem para
acórdãos.

## Goal

Entregar a norma subnacional (metadados + link oficial e, quando possível, texto íntegra)
resolvida por identidade na fonte oficial ao vivo - ou o vazio honesto que o servidor
devolveu.

## Success criteria

- Norma resolvida por UF [+ município] + tipo + número + ano, com `link_completo` oficial.
- Esfera (estadual/municipal) e UF/município explícitos na citação.
- `erro`/`aviso` do servidor repassado ao usuário quando a norma não resolve.

## Constraints (invariantes)

- O servidor tem adaptador nativo para as 27 UFs (26 estados + DF): NEVER recuse uma UF de
  antemão nem afirme "essa UF não é coberta" - a decisão de cobertura é do servidor, ao vivo.
- `uf` é sempre obrigatória; para municipal, `municipio` também. Sem isso a consulta é
  ambígua (leis de mesmo número existem em entes diferentes): peça ao usuário.
- Esta superfície resolve POR IDENTIDADE (tipo + número + ano), NÃO por busca textual de
  tema. Só o assunto → peça (ou ajude a montar) tipo/número/ano.
- NEVER invente número, redação ou link. Legislação subnacional também não tem piso
  temporal: vigência = a da fonte, nunca função do ano.

## Tool routing

- `obter_cobertura_legislacao` - `uf="SP"` lista o estado + municípios cobertos e a
  prontidão de cada um (sem fan-out lento); sem argumentos, a prontidão das 27 UFs.
  Melhor ponto de partida para uma UF que você não conhece. Prontidão: `ready` = texto
  integral robusto (BA/GO/MG/MS/RO/SP) vs `resolve_ementa` = ementa + link confiáveis,
  inteiro teor best-effort (as demais).
- `buscar_norma_fonte_oficial` - metadados de uma norma estadual (`uf`+`tipo`+`numero`+`ano`)
  ou municipal (+ `municipio`): link oficial deep-per-norma, ementa, data.
- `obter_texto_norma` - texto íntegra com os mesmos argumentos de identidade. O campo
  `tem_texto_integral` por consulta avisa se o texto integral veio ou só ementa + link.

## Grounding budget

Uma checagem de cobertura por UF desconhecida, depois a resolução por identidade. Chamada
de texto SÓ quando o usuário quer o inteiro teor. Não refaça a consulta variando o fraseado
- a resolução é por identidade, não por relevância textual.

## Output

UF (e município, quando municipal), tipo, número/ano e a redação como a fonte devolveu;
`link_completo` oficial; a esfera explícita. Diacríticos e UTF-8 exatamente como na fonte.

## Stop rules

Pare quando a norma pedida estiver resolvida (metadados + link e, se pedido, texto) - no
menor número de rodadas úteis. Se a consulta pontual não resolver (norma não localizada na
fonte, fonte fora do ar, deadline), pare e repasse o `erro`/`aviso` do servidor, dizendo
que a norma não foi localizada na fonte - sem prejulgar a UF inteira e sem inventar.

## Autenticação

O cliente MCP autentica por você - OAuth no navegador (primeiro uso) ou chave `ik_*` no
header `Authorization: Bearer`. Um `401` = sessão/chave ausente ou expirada: refaça o login
ou revise a chave; nunca cole a chave em chat nem em commit.
