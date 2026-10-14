---
name: pesquisa-juridica-lex
description: Solicita pesquisa jurídica integrada ao Lex (recurso beta, com limites por conta), com consulta, contexto e restrições preservados. Use somente quando a ferramenta Lex correspondente estiver realmente disponível.
---

# Pesquisa jurídica com Lex (beta)

pesquisa_juridica_lex e pesquisa_profunda_lex são recursos **beta**: diga isso ao usuário quando o assunto surgir; o comportamento e os limites podem mudar. Use essas duas ferramentas, e consultar_tarefa_lex e cancelar_tarefa_lex como controles da pesquisa profunda, somente quando cada uma estiver listada nesta conexão. Se não estiver, informe que a rota não está disponível. Não prometa Events, análises de peças nem execução automática em segundo plano pelo Claude.

## Limites por conta

A pesquisa comum e a profunda consomem quotas distintas, por conta autenticada, compartilhadas entre ChatGPT, Claude, Codex e Microsoft: no beta, pesquisa comum 3 por dia e 10 por semana; pesquisa profunda 1 por dia e 7 por semana. O dia vira às 00:00 de America/Sao_Paulo e a semana, na segunda-feira às 00:00 (America/Sao_Paulo). Esses números podem mudar: a autoridade é o retorno do servidor, não esta skill.

Quando a resposta, ou a recusa por limite, trouxer `limites`, ele tem a forma `{dia: {usadas, limite}, semana: {usadas, limite}, renova_dia_em, renova_semana_em}`, com renova_* em ISO com offset; o servidor pode omiti-lo quando não consegue ler o saldo. Havendo `limites`, mostre ao usuário depois de cada pesquisa o saldo (usadas e limite do dia e da semana) e quando renova, a partir desses campos. Se `limite` vier null (contas administrativas e de teste), diga «sem limite» em vez de calcular saldo. Se `limites` não vier, não calcule saldo nem use os números desta skill como saldo: diga apenas que a pesquisa tem limite por conta e que o saldo não veio nesta resposta. Consultar uma pesquisa profunda (consultar_tarefa_lex) não consome unidade nova; uma pesquisa profunda já iniciada continua consumindo a sua mesmo se cancelada.

**Depois de uma recusa por limite, não repita a chamada**, nem com outra chave_idempotencia: se a recusa trouxe `limites`, informe o saldo e a renovação (renova_dia_em ou renova_semana_em, conforme o limite que recusou); se não trouxe, diga que o limite da conta foi atingido e que o saldo não veio. Ofereça as ferramentas de corpus (pesquisar_decisoes, pesquisar_precedentes, pesquisar_normas e as demais), que não consomem esse limite. Trate erro de quota como erro, não como pesquisa executada.

## Pesquisa regular

Use pesquisa_juridica_lex. Consulta e chave_idempotencia são obrigatórias; contexto, restricoes e conversa_id são opcionais (aceitam null). A chave tem 8 a 128 caracteres entre letras, dígitos, ponto, sublinhado, dois-pontos e hífen; use uma chave nova e única para cada pesquisa nova.

```json
{"consulta": "O STJ admite dano moral in re ipsa em caso de vazamento de dados pessoais?", "chave_idempotencia": "pesq-20261006-0001", "restricoes": "Apenas STJ e STF, de 2020 em diante."}
```

Preserve integralmente a pergunta, fatos, limites territoriais/temporais, fontes e demais restrições do usuário. O Lex exclui doutrina por padrão; não acrescente essa restrição à pergunta. Se o usuário pedir doutrina, preserve o pedido e informe a indisponibilidade devolvida pelo serviço: esta superfície ainda não a oferece. Esta pesquisa é síncrona: espere o resultado antes de responder. O retorno traz resposta_markdown, fontes e cobertura.

## Pesquisa profunda

Use pesquisa_profunda_lex somente quando a intenção do usuário justificar o fluxo assíncrono; os argumentos são os mesmos da regular. Ela devolve consulta_id, que é um ticket, não um resultado; avise o usuário de que a pesquisa foi iniciada e de que consome a quota profunda. Acompanhe com consultar_tarefa_lex, apenas com o consulta_id recebido:

```json
{"consulta_id": "<consulta_id recebido>"}
```

