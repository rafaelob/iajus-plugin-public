# IAJUS - plugin Claude (jurisprudência + legislação BR)

## Licença

O código e as skills deste pacote são licenciados sob Apache-2.0. Consulte [`LICENSE`](LICENSE) e [`NOTICE`](NOTICE) para o escopo do pacote e os componentes excluídos.

> O MCP IAJUS expõe um catálogo enxuto: onze ferramentas de corpus (decisões,
> precedentes, normas, artigos, leitura, relações, vocabulário, cobertura e fonte oficial),
> as pesquisas Lex (beta) e o painel opcional. Cada ferramenta recebe um único objeto
> (`busca`, `leitura`, `relacao` ou `consulta`) com `modo`, e os schemas são fechados.
> Autenticação por **OAuth 2.1** (login no navegador, refresh automático). Ver [histórico de alterações](https://github.com/rafaelob/iajus-plugin-public/blob/main/plugins/iajus-juris/CHANGELOG.md).

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
| `skills/pesquisar-jurisprudencia/` | quando e **como** buscar e **citar** acórdãos, súmulas, temas e informativos com `pesquisar_decisoes` (híbrida, semântica, textual, expressão, ontologia e CNJ), `pesquisar_precedentes`, `pesquisar_informativos` e `ler_documento` (todas as famílias: superiores, TJs, TRFs, TRTs, TREs, Tribunais de Contas, Turmas Recursais, administrativo CARF) |
| `skills/consultar-legislacao/` | como localizar leis e dispositivos **federais** por nome, número ou tema com `pesquisar_normas` e `pesquisar_artigos`, ler o texto com `ler_documento` e conferir alterações e grafo com `consultar_fonte_oficial` e `explorar_relacoes_juridicas` |
| `skills/consultar-legislacao-estadual/` | como consultar legislação **estadual e municipal** e localizar a fonte oficial quando disponível (UF [+ município] + tipo + número + ano) |
| `skills/corpus-status/` | o que a base declara AGORA (`consultar_acervo`): por família, tribunal, precedentes e legislação, com `as_of`, limites e estado da medição |
| `skills/verificar-citacoes/` | confere as citações de um texto (petição, parecer, memorial) contra a fonte oficial: existência, fidelidade e **vigência**, com veredito por citação (CONFIRMADA / DESATUALIZADA / NÃO LOCALIZADA) - o antídoto da alucinação de citação |
| `skills/pesquisa-juridica-lex/` | a pesquisa Lex (**beta**): `pesquisa_juridica_lex` e `pesquisa_profunda_lex`, com `consultar_tarefa_lex` e `cancelar_tarefa_lex` para acompanhar a profunda, e os limites por conta |
| `agents/` (8 subagentes) | `pesquisador-juris`, `elaborador-tese`, `refutador-tese`, `precedentes-vinculantes`, `processo-juris`, `legislacao-juris`, `memorialista-juris`, `conferente-citacoes`. No Claude Code, cada um se chama `iajus-juris:<nome>`. Claude Code e Cowork carregam `agents/*.md`; o chat do claude.ai usa as skills diretamente ([documentação da Anthropic](https://support.claude.com/en/articles/13837440-use-plugins-in-claude), consultada em 2026-10-14). Os especialistas têm somente `Skill` e `Read`: analisam fontes e solicitam consultas à conversa principal, que usa o MCP conectado. Nenhum agente depende do nome que o host dá ao servidor, recebe Bash ou escreve em disco |
| `.mcp.json` | servidor `iajus` (streamable-HTTP) autenticado por **OAuth 2.1** (`oauth.scopes` = `openid email offline_access`) |

As skills são model-invoked: o Claude as usa sozinho quando a tarefa pede
jurisprudência ou legislação. Elas não pré-aprovam nenhuma ferramenta: cada chamada
segue as suas regras de permissão (veja "Como liberar todas as ferramentas"). No
Claude Code, após instalar/habilitar, rode `/reload-plugins`.

`Read` permite ler os arquivos autorizados pela sessão, além das referências do plugin;
`Skill` permite carregar outras skills disponíveis. Os bloqueios e as aprovações do host
continuam valendo. Disponibilize à sessão somente os diretórios pertinentes ao trabalho.

Os especialistas preservam os oito métodos de trabalho. A conversa principal consulta
o IAJUS, entrega os resultados ao especialista e atende às lacunas de pesquisa necessárias,
sem pedir ao usuário que transporte dados entre agentes. A pesquisa simples continua
direta pelas seis skills. Esse fluxo evita a incompatibilidade entre nomes MCP do Claude
Code e do Cowork sem liberar ferramentas de outros servidores aos especialistas.
Leia o [fluxo de pesquisa com especialistas](skills/pesquisar-jurisprudencia/references/coordenacao-agentes.md).

### Ferramentas do MCP

| Tool | Para quê |
|---|---|
| `pesquisar_decisoes` | decisões judiciais por `busca.modo`: `hibrida` (significado + texto, fusão RRF), `semantica`, `textual` (termos, frase exata), `expressao` (regex POSIX), `ontologia` (ramo OJBU) e `cnj` (número de processo, exato ou por componentes) |
| `pesquisar_precedentes` | súmulas, súmulas vinculantes, temas, IRDR, IAC, OJ e precedentes normativos, por número ou matéria; canceladas saem marcadas |
| `ver_historico_juridico` | versões de redação e eventos de um precedente, pelo `id_documento` da pesquisa de precedentes |
| `pesquisar_informativos` | informativos STF e STJ (conteúdo editorial), por texto, ramo, ano ou edição |
| `pesquisar_normas` | legislação por `busca.modo`: híbrida, semântica, textual, expressão, ontologia, nome ou apelido (como CLT) e número |
| `pesquisar_artigos` | artigos e outros dispositivos legais, por texto, norma, tipo de unidade ou ramo |
| `ler_documento` | lê uma norma por tipo, número e ano (`texto_norma`), um dispositivo pelo identificador (`dispositivo`) ou o texto de uma decisão pelo `unit_id` (`texto_acordao`) |
| `explorar_relacoes_juridicas` | citações, quem cita uma norma ou artigo, dispositivos citados por um documento e grafo de alterações de uma norma federal |
| `consultar_vocabulario_juridico` | ontologia OJBU e códigos TPU, protocolo de classificação, tipos documentais e órgãos julgadores de um tribunal |
| `consultar_acervo` | cobertura e atualização declaradas do acervo, e mapa cadastrado de fontes de legislação por esfera e UF |
| `consultar_fonte_oficial` | localização, alterações e enumeração de legislação na fonte oficial (Senado, Planalto e portais) |
| `pesquisa_juridica_lex`, `pesquisa_profunda_lex` | pesquisa Lex (**beta**), comum e profunda; a profunda devolve um `consulta_id` acompanhado por `consultar_tarefa_lex` e interrompido por `cancelar_tarefa_lex` |
| `abrir_painel_iajus` | painel opcional; não inicia pesquisa |
| `apresentar_sintese` | apresenta uma síntese já concluída quando o host oferece o painel; não pesquisa nem valida citações |

> Cobertura e contagens vêm de `consultar_acervo` (skill `corpus-status`) e valem
> pelo `as_of` que o retorno traz; um zero de busca não prova completude do acervo.

Cada resposta traz identificadores, fonte, link, datas e estado de vigência quando a
fonte os informa: cheque a vigência antes de citar como amparo e nunca complete um
campo ausente de memória.

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
2. A instalação do plugin e a conexão MCP são etapas separadas. Abra a aba
   **Connectors** do plugin e conecte o servidor `iajus`: o login OAuth abre no
   navegador (mesma conta da aplicação).
3. As seis skills ficam disponíveis no chat e no Cowork.

### Cursor / Grok Bot (mesmo MCP remoto)

O mesmo diretório também traz o manifesto Cursor (`.cursor-plugin/plugin.json`) e uma cópia em `mcp.json`, que o Cursor descobre sozinho (o `.mcp.json` permanece para Claude e Grok Build). Submissão do marketplace: [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish), apontando para este repositório público.

Após a listagem: no Cursor, **Customize** → busque **IAJUS** / `iajus-juris` → Instalar → autorizar OAuth em `https://mcp.iajus.com.br/mcp`. No Grok Bot, **Plugins** → **IAJUS** → adicionar → autorizar no navegador.

Este empacotamento usa Apache-2.0 para o código e as skills; consulte [`LICENSE`](LICENSE) e [`NOTICE`](NOTICE) para o escopo e as exclusões.

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
   {
     "permissions": {
       "allow": ["mcp__plugin_iajus-juris_iajus__*"],
       "ask": ["mcp__plugin_iajus-juris_iajus__cancelar_tarefa_lex"]
     }
   }
   ```

   A ferramenta que interrompe uma pesquisa profunda (`cancelar_tarefa_lex`) fica em
   `ask` e **sempre pede confirmação**. O Claude Code avalia as regras na ordem `deny`, `ask`, `allow`, e uma
   regra `ask` que casa pede confirmação mesmo quando um `allow` mais amplo, como o `*`
   acima, também casa a mesma chamada
   ([permissões do Claude Code](https://code.claude.com/docs/en/permissions)).

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

Instale o plugin Codex (gêmeo deste, `iajus-juris-codex`) e rode `codex mcp login
iajus` uma vez (instalado pela linha de comando, o login não abre sozinho). Instalar **não**
auto-aprova as chamadas: as suas configurações de aprovação continuam valendo. Para
evitar confirmação por chamada, ajuste o **approval mode** do Codex - veja a doc
oficial de approvals do Codex (<https://developers.openai.com/codex>). Passo a passo
de instalação no [guia do plugin Codex](https://github.com/rafaelob/iajus-plugin-public/blob/main/plugins/iajus-juris-codex/README.md).

### ChatGPT (conector MCP)

O IAJUS conecta-se ao ChatGPT pelo conector MCP remoto no endpoint único
`https://mcp.iajus.com.br/mcp`. Autorize a conexão com OAuth 2.1; as confirmações
continuam sob controle do cliente e do usuário. O plugin não pré-aprova chamadas.

### Alternativa manual no Claude Code: chave `ik_*` em vez de OAuth

Se preferir a chave estática à OAuth, desabilite este plugin e registre o servidor à
parte, digitando a sua chave no comando (não há fallback automático: header presente
e rejeitado falha a conexão, então use OU OAuth OU Bearer). Desabilitar o plugin tira
também as seis skills e os oito subagentes: o servidor avulso entrega só as ferramentas.

```text
claude mcp add --transport http iajus https://mcp.iajus.com.br/mcp --header "Authorization: Bearer <sua chave ik_*>"
```

A chave é validada server-side (hash SHA-256); sem chave válida → `401`. **Nunca
cole a chave em commits ou chat.**

## Codex (mesmo MCP remoto)

Plugin Codex equivalente, também por OAuth 2.1. Caminho **sem git** (ZIP): extraia o
pacote e `codex plugin marketplace add ./iajus-juris-codex` → `codex plugin add
iajus-juris@iajus` → `codex mcp login iajus`. Os outros caminhos de instalação estão em
[guia do plugin Codex](https://github.com/rafaelob/iajus-plugin-public/blob/main/plugins/iajus-juris-codex/README.md).

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
  token OAuth. O plugin não tem hooks, scripts nem servidor local: não executa comandos
  nem grava arquivos. Os especialistas podem ler referências e arquivos autorizados
  pela sessão com `Read`; esse acesso segue as permissões do host.
- **O que o IAJUS guarda no servidor:** nas ferramentas de consulta ao acervo (decisões,
  precedentes, normas, documentos), o texto da consulta pode ser armazenado após um filtro
  automático de dados pessoais e credenciais. Esse filtro não garante anonimização. Também
  ficam registros de uso (ferramenta, recortes como tribunal e ano, data, hora e resultado). Na busca
  semântica, o texto pode ser enviado a provedores externos de embeddings (OpenAI ou Google)
  apenas para gerar o vetor de busca. Na pesquisa Lex (`pesquisa_juridica_lex` e
  `pesquisa_profunda_lex`) a conversa é guardada: consulta, contexto, restrições e resposta
  ficam em histórico próprio do canal MCP e plugin, separado do Studio, ligado à sua conta e
  cifrado. A conversa pode ser retomada por 7 dias a partir da criação, sem renovação, e
  continua guardada, sem descarte automático, até você apagar: pelo Studio, apagando o
  histórico inteiro (todos os canais de uma vez; apagar só um canal não alcança estas
  conversas), ou excluindo a conta.
- **Política de privacidade** (LGPD): <https://iajus.com.br/privacidade>. Descreve
  categorias de dados, finalidades, retenção, subprocessadores e direitos do titular.
- **Termos de uso:** <https://iajus.com.br/termos>.
- **Suporte / contato:** <contato@iajus.com.br> (também o canal do DPO).
- **Editor:** Celeris Juris Tecnologia e Inteligência Jurídica LTDA - CNPJ 68.398.872/0001-93 - <https://iajus.com.br>.
- **Escopo dos dados:** use o MCP para pesquisa jurídica brasileira no corpus IAJUS
  (jurisprudência e legislação normalizadas). Não inclua dados pessoais, credenciais ou
  outros dados sensíveis em consultas. O acesso é autenticado por conta OAuth e validado
  server-side.

## Pesquisa Lex (beta)

A skill `pesquisa-juridica-lex` usa a pesquisa comum (`pesquisa_juridica_lex`) ou profunda (`pesquisa_profunda_lex`) apenas quando essas ferramentas estiverem expostas. Conserva contexto, restrições, IDs e a chave de repetição; `consultar_tarefa_lex` e `cancelar_tarefa_lex` acompanham a mesma tarefa profunda. O painel (`abrir_painel_iajus`) é opcional e não inicia pesquisa.

As duas pesquisas são recurso **beta** e têm limite por conta, compartilhado entre ChatGPT, Claude, Codex e Microsoft: pesquisa comum 3 por dia e 10 por semana; pesquisa profunda 1 por dia e 7 por semana. O dia vira às 00:00 de America/Sao_Paulo e a semana, na segunda-feira às 00:00 (America/Sao_Paulo). Os números podem mudar: vale o que o servidor devolve em `limites` (usadas e limite do dia e da semana, e quando renovam), quando o devolve; sem `limites`, a skill não calcula saldo. Depois de uma recusa por limite a skill não repete a chamada: informa o saldo e a renovação, se vieram, e oferece as ferramentas de corpus, que não consomem esse limite.
