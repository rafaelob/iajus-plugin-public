# IAJUS - plugin Claude (jurisprudência + legislação BR)

> **Versão 2.6.9** - o perfil MCP público expõe 28 ferramentas; as sete tools `jurimetria_*` saíram do perfil MCP público (decisão do
> operador). A superfície pública mantém as **7 modalidades de busca**, a busca de citações
> `buscar_por_citacoes`, a introspecção do corpus `obter_estatisticas_base` (volume por
> tribunal e faixa de anos), grafo de legislação com alterações **por dispositivo**
> (`alteracoes_dispositivo`) e vigência (`status_vigencia`) nas qualificadas e nos hits de
> busca (envelope `trust`). Autenticação por **OAuth 2.1** (login no navegador, refresh
> automático). Ver `CHANGELOG.md`.

Com este plugin, você ganha **skills** que ensinam o agente a pesquisar/citar
jurisprudência e legislação brasileira **+** o **servidor MCP remoto IAJUS** já
configurado. Não precisa configurar o MCP na mão.

O corpus cobre **milhões de acórdãos** nas famílias: **tribunais superiores**
(STF/STJ/TST/TSE/STM/TNU), **TJs** (estaduais), **TRFs**, **TRTs**, **TREs**,
**Tribunais de Contas** (TCU + TCEs), **Turmas Recursais dos JEFs** e
**jurisprudência administrativa** (CARF) - mais **legislação federal, estadual e
municipal**.

## O que vem no plugin

| Componente | Conteúdo |
|---|---|
| `skills/pesquisar-jurisprudencia/` | quando e **como** buscar e **citar** acórdãos/súmulas/RG pelas 7 modalidades de busca (todas as famílias: superiores, TJs, TRFs, TRTs, TREs, Tribunais de Contas, Turmas Recursais, administrativo CARF) |
| `skills/consultar-legislacao/` | como localizar leis/artigos **federais** por termo, tema (ontologia) ou citação literal, com texto íntegra, vigência e grafo de alterações (inclusive **por dispositivo**) |
| `skills/consultar-legislacao-estadual/` | como consultar legislação **estadual e municipal** e localizar a fonte oficial quando disponível (UF [+ município] + tipo + número + ano) |
| `skills/corpus-status/` | o que a base contém AGORA (`obter_estatisticas_base`): por família/órgão/qualificada/esfera, com faixa de anos e cobertura de indexação |
| `skills/verificar-citacoes/` | confere as citações de um texto (petição, parecer, memorial) contra a fonte oficial: existência, fidelidade e **vigência**, com veredito por citação (CONFIRMADA / DESATUALIZADA / NÃO LOCALIZADA) - o antídoto da alucinação de citação |
| `agents/` (8 subagentes) | `pesquisador-juris`, `elaborador-tese`, `refutador-tese`, `precedentes-vinculantes`, `processo-juris`, `legislacao-juris`, `memorialista-juris`, `conferente-citacoes`. No Claude Code, cada um se chama `iajus-juris:<nome>` (ou `@agent-iajus-juris:<nome>`); nos demais clientes, as skills executam o mesmo método diretamente. Cada um declara `tools:` com **allowlist** só das ferramentas do servidor MCP deste plugin: sem Bash, sem escrita em disco |
| `.mcp.json` | servidor `iajus` (streamable-HTTP) autenticado por **OAuth 2.1** (`oauth.scopes` = `openid email offline_access`) |

As skills são model-invoked: o Claude as usa sozinho quando a tarefa pede
jurisprudência ou legislação. Elas não pré-aprovam nenhuma ferramenta: cada chamada
segue as suas regras de permissão (veja "Como liberar todas as ferramentas"). No
Claude Code, após instalar/habilitar, rode `/reload-plugins`.

### As 7 modalidades de busca + qualificadas (tools do MCP)

| Tool | Para quê |
|---|---|
| `buscar_semantica` | busca vetorial/densa por significado (padrão para perguntas conceituais) |
| `buscar_hibrida` | fusão RRF (densa + FTS + trigram + CNJ + ontologia) - melhor relevância geral; em legislação serve por padrão só normas em vigor (`incluir_historico=true` traz revogadas) |
| `buscar_fts` | full-text pt_unaccent, stemming PT, insensível a acento; citação numérica ("súmula 145 do STF") dispara lookup exato |
| `buscar_regex` | regex POSIX (exige ≥3 caracteres literais para ancorar o índice) |
| `buscar_por_cnj` | número de processo CNJ (exato ou por componentes) |
| `buscar_por_ontologia` | ramo/sub-área OJBU via subárvore ltree (L1/L2/L3 TPU + temas transversais) |
| `buscar_por_citacoes` | grafo de citações `legal_edges` (quem cita uma súmula/tema; cadeia multi-hop com `max_hops`; auditoria de lacunas) |
| `buscar_qualificada` | precedentes qualificados (súmula, SV, RG, tema repetitivo, IRDR, IRR, IAC, OJ) com **`status_vigencia`** - canceladas saem marcadas; modo por matéria (`materia=`) |