Os estados são na_fila, em_execucao, concluida, falhou, tempo_esgotado, cancelada e cancelamento_solicitado; o resultado só vem em concluida (campo resultado). Consulte com espaçamento razoável, sem laço rápido, e informe prazo_execucao. cancelar_tarefa_lex, com o mesmo consulta_id, solicita interrupção; cancelamento_solicitado ainda não confirma que a inferência parou, só cancelada confirma, e repetir o pedido é seguro. Não crie outra pesquisa para acompanhar uma tarefa.

## Repetição, conversa e fundamentação

Em resultado desconhecido após timeout ou perda de resposta, repita a mesma chamada com a mesma chave_idempotencia e todos os mesmos argumentos. Uma pesquisa nova exige chave nova. Nunca reutilize a chave com consulta, contexto, restrições ou conversa diferentes, e nunca remova filtro para fazer uma repetição passar. Essa regra vale para timeout, não para recusa por limite (ver acima).

## Histórico e continuidade

Contrato de histórico do MCP verificado em 2026-10-09. Use o conversa_id recebido para continuar uma conversa Lex: leitura e continuidade só ficam disponíveis por 168 horas (sete dias) desde a criação, conforme conversa_expira_em no resultado da pesquisa ou expira_em no histórico; leituras e novas mensagens não prolongam o prazo. Esse prazo de acesso não permite concluir que os dados foram apagados.

As ferramentas de histórico mostram somente o histórico de pesquisa MCP do Lex da conta autenticada e da jurisdição desta conexão. Não acessam conversas nativas do ChatGPT ou Claude, nem o histórico do Studio. Use cada ferramenta de histórico apenas quando ela estiver listada nesta conexão; a presença nesta skill não confirma disponibilidade.

Para encontrar uma conversa, use listar_conversas_lex, se estiver listada. O título é formado pela primeira consulta e limitado a 120 caracteres. O filtro consulta aceita até 120 caracteres e pesquisa somente os títulos, sem distinguir maiúsculas ou acentos; não pesquisa respostas. Cada chamada examina no máximo 20 conversas, da mais recente para a mais antiga. Para consultar páginas seguintes, mantenha a mesma consulta e passe o proximo_cursor recebido como cursor, inclusive depois de uma página vazia; só declare a busca completa quando proximo_cursor for null. Apresente títulos e, se útil, datas e quantidade de turnos como opções; mantenha conversa_id, consulta_id e ultima_consulta_id internos às chamadas.

Depois da escolha, consulte consultar_conversa_lex com o conversa_id correspondente. A ferramenta pagina turnos completos: continue com proximo_inicio até null quando precisar ler todo o histórico. Os itens incluem a consulta, contexto, restrições e resposta, mas não as fontes do resultado original. Para recuperar a pesquisa original com fontes, use abrir_painel_iajus com o consulta_id daquele turno, somente se a ferramenta estiver listada; não repita a pesquisa para recuperar um resultado anterior.

Para um novo turno na mesma conversa, envie o conversa_id, preserve o contexto e as restrições relevantes do usuário e use uma nova chave_idempotencia. Se o resultado ficar incerto, repita exatamente a mesma chamada com a mesma chave e os mesmos argumentos. Esta skill não cobre a exclusão de conversas.

Número de processo citado pelo usuário vai inteiro em consulta, na forma escrita («REsp 1.604.048/RS», CNJ de 20 dígitos); não o complete nem invente a origem. Recusa de entrada: reformule como a mensagem indicar. Falha ou timeout: diga que a pesquisa FALHOU, nunca «não encontrado».

Dados de um julgamento (relator, redator, vencidos, votos, placar, resultado, tese, data, órgão julgador) só valem se vierem de um trecho que o Lex devolveu nesta conversa; sem ele, diga «não localizei esse dado no trecho da decisão». Se nada voltou, não responda o mérito: diga que não localizou e ofereça nova pesquisa. Se o usuário contestar, reconsulte pelo número completo e cite o trecho, sem se esquivar.

Use somente fontes, cobertura, links e limitações presentes na resposta do Lex. Não converta uma busca sem fonte ou incompleta em confirmação jurídica. abrir_painel_iajus é o painel opcional: se o usuário pedir para abrir o mini Studio e a ferramenta estiver listada, chame-a; para exibir uma pesquisa existente, envie apenas o consulta_id recebido. O painel não inicia pesquisa e não é uma chamada adicional para responder à pergunta.

Se as ferramentas Lex não estiverem listadas, informe que a rota integrada do Lex não está disponível nesta conexão; não invente alias, nem replique a chamada com várias buscas de corpus.
