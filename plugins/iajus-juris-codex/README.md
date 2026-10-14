# IAJUS - plugin para OpenAI Codex (via marketplace)

## Licença

O código e as skills deste pacote são licenciados sob Apache-2.0. Consulte [`LICENSE`](LICENSE) e [`NOTICE`](NOTICE) para o escopo do pacote e os componentes excluídos.

Plugin Codex que entrega as **6 skills** jurídicas IAJUS e conecta ao endpoint
MCP único `https://mcp.iajus.com.br/mcp` (ferramentas de pesquisa de decisões, precedentes, normas e artigos, leitura de documentos, relações, vocabulário, cobertura e fonte oficial, mais a pesquisa Lex em beta). Ele cobre a mesma família
funcional do plugin Claude Code `iajus-juris`,
mas mantém instruções próprias para Codex/OpenAI; os pacotes não precisam ser
byte-idênticos. Ambos usam o mesmo MCP remoto. Autentica por **OAuth 2.1** (login no
navegador, refresh automático): o plugin não lê chave nem variável de ambiente da sua
máquina.

## Instalação

### Pacote ZIP, sem git

1. Baixe e extraia o pacote `iajus-juris-codex` (um marketplace local pronto, com
   `.agents/plugins/marketplace.json` + `plugins/iajus-juris-codex/`).
2. Registre o marketplace local e instale o plugin:

```bash
codex plugin marketplace add ./iajus-juris-codex     # caminho local extraído
codex plugin add iajus-juris@iajus
codex mcp login iajus
```

## Login OAuth: um passo, uma vez

O `.mcp.json` declara o servidor com `oauth_resource` + `scopes` e **sem** token
estático. O Codex só abre o login sozinho quando o plugin é instalado pela interface
(`/plugins` na TUI, app ou IDE). Instalado pela linha de comando (`codex plugin add`),
o login **não** abre, nem na primeira chamada: o Codex só avisa que o servidor não fez
login. Rode uma vez:

```bash
codex mcp login iajus
```

O navegador abre em `app.iajus.com.br`. O token fica guardado no Codex e se renova
sozinho via `offline_access`. Se as ferramentas `iajus` não aparecerem ou toda chamada
voltar 401, rode o comando de novo e reinicie a sessão.

## Como liberar todas as ferramentas (autorizar por padrão)

> **Uma linha honesta:** nenhum plugin consegue liberar as ferramentas
> automaticamente no seu cliente - por segurança, a autorização é **sempre uma
> configuração local sua**. Os passos abaixo fazem isso em segundos.

### Codex

1. **Confirme a conexão.** Após instalar, garanta que o MCP `iajus` autenticou:
   pela linha de comando, rode `codex mcp login iajus` (o login não abre sozinho).
2. **Instalar não auto-aprova.** As suas configurações de aprovação do Codex
   continuam valendo depois de instalar o plugin - por isso o Codex pode pedir
   confirmação a cada chamada de ferramenta.
3. **Para evitar confirmação por chamada**, ajuste o **approval mode** do Codex
   (em `~/.codex/config.toml`). A sintaxe exata dos modos de aprovação evolui -
   consulte a doc oficial de approvals do Codex em
  <https://developers.openai.com/codex> e escolha o modo que preferir.

### ChatGPT (conector MCP)

O IAJUS conecta-se ao ChatGPT pelo conector MCP remoto no endpoint único
`https://mcp.iajus.com.br/mcp`. Autorize a conexão com OAuth 2.1; as confirmações
continuam sob controle do cliente e do usuário. O plugin não pré-aprova chamadas.

## O que o plugin configura

- **MCP remoto `iajus`** (`./.mcp.json`): streamable-HTTP em
  `https://mcp.iajus.com.br/mcp`, **OAuth 2.1** (`oauth_resource` RFC 8707 + `scopes`
  `openid email offline_access`). Forma = wrapper **`mcpServers` camelCase**,
  com o servidor `iajus` dentro dele. Nenhum token literal em disco.
- **6 skills** (`./skills/`): `pesquisar-jurisprudencia`, `consultar-legislacao`,
  `consultar-legislacao-estadual`, `corpus-status`, `verificar-citacoes` e `pesquisa-juridica-lex`. Elas cobrem
  tribunais superiores, TJs, TRFs, TRTs, TREs, Tribunais de Contas, Turmas Recursais dos
  JEFs, administrativo (CARF), legislação federal/estadual/municipal, panorama do corpus e
  conferência anti-alucinação.

> Manifesto do plugin: `.codex-plugin/plugin.json`; manifesto do marketplace:
> `.agents/plugins/marketplace.json`. OAuth keys do Codex (`oauth_resource`/`scopes`)
> e a forma de `.mcp.json` (wrapper `mcpServers` camelCase) verificadas
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
vez por superfície). O passo a passo completo está no README do plugin Claude irmão
(`plugins/iajus-juris/README.md`, seção Antigravity 2.0).

## Privacidade e suporte

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
- **Suporte / contato:** <contato@iajus.com.br> (também o canal do DPO).
- **Editor:** Celeris Juris Tecnologia e Inteligência Jurídica LTDA - CNPJ 68.398.872/0001-93 - <https://iajus.com.br>.
- **Escopo dos dados:** use o MCP para pesquisa jurídica brasileira no corpus IAJUS
  (jurisprudência e legislação normalizadas). Não inclua dados pessoais, credenciais ou
  outros dados sensíveis em consultas. O acesso é autenticado por conta OAuth e validado
  server-side.

## Pesquisa Lex (beta)

A skill `pesquisa-juridica-lex` usa a pesquisa comum (`pesquisa_juridica_lex`) ou profunda (`pesquisa_profunda_lex`) apenas quando essas ferramentas estiverem expostas. Conserva contexto, restrições, IDs e a chave de repetição; `consultar_tarefa_lex` e `cancelar_tarefa_lex` acompanham a mesma tarefa profunda. O painel (`abrir_painel_iajus`) é opcional e não inicia pesquisa.

As duas pesquisas são recurso **beta** e têm limite por conta, compartilhado entre ChatGPT, Claude, Codex e Microsoft: pesquisa comum 3 por dia e 10 por semana; pesquisa profunda 1 por dia e 7 por semana (o dia vira às 00:00 de America/Sao_Paulo; a semana, na segunda-feira às 00:00). Os números podem mudar: vale o que o servidor devolve em `limites`, quando o devolve; sem `limites`, a skill não calcula saldo. Depois de uma recusa por limite a skill não repete a chamada: informa o saldo e a renovação, se vieram, e oferece as ferramentas de corpus, que não consomem esse limite.
