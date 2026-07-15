# Grafo de norma + cadeia de alterações (legislação federal)

Referência da modelagem de alterações artigo por artigo (§11 do escopo IAJUS). Consulte
ao responder "esse artigo foi alterado/revogado?", "o que mudou dentro da norma?" ou ao
amparar numa redação vigente que pode ter sido alterada.

## `obter_alteracoes_norma`

Histórico de alterações artigo por artigo: a norma alteradora + a data de cada evento.
Rode antes de afirmar que uma redação está em vigor.

## `obter_grafo_norma`

`norma_ref` canônico (ex. `LEI_8112_1990`) + `max_depth` (1-20, padrão 8). Retorna:

- `cadeia_alteracoes` - quem alterou a norma, recursivamente.
- `dead_ends` - normas revogadas/caducadas alcançáveis.
- conversão MPV→LEI - quando a norma é uma medida provisória convertida.
- `citacoes` - `cita` (o que a norma cita) e `citada_por` (quem a cita).
- `alteracoes_dispositivo` - eventos por dispositivo ("redação dada por", "revogado por",
  "regulamentado por") com `dispositivo_ref` + a norma alteradora. É o bloco que diz o que
  mudou dentro da norma, artigo a artigo.

## Vigência e amparo (invariante)

- Para amparo, sirva só `status=vigente` e sinalize `revogada`/`nao_recepcionada`. Se o
  dispositivo estiver revogado/alterado, aponte a norma alteradora e a data; nunca
  apresente texto revogado como vigente.
- Norma `is_amending_only` (que só existe para alterar outra) não é fonte substantiva -
  cite a norma alterada consolidada.
- A redação vigente de UM dispositivo vem de `obter_dispositivo_legal`; o grafo/alterações
  dizem o histórico, o dispositivo diz o texto atual.
