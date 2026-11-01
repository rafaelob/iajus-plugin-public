---
name: corpus-status
description: Consulta cobertura e atualização declaradas pelo read-model do corpus IAJUS, sempre preservando as_of, limites e estado da medição. Não estima nem extrapola contagens.
---

# Estado do acervo IAJUS

Na conversa principal, execute a pesquisa pelas ferramentas da conexão IAJUS BR. Ao
delegar a um especialista, leia [coordenação dos agentes](../pesquisar-jurisprudencia/references/coordenacao-agentes.md).
Se você for o especialista, as consultas abaixo são solicitações ao principal; analise
somente os resultados recebidos e devolva a lacuna quando faltarem fontes.

Use somente as ferramentas de cobertura listadas nesta conexão. O retorno descreve a medição do leitor; não certifica completude, fonte ao vivo, direito de leitura integral ou serving em produção.

Para consultar a cobertura geral, use consultar_cobertura_acervo. secao aceita tudo (padrão), familias, contadores, orgaos, qualificadas ou legislacao. familia (jurisprudencia, legislacao ou doutrina) só vale com secao orgaos. O campo limite corta linhas de ranking nas seções tudo e orgaos; não reduz totais. Para o mapa de fontes normativas, use consultar_cobertura_legislacao, com esfera (federal, estadual ou municipal) e uf opcionais; federal não aceita uf. O mapa cadastrado não equivale a número de documentos consultáveis.

```text
consultar_cobertura_acervo     {"secao": "orgaos", "familia": "jurisprudencia", "limite": 20}
consultar_cobertura_legislacao {"esfera": "estadual", "uf": "SP"}
```

Repita o as_of, o estado e os limites retornados. live indica medição na chamada; um timestamp anterior é um retrato daquele instante. Não converta ausência de uma métrica, falha, parcialidade ou timeout em zero. Zero medido difere de ausência ou de falha de medição. A saída não atesta que um documento específico está servido. Use consultar_ontologia_ojbu, consultar_tipo_documental ou consultar_orgaos_julgadores apenas para identificar códigos, classificações ou os órgãos julgadores de um tribunal, não para contar cobertura.

Normalmente uma chamada resolve o panorama. Chamadas adicionais só para recortar uma seção ou completar uma métrica efetivamente disponível, não para reformular a mesma pergunta. Não some contagens de recortes que possam se sobrepor.
