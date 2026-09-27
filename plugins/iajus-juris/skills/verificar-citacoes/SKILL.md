---
name: verificar-citacoes
description: 'Verifica as citações jurídicas de um texto (petição, parecer, memorial, decisão) contra fontes oficiais quando disponíveis pelo MCP IAJUS: existência, fidelidade e vigência, com veredito por citação (CONFIRMADA / DESATUALIZADA / NÃO LOCALIZADA). Acione em "confira as citações desta peça", "essas súmulas ainda valem?", "esse acórdão é real ou foi alucinado?", "valide os precedentes citados". É o antídoto da alucinação de citação. NÃO use para pesquisar do zero (use pesquisar-jurisprudencia).'
---

# Verificar citações jurídicas de um texto (IAJUS)

Você tem acesso ao servidor MCP `iajus` para **conferir, uma a uma, se as citações
jurídicas de um texto existem de verdade e estão vigentes**, batendo cada uma contra a
fonte oficial. É o antídoto contra a alucinação de citação - o erro mais perigoso de um
texto jurídico gerado por IA: súmulas que não existem, temas com número trocado, acórdãos
inventados, artigos revogados citados como vigentes. O produto é um **veredito por
citação**, ancorado no que a fonte retornou: **você nunca "confirma" de memória.**

Acione esta skill quando o usuário pedir "confira as citações desta peça", "essas súmulas
ainda estão em vigor?", "esse acórdão é real ou foi alucinado?", "valide os precedentes e
artigos citados". Para pesquisar do zero (achar precedentes que ainda não existem no
texto), use a skill `pesquisar-jurisprudencia`.

## Envelope de desfecho

Leia a chave `desfecho` ANTES de qualquer contagem. Os cinco valores são mutuamente exclusivos:

- `erro` - a consulta FALHOU; ninguém olhou o acervo. Não é ausência.
- `sem_resultado` - a consulta RODOU e o acervo não tem. Zero MEDIDO.
- `nao_terminou` - timeout ou teto. NÃO-MEDIDO; não afirme que «não existe».
- `parcial` - mediu uma parte; declare o que ficou de fora.
- `medida_indisponivel` - a fonte respondeu e NÃO carrega a medida. NÃO-MEDIDO sem avaria; não é zero.

`total: 0` só é ausência medida quando `desfecho` é `sem_resultado`. Sem `desfecho`, ou com `erro`/`nao_terminou`, a citação ficou **não verificável por falha** - não NÃO LOCALIZADA.

## O que você confere (três eixos)

1. **Existência** - a citação corresponde a um registro real na base? (o precedente/norma
   existe, com aquele número/tema/tipo).
2. **Fidelidade** - o que o texto afirma sobre a citação bate com a fonte? (o enunciado, o
   órgão, o relator/redator, a data, o dispositivo dizem o mesmo que a fonte).
3. **Vigência** - o ato ainda está em vigor? (súmula não cancelada/superada; lei/artigo não
   revogado; enunciado sem alteração de redação que mude a conclusão).

## Como verificar por tipo de citação

| Citação no texto | Tool(s) | O que conferir |
|---|---|---|
| Número de processo CNJ | `buscar_por_cnj` | Número completo = casamento exato (ou por componentes). Confira tribunal, data e relator/redator contra o que o texto afirma. |
| Súmula / SV / tema RG / repetitivo / IRDR / IRR / IAC / OJ | `buscar_qualificada` + `obter_versoes_qualificada` | A citação numérica ("súmula 145 do STF", "SV 11", "tema 1234") dispara lookup exato. **Leia e reporte o `status_vigencia`**: cancelada/superada/revogada vem MARCADA - se o texto a cita como amparo vigente, é DESATUALIZADA. Use `obter_versoes_qualificada` quando a redação pode ter mudado. |
| Quem citou / aplica um precedente | `buscar_por_citacoes` | Confere se o precedente realmente sustenta a tese que o texto lhe atribui. |
| Acórdão por tese/ementa (sem número) | `buscar_hibrida` / `buscar_semantica` | Acha o julgado real; se nada casar após escalada, é suspeito de fabricação → NÃO LOCALIZADA. |
| Dispositivo de lei citado (artigo/inciso), com REDAÇÃO | `buscar_dispositivos` + `obter_dispositivo_legal` | Confira DUAS coisas: (1) que o artigo **existe** naquela norma; (2) que a **redação bate** com o que o texto afirma. Redação divergente = DESATUALIZADA (ou o dispositivo foi alterado). |
| A norma inteira foi alterada/revogada? | `obter_alteracoes_norma` / `obter_grafo_norma` | Histórico e grafo de alterações por dispositivo (norma alteradora + data). |
| Status de vigência da norma inteira | `buscar_norma_por_nome` / `buscar_norma_por_numero` | Devolvem o `status` (vigente / revogada) da norma. |
| Lei estadual/municipal | `buscar_norma_fonte_oficial` / `obter_texto_norma` | Por UF [+ município] + tipo + número + ano (consulta ao vivo na fonte oficial). |

