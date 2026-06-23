# Changelog — iaJus (plugin Claude Code público)

Versões relevantes do plugin público `iajus-juris`. Formato baseado em
[Keep a Changelog](https://keepachangelog.com/); versionamento
[SemVer](https://semver.org/). O motor de busca e o corpus vivem no MCP remoto
iaJus — o plugin é o cliente fino.

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
