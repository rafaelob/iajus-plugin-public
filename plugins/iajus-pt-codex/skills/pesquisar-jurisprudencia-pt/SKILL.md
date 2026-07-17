---
name: pesquisar-jurisprudencia-pt
description: Pesquisa e cita jurisprudência portuguesa real (Supremo Tribunal de Justiça, Tribunais da Relação, STA, TCA-Sul/Norte, Tribunal Constitucional, Conflitos, Tribunal de Contas) pelo MCP IAJUS - modalidades semântica, híbrida, texto integral, regex e citações, mais qualificadas (acórdão uniformizador de jurisprudência) com vigência. Acione para acórdão, descritor, sumário, ECLI, tese firmada ou entendimento de um tribunal português. NÃO use para legislação.
allowed-tools: mcp__iajus-pt__buscar_semantica, mcp__plugin_iajus-pt_iajus-pt__buscar_semantica, mcp__iajus-pt__buscar_hibrida, mcp__plugin_iajus-pt_iajus-pt__buscar_hibrida, mcp__iajus-pt__buscar_fts, mcp__plugin_iajus-pt_iajus-pt__buscar_fts, mcp__iajus-pt__buscar_regex, mcp__plugin_iajus-pt_iajus-pt__buscar_regex, mcp__iajus-pt__buscar_por_citacoes, mcp__plugin_iajus-pt_iajus-pt__buscar_por_citacoes, mcp__iajus-pt__buscar_qualificada, mcp__plugin_iajus-pt_iajus-pt__buscar_qualificada, mcp__iajus-pt__obter_versoes_qualificada, mcp__plugin_iajus-pt_iajus-pt__obter_versoes_qualificada
---

# Pesquisar jurisprudência portuguesa (IAJUS)

Tem acesso ao servidor MCP `iajus-pt`, que indexa jurisprudência portuguesa dos tribunais
superiores e das Relações, recolhida da fonte oficial DGSI (www.dgsi.pt), mais o Tribunal de
Contas. **Use o MCP em vez de inventar jurisprudência - a fonte é a verdade; nunca cite de
memória.**

> **Corpus VIVO e em crescimento:** a base é ingerida continuamente; órgãos, anos e famílias
> novos aparecem na pesquisa automaticamente, sem alteração de skill. Um `total: 0` (ou recall
> fraco) para um órgão/ano que já está em cobertura significa **cobertura em andamento**, não
> "não existe": avise o utilizador e ofereça uma alternativa (por exemplo, o tribunal superior).
> Para conferir o que a base contém AGORA, use a skill **estado-corpus-pt**.

## Órgãos cobertos (DGSI + Tribunal de Contas)

| Tribunal | orgao_code | Nota |
|---|---|---|
| Supremo Tribunal de Justiça | `pt_stj` | Supremo comum; profere acórdão uniformizador de jurisprudência (AUJ). |
| Tribunal da Relação de Lisboa | `pt_trl` | 2.ª instância. |
| Tribunal da Relação do Porto | `pt_trp` | 2.ª instância. |
| Tribunal da Relação de Coimbra | `pt_trc` | 2.ª instância. |
| Tribunal da Relação de Évora | `pt_tre` | 2.ª instância. |
| Tribunal da Relação de Guimarães | `pt_trg` | 2.ª instância. |
| Supremo Tribunal Administrativo | `pt_sta` | Cúpula da jurisdição administrativa e fiscal. |
| Tribunal Central Administrativo Sul | `pt_tcas` | Administrativo e fiscal, 2.ª instância. |
| Tribunal Central Administrativo Norte | `pt_tcan` | Administrativo e fiscal, 2.ª instância. |
| Tribunal Constitucional | `pt_tc` | Fiscalização da constitucionalidade; força obrigatória geral (Art. 281.º CRP). |
| Conflitos | `pt_conflitos` | Conflitos de jurisdição/competência. |
| Tribunal de Contas | `pt_tdc` | Fiscalização prévia e concomitante das contas públicas. |

## Escolha da modalidade

Comece pela modalidade certa para a pergunta. As pesquisas devolvem um envelope uniforme
(`{ modalidade, total, resultados:[...] }`) e são somente-leitura.

| Pergunta do utilizador | Tool | Porquê |
|---|---|---|
| Tema/conceito ("responsabilidade civil por acidente de viação") | `buscar_semantica` | Vetorial/densa - casa por significado. **Padrão para perguntas conceituais.** |
| Melhor resultado geral (relevância máxima) | `buscar_hibrida` | Funde semântica e texto integral por RRF. **Recorra a ela quando a densa vier fraca.** |
| Expressão exata / termo técnico literal ("enriquecimento sem causa") | `buscar_fts` | Texto integral, insensível a acento; procura a expressão tal como escrita. |
| Padrão literal / forma de citação ("Artigo 483.º", um número de ECLI) | `buscar_regex` | Expressão regular; inclua um trecho literal com pelo menos 3 caracteres. |
| Quem citou um acórdão/qualificada, ou o que um acórdão cita | `buscar_por_citacoes` | Rede de citações; traz quem aplicou um dado precedente. |
| Entendimento CONSOLIDADO/uniformizador de um tribunal | `buscar_qualificada` | Lê os acórdãos uniformizadores de jurisprudência e demais precedentes qualificados. **Prefira-os a um acórdão isolado** para "o entendimento atual". |
| A redação de uma qualificada mudou? Histórico de versões | `obter_versoes_qualificada` | Versões da redação de um precedente uniformizador (mudou, quando e porquê). |

