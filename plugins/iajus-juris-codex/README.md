# IAJUS - plugin para OpenAI Codex (via marketplace)

Plugin Codex que entrega as **4 skills** jurídicas IAJUS + o servidor MCP remoto já
configurado. É o gêmeo Codex do plugin Claude Code `iajus-juris` (mesmas skills
byte-idênticas, mesmo MCP remoto). Autentica por **OAuth 2.1 por padrão** (login no
navegador, refresh automático); a chave `ik_*` continua disponível como **fallback
manual** (Bearer).

## Instalação

### Opção A - ZIP, sem git (recomendado para a maioria)

1. Baixe e extraia o pacote `iajus-juris-codex` (um marketplace local pronto, com
   `.agents/plugins/marketplace.json` + `plugins/iajus-juris-codex/`).
2. Registre o marketplace local e instale o plugin:

```bash
codex plugin marketplace add ./iajus-juris-codex     # caminho local extraído
codex plugin add iajus-juris@iajus
```

### Opção B - marketplace privado IAJUS (git, para quem usa credential-helper)

```bash
# o catálogo é servido por git smart-HTTP privado (HTTP Basic: usuário = seu e-mail,
# senha = sua chave ik_*). Configure a credencial no git credential-helper -
# NUNCA coloque usuário/senha na URL (vaza em config/history/logs).
codex plugin marketplace add https://dist.iajus.com.br/marketplace.git
codex plugin add iajus-juris@iajus
```

> O Basic gateia só o **clone do catálogo**. O **runtime do MCP** usa OAuth (ou a
> `ik_*` via fallback abaixo). Acesso (conta + `ik_*`) em <https://iajus.com.br>.

## Runtime: OAuth na primeira chamada

O `.mcp.json` declara o servidor com `oauth_resource` + `scopes` e **sem** token
estático, então o Codex dispara o **login OAuth** automaticamente - no install do
plugin ou na primeira chamada a uma tool `iajus` (abre o navegador em
`app.iajus.com.br`, guarda o token no store do Codex, renova sozinho via
`offline_access`). Se o login não disparar sozinho, force com:

```bash
codex mcp login iajus
```

## Como liberar todas as ferramentas (autorizar por padrão)

> **Uma linha honesta:** nenhum plugin consegue liberar as ferramentas
> automaticamente no seu cliente - por segurança, a autorização é **sempre uma
> configuração local sua**. Os passos abaixo fazem isso em segundos.

### Codex

1. **Confirme a conexão.** Após instalar, garanta que o MCP `iajus` autenticou -
   o OAuth abre no navegador na primeira chamada, ou force com `codex mcp login
   iajus`.
2. **Instalar não auto-aprova.** As suas configurações de aprovação do Codex
   continuam valendo depois de instalar o plugin - por isso o Codex pode pedir
   confirmação a cada chamada de ferramenta.
3. **Para evitar confirmação por chamada**, ajuste o **approval mode** do Codex
   (em `~/.codex/config.toml`). A sintaxe exata dos modos de aprovação evolui -
   consulte a doc oficial de approvals do Codex em
   <https://developers.openai.com/codex> e escolha o modo que preferir. As
   ferramentas IAJUS são **read-only**, então tendem a se encaixar bem em modos
   menos restritivos.

### ChatGPT (conector / Developer Mode / Apps)

As ferramentas IAJUS são **read-only** (marcadas com `readOnlyHint`), então o
ChatGPT tende a pedir **menos** confirmação. A aprovação no ChatGPT é **por
ferramenta e por conversa**: na primeira vez que uma ferramenta é usada numa
conversa, o ChatGPT pede confirmação e você pode marcar **"lembrar"** para o resto
**daquela** conversa (não persiste entre conversas). Como **App aprovado pelo
workspace**, o administrador aprova o App uma vez e o ChatGPT usa um snapshot
congelado das ferramentas. O autor não consegue pré-aprovar por você - é o
comportamento de segurança do próprio ChatGPT.

## O que o plugin configura

