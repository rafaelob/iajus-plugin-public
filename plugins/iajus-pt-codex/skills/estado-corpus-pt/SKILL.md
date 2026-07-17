---
name: estado-corpus-pt
description: Mostra o que o corpus português da IAJUS contém AGORA (por órgão, ano e família) pelo MCP IAJUS. Acione antes de concluir que "não existe" jurisprudência ou legislação para um tribunal/ano - um resultado vazio na pesquisa pode ser cobertura em andamento, e esta skill confirma o que já está na base.
allowed-tools: mcp__iajus-pt__obter_estatisticas_base, mcp__plugin_iajus-pt_iajus-pt__obter_estatisticas_base
---

# Estado do corpus português (IAJUS)

Tem acesso à tool `obter_estatisticas_base`, que devolve o estado atual do corpus PT: quantos
registos existem por órgão, por ano e por família (jurisprudência, legislação). **Use-a para
confirmar o retrato atual antes de afirmar cobertura ou ausência.**

## Quando usar

- Antes de reportar que "não há" jurisprudência de um tribunal/ano. Um `total: 0` confirma apenas
  que a consulta não retornou registos; esta tool mostra o retrato disponível por órgão e ano.
- Para dar ao utilizador um panorama honesto, chame a tool e enumere somente os órgãos, anos e
  famílias presentes na resposta atual. Não use uma lista fixa desta skill como prova de cobertura.
- Para confirmar a janela temporal disponível de um órgão antes de uma pesquisa por faixa de ano.

## Como interpretar

- O corpus é **vivo**: os números crescem entre sessões à medida que novos órgãos, anos e
  famílias são ingeridos. Reporte o estado como um retrato do momento, não como um total final.
- Se um órgão esperado aparecer com contagem baixa, zero ou estiver ausente, reporte exatamente
  esse estado. Não conclua se é ausência na fonte, ingestão pendente ou cobertura incompleta sem
  um campo autoritativo. Cite o timestamp ou frescor quando a resposta o trouxer; caso contrário,
  diga que é o estado no momento da consulta.

## Honestidade

Reporte os números tal como a tool os devolve. Não arredonde para cima, não repita totais ou listas
estáticas de documentação e não afirme completude que a tool não confirma. A resposta atual da
tool é a autoridade para o retrato apresentado. Se a tool devolver um envelope de erro, diga-o e
não invente contagens.

## Autenticação

OAuth 2.1 é o caminho canónico. Uma chave `ik_*` no cabeçalho `Authorization: Bearer` é apenas o
fallback documentado. Um **401** indica sessão/chave em falta ou expirada. A tool é somente-leitura.
