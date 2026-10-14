---
name: consultar-legislacao-estadual
description: Consulta normas estaduais e municipais brasileiras quando a conexão IAJUS oferece a rota e os filtros territoriais correspondentes. Não presume disponibilidade ao vivo por UF ou município.
---

# Consultar legislação estadual e municipal

Na conversa principal, execute a pesquisa pelas ferramentas da conexão IAJUS BR. Ao
delegar a um especialista, leia [coordenação dos agentes](../pesquisar-jurisprudencia/references/coordenacao-agentes.md).
Se você for o especialista, as consultas abaixo são solicitações ao principal; analise
somente os resultados recebidos e devolva a lacuna quando faltarem fontes.

Use somente ferramentas e filtros que a conexão realmente listar. Um mapa cadastral não comprova que a fonte responda agora nem quantos documentos estão servidos.

Em pergunta de direitos, deveres ou regime sem indicação do ente, pergunte se é a União, um estado ou um município antes de buscar norma estadual ou municipal; a norma federal canônica está em consultar-legislacao.

Não imponha piso temporal para legislação estadual ou municipal. Use ano apenas como identidade ou recorte solicitado; determine vigência pela fonte consultada, nunca pelo ano de publicação.

Rotas, quando listadas:

- consultar_fonte_oficial com operacao localizar identifica uma norma estadual ou municipal pela identidade exata: numero e filtros esfera, tipo e ano, mais uf (estadual) ou municipio (municipal), todos obrigatórios nessas esferas. A busca por tema (consulta) é só federal; alteracoes e enumerar também são só federais.
- ler_documento com leitura.modo texto_norma lê o texto pela identidade: numero e filtros tipo e ano, mais esfera estadual com uf ou esfera municipal com municipio. Estado e município exigem prova temporal do executor.
- pesquisar_normas aceita esfera estadual ou municipal apenas nos modos nome_ou_apelido e numero.

Exemplos:

```json
{"consulta": {"operacao": "localizar", "numero": "10177", "filtros": {"esfera": "estadual", "uf": "SP", "tipo": "LEI", "ano": 1998}}}
{"leitura": {"modo": "texto_norma", "numero": "10177", "filtros": {"tipo": "LEI", "ano": 1998, "esfera": "estadual", "uf": "SP"}}}
```

Aplique UF, município, tipo, número e ano somente nos ramos que o schema aceitar. Se faltar um dado exigido pelo schema, peça-o ao usuário em vez de inventar a identidade.

consultar_acervo com consulta.modo cobertura_legislacao mostra o mapa cadastrado de fontes por esfera e UF (filtros esfera e uf). Isso não prova que uma consulta pontual funcione, que haja documentos servidos ou que a cobertura seja completa. Só descreva disponibilidade, texto, vigência e link oficial que o resultado de uma chamada real confirmar.

Trate erro, indisponibilidade, timeout, parcialidade e zero medido conforme os indicadores devolvidos. Um mapa de adaptadores ou uma resposta vazia sem evidência de consulta não autoriza afirmar que a UF não é coberta.
