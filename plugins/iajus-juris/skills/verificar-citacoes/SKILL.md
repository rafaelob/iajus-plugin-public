---
name: verificar-citacoes
description: Verifica uma a uma citações de decisões, precedentes e normas contra registros e fontes disponíveis. Use para checar existência, fidelidade e vigência; não use para pesquisar do zero.
---

# Verificar citações jurídicas

Confira cada citação separadamente contra ferramentas e schemas listados nesta conexão. Se a rota necessária não estiver disponível, informe a limitação em vez de presumir outro nome.

| Citação | Rota quando listada |
|---|---|
| Processo CNJ | `pesquisar_decisoes` com busca.modo cnj e o número completo. |
| Súmula, tema ou outro precedente qualificado | `pesquisar_precedentes` por numero/tribunal; use `ver_historico_juridico` com id_documento devolvido quando a redação ou vigência exigir histórico. |
| Acórdão citado por tese ou ementa | `pesquisar_decisoes` em modo hibrida, semantica ou textual, com os filtros pertinentes. |
| Norma ou dispositivo legal | `pesquisar_normas` por nome/número; `consultar_fonte_oficial` para localização/alterações; `pesquisar_artigos` para localizar um dispositivo; `ler_documento` para ler a norma ou dispositivo identificado. |
| Relação de citações | `explorar_relacoes_juridicas` somente depois de obter o identificador pelo resultado de busca. |

Confirme identidade e conteúdo pelo registro retornado. Preserve link oficial, órgão, número, data, redação e situação exatamente como aparecem. Declare CONFIRMADA somente quando a fonte sustentar a referência e a afirmação do texto. Declare DESATUALIZADA apenas com evidência de versão ou estado adverso. Use NÃO LOCALIZADA somente após uma busca concluída e medida; use NÃO VERIFICÁVEL quando houver erro, indisponibilidade, limite, retorno parcial ou falta de fonte/conteúdo suficiente.

Um status ausente não confirma vigência; ausência de histórico não prova que não houve alteração. Um zero sem indicador de medição não prova inexistência. Se a resposta legacy trouxer desfecho, sem_resultado é zero medido; erro, nao_terminou, parcial e medida_indisponivel deixam a citação não verificável.

## Conexão legacy

Se as rotas integradas não estiverem listadas, use somente ferramentas legacy descobertas: `buscar_por_cnj`; `buscar_qualificada` e `obter_versoes_qualificada`; `buscar_informativos_stf` ou `buscar_informativos_stj`; `buscar_hibrida`, `buscar_semantica`, `buscar_fts`, `buscar_regex` ou `buscar_por_ontologia`; `buscar_dispositivos`, `buscar_norma_por_nome`, `buscar_norma_por_numero`, `buscar_norma_fonte_oficial`, `listar_normas`, `obter_texto_norma`, `obter_dispositivo_legal`, `obter_alteracoes_norma` ou `obter_grafo_norma`. Para relações, use `buscar_por_citacoes`, `buscar_citantes_dispositivo` ou `obter_dispositivos_citados` conforme o sentido e os IDs retornados. Use cobertura apenas para interpretar ausência: `obter_estatisticas_base` e `obter_cobertura_legislacao`. Siga o schema próprio de cada rota; não envie nela campos de outra ferramenta nem remova filtros ao repetir uma consulta.
