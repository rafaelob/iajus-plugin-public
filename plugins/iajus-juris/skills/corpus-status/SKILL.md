---
name: corpus-status
description: Consulta cobertura e atualização declaradas pelo read-model do corpus IAJUS, sempre preservando as_of, limites e estado da medição. Não estima nem extrapola contagens.
---

# Estado do acervo IAJUS

Use somente as ferramentas de cobertura listadas nesta conexão. O retorno descreve a medição do leitor; não certifica completude, fonte ao vivo, direito de leitura integral ou serving em produção.

Para consultar a cobertura geral, use consultar_acervo com consulta.modo cobertura. A seção filtros.secao aceita tudo, familias, contadores, orgaos, qualificadas ou legislacao. filtros.familia (jurisprudencia, legislacao ou doutrina) só vale com secao orgaos. O campo limite corta linhas de ranking nas seções tudo e orgaos; não reduz totais. Para o mapa de fontes normativas, use modo cobertura_legislacao e os filtros esfera e uf aceitos pelo schema. O mapa cadastrado não equivale a número de documentos consultáveis.

```json
{"consulta": {"modo": "cobertura", "filtros": {"secao": "orgaos", "familia": "jurisprudencia"}, "limite": 20}}
{"consulta": {"modo": "cobertura_legislacao", "filtros": {"esfera": "estadual", "uf": "SP"}}}
```

Repita o as_of, o estado e os limites retornados. live indica medição na chamada; um timestamp anterior é um retrato daquele instante. Não converta ausência de uma métrica, falha, parcialidade ou timeout em zero. Zero medido difere de ausência ou de falha de medição. A saída não atesta que um documento específico está servido. Use consultar_vocabulario_juridico apenas para identificar códigos, classificações ou os órgãos julgadores de um tribunal (modo orgaos_julgadores), não para contar cobertura.

Normalmente uma chamada resolve o panorama. Chamadas adicionais só para recortar uma seção ou completar uma métrica efetivamente disponível, não para reformular a mesma pergunta. Não some contagens de recortes que possam se sobrepor.
