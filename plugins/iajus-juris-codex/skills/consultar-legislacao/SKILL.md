---
name: consultar-legislacao
description: Pesquisa, lê e confere normas brasileiras. Use para texto de lei, dispositivo, identificação normativa, vigência ou alterações; legislação estadual e municipal tem skill própria.
allowed-tools: mcp__iajus__pesquisar_normas, mcp__iajus__pesquisar_artigos, mcp__iajus__ler_documento, mcp__iajus__localizar_norma_fonte_oficial, mcp__iajus__consultar_alteracoes_norma, mcp__iajus__enumerar_normas_federais, mcp__iajus__explorar_relacoes_juridicas, mcp__iajus__consultar_tipo_documental
---

# Consultar legislação brasileira

Se a conexão retornar 401 ou pedir autenticação, oriente a reconexão no host: no ChatGPT, reconectar o IAJUS pelas configurações da conexão; no Codex, executar `codex mcp login iajus` e reiniciar a sessão. Não peça tokens ou senhas no chat. Se uma ferramenta continuar ausente após autenticação, informe a indisponibilidade nesta conexão; não invente alias nem responda o mérito jurídico de memória.

Use apenas ferramentas e schemas listados nesta conexão. Os schemas são fechados: envie só os campos do ramo escolhido. Se a rota necessária não estiver disponível, informe isso sem presumir outro nome.

## Normas e dispositivos

Escolha a rota pela intenção, usando-a somente se estiver listada:

- pesquisar_normas recebe busca com modo: hibrida, semantica, textual, expressao, ontologia, nome_ou_apelido ou numero. Use só os filtros do ramo selecionado. Esfera e visão normativa aparecem apenas nos ramos nome_ou_apelido e numero; não acrescente filtros por analogia.
- pesquisar_artigos localiza dispositivos legais, não artigos acadêmicos: busca com consulta opcional e filtros norma (norma_id, como LEI_13709_2018), tipo_unidade, ojbu_l1 e visao_normativa. Devolve até 200 resultados, sem cursor. Use o identificador retornado ao ler um dispositivo.
- ler_documento recebe leitura. Para texto normativo use modo texto_norma (numero e filtros tipo e ano, obrigatórios); para um dispositivo específico use modo dispositivo com id_dispositivo (o node_id retornado por uma busca).
- Fonte oficial (identificar uma norma antes de lê-la com ler_documento, alterações, inventário de um ano): localizar_norma_fonte_oficial, consultar_alteracoes_norma e enumerar_normas_federais, uma ferramenta por operação, descritas na seção «Fonte oficial: uma ferramenta por operação».
- explorar_relacoes_juridicas consulta o grafo de uma norma federal pelo modo grafo_norma e, pelos modos citantes_dispositivo e dispositivos_citados, quem cita um artigo e o que um documento cita; use identificadores devolvidos pela pesquisa.
- consultar_tipo_documental indica a família e sugestões de ramo de um tipo normativo (como LEI) pelo tipo, número e ano (numero e ano opcionais; ano exige numero).

Exemplos:

```json
{"busca": {"modo": "numero", "numero": "8112", "filtros": {"tipo": "LEI", "ano": 1990, "esfera": "federal"}}}
{"leitura": {"modo": "texto_norma", "numero": "8112", "filtros": {"tipo": "LEI", "ano": 1990, "artigo": "117"}}}
{"leitura": {"modo": "dispositivo", "id_dispositivo": "<node_id devolvido pela pesquisa>"}}
```

Fonte oficial, por tema, com localizar_norma_fonte_oficial e campos planos:

```json
{"consulta": "licitações e contratos administrativos", "tipo": "LEI"}
```

Na leitura federal, formato texto (o padrão) recorta artigo, artigo_fim e anexo; formato markdown pagina por offset, prosseguindo com next_offset. Estado e município exigem prova temporal do executor, e não há leitura histórica por data. Observe direitos, versão e limites devolvidos.

Em pesquisar_normas, o limite padrão é 20, ajustável de 1 a 100 conforme a quantidade pedida. Híbrida, semântica e ontologia paginam até três páginas, limitadas a 100 resultados (60 com páginas de 20); textual e expressão usam a paginação oferecida pelo executor. Para continuar, repita consulta, filtros e tamanho com page_info.next_cursor em busca.cursor. Pare na quantidade pedida ou sem continuação. Cursor recusado exige reiniciar, sem contar páginas repetidas como novas. Truncamento ou fim da janela não prova fim do acervo; refine o recorte. Nome e número não têm cursor; pesquisar_artigos mantém seu próprio schema.

Busca híbrida ou semântica de normas pode chamar provedores e atualizar caches, conforme a descrição da ferramenta. Escolha essa modalidade somente quando a pesquisa temática for necessária. Não apresente um resultado indexado como texto oficial se a chamada não retornou esse texto ou link.

Leia redação e estado de vigência somente do resultado/fonte retornado. A ausência de um campo de status ou de histórico não prova vigência nem inexistência de alterações. Se não houver resultado medido, falha ou recorte parcial, diga qual limite ocorreu. Nunca retire um filtro para tentar fazer a chamada passar sem autorização do usuário.

