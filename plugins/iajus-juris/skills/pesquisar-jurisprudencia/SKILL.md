---
name: pesquisar-jurisprudencia
description: Pesquisa decisões, precedentes qualificados, informativos e relações jurídicas brasileiras no IAJUS. Use para acórdãos, número CNJ, súmulas, temas e teses de tribunais. Não use para pesquisar legislação.
---

# Pesquisar jurisprudência brasileira

Use somente os nomes e schemas que a conexão realmente exibe. Se uma rota não estiver listada, informe a indisponibilidade e escolha outra apenas quando ela atender à mesma intenção.

## Pesquisa de decisões

Quando pesquisar_decisoes estiver listado, envie um único objeto busca com modo. Os schemas são fechados: use apenas os campos aceitos pelo modo e mantenha todos os filtros escolhidos em uma repetição.

| modo | Uso e filtros próprios |
|---|---|
| hibrida | Pergunta em consulta; combina sinais. Aceita tribunal, anos e datas inicial/final, relator, órgão julgador, tipo e unidade, classe e filtros CNJ, tema, OJBU e natureza do foro. |
| semantica | Consulta por significado. Aceita tribunal, um ano, tipo de unidade e, no BR, ramo OJBU, natureza do foro e discovery_only. Não aceita faixa de anos. |
| textual | Termos no texto; filtros de órgão, classe, datas/anos e frase_exata, mais filtros CNJ/tema brasileiros. Ordenação e cursor só conforme o schema. |
| expressao | Expressão regular POSIX; filtros de órgão, classe, datas/anos e ignorar_maiusculas. allow_unindexed_scan é opt-in explícito do campo e o padrão é falso. |
| ontologia | Informe filtros.ojbu_l1 ou filtros.tema_transversal. L2/L3 e escopo do rótulo dependem do ramo; tema_transversal não combina com filtros de ramo. Consulte references/ontologia-ojbu.md. |
| cnj | Informe numero completo ou componentes CNJ, não ambos. Ordenação e cursor seguem este modo. |

O limite padrão é 20, ajustável de 1 a 100 conforme a quantidade pedida. Para buscar mais, use page_info.next_cursor em busca.cursor e repita consulta, filtros e tamanho; pare na quantidade solicitada ou quando não houver continuação. Não percorra todas as páginas sem necessidade.

Híbrida, semântica, textual e ontológica percorrem uma janela de até três páginas, limitada a 100 resultados (60 com páginas de 20). Fim da janela ou truncated não significa fim do acervo: refine o recorte para pesquisar além dela. Cursor expirado, inválido ou ranking alterado exige reiniciar a busca; não acumule a primeira página repetida como novos resultados. Expressão e CNJ usam a paginação oferecida pelo executor; varredura sem índice pode não oferecer cursor. A consulta textual por número CNJ exato tem restrições próprias e não entra nessa janela.

## Outras intenções

- Pesquisar tese qualificada: pesquisar_precedentes, por numero ou matéria, com filtros de espécie/tribunal/vinculatividade; o limite é 10 e não há cursor. Canceladas ficam incluídas e marcadas por padrão.
- Pesquisar conteúdo editorial: pesquisar_informativos aceita tribunal STF ou STJ, consulta e filtros de ramo, ano ou edição. O padrão é 20 notas, com limite de 1 a 50. Para continuar, repita o recorte e a quantidade com page_info.next_cursor em busca.cursor; pare na quantidade pedida. A janela recuperada cobre até três páginas, limitada a 50 notas, e seu fim não prova fim do acervo. Informativo não substitui o acórdão nem prova vinculatividade.
- Ver versões de uma tese: ver_historico_juridico exige id_documento devolvido por uma pesquisa de precedentes.
- Explorar citações: use explorar_relacoes_juridicas somente com identificadores devolvidos pela busca; escolha o modo admitido em seu schema. Não invente IDs nem converta uma relação em decisão de mérito.
- Consultar cobertura: use consultar_acervo e indique o as_of retornado. Contagem de cobertura não comprova completude nem serving.
- Abrir painel: chame abrir_painel_iajus apenas se a conexão o listar e o usuário pedir o painel ou houver um resultado profundo existente que deva ser aberto. Abrir o painel não inicia pesquisa.

Cite apenas registros, links, trechos e estados que o retorno realmente trouxer. Diferencie zero medido, erro, timeout, indisponibilidade e resultado parcial conforme os campos retornados. Se a resposta legacy trouxer desfecho, sem_resultado é o zero medido; erro, nao_terminou, parcial e medida_indisponivel não provam ausência. Consulte references/campos-e-trust.md para tratar campos de autoridade.

## Conexão legacy

Se pesquisar_decisoes não estiver listada, use as rotas de busca que a conexão oferecer: `buscar_hibrida`, `buscar_semantica`, `buscar_fts`, `buscar_regex`, `buscar_por_ontologia` ou `buscar_por_cnj`. Para órgãos julgadores, `listar_orgaos_julgadores` enumera valores disponíveis para o filtro; não é uma busca de decisões. Para precedentes, use `buscar_qualificada` e, quando a redação exigir, `obter_versoes_qualificada`. Para conteúdo editorial use `buscar_informativos_stf` ou `buscar_informativos_stj`. Para citações, escolha `buscar_por_citacoes`, `buscar_citantes_dispositivo` ou `obter_dispositivos_citados` conforme a direção da relação. `obter_ontologia_juridica`, `obter_classificacao_tipo` e `obter_protocolo_classificacao` ajudam a resolver códigos e classificações; `obter_estatisticas_base` informa cobertura declarada. Use cada schema legacy como publicado e preserve os filtros ao repetir. Se nenhuma rota pertinente estiver listada, informe que ela não está disponível.
