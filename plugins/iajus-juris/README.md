# iaJus — plugin Claude Code (jurisprudência, doutrina & legislação BR)

Um plugin: você ganha **três skills** que ensinam o agente a pesquisar, **citar** e
classificar jurisprudência, doutrina e legislação brasileira **+** o **servidor MCP
remoto iaJus** já configurado. Não precisa configurar o MCP na mão.

> Este repositório é **público** e contém **apenas o manifesto** do plugin (skills +
> configuração do MCP) — **zero segredo**. O acesso aos dados é gateado **na camada
> MCP** pela sua chave `ik_*`: sem chave válida, toda busca responde **401**.
> **Peça a sua chave em https://iajus.com.br.**

## O que vem no plugin

| Componente | Conteúdo |
|---|---|
| `skills/pesquisar-jurisprudencia/` | quando e **como** buscar e **citar** acórdãos/súmulas/RG pelas 8 modalidades |
| `skills/consultar-doutrina/` | como localizar doutrina de autores (obra, capítulo) com a referência bibliográfica |
| `skills/consultar-legislacao/` | como localizar leis/artigos por termo, tema (ontologia) ou citação literal |
| `.mcp.json` | servidor `iajus` (streamable-HTTP) com header `Authorization: Bearer ${user_config.api_token}` |

As skills são model-invoked: o Claude as usa sozinho quando a tarefa pede
jurisprudência, doutrina ou legislação. Após instalar/habilitar, rode `/reload-plugins`.

### As 8 modalidades de busca (tools do MCP)

| Tool | Para quê |
|---|---|
| `buscar_semantica` | busca vetorial/densa por significado (padrão para perguntas conceituais) |
| `buscar_hibrida` | fusão RRF (densa + FTS + trigram + CNJ + ontologia) — melhor relevância geral |
| `buscar_fts` | full-text pt_unaccent, stemming PT, insensível a acento |
| `buscar_regex` | regex POSIX (exige ≥3 caracteres literais para ancorar o índice) |
| `buscar_por_cnj` | número de processo CNJ (exato ou por componentes) |
| `buscar_por_ontologia` | ramo/sub-área OJBU via subárvore ltree (L1/L2/L3 TPU + temas transversais) |
| `buscar_grafo` | grafo de citações `legal_edges` (quem cita uma súmula/tema; auditoria de lacunas) |
| `buscar_jurimetria` | filtros tipados + agregações (contagem por tribunal/relator/ano/classe/resultado) |

Todas retornam o mesmo envelope (`{ modalidade, total, resultados:[…] }`), cobrem as
famílias `jurisprudencia` + `doutrina` + `legislacao` e são **read-only**.

> **Filtro por órgão:** as modalidades de **significado** (`buscar_semantica`,
> `buscar_hibrida`) usam `tribunal=STF`; as de **texto/estrutura** (`buscar_fts`,
> `buscar_regex`, `buscar_por_ontologia`) usam `orgao_code=stf` (sigla minúscula).

## Como a chave entra (mecanismo `userConfig`)

O plugin declara `userConfig.api_token` marcado `sensitive: true` e `required: true`.
Ao **habilitar** o plugin, o Claude Code pede a sua chave `ik_*`, **mascara** o input
e a guarda no **keychain do sistema** — nunca em `settings.json`, nunca no
repositório. O `.mcp.json` injeta `Authorization: Bearer ${user_config.api_token}`
em toda request. **É a instalação de credencial única**: uma coisa instala o plugin
e autentica o MCP.

A chave é validada server-side (hash SHA-256). Sem chave válida → todo `buscar_*`
responde **401**. O "restrito" é imposto na **camada MCP** (os dados), não no
repositório do marketplace (que só tem manifesto, **zero segredo**).

## Instalar — Claude Code

```text
/plugin marketplace add github.com/<owner>/<repo>   # marketplace público iaJus
/plugin install iajus-juris@iajus
/plugin enable iajus-juris@iajus                     # aqui o Claude pede sua chave ik_* (guardada no keychain)
/reload-plugins                                      # conecta o servidor MCP iajus com a chave
```

O plugin vem **desabilitado por padrão** (`defaultEnabled: false`) porque conecta a
um serviço externo. Ao habilitar, informe a chave quando solicitado. Para trocar o
endpoint, preencha o campo opcional **iaJus MCP URL** (`user_config.mcp_url`); em
branco, usa o padrão de produção:

```
https://iajus-mcp-234355917040.southamerica-east1.run.app/mcp
```

## Instalar — Cowork / Claude Desktop

No Cowork/Desktop, adicione o plugin apontando para o **mesmo repositório GitHub
público** (Settings → Plugins → "Add plugin" → URL do repositório
`github.com/<owner>/<repo>`). Ao habilitar, informe a chave `ik_*` quando
solicitado. O endpoint do MCP é o mesmo (campo opcional **iaJus MCP URL**; em branco
usa o padrão de produção acima).

## Codex (mesmo MCP remoto)

O Codex consome MCP via `~/.codex/config.toml`. Aponte para o mesmo endpoint com a
chave por variável de ambiente:

```bash
codex mcp add iajus --url https://iajus-mcp-234355917040.southamerica-east1.run.app/mcp \
  --bearer-token-env-var IAJUS_API_TOKEN
export IAJUS_API_TOKEN=ik_live_SUA_CHAVE_AQUI   # nunca commitar/colar em chat
```

## Atualizar para novas versões

```text
/plugin marketplace update iajus
/plugin update iajus-juris@iajus
/reload-plugins
```

A sua chave no keychain é preservada na atualização. Novas ferramentas e mais
cobertura entram no **servidor** e ficam disponíveis sem reinstalar (mesmo endereço,
mesma chave).

## Compatibilidade de clientes

> Clientes que aceitam Bearer estático: Claude Code, Claude Desktop, Cowork, Cursor,
> Gemini CLI, Codex (headless). Claude.ai web e ChatGPT exigem OAuth (v2 — em
> preparação). A chave é validada server-side; nunca trafega na URL nem em log.
> **Não cole a chave em commits ou chat.** Solicite a sua em https://iajus.com.br.
