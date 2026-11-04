# IAJUS Direito Brasileiro

Pesquise jurisprudência e legislação brasileiras, confira citações e consulte a cobertura do acervo com referências às fontes disponíveis.

Plugin nativo do Google Antigravity, versão 3.0.29. Ele conecta ao servidor MCP remoto IAJUS do Brasil, em https://mcp.iajus.com.br/mcp, e exige uma conta IAJUS. Os resultados trazem a fonte, a data e o link oficial quando disponíveis.

## O que vem no pacote

- O servidor MCP `iajus` (Streamable HTTP), que o Antigravity identifica como `iajus-juris_iajus`.
- As skills de pesquisa jurídica, que também podem ser chamadas por `/<nome>`:

  - `consultar-legislacao-estadual`: Consulta normas estaduais e municipais brasileiras quando a conexão IAJUS oferece a rota e os filtros territoriais correspondentes. Não presume disponibilidade ao vivo por UF ou município.
  - `consultar-legislacao`: Pesquisa, lê e confere normas brasileiras. Use para texto de lei, dispositivo, identificação normativa, vigência ou alterações; legislação estadual e municipal tem skill própria.
  - `corpus-status`: Consulta cobertura e atualização declaradas pelo read-model do corpus IAJUS, sempre preservando as_of, limites e estado da medição. Não estima nem extrapola contagens.
  - `pesquisa-juridica-lex`: Solicita pesquisa jurídica integrada ao Lex (recurso beta, com limites por conta), com consulta, contexto e restrições preservados. Use somente quando a ferramenta Lex correspondente estiver realmente disponível.
  - `pesquisar-jurisprudencia`: Pesquisa decisões, precedentes qualificados, informativos e relações jurídicas brasileiras no IAJUS. Use para acórdãos, número CNJ, súmulas, temas e teses de tribunais. Não use para pesquisar legislação.
  - `verificar-citacoes`: Verifica uma a uma citações de decisões, precedentes e normas contra registros e fontes disponíveis. Use para checar existência, fidelidade e vigência; não use para pesquisar do zero.

As skills usam somente as ferramentas que a conexão listar para a sua conta. Uma ferramenta ausente é informada como indisponível, nunca substituída por outra.

## Instalação

Valide e instale a pasta do plugin com o CLI do Antigravity:

```bash
agy plugin validate --strict ./iajus-juris-antigravity
agy plugin install ./iajus-juris-antigravity
agy plugin list --json
```

Para remover, use `agy plugin uninstall iajus-juris`. Para trocar de versão numa instalação feita a partir de uma pasta ou de um repositório, desinstale o plugin e instale a pasta da nova versão.

## Login

O plugin não traz credenciais. Quando o servidor conecta pela primeira vez, o Antigravity descobre o login IAJUS (OAuth 2.1) e o abre no navegador: entre com a sua conta IAJUS. Se a sessão expirar ou a conexão pedir autenticação de novo, abra Agent settings, aba Customizations, e use Authenticate ao lado do servidor IAJUS deste plugin. Nunca cole tokens, senhas ou chaves no chat nem nos arquivos do plugin.

## Limites

- Zero resultados, ou o fim da janela de uma busca, não provam que a decisão ou a norma não existe.
- A cobertura do acervo é a que as ferramentas de cobertura declaram, com a data da medição.
- A pesquisa Lex é um recurso beta com limite de uso por conta, somado entre os hosts conectados à mesma conta IAJUS; o saldo vem na resposta do servidor.
- O plugin não protocola, não assina e não altera processos, e não pesquisa pessoas por dados privados.

## Suporte e políticas

- [Site](https://iajus.com.br)
- [Suporte](https://iajus.com.br/contato)
- [Política de privacidade](https://iajus.com.br/privacidade)
- [Termos de uso](https://iajus.com.br/termos)

Os arquivos deste pacote seguem a licença Apache-2.0 (veja LICENSE e NOTICE). O servidor MCP IAJUS, suas APIs e o acervo jurídico não são licenciados por este pacote; o acesso segue os planos, a autenticação, os limites e os termos do IAJUS.
