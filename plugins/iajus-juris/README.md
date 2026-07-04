# iaJus — plugin Claude Code (jurisprudência + legislação BR)

> **Versão 1.6.1** — jurimetria agregada exata (`jurimetria_volume` / `jurimetria_relator`
> / `jurimetria_classe` / `jurimetria_orgao_julgador`), taxa de desfecho
> (`jurimetria_resultado`, com denominador duplo) e lag de publicação
> (`jurimetria_lag_publicacao`), grafo de legislação com alterações
> **por dispositivo** (`alteracoes_dispositivo`) e vigência (`status_vigencia`) nas
> qualificadas e nos hits de busca (envelope `trust`). Autenticação por **OAuth 2.1 por
> padrão** (login no navegador, refresh automático); chave `ik_*` como **fallback
> manual**. Compatível com 1.x. Ver `CHANGELOG.md`.

Um plugin: você ganha **skills** que ensinam o agente a pesquisar/citar
jurisprudência e legislação brasileira **+** o **servidor MCP remoto iaJus** já
configurado. Não precisa configurar o MCP na mão.

O corpus cobre **milhões de acórdãos** nas famílias: **tribunais superiores**
(STF/STJ/TST/TSE/STM/TNU), **TJs** (estaduais), **TRFs**, **TRTs**, **TREs**,
**Tribunais de Contas** (TCU + TCEs), **Turmas Recursais dos JEFs** e
**jurisprudência administrativa** (CARF) — mais **legislação federal, estadual e
municipal**.

## O que vem no plugin

| Componente | Conteúdo |
|---|---|
| `skills/pesquisar-jurisprudencia/` | quando e **como** buscar e **citar** acórdãos/súmulas/RG pelas 8 modalidades + as 4 tools de **jurimetria agregada** (todas as famílias: superiores, TJs, TRFs, TRTs, TREs, Tribunais de Contas, Turmas Recursais, administrativo CARF) |
| `skills/consultar-legislacao/` | como localizar leis/artigos **federais** por termo, tema (ontologia) ou citação literal, com texto íntegra, vigência e grafo de alterações (inclusive **por dispositivo**) |
| `skills/consultar-legislacao-estadual/` | como consultar legislação **estadual e municipal** ao vivo na fonte oficial (UF [+ município] + tipo + número + ano) |
| `skills/corpus-status/` | o que a base contém AGORA (`estatisticas_corpus_pg`): por família/órgão/qualificada/esfera, com faixa de anos e cobertura de indexação |
| `.mcp.json` | servidor `iajus` (streamable-HTTP) autenticado por **OAuth 2.1** (`oauth.scopes` = `openid email profile offline_access`) |

As skills são model-invoked: o Claude as usa sozinho quando a tarefa pede
jurisprudência ou legislação. Após instalar/habilitar, rode `/reload-plugins`.
(Doutrina é premium — fica só no perfil VadeFocus, fora deste plugin.)

### As 8 modalidades de busca + qualificadas + jurimetria (tools do MCP)

| Tool | Para quê |
|---|---|
| `buscar_semantica` | busca vetorial/densa por significado (padrão para perguntas conceituais) |
| `buscar_hibrida` | fusão RRF (densa + FTS + trigram + CNJ + ontologia) — melhor relevância geral; em legislação serve por padrão só normas em vigor (`incluir_historico=true` traz revogadas) |
| `buscar_fts` | full-text pt_unaccent, stemming PT, insensível a acento; citação numérica ("súmula 145 do STF") dispara lookup exato |
| `buscar_regex` | regex POSIX (exige ≥3 caracteres literais para ancorar o índice) |
| `buscar_por_cnj` | número de processo CNJ (exato ou por componentes) |
| `buscar_por_ontologia` | ramo/sub-área OJBU via subárvore ltree (L1/L2/L3 TPU + temas transversais) |
| `buscar_grafo` | grafo de citações `legal_edges` (quem cita uma súmula/tema; cadeia multi-hop com `max_hops`; auditoria de lacunas) |
| `buscar_jurimetria` | navegação/facet por colunas estruturadas dos acórdãos (filtros tipados + `group_by`) |
| `jurimetria_volume` | contagem EXATA de decisões por órgão × ano (read-model agregado, com envelope de honestidade) |
| `jurimetria_relator` | quem mais relata num órgão (top-50 por volume; LGPD n<20 suprimido) |
| `jurimetria_classe` | volume por classe processual CNJ de um órgão (+ `cobertura_classe_pct`) |
| `jurimetria_orgao_julgador` | volume por câmara/turma/seção de um órgão |
| `consultar_qualificada` | precedentes qualificados (súmula, SV, RG, tema repetitivo, IRDR, IRR, IAC, OJ) com **`status_vigencia`** — canceladas saem marcadas; modo por matéria (`materia=`) |

As buscas retornam o mesmo envelope (`{ modalidade, total, resultados:[…] }`), cobrem
as famílias `jurisprudencia` + `legislacao` e são read-only. Os hits trazem o envelope
de confiança `trust` (`{authority_tier, status_vigencia, trecho}`) — cheque a vigência
antes de citar como amparo.

## Autenticação: OAuth 2.1 (padrão)

