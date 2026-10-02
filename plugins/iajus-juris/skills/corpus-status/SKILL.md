---
name: corpus-status
description: Consulta cobertura e atualização declaradas pelo read-model do corpus IAJUS, sempre preservando as_of, limites e estado da medição. Não estima nem extrapola contagens.
---

# Estado do acervo IAJUS

Use somente as ferramentas de cobertura listadas nesta conexão. O retorno descreve a medição do leitor; não certifica completude, fonte ao vivo, direito de leitura integral ou serving em produção.

Para consultar a cobertura geral, use consultar_acervo com modo cobertura. A seção filtros.secao aceita tudo, familias, contadores, orgaos, qualificadas ou legislacao. filtros.familia só vale com secao orgaos. O campo limite corta linhas de ranking nas seções tudo e orgaos; não reduz totais. Para o mapa de fontes normativas, use modo cobertura_legislacao e filtros de esfera/UF aceitos pelo schema. O mapa cadastrado não equivale a número de documentos consultáveis.

Repita o as_of, o estado e os limites retornados. live indica medição na chamada; um timestamp anterior é um retrato daquele instante. Não converta ausência de uma métrica, falha, parcialidade ou timeout em zero. A saída não atesta que um documento específico está servido. Use consultar_vocabulario_juridico apenas para identificar códigos ou classificações, não para contar cobertura.

## Conexão legacy

Se consultar_acervo não estiver listada, use `obter_estatisticas_base` para cobertura declarada e `obter_cobertura_legislacao` para o mapa normativo, somente se aparecerem na conexão. `listar_orgaos_julgadores` enumera valores de filtro, não mede cobertura. `obter_ontologia_juridica`, `obter_classificacao_tipo` e `obter_protocolo_classificacao` explicam códigos, não contam documentos. Preserve desfecho, limites e as_of. erro, nao_terminou, parcial e medida_indisponivel não são zero medido.
