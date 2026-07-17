# IAJUS - Jurisprudência & Legislação PT (plugin Claude Code)

Pesquisa e cita **jurisprudência e legislação portuguesa real** diretamente no Claude
(Claude Desktop, claude.ai e Claude Code), pelo servidor MCP remoto da IAJUS. As skills
orientam o Claude a usar a fonte oficial e a nunca citar de memória.

## O que inclui

- **Servidor MCP remoto** `iajus-pt` (`https://pt.mcp.iajus.com.br/mcp`) com pesquisa e
  leitura de jurisprudência e legislação PT.
- **Três skills pt-PT** que ensinam o Claude a escolher a modalidade certa, escalar a
  pesquisa e conferir cada citação antes de entregar:
  - `pesquisar-jurisprudencia-pt` - acórdãos do STJ, Tribunais da Relação (Lisboa, Porto,
    Coimbra, Évora, Guimarães), STA, TCA-Sul e Norte, Tribunal Constitucional, Conflitos e
    Tribunal de Contas, via DGSI; acórdão uniformizador de jurisprudência (AUJ), descritores,
    ECLI.
  - `consultar-legislacao-pt` - legislação do Diário da República (Lei, Decreto-Lei,
    Portaria, Decreto Regulamentar, Constituição), texto integral, ELI, vigência e caducidade.
  - `estado-corpus-pt` - o que o corpus PT contém agora (por órgão, ano e família).

## Fontes

- **Jurisprudência:** DGSI (www.dgsi.pt) - bases dos tribunais superiores e das Relações.
- **Legislação:** Diário da República (DRE/INCM), com identificador europeu de legislação (ELI).

O corpus é VIVO e cresce continuamente; órgãos, anos e famílias novos aparecem na pesquisa
sem alteração de plugin.

## Como ligar

O cliente MCP autentica por si. A autenticação canónica é **OAuth 2.1**: no primeiro uso, o
Claude abre o navegador para iniciar sessão em `app.iajus.com.br`. Não é preciso colar nenhuma
chave.

- **Claude Desktop / claude.ai:** adicione o conector com o URL
  `https://pt.mcp.iajus.com.br/mcp`; o login OAuth abre no primeiro uso.
- **Claude Code:** instale o plugin a partir do marketplace IAJUS; o `.mcp.json` já aponta o
  host PT e usa OAuth.

**Alternativa (canal privado/CLI):** uma chave `ik_*` no cabeçalho `Authorization: Bearer`.
Nunca cole a chave em conversa nem em commit. Um **401** significa sessão ou chave em falta ou
expirada: refaça o login ou reveja a chave.

## Notas

- Todas as tools são **somente-leitura**: pesquisam e citam, nunca escrevem.
- Preserve os acentos e o UTF-8 exatamente como na fonte.
- Vocabulário e direito **português** (não brasileiro): acórdão, Tribunal da Relação, DGSI,
  Diário da República, ELI, ECLI, descritores, acórdão uniformizador de jurisprudência.

Homepage: https://iajus.pt