> Contagens agregadas (volume por tribunal, faixa de anos coberta) vêm de
> `obter_estatisticas_base` (skill `corpus-status`).

As buscas retornam o mesmo formato (`{ modalidade, total, resultados:[…] }`), cobrem
as famílias `jurisprudencia` + `legislacao`. Os hits trazem o envelope
de confiança `trust` (`{authority_tier, status_vigencia, trecho}`) - cheque a vigência
antes de citar como amparo.

## Autenticação: OAuth 2.1

O `.mcp.json` declara o servidor `iajus` (`type: http`) **sem header de
autorização**, então na primeira conexão o cliente detecta o `401` do MCP,
descobre o Authorization Server pelo Protected Resource Metadata
(`/.well-known/oauth-protected-resource/mcp` → AS `app.iajus.com.br`) e abre o
**login OAuth no navegador** (a mesma conta "Entrar" da aplicação). O token é
guardado com segurança pelo cliente e **renovado automaticamente** (o AS anuncia
`offline_access`, que o cliente anexa ao escopo para refresh sem novo login).
Nenhuma chave é digitada nem guardada, e o plugin não lê variável de ambiente nem
arquivo da sua máquina.

A autorização é por usuário (a conta IAJUS), validada server-side. O "restrito" é
imposto na **camada MCP** (os dados), não no repositório do marketplace (que só tem
manifesto, **zero segredo**).

## Instalar

### Claude Code

```text
/plugin marketplace add https://github.com/rafaelob/iajus-plugin-public   # repo público do marketplace (cliente)
/plugin install iajus-juris@iajus
/plugin enable iajus-juris@iajus
/reload-plugins                              # conecta o MCP iajus; o Claude abre o login OAuth no navegador
```

O plugin já traz o endpoint do MCP fixado no padrão de produção. Ao usar a primeira tool,
o Claude abre o login OAuth no navegador (mesma conta da aplicação) e renova o token sozinho
via `offline_access`:

```
https://mcp.iajus.com.br/mcp
```

### claude.ai e Cowork

1. Em **Customize > Plugins > Add > Add marketplace**, informe
   `https://github.com/rafaelob/iajus-plugin-public` e instale o `iajus-juris`.
2. Abra a aba **Connectors** do plugin e conecte o servidor `iajus`: o login OAuth abre
   no navegador (mesma conta da aplicação).
3. As cinco skills ficam disponíveis no chat e no Cowork.

### Cursor / Grok Bot (mesmo MCP remoto)

O mesmo diretório também traz o manifesto Cursor (`.cursor-plugin/plugin.json`) e uma cópia em `mcp.json`, que o Cursor descobre sozinho (o `.mcp.json` permanece para Claude e Grok Build). Submissão do marketplace: [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish), apontando para este repositório público.

Após a listagem: no Cursor, **Customize** → busque **IAJUS** / `iajus-juris` → Instalar → autorizar OAuth em `https://mcp.iajus.com.br/mcp`. No Grok Bot, **Plugins** → **IAJUS** → adicionar → autorizar no navegador.

A revisão do Cursor Marketplace atualmente prefere plugins open-source. Este empacotamento usa a mesma licença proprietária de distribuição deste diretório (`LicenseRef-Proprietary` / `LICENSE`); a licença não foi alterada para a embalagem Cursor.

## Como liberar todas as ferramentas (autorizar por padrão)

> **Uma linha honesta:** nenhum plugin consegue liberar as ferramentas
> automaticamente no seu cliente - por segurança, a autorização é **sempre uma
> configuração local sua**. Os passos abaixo fazem isso em segundos.

### Claude Code

1. **Confirme a conexão.** Rode `/mcp` e veja o servidor **`iajus`** listado com a
   contagem de ferramentas ao lado. Se pedir login, é o OAuth - autentique no
   navegador (mesma conta da aplicação) e volte.
2. **"Não aparecem todas" é normal.** O **Tool Search** vem ligado por padrão e
   carrega as ferramentas conforme o Claude precisa delas - só os nomes entram no
   começo, o schema completo entra no uso. Não é bug: as ferramentas estão
   conectadas (a contagem ao lado de `iajus` no `/mcp` confirma).
3. **Para não aprovar a cada uso**, adicione ao seu `~/.claude/settings.json`
   (global) ou ao `.claude/settings.local.json` (do projeto):

   ```json
   { "permissions": { "allow": ["mcp__plugin_iajus-juris_iajus__*"] } }
   ```

   > ⚠️ **Pegadinha do nome escopado.** Ferramenta de **plugin** usa o prefixo
   > `mcp__plugin_<nome-do-plugin>_<nome-do-servidor>__<ferramenta>`. Aqui o plugin
   > se chama `iajus-juris` e o servidor (no `.mcp.json`) se chama `iajus`, então o
   > prefixo correto é **`plugin_iajus-juris_iajus`** - o `*` libera todas as
   > ferramentas de uma vez. Usar o nome cru `iajus-juris` (ou só `iajus`) **não
   > casa** e continua pedindo aprovação. Requer uma versão recente do Claude Code.

