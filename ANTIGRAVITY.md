# IAJUS para Antigravity

O pacote brasileiro `iajus-juris` contém seis skills e a configuração do servidor MCP remoto IAJUS. A versão está no [manifesto da pasta de instalação](plugins/iajus-juris-antigravity/plugin.json).

## Instalar a partir deste repositório

Baixe ou faça checkout deste repositório. Na raiz, use o CLI do Antigravity:

```bash
agy plugin validate --strict ./plugins/iajus-juris-antigravity
agy plugin install ./plugins/iajus-juris-antigravity
agy plugin list --json
```

Remova com `agy plugin uninstall iajus-juris`. O pacote não inclui credenciais. A autenticação do modelo no Antigravity é distinta do login OAuth da conta IAJUS no servidor MCP.

## Estado da integração

Em 3 de novembro de 2026, o CLI oficial 1.4.10 validou a versão 3.0.28 com `--strict`, instalou o pacote e listou `iajus-juris` num perfil isolado. A autenticação MCP, a pesquisa com a conta IAJUS, a renovação da sessão e a aprovação no marketplace continuam pendentes.

Use apenas as ferramentas realmente descobertas para sua conta e seus schemas. As skills não ampliam o catálogo, não habilitam extensões que o host não suporta e não garantem cobertura integral do acervo. O saldo e os limites são informados pelo serviço.

## Origem e manutenção

Os arquivos do pacote são gerados pelo pipeline IAJUS a partir do canal brasileiro Codex. Não edite manualmente os arquivos gerados.

Suporte: [iajus.com.br/contato](https://iajus.com.br/contato). Consulte também o [README do pacote](plugins/iajus-juris-antigravity/README.md), a licença e os limites de uso nele descritos.
