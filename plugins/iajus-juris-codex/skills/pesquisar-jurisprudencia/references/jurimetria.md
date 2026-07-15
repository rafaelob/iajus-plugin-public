# Jurimetria agregada IAJUS (contagens/taxas exatas)

Referência detalhada da família de jurimetria. Consulte quando a pergunta for
QUANTITATIVA (volume, ranking, taxa de desfecho, lag) e você precisar da assinatura
exata de cada tool e das regras de honestidade completas.

As 7 tools de jurimetria são servidas do read-model agregado do Postgres (rollups do
projector - nunca varrem a tabela quente). Todas exigem um **recorte** (`orgao` e/ou
`ano` - regra scan-safety) e devolvem o **envelope de honestidade** `jurimetria`
`{snapshot_id, as_of, denominator_definition, value_kind, coverage_pct, truncado}`.

| Tool | Assinatura | Retorno / uso |
|---|---|---|
| `jurimetria_volume` | `orgao?` e/ou `ano?` (ou `ano_de`/`ano_ate`); `top` 1-200 (padrão 50). Pelo menos um recorte. | Volume por órgão × ano. |
| `jurimetria_relator` | `orgao` (obrigatório), `relator?` (substring), `top` 1-50 (padrão 25). | Quem mais relata + período. LGPD n<20 suprimido. |
| `jurimetria_classe` | `orgao` (obrigatório), `ano?` ou `ano_de`/`ano_ate`, `top` 1-100 (padrão 25). | Volume por classe CNJ; `classe_cnj_code=-1` = sem classe; `cobertura_classe_pct`. |
| `jurimetria_orgao_julgador` | `orgao` (obrigatório), `filtro?` (substring), `top` 1-100 (padrão 25). | Volume por câmara/turma/seção (sem supressão LGPD). |
| `jurimetria_resultado` | `orgao?` e/ou recorte de ano. Pelo menos um recorte. | Taxa de desfecho por órgão × ano, com **denominador duplo** (`share_over_known`, `share_over_all`) + `coverage_pct`. LGPD n<20 suprimido. |
| `jurimetria_lag_publicacao` | `orgao?` OU `ano?`. Pelo menos um recorte. | Lag de publicação (dias publicação−julgamento, p50/p90). NÃO é duração do processo. |
| `jurimetria_desfecho_cruzado` | `orgao` (obrigatório) + eixo (`por="classe"`/`por="relator"`), recorte de ano opcional. | Taxa de desfecho cruzada por um segundo eixo, janela por dia. LGPD n<20 suprimido. |

## Regras de honestidade

- Taxa de desfecho SÓ via `jurimetria_resultado` / `jurimetria_desfecho_cruzado`, sempre
  com denominador duplo e `coverage_pct`. Cobertura baixa = "baixa cobertura", não taxa nua.
- Lag de publicação NÃO é duração do processo (ajuizamento→trânsito). Sem cobertura ou
  órgão sem `data_publicacao` → "sem cobertura", não estime.
- `total: 0` / "sem cobertura" ≠ zero no mundo real (cobertura em andamento).
- Reporte `as_of` quando o número embasar uma afirmação.
- LGPD: figura nominal / célula cruzada com n<20 é suprimida - não contorne nem estime.
- Sem recorte a tool recusa com `{erro:...}` - peça o recorte, não insista.
