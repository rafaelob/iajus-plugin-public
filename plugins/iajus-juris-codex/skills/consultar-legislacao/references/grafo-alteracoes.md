# Ler uma norma e conferir alterações

Escolha ferramentas pelo propósito e pelo schema listado nesta conexão:

- consultar_fonte_oficial com consulta.operacao alteracoes consulta eventos registrados para a identidade da norma. O resultado pode indicar que a fonte/executor não oferece histórico para aquele item.
- explorar_relacoes_juridicas com relacao.modo grafo_norma consulta relações do grafo para a norma indicada.
- pesquisar_artigos pode localizar dispositivos. Use o id_dispositivo devolvido para ler_documento com leitura.modo dispositivo.
- ler_documento com leitura.modo texto_norma lê o texto pela identidade normativa aceita; observe formato, limites, versão e recorte devolvidos.

Grafo e lista de alterações são evidências distintas. Não deduza vigência atual, completude histórica ou inexistência de mudança da falta de um evento. Para citar a redação, use o texto e a fonte retornados; sinalize limites de data, executor, território ou leitura integral.

Normas estaduais/municipais exigem que o retorno comprove a identidade e a leitura daquele recorte. O mapa de fontes em consultar_acervo é cadastral e não testa disponibilidade agora.

Se as ferramentas integradas não estiverem listadas, use apenas as rotas legacy descobertas: `obter_alteracoes_norma`, `obter_grafo_norma`, `obter_dispositivo_legal` e `obter_texto_norma`. Cada rota usa seu próprio schema; não envie campos de outra ferramenta.

Use as ferramentas e schemas disponíveis nesta conexão; se uma rota estiver indisponível, informe a limitação.
