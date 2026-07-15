# Campos canônicos + envelope de confiança (trust)

Referência dos campos canônicos que a busca devolve e do envelope de confiança `trust`.
Consulte ao rotular precedentes por tipo/matéria, ao atribuir autoria de um acórdão e ao
conferir vigência antes de amparar.

## Taxonomia + matéria + autoria

- `buscar_qualificada` devolve a taxonomia CANÔNICA: `tipo_canonico`, `tipo_label`
  (rótulo PT-BR) e `tipo_familia` (vinculante / editorial / …). Agrupe/rotule por eles,
  não pelo texto da ementa.
- `materia` (= `ramo_hint`, ~100% das qualificadas): matéria/ramo canônico. Use como facet
  de recorte e em jurimetria.
- `redator_acordao` (nos acórdãos): o redator, autoria pelo art. 941 do CPC, com `revisor`
  quando houver - distinto do relator sorteado quando vencido.

## Envelope de confiança `trust`

- Os hits de busca (`buscar_regex`, `buscar_fts`, `buscar_por_ontologia`, `buscar_por_cnj`,
  `buscar_hibrida`) trazem `trust: {authority_tier, status_vigencia, trecho}`. Cheque
  `status_vigencia` antes de citar qualificada/legislação como amparo.

## Vigência nas qualificadas

- `buscar_qualificada` traz `status_vigencia` (`vigente` / `cancelada` / …); canceladas
  saem MARCADAS (vigentes primeiro; `aviso` quando nada vigente casa). `incluir_canceladas=false`
  oculta. Modo por matéria: `buscar_qualificada(materia="Direito Tributário", tipo="sumula")`.

## Citação numérica dispara lookup exato

- Em `buscar_fts`/`buscar_semantica`, uma consulta que cita "súmula 145 do STF", "SV 11",
  "OJ 191" ou "tema 1234" prepende o casamento EXATO por número (`signals.qualificada_numero`)
  antes dos hits textuais.
