# Ontologia OJBU (ramos reais - códigos TPU)

Referência dos códigos da ontologia OJBU (alinhada ao CNJ/TPU). Consulte-a ao usar
`buscar_por_ontologia`, que recebe `l1_code` (código TPU do ramo, inteiro),
opcionalmente `l2_code`/`l3_code` (a sub-área exige o `l1_code` do seu ramo) **ou**
`tema_transversal`.

## Os 21 ramos L1 (código TPU → ramo)

São os nós reais da ontologia:

| `l1_code` | Ramo | | `l1_code` | Ramo |
|---|---|---|---|---|
| 14 | Tributário | | 9633 | Criança e Adolescente |
| 195 | Previdenciário | | 9985 | Administrativo e Dir. Público |
| 287 | Penal | | 10110 | Ambiental |
| 864 | Trabalho | | 11068 | Penal Militar |
| 899 | Civil | | 11428 | Eleitoral |
| 1156 | Consumidor | | 12480 | Saúde |
| 1209 | Processual Penal | | 12734 | Assistencial |
| 6191 | Internacional | | 12775 | Educação |
| 7724 | Registros Públicos | | 1146 | Marítimo |
| 8826 | Processual Civil e do Trabalho | | 11049 | Processual Penal Militar |

## Temas transversais (`tema_transversal=`)

- `DDG` - Digital e Proteção de Dados (ex.: LGPD)
- `DER` - Econômico e Regulação
- `DFN` - Financeiro e Orçamentário
- `DAG` - Agrário e Fundiário Rural
- `DID` - Pessoa Idosa e Envelhecimento

Use o tema transversal para recortes que cruzam ramos (ex.: LGPD → `DDG`), e o `l1_code`
para o ramo em si.
