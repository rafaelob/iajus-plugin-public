---
name: pesquisa-juridica-lex
description: Solicita pesquisa jurídica integrada ao Lex com consulta, contexto e restrições preservados. Use somente quando a ferramenta Lex correspondente estiver realmente disponível.
allowed-tools: mcp__iajus__pesquisa_juridica_lex, mcp__iajus__pesquisa_profunda_lex, mcp__iajus__consultar_tarefa_lex, mcp__iajus__cancelar_tarefa_lex, mcp__iajus__abrir_painel_iajus
---

# Pesquisa jurídica com Lex

Use pesquisa_juridica_lex, pesquisa_profunda_lex, consultar_tarefa_lex, cancelar_tarefa_lex e abrir_painel_iajus somente quando cada ferramenta correspondente estiver listada nesta conexão. Se não estiver, informe que a rota não está disponível. Não prometa Events, análises de peças ou execução automática em segundo plano pelo Claude.

Para pesquisa regular, use pesquisa_juridica_lex quando listada. Consulta e chave_idempotencia são obrigatórias; contexto, restricoes e conversa_id são opcionais. Preserve integralmente a pergunta, fatos, limites territoriais/temporais, fontes e demais restrições do usuário. O Lex exclui doutrina por padrão; não acrescente essa restrição à pergunta. Se o usuário pedir doutrina, preserve o pedido e informe a indisponibilidade devolvida pelo serviço: esta superfície ainda não a oferece. Esta pesquisa é síncrona: espere o resultado antes de responder.

Para uma pesquisa profunda, use pesquisa_profunda_lex somente quando a ferramenta estiver listada e a intenção do usuário justificar o fluxo assíncrono. Ela devolve consulta_id; isso é um ticket, não um resultado. Consulte consultar_tarefa_lex apenas com o consulta_id recebido. cancelar_tarefa_lex solicita interrupção; um estado de cancelamento solicitado ainda não confirma que a inferência parou. Não crie outra pesquisa para acompanhar uma tarefa.

Em resultado desconhecido após timeout ou perda de resposta, repita a mesma chamada com a mesma chave_idempotencia e todos os mesmos argumentos. Uma chamada nova exige uma chave nova. Nunca reutilize a chave com consulta, contexto, restrições ou conversa diferentes, e nunca remova filtro para fazer uma repetição passar.

A conversa identificada por conversa_id vale exatamente 168 horas desde a criação, sem renovação, e fica separada do histórico do Studio. Os limites são 3 pesquisas regulares por dia e 10 por semana, mais 1 pesquisa profunda por dia e 7 por semana, por conta autenticada; as janelas usam America/Sao_Paulo. Trate erro de quota como erro, não como pesquisa executada.

Use somente fontes, cobertura, links e limitações presentes na resposta do Lex. Não converta uma busca sem fonte ou incompleta em confirmação jurídica. Se o usuário pedir para abrir o mini Studio e abrir_painel_iajus estiver listado, chame-o; para exibir uma pesquisa profunda existente, envie apenas o consulta_id recebido. O painel não inicia pesquisa e não é uma chamada adicional para responder à pergunta.

Se as ferramentas Lex não estiverem listadas, informe que a rota integrada do Lex não está disponível nesta conexão; não invente alias, nem replique a chamada com várias buscas de corpus.
