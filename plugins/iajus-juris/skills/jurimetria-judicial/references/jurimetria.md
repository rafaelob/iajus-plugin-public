# Jurimetria agregada IAJUS (contagens/taxas exatas)

Referência detalhada da família de jurimetria. Consulte-a quando a pergunta for
QUANTITATIVA (volume, ranking, taxa de desfecho, lag) e você precisar da assinatura
exata de cada tool e das regras de honestidade completas.

As 7 tools de jurimetria são servidas do read-model agregado do Postgres (mantido por
projector - nunca varrem a tabela quente; respostas exatas e rápidas). Todas exigem um
**recorte** (regra scan-safety) e devolvem o **envelope de honestidade** `jurimetria`
`{snapshot_id, as_of, denominator_definition, value_kind, coverage_pct, truncado}`.

| Tool | Assinatura | Para quê |
|---|---|---|
| `jurimetria_volume` | `orgao?` e/ou `ano?` (ou `ano_de`/`ano_ate`); `top` 1-200 (padrão 50). **Pelo menos um recorte é obrigatório.** | Volume por órgão × ano. Ex.: `jurimetria_volume(orgao="tjrj")` → série anual do TJRJ; `jurimetria_volume(ano=2024, top=20)` → maiores órgãos em 2024. |
| `jurimetria_relator` | `orgao` (OBRIGATÓRIO), `relator?` (substring, case-insensitive), `top` 1-50 (padrão 25) | Quem mais relata num órgão + período de atuação (`ano_min`/`ano_max`). Rollup top-50 por volume. **LGPD:** figura nominal com n<20 é suprimida ("amostra insuficiente"). |
| `jurimetria_classe` | `orgao` (OBRIGATÓRIO), `ano?` ou `ano_de`/`ano_ate`, `top` 1-100 (padrão 25) | Volume por classe processual CNJ. O bucket `classe_cnj_code=-1` é a massa SEM classe resolvida; o envelope traz `cobertura_classe_pct`. Rótulos de código via `obter_protocolo_classificacao`. |
| `jurimetria_orgao_julgador` | `orgao` (OBRIGATÓRIO), `filtro?` (substring), `top` 1-100 (padrão 25) | Volume por câmara/turma/seção (unidade organizacional - sem supressão LGPD) + período de atuação. Ex.: "quais câmaras do TJRJ mais julgam?". |
| `jurimetria_resultado` | `orgao?` e/ou recorte de ano (`ano?` ou `ano_de`/`ano_ate`). **Pelo menos um recorte é obrigatório.** | Taxas de DESFECHO (provimento/improvimento etc.) por órgão × ano, dos rollups `agg_decisions_resultado(_cov)`. Cada desfecho vem com **denominador duplo** rotulado: `share_over_known` (n/decididas - a fração ENTRE as decisões cujo desfecho o extractor determinou) E `share_over_all` (n/total). **LGPD:** recortes com <20 decisões conhecidas são suprimidos ("amostra insuficiente"). |
| `jurimetria_lag_publicacao` | `orgao?` OU `ano?`. **Pelo menos um recorte é obrigatório.** | Lag de publicação (dias `data_publicacao − data_julgamento`, p50/p90) por órgão × ano, do rollup `agg_decisions_lag_pub`. É a ÚNICA métrica temporal servida hoje - **NÃO é duração do processo** (ajuizamento→trânsito). Órgãos sem `data_publicacao` (vários TJs) saem sem lag. |
| `jurimetria_desfecho_cruzado` | `orgao` (OBRIGATÓRIO) + um eixo de cruzamento (ex. `por="classe"` ou `por="relator"`), com recorte de ano opcional | Cruza a taxa de desfecho (provimento/improvimento) por um segundo eixo (classe, relator, câmara), numa janela limitada por dia. Mantém a supressão LGPD (célula com n<20 é suprimida). Use para "o TJRJ provê mais apelações ou agravos", sempre reportando o denominador. |

## Regras de honestidade (obrigatório)

- **Taxa de desfecho SÓ via `jurimetria_resultado`** - nunca infira provimento/improvimento
  a partir de contagens de volume. A tool traz **denominador duplo** (`share_over_known` vs
  `share_over_all`): reporte a taxa SEMPRE com o denominador, e o `coverage_pct`
  (=100·conhecidas/total). Cobertura baixa = **não** é representativa (o extractor abstém em
  dispositivo ambíguo - abstenção não é zero); diga "baixa cobertura", não uma taxa nua.
- **Lag de publicação (`jurimetria_lag_publicacao`) NÃO é duração do processo** - é só o
  intervalo publicação−julgamento. Quando `sem_cobertura` (coverage baixo) ou o órgão não
  expõe `data_publicacao`, reporte "sem cobertura" e NÃO estime.
- `aviso: "sem cobertura para o recorte"` = lacuna de ingestão/rollup, **não** volume
  zero no mundo real - reporte assim.
- **`total: 0` ou "sem cobertura" ≠ zero no mundo real** - é cobertura em andamento;
  avise o usuário, não afirme ausência de julgados.
- Reporte `as_of` (data do snapshot) quando o número embasar uma afirmação.
- **LGPD:** figura nominal (relator) ou célula cruzada com n<20 é suprimida ("amostra
  insuficiente") - não contorne a supressão nem estime o valor suprimido.
- Sem recorte (`orgao`/`ano`) a tool recusa com erro explicando - não insista; peça o
  recorte ao usuário.