### claude.ai e Cowork

A aprovação é sua, na UI do conector: na aba **Connectors** do plugin, abra o conector
`iajus` e escolha, ferramenta a ferramenta, quais ficam sempre permitidas (você pode
permitir todas). O autor do plugin não controla essa lista.

### Codex

Instale o plugin Codex (gêmeo deste, `iajus-juris-codex`) e confirme que o MCP
`iajus` autenticou (OAuth no navegador, ou `codex mcp login iajus`). Instalar **não**
auto-aprova as chamadas: as suas configurações de aprovação continuam valendo. Para
evitar confirmação por chamada, ajuste o **approval mode** do Codex - veja a doc
oficial de approvals do Codex (<https://developers.openai.com/codex>). Passo a passo
de instalação em `plugins/iajus-juris-codex/README.md`.

### ChatGPT (conector MCP)

O IAJUS conecta-se ao ChatGPT pelo conector MCP remoto no endpoint único
`https://mcp.iajus.com.br/mcp`. Autorize a conexão com OAuth 2.1; as confirmações
continuam sob controle do cliente e do usuário. O plugin não pré-aprova chamadas.

### Alternativa manual no Claude Code: chave `ik_*` em vez de OAuth

Se preferir a chave estática à OAuth, desabilite este plugin e registre o servidor à
parte, digitando a sua chave no comando (não há fallback automático: header presente
e rejeitado falha a conexão, então use OU OAuth OU Bearer). Desabilitar o plugin tira
também as cinco skills e os oito subagentes: o servidor avulso entrega só as ferramentas.

```text
claude mcp add --transport http iajus https://mcp.iajus.com.br/mcp --header "Authorization: Bearer ik_live_..."
```

A chave é validada server-side (hash SHA-256); sem chave válida → `401`. **Nunca
cole a chave em commits ou chat.**

## Codex (mesmo MCP remoto)

Plugin Codex equivalente, também por OAuth 2.1. Caminho **sem git** (ZIP): extraia o
pacote e `codex plugin marketplace add ./iajus-juris-codex` → `codex plugin add
iajus-juris@iajus`. Os outros caminhos de instalação estão em
`plugins/iajus-juris-codex/README.md`.

## Antigravity 2.0 (Google): mesmo MCP remoto

O Antigravity 2.0 (IDE e CLI) consome um MCP remoto pelo arquivo de configuração
compartilhado `~/.gemini/config/mcp_config.json` (no Windows, `~` é a pasta do seu
usuário). Também dá para chegar nele pela UI:
painel do agente, menu MCP Servers, Manage MCP Servers, View raw config.

Para HTTP remoto o Antigravity usa a chave `serverUrl` (não `url`) dentro de
`mcpServers`. O IAJUS autentica por **OAuth 2.1**: o Authorization Server em
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

## Compatibilidade de clientes (autenticação)

> **OAuth 2.1** (padrão, recomendado): Claude Code, Claude Desktop, claude.ai, Cowork,
> ChatGPT (conector/Developer Mode), Codex, Antigravity 2.0 (Google), Cursor e Grok Bot.
> **Bearer `ik_*`** (alternativa manual, fora deste plugin): Claude Code, pelo comando
> descrito acima, com a chave digitada por você. A chave é validada server-side; nunca
> trafega na URL nem em log. **Não cole a chave em commits ou chat.**

## Dados, privacidade e suporte

- **O que o plugin envia:** só as chamadas de ferramenta que o Claude faz ao servidor
  MCP `https://mcp.iajus.com.br/mcp` (a consulta e os parâmetros de busca), com o seu
  token OAuth. O plugin não tem hooks, scripts nem servidor local: não roda comandos,
  não lê arquivos e não guarda dados na sua máquina.
- **Política de privacidade** (LGPD): <https://iajus.com.br/privacidade>. Descreve
  categorias de dados, finalidades, retenção, subprocessadores e direitos do titular.
- **Termos de uso:** <https://iajus.com.br/termos>.
- **Suporte / contato:** <contato@iajus.com.br> (também o canal do DPO).
- **Editor:** Celeris Juris Tecnologia e Inteligência Jurídica LTDA - CNPJ 68.398.872/0001-93 - <https://iajus.com.br>.
- **Escopo dos dados:** use o MCP para pesquisa jurídica brasileira no corpus IAJUS
  (jurisprudência e legislação normalizadas). Não inclua dados pessoais, credenciais ou
  outros dados sensíveis em consultas. O acesso é autenticado por conta OAuth e validado
  server-side.
