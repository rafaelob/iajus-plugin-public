---
name: consultar-legislacao
description: Pesquisa, lê e confere normas brasileiras. Use para texto de lei, dispositivo, identificação normativa, vigência ou alterações; legislação estadual e municipal tem skill própria.
---

# Consultar legislação brasileira

Use apenas ferramentas e schemas listados nesta conexão. Se a rota necessária não estiver disponível, informe isso sem presumir outro nome.

## Normas e dispositivos

Escolha a rota pela intenção, usando-a somente se estiver listada:

- pesquisar_normas recebe busca com modo: hibrida, semantica, textual, expressao, ontologia, nome_ou_apelido ou numero. Use só os filtros do ramo selecionado. Esfera e visão normativa aparecem apenas nos ramos que as declaram; não acrescente filtros por analogia.
- pesquisar_artigos localiza dispositivos legais, não artigos acadêmicos. Use o identificador retornado ao ler um dispositivo.
- ler_documento recebe leitura. Para texto normativo use modo texto_norma e a identidade exigida pelo schema; para trecho específico use modo dispositivo com id_dispositivo retornado por uma busca.
- consultar_fonte_oficial recebe consulta com operação localizar, alteracoes ou enumerar, quando esse ramo estiver listado. Informe somente fonte, identidade e filtros aceitos pela variante.
- explorar_relacoes_juridicas pode consultar o grafo de uma norma pelo modo grafo_norma; use a referência canônica devolvida pela pesquisa.

Em pesquisar_normas, o limite padrão é 20, ajustável de 1 a 100 conforme a quantidade pedida. Híbrida, semântica e ontologia paginam até três páginas, limitadas a 100 resultados (60 com páginas de 20); textual e expressão usam a paginação oferecida pelo executor. Para continuar, repita consulta, filtros e tamanho com page_info.next_cursor em busca.cursor. Pare na quantidade pedida ou sem continuação. Cursor recusado exige reiniciar, sem contar páginas repetidas como novas. Truncamento ou fim da janela não prova fim do acervo; refine o recorte. Nome, número e pesquisar_artigos mantêm seus próprios schemas, sem presumir cursor.

Busca híbrida ou semântica de normas pode chamar provedores e atualizar caches, conforme a descrição da ferramenta. Escolha essa modalidade somente quando a pesquisa temática for necessária. Não apresente um resultado indexado como texto oficial se a chamada não retornou esse texto ou link.

Leia redação e estado de vigência somente do resultado/fonte retornado. A ausência de um campo de status ou de histórico não prova vigência nem inexistência de alterações. Se não houver resultado medido, falha ou recorte parcial, diga qual limite ocorreu. Nunca retire um filtro para tentar fazer a chamada passar sem autorização do usuário.

Para mudanças por dispositivo, abra references/grafo-alteracoes.md. Para legislação subnacional, use consultar-legislacao-estadual.

## Conexão legacy

Se as rotas integradas não estiverem listadas, use somente rotas legacy que aparecerem nesta conexão e seus próprios schemas: `buscar_norma_por_nome` e `buscar_norma_por_numero` para localizar; `buscar_dispositivos` para localizar dispositivos; `buscar_norma_fonte_oficial`, `listar_normas` e `obter_texto_norma` para fontes e texto; `obter_dispositivo_legal` para ler dispositivo; `obter_alteracoes_norma` e `obter_grafo_norma` para histórico e relações. Para citações, use `buscar_por_citacoes`, `buscar_citantes_dispositivo` ou `obter_dispositivos_citados` com identificadores devolvidos. Para cobertura, use `obter_cobertura_legislacao` ou `obter_estatisticas_base`; `obter_ontologia_juridica`, `obter_classificacao_tipo` e `obter_protocolo_classificacao` servem para vocabulário e classificação. Buscas temáticas por `buscar_hibrida`, `buscar_semantica`, `buscar_fts` ou `buscar_por_ontologia` só cabem quando a intenção e o schema forem compatíveis. Não reduza filtros escolhidos para repetir uma chamada.
