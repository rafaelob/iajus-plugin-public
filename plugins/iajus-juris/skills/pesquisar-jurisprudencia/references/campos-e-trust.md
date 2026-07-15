# Campos canônicos + envelope de confiança (trust)

Referência dos campos canônicos que a busca devolve (taxonomia, matéria, autoria) e do
envelope de confiança `trust`. Consulte-a ao rotular precedentes por tipo/matéria, ao
atribuir a autoria de um acórdão e ao conferir vigência antes de amparar.

## Taxonomia + matéria + autoria

- **`buscar_qualificada` devolve a taxonomia CANÔNICA do precedente:** `tipo_canonico`
  (o tipo padronizado), `tipo_label` (rótulo PT-BR pronto para exibição) e `tipo_familia`
  (a família qualitativa: vinculante / editorial / …). Use-os para agrupar e rotular os
  precedentes por tipo, em vez de inferir do texto da ementa.
- **`materia`** acompanha cada qualificada (= `ramo_hint`, presente em ~100% delas): é a
  matéria/ramo canônico do precedente. Use como **facet de recorte** ("súmulas de
  Tributário") e em perguntas de **jurimetria** (distribuição por matéria).
- **`redator_acordao`** vem nos acórdãos: é o magistrado **redator** do acórdão (autoria
  pelo art. 941 do CPC), com `revisor` quando houver - o autor do acórdão a citar, distinto
  do relator sorteado nos casos de relator vencido.

## Envelope de confiança `trust`

- Os hits de busca (`buscar_regex`, `buscar_fts`, `buscar_por_ontologia`, `buscar_por_cnj`,
  `buscar_hibrida`) trazem `trust: {authority_tier, status_vigencia, trecho}`:
  `authority_tier` gradua a autoridade do órgão/tipo; **cheque `status_vigencia` antes de
  citar qualificada/legislação como amparo** - ato não-vigente vem sinalizado, nunca oculto.

## Vigência nas qualificadas

- **`buscar_qualificada`** - cada resultado traz `status_vigencia` (`vigente` /
  `cancelada` / …); canceladas/superadas saem **MARCADAS** (vigentes primeiro, com `aviso`
  quando nada vigente casa). `incluir_canceladas=false` oculta-as. Além do lookup por
  número, há o modo **navegar por matéria**:
  `buscar_qualificada(materia="Direito Tributário", tipo="sumula")`.

## Citação numérica dispara lookup exato

- Em `buscar_fts`/`buscar_semantica`, uma consulta que cita "súmula 145 do STF", "súmula
  vinculante 11", "OJ 191" ou "tema 1234" prepende o casamento EXATO por número
  (sinalizado em `signals.qualificada_numero`), antes dos hits textuais.