Quando a cobertura for necessária à pergunta, consulte primeiro corpus-status e preserve seus limites. Em seguida, identifique a norma com pesquisar_normas ou localizar_norma_fonte_oficial; se vigência ou histórico forem relevantes para uma norma federal, confira as alterações disponíveis com consultar_alteracoes_norma. Leia a redação pertinente com ler_documento em texto_norma (numero, filtros.tipo e filtros.ano) ou localize o dispositivo com pesquisar_artigos e leia-o em dispositivo (id_dispositivo devolvido). Cite a identidade, a fonte e o trecho efetivamente obtidos. Um resultado de busca ou link isolado não comprova o conteúdo; se faltar leitura ou estado suficiente, diga qual etapa ficou sem prova.

A data de vigência que se quer apurar e a data limite do conhecimento, se pedida, são recortes distintos. Os modos texto_norma e dispositivo de ler_documento não recebem data histórica nem corte de conhecimento. visao_normativa historica ou incluir_historico podem recuperar material histórico, mas não reconstroem por si só a redação vigente numa data escolhida. Para uma pergunta histórica, use fonte retornada que comprove a redação e o período pertinentes; se houver corte de conhecimento, é necessária também evidência de que o registro estava disponível até aquele momento. Sem a prova necessária, declare o limite. Se a data mudar a resposta e não puder ser inferida, pergunte; caso contrário, informe o recorte temporal adotado.

Uma resolução por nome, número ou tema direciona a leitura. Consultas adicionais só para dispositivo faltante, cadeia de alterações ou metadado necessário à afirmação, nunca apenas para reformular. Antes de afirmar vigência de dispositivo, confira as alterações disponíveis e declare eventual limitação do histórico. Não imponha um piso temporal não solicitado.

## Fonte oficial: uma ferramenta por operação

Cada operação da fonte oficial é uma ferramenta própria, com campos planos. Escolha pela intenção:

- localizar_norma_fonte_oficial: descobrir ou identificar uma norma antes de lê-la. Por tema, com consulta (só federal; tipo, ano e limite de 1 a 50, padrão 20, são opcionais), ou pela identidade exata, com numero e tipo. esfera omitida é federal; a estadual exige uf, numero, tipo e ano; a municipal exige municipio, numero, tipo e ano e não aceita uf (para «Belo Horizonte, MG», envie só municipio «Belo Horizonte»). consulta e numero não se combinam, e limite só vale na busca temática. Não lê o texto: use ler_documento com a identidade devolvida.
- consultar_alteracoes_norma: numero, tipo e ano de uma norma federal já identificada; devolve as alterações registradas. Não dá a redação vigente em data histórica.
- enumerar_normas_federais: tipo e ano federais; lista o que a fonte devolve para esse par (limite de 1 a 500, padrão 100, sem cursor). Serve para inventariar um ano, não para achar uma norma por tema ou número: use localizar_norma_fonte_oficial.

A fonte oficial é aberta e pode estar indisponível ou responder em parte. Leia status, falha, parcialidade e zero medido do retorno antes de responder:

- Entrada recusada (entrada_invalida): falta um campo ou há combinação não aceita, como esfera estadual sem uf, esfera municipal com uf ou limite com identidade exata. A mensagem é sempre «Confira os campos e os limites da consulta.» e não nomeia o campo: não corrija por palpite; reconfira os campos obrigatórios e os limites da operação no schema listado e repita. Não é falha da fonte nem prova de que a norma não existe. O que só o usuário sabe (tipo, número, ano), peça a ele.
- Vazio: diga apenas o zero medido que o retorno declara, neste recorte. Falha, timeout, indisponibilidade ou resultado parcial não sustentam «a norma não existe»; informe o limite ocorrido.
- Lista (busca temática ou enumeração): é o que a fonte devolveu até o limite, sem cursor; não é o conjunto de todas as normas.
- Alterações: a falta de eventos não prova que a norma nunca foi alterada nem que a redação é a vigente.

## Norma canônica antes da jurisprudência

Em pergunta de direitos, deveres ou regime, busque primeiro a norma canônica pelo número, antes de jurisprudência ou doutrina: pesquisar_normas com busca.modo numero (numero, filtros tipo e ano, e esfera quando conhecida), depois pesquisar_artigos e ler_documento para os dispositivos pertinentes. O lookup por número não depende de ranking; uma busca temática aberta pode não trazer a norma canônica no topo, e nome ou apelido pode não resolver.

| Assunto | Norma a buscar primeiro |
|---|---|
| Servidores públicos federais | Lei 8.112/1990 (tipo LEI, número 8112, ano 1990, esfera federal) e CF/1988, art. 39 |
| Licitações e contratos administrativos | Lei 14.133/2021 |
| Tributário | CTN, Lei 5.172/1966 |
| Processo civil | CPC, Lei 13.105/2015 |
| Trabalho | CLT, Decreto-Lei 5.452/1943 |
| Criança e adolescente | ECA, Lei 8.069/1990 |
| Consumidor | CDC, Lei 8.078/1990 |

Se o ente for ambíguo (União, estado ou município; servidor sem indicação de ente), pergunte antes e não misture leis de outro ente. Só diga «norma ausente» neste recorte se o lookup canônico foi concluído com zero medido; falha não comprova ausência. Se a ferramenta certa nem foi chamada, chame-a.

Para mudanças por dispositivo, abra references/grafo-alteracoes.md. Para legislação subnacional, use consultar-legislacao-estadual.
