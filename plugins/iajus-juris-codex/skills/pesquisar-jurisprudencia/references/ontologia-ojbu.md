# Pesquisar por OJBU

Use pesquisar_decisoes com busca.modo ontologia quando esse nome estiver listado. O recorte fica em busca.filtros:

- ojbu_l1 aceita código positivo ou slug e é obrigatório salvo quando tema_transversal for usado.
- ojbu_l2 e ojbu_l3 são códigos positivos; informe ojbu_l1 junto.
- tema_transversal é um recorte alternativo. Não o misture com filtros de ramo, escopo, relator ou classificação principal.
- tribunal, anos, escopo_rotulo, somente_principal e relator só se aplicam conforme o ramo descrito pelo schema.
- Pesquisar um conceito amplo sem seleção OJBU é outra intenção: use hibrida ou semantica em vez de presumir um código.

Consulte consultar_vocabulario_juridico no modo ontologia, se listado, para resolver classificações existentes. Não adivinhe códigos nem trate um rótulo sem resultado como ausência do assunto no corpus.

Na conexão legacy, use `buscar_por_ontologia` apenas quando descoberto e siga seu próprio schema, que pode usar l1_code/l2_code/l3_code em vez dos campos da rota integrada.

Use as ferramentas e schemas disponíveis nesta conexão; se uma rota estiver indisponível, informe a limitação.
