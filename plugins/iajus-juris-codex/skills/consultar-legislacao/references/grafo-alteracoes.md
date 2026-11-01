# Ler uma norma e conferir alterações

Escolha ferramentas pelo propósito e pelo schema listado nesta conexão:

- consultar_alteracoes_norma, com numero, tipo e ano, consulta eventos registrados para a identidade da norma federal. O resultado pode indicar que a fonte/executor não oferece histórico para aquele item.
- explorar_relacoes_juridicas com relacao.modo grafo_norma consulta relações do grafo para a norma indicada.
- pesquisar_artigos pode localizar dispositivos. Use o id_dispositivo devolvido para ler_documento com leitura.modo dispositivo.
- ler_documento com leitura.modo texto_norma lê o texto pela identidade normativa aceita; observe formato, limites, versão e recorte devolvidos.

Grafo e lista de alterações são evidências distintas. Não deduza vigência atual, completude histórica ou inexistência de mudança da falta de um evento. Para citar a redação, use o texto e a fonte retornados; sinalize limites de data, executor, território ou leitura integral.

Não apresente redação revogada como amparo vigente. Quando o retorno identificar norma apenas alteradora (is_amending_only=true), use como amparo substantivo a norma alterada consolidada no recorte temporal pedido. Ao explicar uma alteração, informe norma alteradora e data quando retornadas.

Normas estaduais/municipais exigem que o retorno comprove a identidade e a leitura daquele recorte. O mapa de fontes em consultar_cobertura_legislacao é cadastral e não testa disponibilidade agora.

Exemplos (federal; o grafo usa a norma_id canônica, como LEI_8112_1990):

```text
consultar_alteracoes_norma  {"numero": "8112", "tipo": "LEI", "ano": 1990}
explorar_relacoes_juridicas {"relacao": {"modo": "grafo_norma", "filtros": {"norma_ref": "LEI_8112_1990"}}}
```

consultar_alteracoes_norma só cobre norma federal. Para estadual ou municipal não há consulta de alterações: diga que a fonte consultada não oferece esse histórico.

Use as ferramentas e schemas disponíveis nesta conexão; se uma rota estiver indisponível, informe a limitação.
