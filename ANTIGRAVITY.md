# IAJUS para Antigravity

O pacote brasileiro está em [`plugins/iajus-juris-antigravity`](plugins/iajus-juris-antigravity/). Ele contém seis skills e a configuração do servidor MCP remoto IAJUS. A identidade do plugin é `iajus-juris`, versão 3.0.24.

## Instalar a partir deste repositório

Baixe ou faça checkout deste repositório. Na raiz, use o CLI do Antigravity:

```bash
agy plugin validate --strict ./plugins/iajus-juris-antigravity
agy plugin install ./plugins/iajus-juris-antigravity
agy plugin list --json
```

Remova com `agy plugin uninstall iajus-juris`. O pacote não inclui credenciais. A autenticação do modelo no Antigravity é distinta do login OAuth da conta IAJUS no servidor MCP.

## Estado da integração

Esta publicação disponibiliza o pacote; não declara aprovação no marketplace. A versão 3.0.24 passou pela validação estrita, instalação, listagem e remoção no CLI oficial 1.4.10, na AWS em 02/11/2026: seis skills, um servidor MCP e inventários vazios antes da instalação e após a remoção. A validação de arquivos não comprova autenticação, pesquisa ou renovação de sessão. O aceite dessas três etapas nativas permanece pendente. [Evidências e acompanhamento](https://github.com/rafaelob/iajus/issues/7799#issuecomment-6577356280).

Use apenas as ferramentas realmente descobertas para sua conta e seus schemas. As skills não ampliam o catálogo, não habilitam extensões que o host não suporta e não garantem cobertura integral do acervo. O saldo e os limites são informados pelo serviço.

## Origem e manutenção

Os arquivos do pacote são gerados pelo projeto IAJUS, usando `scripts/ops/build_plugin_submission_bundle.py --country BR --alvo antigravity`, a partir do canal brasileiro Codex. A versão aqui publicada foi gerada na fonte `b7f405c3d8dc35d080c9fb0fad14f1de8aed006d`, corrida AWS `45513a46c196`; não edite manualmente os arquivos gerados.

Suporte: [iajus.com.br/contato](https://iajus.com.br/contato). Consulte também o [README do pacote](plugins/iajus-juris-antigravity/README.md), a licença e os limites de uso nele descritos.