- **MCP remoto `iajus`** (`./.mcp.json`): streamable-HTTP em
  `https://mcp.iajus.com.br/mcp`, **OAuth 2.1** (`oauth_resource` RFC 8707 + `scopes`
  `openid email profile offline_access`). Forma = **mapa direto de servidor** (sem
  wrapper `mcpServers`/`mcp_servers`) - a forma que o loader do Codex aceita sem
  ambiguidade de casing. Nenhum token literal em disco.
- **4 skills** (`./skills/`): `pesquisar-jurisprudencia` (busca + jurimetria agregada),
  `consultar-legislacao`, `consultar-legislacao-estadual` e `corpus-status`. Idênticas
  às do plugin Claude Code - cobrem tribunais superiores, TJs, TRFs, TRTs, TREs,
  Tribunais de Contas, Turmas Recursais dos JEFs, administrativo (CARF) e legislação
  federal/estadual/municipal. (Doutrina é premium e não faz parte deste plugin.)

## Fallback manual: chave `ik_*` (Bearer) em vez de OAuth

Se preferir a chave estática à OAuth, registre o MCP via CLI com o Bearer por
variável de ambiente (esse caminho **não** dispara OAuth):

```bash
export IAJUS_API_TOKEN="ik_live_..."           # bash/zsh; PowerShell: $env:IAJUS_API_TOKEN="ik_live_..."
codex mcp add iajus --url https://mcp.iajus.com.br/mcp --bearer-token-env-var IAJUS_API_TOKEN
```

Build antigo do Codex sem `url` remoto: bridge stdio
`npx -y mcp-remote https://mcp.iajus.com.br/mcp --header "Authorization: Bearer ${IAJUS_API_TOKEN}"`
(requer Node/npx). A configuração do servidor está em `./.mcp.json`.

> Manifesto do plugin: `.codex-plugin/plugin.json`; manifesto do marketplace:
> `.agents/plugins/marketplace.json`. OAuth keys do Codex (`oauth_resource`/`scopes`)
> e a forma de `.mcp.json` (mapa direto / wrapper `mcpServers` camelCase) verificadas
> na fonte do `openai/codex` (`codex-rs/codex-mcp/src/plugin_config.rs`).

## Antigravity 2.0 (Google): mesmo MCP remoto

O mesmo servidor MCP remoto também conecta no Antigravity 2.0 (IDE e CLI) pelo arquivo
compartilhado `~/.gemini/config/mcp_config.json` (o Antigravity usa a chave `serverUrl`,
não `url`, para HTTP remoto). O IAJUS autentica por **OAuth 2.1**: o Authorization Server
em `app.iajus.com.br` suporta DCR, então o login OAuth dispara sozinho e você adiciona o
servidor **sem token estático**:

```json
{ "mcpServers": { "iajus": { "serverUrl": "https://mcp.iajus.com.br/mcp" } } }
```

Salve e autentique em Settings, Customizations, Installed MCP Servers, Authenticate (uma
vez por superfície). Se um build do Antigravity não enviar o token OAuth no HTTP direto
e retornar `401`, use a ponte stdio `mcp-remote` com o Bearer por variável de ambiente
(igual ao fallback `ik_*` acima): `npx -y mcp-remote https://mcp.iajus.com.br/mcp
--header "Authorization: Bearer ${IAJUS_API_TOKEN}"`. **Nunca** cole a chave literal no
arquivo. O passo a passo completo está no README do plugin Claude Code irmão
(`plugins/iajus-juris/README.md`, seção Antigravity 2.0).

## Privacidade e suporte

- **Política de privacidade** (LGPD): <https://iajus.com.br/privacidade>. Descreve
  categorias de dados, finalidades, retenção, subprocessadores e direitos do titular.
- **Suporte / contato:** <contato@iajus.com.br> (também o canal do DPO).
- **Editor:** IAJUS / Celeris (CVO Alliance Ltda.) - <https://iajus.com.br>.
- **Escopo dos dados:** as tools são **read-only** e servem o corpus próprio IAJUS
  (jurisprudência e legislação brasileira - registro público, normalizado e
  classificado). O consumo de busca é autenticado por conta (OAuth) e validado
  server-side; nenhuma tool retorna credenciais nem grava dados.