Notas de uso:
- **Filtragem por órgão:** `buscar_semantica` e `buscar_hibrida` aceitam o nome do tribunal;
  `buscar_fts` e `buscar_regex` filtram por `orgao_code` (o slug minúsculo da tabela acima, por
  exemplo `pt_stj`). Não misture os dois: a tool rejeita o argumento incorreto e devolve
  `{ "erro": "...", "resultados": [] }` (nunca stack trace) - leia a mensagem e ajuste.
- **Recorte por ano:** a densa aceita um ano exato; as demais aceitam uma faixa (`ano_min`/
  `ano_max`). PT não tem piso temporal: recolhe-se todo o histórico que a fonte publica.
- `buscar_regex` recusa padrões só de metacaracteres; inclua um trecho literal com pelo menos
  3 caracteres.

## Método do pesquisador (5 passos)

Uma pesquisa jurídica a sério quase nunca é UMA chamada: é uma varredura que escala de
modalidade até a cobertura estabilizar.

1. **Priorize o conteúdo consolidado antes do acórdão comum.** Para "qual o entendimento
   atual / a tese firmada", comece por `buscar_qualificada` (acórdão uniformizador de
   jurisprudência e demais qualificadas): o precedente uniformizador vence um acórdão isolado,
   e cita-o com a vigência já conferida. Só depois desça ao acórdão individual (semântica/
   híbrida) para exemplificar a aplicação da tese.
2. **Escale a modalidade em vez de cair no vazio.** Uma pesquisa fraca (`total: 0`, ou hits
   cujo trecho não responde) NÃO é sinal de parar - é sinal de escalar, nesta ordem:
   - `buscar_semantica` -> **`buscar_hibrida`** com a MESMA consulta (a fusão resgata o que a
     densa isolada perdeu);
   - **reformule** com os termos que apareceram nos primeiros hits (relator, descritor,
     dispositivo citado, número de acórdão);
   - **troque de modalidade pela forma da pergunta:** termo técnico literal -> `buscar_fts`;
     citação de artigo/ECLI -> `buscar_regex`; rede de um precedente -> `buscar_por_citacoes`;
   - **suba na hierarquia de autoridade:** se uma Relação vier vazia para a tese, tente o
     Supremo competente (STJ para o comum, STA para o administrativo/fiscal, TC para a
     constitucionalidade) - a tese firmada costuma estar lá.
3. **Refine até a cobertura estabilizar** (rondas sem resultado novo relevante) e cruze as
   modalidades: densa/híbrida para o panorama, FTS/regex para termos e citações literais,
   citações para a rede do precedente-chave.
4. **Envelope de honestidade.** Reporte o vazio como vazio, com o motivo: um `total: 0` de
   órgão/ano em cobertura é **cobertura em andamento**, não "o precedente não existe" (diga-o
   e ofereça a fonte superior); um filtro rejeitado pede reenvio, não descarte do resultado;
   um `{ "erro": ... }` pede ajuste do argumento. Nunca preencha a lacuna com um precedente
   plausível porém fabricado.
5. **Passada de conferência anti-alucinação (obrigatória antes de entregar).** Toda citação
   que entregar tem de ter vindo de uma chamada REAL nesta sessão, com o `link_completo` que a
   fonte devolveu. Antes de fechar, confira cada citação:
   - **existe?** o acórdão com aquele número/descritor/ECLI voltou de uma chamada;
   - **é fiel?** o sumário, o tribunal, o relator e a data que afirma batem com a fonte;
   - **está vigente?** confira `status_vigencia` (uma qualificada revogada/superada sai
     MARCADA, nunca como amparo vigente).
   Um número, sumário, relator ou link só entra na resposta se veio da tool. Sem retorno, é
   NÃO LOCALIZADA (possível alucinação) - reporte assim, nunca "provavelmente existe".

## Como citar (obrigatório)

- **Sempre** cite o campo `link_completo` do registo devolvido - é a URL estável do acórdão na
  DGSI. **Nunca invente** número, sumário, descritor ou link.
- Cite o `tribunal`, o `numero_processo`, o `relator` e a `data` (julgamento/publicação) quando
  presentes; quando houver, apresente o **ECLI** e os **descritores**. Resuma o `sumário` em 1-2
  frases.
- Ao citar uma qualificada (acórdão uniformizador de jurisprudência), **confira a vigência**
  em `status_vigencia` e sinalize explicitamente se estiver revogada/superada - nunca a
  apresente como vigente.
- Se a pesquisa **não** devolver resultado relevante (`total: 0` ou hits fracos), **diga-o
  honestamente** - não preencha a lacuna com um precedente fabricado.

## Vocabulário (direito português, não brasileiro)

Use: acórdão, Tribunal da Relação, Supremo Tribunal de Justiça, acórdão uniformizador de
jurisprudência (AUJ), descritores, sumário, ECLI, força obrigatória geral. **Não** transponha
instituições, precedentes qualificados ou sistemas de classificação de outros ordenamentos.

## Autenticação e aprovação de ferramentas

- O cliente MCP autentica por si: **login OAuth** (o navegador abre no primeiro uso) **ou**
  chave `ik_*` no cabeçalho `Authorization: Bearer` (canal privado). Um **401** indica sessão
  ou chave em falta/expirada: peça ao utilizador para refazer o login ou rever a chave; **nunca**
  cole a chave em conversa nem em commit.
- Todas as tools são **somente-leitura** (`readOnlyHint`): não escrevem nada. Se o cliente pedir
  aprovação por chamada, oriente o utilizador a autorizar uma vez e marcar "sempre permitir"; é
  seguro liberar em bloco. Prefira a pesquisa direta (`buscar_hibrida`/`buscar_semantica`), que
  já devolve sumário e `link_completo` oficial de cada acórdão.
