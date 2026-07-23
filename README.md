# IAJUS: jurisprudência e legislação brasileira no seu assistente de IA

Este é o marketplace oficial do plugin **IAJUS**. Ele conecta o seu assistente (Claude Code, Codex ou ChatGPT) à base do IAJUS: jurisprudência dos tribunais superiores e regionais, súmulas e teses firmadas, e legislação federal, estadual e municipal com vigência conferida. Tudo com fonte oficial e link estável em cada resposta: o plugin existe para o seu assistente **citar o que existe de verdade**, nunca inventar precedente.

> Este marketplace é exclusivo do **Brasil**. Procura jurisprudência e legislação de **Portugal**? É um marketplace separado: [github.com/rafaelob/iajus-plugin-public-pt](https://github.com/rafaelob/iajus-plugin-public-pt).

## O que você ganha

- **Pesquisa de jurisprudência** em múltiplas modalidades (tese consolidada primeiro: súmulas, temas de repercussão geral e repetitivos; depois busca híbrida, semântica e por número CNJ), sempre com ementa e link oficial.
- **Legislação com vigência**: texto vigente de leis federais, estaduais e municipais, cadeia de alterações artigo por artigo e verificação na fonte oficial.
- **Panorama do corpus**: quanto a base cobre por tribunal e a faixa de anos de cada acervo, servido do read-model com carimbo de atualização.
- **Skills prontas** que ensinam o assistente o método de pesquisa jurídica, e (no Claude Code) **subagentes especializados**: pesquisador, elaborador de tese, refutador de tese (advogado do diabo), precedentes vinculantes, dossiê de processo, especialista em legislação, memorialista e conferente de citações.

## Instalar

### ChatGPT e Codex (pela interface, em poucos cliques)

1. Abra o diretório de plugins na interface (área de apps e plugins).
2. Procure por **IAJUS** e clique em adicionar.
3. Faça login com a sua conta IAJUS quando a janela de autorização abrir.

No Codex, este mesmo repositório também funciona como marketplace direto:

```bash
codex plugin marketplace add https://github.com/rafaelob/iajus-plugin-public
codex plugin add iajus-juris@iajus
```

### Claude Code (comandos, um de cada vez)

```text
/plugin marketplace add https://github.com/rafaelob/iajus-plugin-public
/plugin install iajus-juris@iajus
/plugin enable iajus-juris@iajus
/reload-plugins
```

O login OAuth abre no navegador na primeira conexão: entre com a sua conta IAJUS e pronto.

### Outros assistentes (conexão direta ao servidor MCP)

Assistentes que aceitam um servidor MCP por URL conectam ao IAJUS sem plugin, apontando para `https://mcp.iajus.com.br/mcp` e autenticando por **OAuth 2.1** (login no navegador). Você ganha as mesmas ferramentas de pesquisa jurídica; as skills prontas (pacotes de plugin do Claude Code e do Codex) e os subagentes especializados (só no Claude Code) descritos acima não acompanham a conexão direta.

- **Mistral (Le Chat):** Contexto → Conectores → Adicionar Conector → aba "Conector MCP personalizado" → Título `IAJUS`, Servidor `https://mcp.iajus.com.br/mcp`, Descrição `Pesquisa Jurídica`, Método de Autenticação **OAuth2.1** → Conectar → Autorizar.
- **Manus:** Plugins → Criar → "Adicionar MCP por URL" → Nome `IAJUS`, URL `https://mcp.iajus.com.br/mcp` → Salvar → no card do conector (MCP personalizado) → Conectar → login IAJUS → Autorizar.
- **Grok (SuperGrok):** no composer → menu **+** → "Adicionar conector" → "Customizado" → Nome `IAJUS`, URL `https://mcp.iajus.com.br/mcp` → "Adicionar conector" → login IAJUS → Autorizar → invocar com **@IAJUS** no composer.

## Conta e acesso

Crie a sua conta em [iajus.com.br](https://iajus.com.br). A autenticação padrão é **OAuth 2.1** (login no navegador, renovação automática): você nunca precisa colar token em arquivo. Detalhes de cada pacote, inclusive o fallback manual por chave, estão nos READMEs de `plugins/iajus-juris` (Claude Code) e `plugins/iajus-juris-codex` (Codex).

## Suporte

Dúvidas ou problemas: [iajus.com.br](https://iajus.com.br) ou contato@iajus.com.br.

Política de privacidade: [iajus.com.br/privacidade](https://iajus.com.br/privacidade). Termos de uso: [iajus.com.br/termos](https://iajus.com.br/termos).
