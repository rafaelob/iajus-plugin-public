# IAJUS: jurisprudência e legislação brasileira no seu assistente de IA

Este é o marketplace oficial do plugin **IAJUS**. Ele conecta o seu assistente (Claude Code, Codex ou ChatGPT) à base do IAJUS: jurisprudência dos tribunais superiores e regionais, súmulas e teses firmadas, legislação federal, estadual e municipal com vigência conferida, e jurimetria com números exatos. Tudo com fonte oficial e link estável em cada resposta: o plugin existe para o seu assistente **citar o que existe de verdade**, nunca inventar precedente.

## O que você ganha

- **Pesquisa de jurisprudência** em múltiplas modalidades (tese consolidada primeiro: súmulas, temas de repercussão geral e repetitivos; depois busca híbrida, semântica e por número CNJ), sempre com ementa e link oficial.
- **Legislação com vigência**: texto vigente de leis federais, estaduais e municipais, cadeia de alterações artigo por artigo e verificação na fonte oficial.
- **Jurimetria honesta**: volume de julgados, ranking de relatores, taxas de resultado e tempos de publicação, servidos de agregados exatos.
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

## Conta e acesso

Crie a sua conta em [iajus.com.br](https://iajus.com.br). A autenticação padrão é **OAuth 2.1** (login no navegador, renovação automática): você nunca precisa colar token em arquivo. Detalhes de cada pacote, inclusive o fallback manual por chave, estão nos READMEs de `plugins/iajus-juris` (Claude Code) e `plugins/iajus-juris-codex` (Codex).

## Suporte

Dúvidas ou problemas: [iajus.com.br](https://iajus.com.br) ou contato@iajus.com.br.