O `.mcp.json` declara o servidor `iajus` (`type: http`) **sem header de
autorização**, então na primeira conexão o Claude Code detecta o `401` do MCP,
descobre o Authorization Server pelo Protected Resource Metadata
(`/.well-known/oauth-protected-resource/mcp` → AS `app.iajus.com.br`) e abre o
**login OAuth no navegador** (a mesma conta "Entrar" da aplicação). O token é
guardado com segurança pelo Claude Code e **renovado automaticamente** (o AS anuncia
`offline_access`, que o Claude Code anexa ao escopo para refresh sem novo login).
Nenhuma chave é digitada nem guardada por padrão.

A autorização é por usuário (a conta iaJus), validada server-side. O "restrito" é
imposto na **camada MCP** (os dados), não no repositório do marketplace (que só tem
manifesto, **zero segredo**).

## Instalar

```text
/plugin marketplace add github.com/rafaelob/iajus-plugin-public   # repo público do marketplace (cliente)
/plugin install iajus-juris@iajus
/plugin enable iajus-juris@iajus
/reload-plugins                              # conecta o MCP iajus; o Claude abre o login OAuth no navegador
```

O plugin vem **habilitado por padrão** (`defaultEnabled: true`) e já traz o endpoint do
MCP fixado no padrão de produção. Ao usar a primeira tool, o Claude abre o login OAuth no
navegador (mesma conta da aplicação) e renova o token sozinho via `offline_access`:

```
https://mcp.iajus.com.br/mcp
```

### Fallback manual: chave `ik_*` (Bearer) em vez de OAuth

Se preferir a chave estática à OAuth, registre o servidor fora do plugin com o
header Bearer (não há fallback automático: header presente e rejeitado falha a
conexão, então use OU OAuth OU Bearer):

```text
/mcp add iajus --transport http --url https://mcp.iajus.com.br/mcp --header "Authorization: Bearer ik_live_..."
```

A chave é validada server-side (hash SHA-256); sem chave válida → `401`. **Nunca
cole a chave em commits ou chat.**

## Codex (mesmo MCP remoto)

Plugin Codex equivalente (OAuth por padrão). Caminho **sem git** (ZIP): extraia o
pacote e `codex plugin marketplace add ./iajus-juris-codex` → `codex plugin add
iajus-juris@iajus`. Caminho **git privado**: `codex plugin marketplace add
https://dist.iajus.com.br/marketplace.git` (HTTP Basic via git credential-helper —
e-mail + `ik_*`; **nunca** credencial na URL). Fallback `ik_*`: `codex mcp add iajus
--url https://mcp.iajus.com.br/mcp --bearer-token-env-var IAJUS_API_TOKEN`. Detalhes
em `plugins/iajus-juris-codex/README.md`.

## Antigravity 2.0 (Google): mesmo MCP remoto

O Antigravity 2.0 (IDE e CLI) consome um MCP remoto pelo arquivo de configuração
compartilhado `~/.gemini/config/mcp_config.json` (no Windows,
`%USERPROFILE%\.gemini\config\mcp_config.json`). Também dá para chegar nele pela UI:
painel do agente, menu MCP Servers, Manage MCP Servers, View raw config.

Para HTTP remoto o Antigravity usa a chave `serverUrl` (não `url`) dentro de
`mcpServers`. O iaJus autentica por **OAuth 2.1**: o Authorization Server em
`app.iajus.com.br` suporta registro dinâmico de cliente (DCR), então o Antigravity
descobre e faz o login sozinho quando o servidor precisa de OAuth. Adicione o servidor
**sem token estático**:

```json
{
  "mcpServers": {
    "iajus": {
      "serverUrl": "https://mcp.iajus.com.br/mcp"
    }
  }
}
```

Salve o arquivo (o Antigravity recarrega sozinho; se não aparecer, vá em Settings,
Customizations, Refresh) e faça a autenticação da superfície em Settings,
Customizations, Installed MCP Servers, Authenticate. O login OAuth abre no navegador
(mesma conta da aplicação); cada superfície (2.0, IDE, CLI) autentica uma vez.

### Fallback `ik_*` (ponte stdio, quando o login OAuth não dispara)

Alguns builds do Antigravity ainda tropeçam no envio do token OAuth no HTTP direto e
respondem `401`. Nesse caso use a ponte stdio `mcp-remote` com o Bearer por variável
de ambiente (requer Node/npx; **nunca** cole a chave literal no arquivo):

```json
{
  "mcpServers": {
    "iajus": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.iajus.com.br/mcp",
               "--header", "Authorization: Bearer ${IAJUS_API_TOKEN}"]
    }
  }
}
```

Defina `IAJUS_API_TOKEN` com a sua chave `ik_*` no ambiente antes de abrir o
Antigravity. A chave é validada server-side (hash SHA-256); sem chave válida, `401`.

## Compatibilidade de clientes (autenticação)

> **OAuth 2.1** (padrão, recomendado): Claude Code, Claude Desktop, claude.ai web,
> ChatGPT (conector/Developer Mode), Codex, Antigravity 2.0 (Google). **Bearer `ik_*`**
> (fallback): Claude Code/Desktop, Cursor, Gemini CLI, Codex (headless), Antigravity
> (ponte `mcp-remote`). A chave é validada server-side; nunca trafega na URL nem em log.
> **Não cole a chave em commits ou chat.**