## Método (execute a verificação, não a reescrita)

Verificações independentes rodam em paralelo (cada citação é uma unidade); esgote a busca
antes de reprovar QUALQUER citação; feche com um resumo.

1. **Extraia as citações do texto** - liste cada precedente, súmula, tema, artigo e norma
   que o texto invoca, com o que o texto AFIRMA sobre cada um (enunciado, órgão, data,
   redação). Essa afirmação é o que você vai bater contra a fonte.
2. **Bata cada citação contra a fonte** pela tabela acima. Uma citação só se confirma se
   uma chamada REAL ao MCP nesta sessão a retornou.
3. **Esgote a busca antes de reprovar.** Se a primeira modalidade vier vazia, escale
   (semântica → híbrida → reformular → FTS/CNJ → tribunal superior) antes de marcar NÃO
   LOCALIZADA - uma citação real pode estar sob outra forma.
4. **Confira a vigência sempre.** Uma súmula/qualificada que existe mas está
   cancelada/superada (`status_vigencia`), ou uma lei/artigo revogado, é DESATUALIZADA,
   não CONFIRMADA - mesmo que o enunciado exista.
5. **Emita o veredito por citação** (formato abaixo) e feche com um resumo.

## Veredito por citação (formato de saída)

Para cada citação, emita uma linha de veredito:

- **CONFIRMADA** - existe, fiel e vigente. Anexe o `link_completo` oficial e o dado que
  confirma (enunciado/ementa/redação, tipo, órgão, data).
- **DESATUALIZADA** - existe, mas o `status_vigencia`/`status` é cancelada/superada/revogada,
  OU o texto afirma algo divergente da fonte (número, órgão, redação). Diga exatamente o que
  diverge e qual é o dado correto na fonte (com link).
- **NÃO LOCALIZADA** - a fonte devolveu `desfecho=sem_resultado` após busca adequada
  (inclusive escalada). **É um alerta de possível alucinação** - reporte como não
  verificável, NUNCA "confirme" para completar. Distinga: cobertura em andamento
  (`sem_resultado` em órgão já em cobertura) OU citação fabricada. Se `desfecho` for
  `erro` ou `nao_terminou`, a citação ficou **não verificável por falha**, não
  NÃO LOCALIZADA.

Feche com um **resumo**: N confirmadas, N desatualizadas, N não localizadas - e destaque as
não localizadas como as que exigem atenção (risco de citação fabricada).

## Regras (inegociáveis)

- **Nunca confirme de memória.** Uma citação só é CONFIRMADA se a chamada ao MCP nesta
  sessão a retornou. Sem retorno, é NÃO LOCALIZADA - nunca um "provavelmente existe".
- **Vigência sempre conferida** (`status_vigencia`/`status`); ato não-vigente é
  DESATUALIZADA, não CONFIRMADA.
- **Dispositivo: existência E redação** - artigo só é CONFIRMADO se existe naquela norma E
  a redação vigente bate com o que o texto afirma.
- **Reporte o link oficial** em toda citação confirmada ou corrigida, para o veredito ser
  rastreável.
- **Não edite o texto do usuário.** Você entrega o veredito; a correção é decisão do autor.

## Subagentes IAJUS (Claude Code)

No **Claude Code**, um lote grande de citações delega bem ao subagente
**`conferente-citacoes`** (mesmo método, contexto isolado), que se chama
`iajus-juris:conferente-citacoes`: passe a ele o texto a verificar. Ele devolve o veredito
por citação + o resumo. Nos demais clientes (claude.ai, Cowork, ChatGPT, Codex),
**execute a verificação você mesmo** pelo método acima, sem delegar.

**Autenticação:** o cliente MCP autentica por você - via **login OAuth** (claude.ai /
ChatGPT / Codex / Cowork abrem o navegador no primeiro uso) **ou** por chave `ik_*` no
header `Authorization: Bearer` (canal CLI/privado). Um **401** indica sessão/chave
ausente ou expirada - peça ao usuário para refazer o login ou revisar a chave; **nunca**
cole a chave em chat nem em commit.
