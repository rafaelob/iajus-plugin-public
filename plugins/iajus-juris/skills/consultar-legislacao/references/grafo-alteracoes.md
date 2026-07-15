# Grafo de norma + cadeia de alterações (legislação federal)

Referência detalhada da modelagem de alterações artigo por artigo (§11 do escopo
IAJUS). Consulte-a ao responder "esse artigo foi alterado/revogado?", "o que mudou
dentro da norma?" ou ao amparar numa redação vigente que pode ter sido alterada.

## `obter_alteracoes_norma`

Histórico de alterações **artigo por artigo**: a norma alteradora + a data de cada
evento. Leis antigas mudam de redação dispositivo a dispositivo; rode esta tool antes de
afirmar que uma redação está em vigor.

## `obter_grafo_norma` (o mapa completo)

Recebe `norma_ref` canônico (ex. `LEI_8112_1990`) + `max_depth` (1-20, padrão 8).
Retorna:

- **`cadeia_alteracoes`** - quem alterou a norma, recursivamente.
- **`dead_ends`** - normas revogadas/caducadas alcançáveis pela cadeia.
- **conversão MPV→LEI** - quando a norma é uma medida provisória convertida.
- **`citacoes`** - `cita` (o que a norma cita) e `citada_por` (quem a cita).
- **`alteracoes_dispositivo`** - eventos por dispositivo ("redação dada por",
  "revogado por", "regulamentado por") com `dispositivo_ref` + a norma alteradora. É o
  bloco que diz **o que mudou dentro da norma, artigo a artigo**.

## Vigência e amparo (regra dura)

- **Para amparo jurídico, sirva só `status=vigente`** e sinalize explicitamente
  `revogada`/`nao_recepcionada`. Se o dispositivo estiver revogado/alterado (visto em
  `obter_alteracoes_norma` ou no grafo `altera_norma`), diga isso: aponte a norma
  alteradora e a data; nunca apresente texto revogado como vigente.
- **Norma `is_amending_only`** (que só existe para alterar/revogar outra) não é fonte
  substantiva do direito - cite a norma alterada consolidada, não a norma alteradora.
- **Redação vigente de UM dispositivo** vem de `obter_dispositivo_legal` (isola
  artigo/parágrafo/inciso com a redação em vigor); o grafo/alterações dizem o histórico,
  o dispositivo diz o texto atual.
