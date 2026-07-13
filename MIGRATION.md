# Migração para o MCP v3 (plugin 2.0.0) - guia de-para

Em 2026-07-10 a superfície de tools do MCP IAJUS foi renomeada para o vocabulário
final v3. O plugin **2.0.0** já usa os nomes novos. Este guia cobre quem chama as
tools diretamente (scripts, agentes próprios, integrações MCP).

## O que mudou

Convenção de prefixos por semântica:

| Prefixo | Semântica |
|---|---|
| `buscar_*` | busca ranqueada (retorna lista de hits) |
| `obter_*` | fetch de UM recurso identificado (norma, dispositivo, seção) |
| `listar_*` | enumeração/catálogo |
| `jurimetria_*` | agregados exatos do read-model (envelope de honestidade) |

Contagem de tools deste plugin após o corte: **34 tools**.

## Compatibilidade - nomes antigos continuam funcionando (por ora)

O servidor mantém um **middleware de alias**: chamadas `tools/call` com nome
antigo são roteadas para a tool nova, com resposta idêntica - nenhum cliente
quebra no corte. Os nomes antigos NÃO aparecem mais em `tools/list`.

**Prazo de deprecação: os aliases são mantidos por 1 release MAJOR.** Migre as
chamadas para os nomes novos antes da 3.0.0.

## De-para completo (renomeadas)

| Nome antigo | Nome novo |
|---|---|
| `buscar_jurimetria` | `jurimetria_volume` |
| `classificar_norma` | `obter_classificacao_tipo` |
| `consultar_grafo_legislacao` | `obter_grafo_norma` |
| `consultar_informativos_stf` | `buscar_informativos_stf` |
| `consultar_informativos_stj` | `buscar_informativos_stj` |
| `consultar_legislacao_uf` | `buscar_norma_uf` |
| `consultar_ontologia_juridica` | `obter_ontologia_juridica` |
| `consultar_protocolo_classificacao` | `obter_protocolo_classificacao` |
| `consultar_qualificada` | `buscar_qualificada` |
| `consultar_codigo` | `obter_codigo` |
| `estatisticas_da_base` | `obter_estatisticas_base` |
| `ler_dispositivo_legal` | `obter_dispositivo_legal` |
| `listar_legislacao_federal` | `listar_normas` |
| `obter_alteracoes_legislacao` | `obter_alteracoes_norma` |
| `pesquisar_legislacao` | `buscar_dispositivos` |
| `quem_cita_dispositivo` | `buscar_citantes_dispositivo` |

## Consolidações (várias tools antigas → UMA nova, parâmetro `esfera`)

As famílias por esfera viraram uma tool única; passe `esfera`
(`federal` | `estadual` | `municipal`) quando quiser restringir:

| Nomes antigos | Nome novo |
|---|---|
| `buscar_legislacao_federal`, `consultar_legislacao_federal`, `consultar_legislacao_estadual`, `consultar_legislacao_municipal` | `buscar_norma_fonte_oficial` |
| `obter_texto_legislacao`, `obter_texto_legislacao_markdown`, `obter_texto_legislacao_estadual`, `obter_texto_legislacao_municipal` | `obter_texto_norma` |
| `cobertura_legislacao_uf`, `legislacao_estadual_status`, `legislacao_municipal_status` | `obter_cobertura_legislacao` |

## Inalteradas (nome já era o final)

`buscar_fts`, `buscar_hibrida`, `buscar_semantica`, `buscar_regex`,
`buscar_por_cnj`, `buscar_por_citacoes`, `buscar_por_ontologia`,
`buscar_norma_por_nome`, `buscar_norma_por_numero`, `buscar_juris_widget`,
`jurimetria_classe`, `jurimetria_relator`, `jurimetria_orgao_julgador`,
`jurimetria_resultado`, `jurimetria_lag_publicacao`,
`jurimetria_desfecho_cruzado`, `jurimetria_volume`,
`obter_dispositivos_citados`, `obter_versoes_qualificada`,
`obter_secao_doutrina`, `obter_unidade_completa`, `search`, `fetch`.

## Como migrar

1. Atualize o plugin para **2.0.0** (reinstale pelo marketplace) - as skills já
   pré-autorizam os nomes novos nos dois aliases de namespace.
2. Se você chama tools por nome em código próprio, troque pelos nomes novos da
   tabela acima; nas consolidadas, adicione `esfera` se dependia da variante.
3. Nada mais muda: parâmetros, formato de resposta, autenticação (OAuth 2.1 /
   chave `ik_*` fallback) e endpoints permanecem os mesmos.
