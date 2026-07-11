# Changelog — IAJUS (plugin Claude Code público)

Versões relevantes do plugin público `iajus-juris`. Formato baseado em
[Keep a Changelog](https://keepachangelog.com/); versionamento
[SemVer](https://semver.org/). O motor de busca e o corpus vivem no MCP remoto
IAJUS — o plugin é o cliente fino.

## [2.0.0] — 2026-07-10

Corte MCP v3: renomeacao da superficie de tools para o vocabulario final (``buscar_*`` busca ranqueada, ``obter_*`` fetch de um recurso, ``listar_*`` enumeracao, ``jurimetria_*`` agregados). Bump MAJOR porque nomes de tool mudam (quebra clientes que chamavam os nomes antigos). Contagem final de tools por perfil: iajus 34 · vadefocus 32.

### Mudado

De-para das tools que este plugin referencia:

| Nome antigo | Nome novo |
|---|---|
| `buscar_jurimetria` | `jurimetria_volume` |
| `buscar_legislacao_federal` | `buscar_norma_fonte_oficial` |
| `classificar_norma` | `obter_classificacao_tipo` |
| `cobertura_legislacao_uf` | `obter_cobertura_legislacao` |
| `consultar_grafo_legislacao` | `obter_grafo_norma` |
| `consultar_informativos_stf` | `buscar_informativos_stf` |
| `consultar_informativos_stj` | `buscar_informativos_stj` |
| `consultar_legislacao_estadual` | `buscar_norma_fonte_oficial` |
| `consultar_legislacao_federal` | `buscar_norma_fonte_oficial` |
| `consultar_legislacao_municipal` | `buscar_norma_fonte_oficial` |
| `consultar_ontologia_juridica` | `obter_ontologia_juridica` |
| `consultar_protocolo_classificacao` | `obter_protocolo_classificacao` |
| `consultar_qualificada` | `buscar_qualificada` |
| `estatisticas_da_base` | `obter_estatisticas_base` |
| `legislacao_estadual_status` | `obter_cobertura_legislacao` |
| `legislacao_municipal_status` | `obter_cobertura_legislacao` |
| `ler_dispositivo_legal` | `obter_dispositivo_legal` |
| `listar_legislacao_federal` | `listar_normas` |
| `obter_alteracoes_legislacao` | `obter_alteracoes_norma` |
| `obter_texto_legislacao` | `obter_texto_norma` |
| `obter_texto_legislacao_estadual` | `obter_texto_norma` |
| `obter_texto_legislacao_markdown` | `obter_texto_norma` |
| `obter_texto_legislacao_municipal` | `obter_texto_norma` |
| `pesquisar_legislacao` | `buscar_dispositivos` |
| `quem_cita_dispositivo` | `buscar_citantes_dispositivo` |

Guia completo de migração (50 entradas, consolidações por `esfera`, política de
aliases): `MIGRATION.md` na raiz deste repositório.

## [1.7.3] — 2026-07-10

- Metadados: separadores tipograficos normalizados (sem travessao) nos manifests e catalogos (Claude + Codex). Sem mudanca de tools.

## [1.7.2] — 2026-07-08

Correção de compatibilidade com clientes Claude: a superfície geral do MCP
(`mcp.iajus.com.br/mcp`) deixou de servir os componentes visuais do ChatGPT
App (`buscar_juris_widget`, `search`, `fetch` e o metadado MCP-Apps `ui.*`).
Clientes Claude passaram a validar o domínio de UI contra o sandbox
`{hash}.claudemcpcontent.com` e algumas pesquisas falhavam com
"Invalid ui.domain format". Os widgets continuam disponíveis apenas na
superfície dedicada do ChatGPT App. A skill de pesquisa não referencia mais
`buscar_juris_widget` (em clientes comuns a busca é a `buscar_hibrida`).
Superfície geral: 42 ferramentas.

## [1.7.1] — 2026-07-08

Correção de conexão OAuth: o servidor MCP passa a anunciar os *scopes*
suportados (`openid email profile offline_access`) no metadado de
*protected-resource* (antes vinha vazio), para que clientes estritos
(Claude.ai web, ChatGPT connector) completem a negociação OAuth com
`offline_access` e obtenham *refresh token* — sem isso a sessão expirava e o
cliente reconectava parcialmente, exibindo **poucas ferramentas** ou
ferramentas "não autorizadas". Bump PATCH (correção; superfície de tool
inalterada — as 45 ferramentas continuam iguais). Reinstale/atualize o plugin
para o cliente re-negociar a conexão.

### Corrigido (cobertura de skills)

- **As skills passam a pré-autorizar as 4 tools novas do servidor** que ficaram
  de fora da 1.7.0: `obter_versoes_qualificada` (histórico de redação de
  súmula/tema), `obter_dispositivos_citados` e `quem_cita_dispositivo` (grafo de
  citações CIT-04) e `jurimetria_desfecho_cruzado` (taxa de desfecho cruzada por
  eixo). Todas entram na skill `pesquisar-jurisprudencia` nos DOIS aliases
  (`mcp__iajus__*` e `mcp__plugin_iajus-juris_iajus__*`). A skill também passa a
  cobrir `buscar_juris_widget` (busca híbrida na superfície de conector). Uma
  tool que existe no servidor mas não aparece em nenhum `allowed-tools` de skill
  não vem pré-permitida ao cliente (o sintoma "poucas tools / não autorizadas");
  um novo *gate* offline (`tests/saas/test_plugin_skills_tool_coverage.py`)
  trava zero-stale + cobertura total + ambos os aliases para nunca mais
  regredir.

## [1.7.0] — 2026-07-08

Sincroniza as skills com a **superfície MCP v2.0** consolidada no servidor: dois
renames de tool, e a consolidação da navegação por facet na família de jurimetria
agregada. Bump MINOR (mudança de superfície de tool).

### Mudado

- **`buscar_grafo` → `buscar_por_citacoes`** — a busca de grafo de citações
  (`legal_edges`: quem cita uma súmula/tema, o que um acórdão cita) foi renomeada no
  servidor; o nome antigo vazava o detalhe de implementação "grafo". As skills
  `pesquisar-jurisprudencia` (tabela de modalidades + boas práticas + `allowed-tools`) e
  o agente `pesquisador-juris` passam a usar o novo nome. Comportamento idêntico.
- **`estatisticas_corpus_pg` → `estatisticas_da_base`** — a introspecção ao vivo do
  corpus foi renomeada (o sufixo `_pg` vazava o motor). A skill `corpus-status`
  (`allowed-tools` + corpo) e as referências cruzadas em `pesquisar-jurisprudencia` e
  `consultar-legislacao` apontam para o novo nome.

### Removido

- **`buscar_jurimetria` (navegação/facet por colunas)** sai das skills e do agente: a
  navegação estruturada foi **consolidada** na família de jurimetria agregada exata já
  documentada — `jurimetria_volume` / `jurimetria_relator` / `jurimetria_classe` /
  `jurimetria_orgao_julgador` / `jurimetria_resultado` / `jurimetria_lag_publicacao` —
  que respondem VOLUME, ranking, classe/câmara, taxa de desfecho (denominador duplo) e
  lag de publicação, todas com o envelope de honestidade. Para números, use essas seis.

### Notas

- A contagem passa de "8 modalidades de busca" para **7** (semântica, híbrida, FTS,
  regex, CNJ, ontologia, citações) + a família de **jurimetria agregada** + qualificadas.
  As quatro `pesquisar_*` de lookup legadas (jurisprudência/indexed/tema/processo) já
  não eram documentadas nas skills — o perfil público as esconde no servidor.
- As duas variantes (`iajus-juris` e `iajus-juris-codex`) permanecem byte-idênticas nos
  corpos das skills; todas as descrições de frontmatter seguem < 500 chars.

## [1.6.1] — 2026-07-03

Documenta e habilita as duas tools de jurimetria já servidas no perfil geral `iajus`
mas ausentes das skills, e conserta a regra de honestidade agora obsoleta.

### Adicionado

- **`jurimetria_resultado`** documentada e habilitada na skill `pesquisar-jurisprudencia`:
  taxas de DESFECHO (provimento/improvimento) por órgão × ano, dos rollups
  `agg_decisions_resultado(_cov)`, com **denominador duplo** rotulado (`share_over_known`
  vs `share_over_all`) e `coverage_pct`, LGPD n<20 suprimido. Recorte obrigatório.
- **`jurimetria_lag_publicacao`** documentada e habilitada na mesma skill: lag de
  publicação (dias `data_publicacao − data_julgamento`, p50/p90) por órgão × ano, do
  rollup `agg_decisions_lag_pub` — a ÚNICA métrica temporal servida hoje. Recorte
  obrigatório.

### Corrigido

- **Regra de honestidade obsoleta** na skill `pesquisar-jurisprudencia`: o texto dizia
  "taxas de resultado ainda não são servidas / `taxas_resultado: sem cobertura` / NUNCA
  infira taxa" — mas `jurimetria_resultado` **passou a servir exatamente essas taxas**. A
  regra agora orienta a usar `jurimetria_resultado` com o denominador duplo e o
  `coverage_pct`, e separa "não é duração de processo" para o lag de publicação. As duas
  variantes (`iajus-juris` e `iajus-juris-codex`) permanecem byte-idênticas; todas as
  descrições de frontmatter seguem < 500 chars (a descrição não mudou — as tools novas
  vivem no corpo + `allowed-tools`).

### Diferido (dependência server-side)

- **Grafo de citação de dispositivos e versões de qualificada** — `obter_dispositivos_citados`,
  `quem_cita_dispositivo` e `obter_versoes_qualificada` NÃO entram nas skills ainda: o
  allowlist curado do perfil `iajus` (`saas/mcp/profiles.py::IAJUS_TOOLS`) ainda não as
  lista, então o prune curado as descartaria silenciosamente ("Unknown tool"). Documentá-las
  agora referenciaria tools não-chamáveis. Entram na skill DEPOIS que o perfil server-side
  passar a servi-las.

## [1.6.0] — 2026-07-02

Documenta e habilita a superfície nova do MCP servida no perfil geral `iajus`:
jurimetria agregada exata, grafo de legislação com alterações por dispositivo (§11) e
vigência (`status_vigencia`) nas qualificadas e nos hits de busca.

### Adicionado

- **4 tools de jurimetria agregada** documentadas e habilitadas na skill
  `pesquisar-jurisprudencia`: `jurimetria_volume` (volume por órgão × ano; recorte
  obrigatório), `jurimetria_relator` (ranking de relatores de um órgão; LGPD n<20
  suprimido), `jurimetria_classe` (volume por classe CNJ + `cobertura_classe_pct`) e
  `jurimetria_orgao_julgador` (volume por câmara/turma/seção). Contagens EXATAS do
  read-model agregado (rollups do projector — scan-safe), sempre com o **envelope de
  honestidade** `{snapshot_id, as_of, denominator_definition, value_kind, coverage_pct,
  truncado}`; taxas de resultado seguem "sem cobertura" (nunca inferidas). A skill agora
  distingue: números exatos → `jurimetria_*`; navegação/facet estruturada →
  `buscar_jurimetria`.
- **Grafo de legislação por dispositivo (§11)** na skill `consultar-legislacao`:
  `consultar_grafo_legislacao` passou a retornar `alteracoes_dispositivo` (eventos por
  artigo — "redação dada por"/"revogado por"/"regulamentado por", com `dispositivo_ref`
  + norma alteradora) e `citacoes` (`cita`/`citada_por`), além da cadeia de alterações,
  dead-ends e conversão MPV→LEI.
- **Vigência em toda a superfície** documentada nas duas skills de busca:
  `consultar_qualificada` marca `status_vigencia` (canceladas/superadas saem MARCADAS,
  vigentes primeiro; `incluir_canceladas=false` oculta; novo modo por matéria via
  `materia=`); hits de busca trazem o envelope de confiança `trust`
  `{authority_tier, status_vigencia, trecho}`; em legislação, `buscar_hibrida` serve por
  padrão só normas em vigor (`incluir_historico=true` para pesquisa histórica) e expõe
  `is_amending_only`; citações numéricas ("súmula 145 do STF", "tema 1234") disparam
  lookup exato prependado em `buscar_fts`/`buscar_semantica`.

### Corrigido

- **Banner de versão do README** estava "1.4.1" com manifestos 1.5.0 (mesma classe do
  fix da 1.4.1); README e manifestos agora dizem **quatro** skills (a `corpus-status`
  da 1.5.0 faltava na tabela "O que vem no plugin" e nas descrições "três skills").
- **Recorte de ano na skill de jurisprudência:** `buscar_semantica` aceita `ano` (um
  ano exato), não `ano_min`/`ano_max` (que são da `buscar_hibrida`/FTS/regex/ontologia);
  `ramo_l1` vale para ambas (não só para a híbrida).
- **Pisos temporais do corpus** atualizados na skill (desde 2000 em todos os
  tribunais — exceções: controle concentrado do STF desde 1988, TCU desde 1992,
  TRF6 desde 2022; o texto anterior dizia "2013-2026 / TST-TRTs-TREs 2016-2026").

## [1.5.0] — 2026-06-28

Nova skill de **estado do corpus ao vivo** e semântica de corpus vivo/em crescimento
nas skills de busca. Descrições de SKILL.md encurtadas para < 500 chars (regra do
operador 2026-06-28).

### Adicionado

- **Skill `corpus-status`** — ensina e habilita a tool `estatisticas_corpus_pg`, que lê
  o **read-model Postgres ao vivo** (o mesmo que as buscas consultam) e responde "o que
  tem na base AGORA?": por família (jurisprudência/doutrina/legislação) total de
  unidades, nº de órgãos, faixa de anos e cobertura de embedding `_3s`/FTS; por órgão
  contagem + anos cobertos; por espécie de qualificada (vigentes/canceladas); e
  legislação por esfera e status. Totalmente data-driven — nova ingestão aparece sem
  mudança de skill. Presente nas duas variantes (`iajus-juris` e `iajus-juris-codex`),
  bodies byte-idênticos.
- **Semântica de corpus VIVO** nas skills `pesquisar-jurisprudencia` e
  `consultar-legislacao`: um `total: 0` para um órgão/ano/norma já em cobertura =
  **cobertura em andamento**, não inexistência; órgãos/anos/famílias novos aparecem na
  busca automaticamente. As skills apontam para `corpus-status` para conferir o estado
  atual.

### Mudado

- **Descrições de frontmatter das SKILL.md encurtadas para < 500 caracteres** (regra do
  operador 2026-06-28, travada em `tests/saas/test_plugin_marketplace_contracts.py`).
  As três skills de busca tinham descrições de 719-778 chars; o detalhe vive no corpo da
  skill, a descrição é só o gatilho. Significado, gatilhos "Acione quando…" e diacríticos
  preservados.

## [1.4.1] — 2026-06-27

Documenta tools do MCP que já eram servidas no perfil `iajus` mas não apareciam em
nenhuma skill, e conserta referências de caminho quebradas nos READMEs.

### Adicionado

- **Skill `consultar-legislacao`** passa a documentar e habilitar as tools de **amparo
  §11** `buscar_norma_por_nome` (resolve a norma pelo nome/apelido — ex.: "CLT", "Código
  de Defesa do Consumidor") e `buscar_norma_por_numero` (resolve por `tipo` + `numero` —
  ex.: "Lei 8078"). Ambas devolvem o **`status` de vigência** (vigente / revogada) e
  **substituem** os antigos `pesquisar_*` de lookup; para amparo jurídico, sirva apenas
  `status=vigente`. A mesma skill ganha `consultar_protocolo_classificacao` (protocolo de
  decisão CNJ/TPU).
- **Skill `pesquisar-jurisprudencia`** passa a documentar e habilitar
  `consultar_informativos_stf` e `consultar_informativos_stj` (informativos de
  jurisprudência do STF/STJ — a síntese oficial dos julgados de destaque por edição).

### Corrigido

- **Referências de caminho quebradas nos READMEs** repontadas para caminhos in-package
  válidos no espelho público (`saas/plugin/plugins/iajus-juris-codex/README.md` →
  `plugins/iajus-juris-codex/README.md`; `saas/clients/codex/config.toml.example` →
  `./.mcp.json`).
- **Banner de versão do README** do plugin Claude Code corrigido (estava "1.3.0" enquanto
  os manifestos e o CHANGELOG já eram 1.4.0).

## [1.4.0] — 2026-06-24

Reflete os campos canônicos novos do MCP (taxonomia + matéria + autoria) que o agente
passa a citar e a usar como facet de recorte e jurimetria.

### Adicionado

- **Skill `pesquisar-jurisprudencia`** documenta os campos novos do MCP: em
  `consultar_qualificada`, a taxonomia canônica `tipo_canonico` / `tipo_label` /
  `tipo_familia` (vinculante/editorial/…) e `materia` (matéria/ramo canônico, `ramo_hint`,
  presente em ~100% das qualificadas — facet de recorte e de jurimetria); nos acórdãos,
  `redator_acordao` e `revisor` (a autoria do acórdão pelo art. 941 do CPC, distinta do
  relator sorteado quando vencido). Orienta o agente a agrupar por `materia`/`tipo_label`
  e a atribuir a autoria ao redator.

## [1.3.0] — 2026-06-18

Autenticação por **OAuth 2.1 por padrão** (login no navegador, refresh automático);
a chave `ik_*` continua disponível como **fallback manual** (ver README).

### Mudado

- **OAuth 2.1 vira o padrão de autenticação** — na primeira conexão o Claude Code
  detecta o `401` do MCP, descobre o Authorization Server pelo Protected Resource
  Metadata (`/.well-known/oauth-protected-resource/mcp` → AS `app.iajus.com.br`) e
  abre o **login no navegador** (a mesma conta da aplicação). O token é guardado com
  segurança e **renovado automaticamente** (escopo `offline_access`); nenhuma chave é
  digitada nem guardada por padrão.
- **Chave `ik_*` (Bearer) como fallback manual** — para quem prefere token estático a
  OAuth, registre o servidor fora do plugin com o header Bearer (ver README). A chave
  é validada server-side; nunca trafega na URL nem em log.
- **3 skills no plugin** — `pesquisar-jurisprudencia`, `consultar-legislacao`
  (federal) e `consultar-legislacao-estadual` (estadual e municipal).
- **8 modalidades de busca + qualificadas** descritas no plugin (`buscar_semantica`,
  `buscar_hibrida`, `buscar_fts`, `buscar_regex`, `buscar_por_cnj`,
  `buscar_por_ontologia`, `buscar_grafo`, `buscar_jurimetria`) e
  `consultar_qualificada`.

## [1.x] — funcionalidade acumulada

Compatível com a 1.0.0 (endpoint MCP e as modalidades de busca não mudam).

### Adicionado

- **Novas famílias de órgãos** no corpus, além de superiores/TJs/TRFs/TRTs/TREs:
  - **Tribunais de Contas** — jurisdição "contas": TCU + TCEs (e TCMs).
  - **Jurisprudência administrativa** — CARF (Conselho Administrativo de Recursos
    Fiscais), com CRSFN e CCMG na mesma família administrativa.
  - **Turmas Recursais dos JEFs** (TR1–TR6) — recursos dos Juizados Especiais
    Federais.
- Famílias descritas qualitativamente: **tribunais superiores, TJs, TRFs, TRTs, TREs,
  Tribunais de Contas, Turmas Recursais, administrativo CARF + legislação federal,
  estadual e municipal**, cobrindo **milhões de acórdãos**.
- **Busca semântica e híbrida cobrem jurisprudência e legislação** — um termo
  conceitual recupera também dispositivos legais pertinentes; faceta por tribunal mais
  precisa na busca semântica.

### Corrigido

- **Resultados de busca trazem campos úteis** (relator, data de julgamento, ementa e
  `link_completo` estável) em vez de internals técnicos. Links oficiais
  deep-per-record permanecem a regra de citação.

## [1.0.0] — 2026-06-03

Release estável inicial: skills model-invoked (jurisprudência, legislação federal,
legislação estadual/municipal), servidor MCP remoto `iajus` configurado, modalidades
de busca e classificação OJBU/CNJ.
