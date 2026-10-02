---
name: consultar-legislacao-estadual
description: Consulta normas estaduais e municipais brasileiras quando a conexão IAJUS oferece a rota e os filtros territoriais correspondentes. Não presume disponibilidade ao vivo por UF ou município.
---

# Consultar legislação estadual e municipal

Use somente ferramentas e filtros que a conexão realmente listar. Um mapa cadastral não comprova que a fonte responda agora nem quantos documentos estão servidos.

Quando listada, pesquisar_normas pode aceitar esfera estadual/municipal e recortes territoriais em alguns modos. consultar_fonte_oficial pode localizar uma norma por uma variante de identidade estadual ou municipal; ler_documento pode ler norma por identidade. Aplique UF, município, tipo, número e ano somente nos ramos que o schema aceitar. Se faltar um dado exigido pelo schema, peça-o ao usuário em vez de inventar a identidade.

consultar_acervo com modo cobertura_legislacao mostra o mapa cadastrado de fontes por esfera e UF. Isso não prova que uma consulta pontual funcione, que haja documentos servidos ou que a cobertura seja completa. Só descreva disponibilidade, texto, vigência e link oficial que o resultado de uma chamada real confirmar.

Trate erro, indisponibilidade, timeout, parcialidade e zero medido conforme os indicadores devolvidos. Um mapa de adaptadores ou uma resposta vazia sem evidência de consulta não autoriza afirmar que a UF não é coberta.

## Conexão legacy

Se as rotas integradas não estiverem listadas, use somente ferramentas legacy descobertas: `buscar_norma_fonte_oficial` e `obter_texto_norma` para identidade, fonte e conteúdo; `obter_cobertura_legislacao` para consultar o mapa cadastral. Buscas temáticas por `buscar_hibrida`, `buscar_semantica`, `buscar_fts` ou `buscar_por_ontologia` só servem quando estiverem listadas e o schema aceitar o recorte territorial. Não converta filtros para nomes presumidos nem remova filtros para repetir uma chamada.
