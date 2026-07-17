---
name: consultar-legislacao-pt
description: 'Consulta legislação portuguesa vigente pelo MCP IAJUS - encontra a norma por nome ("Código Civil", "Regime Jurídico do Arrendamento Urbano") ou por número e tipo (Lei, Decreto-Lei, Portaria, Decreto Regulamentar, Constituição), lê o texto integral e confere a vigência/caducidade. Fonte: Diário da República, com identificador europeu de legislação (ELI). Acione para o texto ou a vigência de uma norma portuguesa. NÃO use para jurisprudência.'
allowed-tools: mcp__iajus-pt__buscar_norma_por_nome, mcp__plugin_iajus-pt_iajus-pt__buscar_norma_por_nome, mcp__iajus-pt__buscar_norma_por_numero, mcp__plugin_iajus-pt_iajus-pt__buscar_norma_por_numero, mcp__iajus-pt__obter_texto_norma, mcp__plugin_iajus-pt_iajus-pt__obter_texto_norma
---

# Consultar legislação portuguesa (IAJUS)

Tem acesso ao servidor MCP `iajus-pt`, que indexa legislação portuguesa recolhida do **Diário
da República** (DRE/INCM), com o identificador europeu de legislação (**ELI**). **Use o MCP em
vez de citar de memória - a fonte é a verdade.**

> **Sem piso temporal.** A legislação portuguesa cobre toda a norma recepcionada e **vigente**,
> de qualquer ano (um Decreto-Lei antigo ainda em vigor está em escopo). A vigência decide-se
> pela situação da fonte, nunca pelo ano.

## Tools

| Objetivo | Tool | Como |
|---|---|---|
| Encontrar a norma pelo nome/título | `buscar_norma_por_nome` | "Código Civil", "Código do Trabalho", "Regime Jurídico do Arrendamento Urbano". Devolve as candidatas com tipo, número, data e ELI. |
| Encontrar a norma pelo número e tipo | `buscar_norma_por_numero` | Passe o número e o tipo (por exemplo, Decreto-Lei n.º 47344 - Código Civil). Desambigua pelo tipo quando o número se repete entre tipos. |
| Ler o texto integral (consolidado vigente) | `obter_texto_norma` | Dado o identificador devolvido acima, traz o texto em vigor, artigo a artigo. |

## Tipos de norma (Portugal)

Constituição da República Portuguesa, Lei, Decreto-Lei, Lei Orgânica, Lei Constitucional,
Portaria, Decreto Regulamentar, Resolução da Assembleia da República, Resolução do Conselho de
Ministros, Despacho, e (regionais) Decreto Legislativo Regional dos Açores e da Madeira.

## Método (3 passos)

1. **Localize a norma certa.** Se o utilizador der o nome, use `buscar_norma_por_nome`; se der
   o número, use `buscar_norma_por_numero` **com o tipo** (o mesmo número pode existir em tipos
   diferentes). Confirme pelo título, data e ELI antes de avançar - não adivinhe entre
   candidatas parecidas.
2. **Leia o texto vigente.** Use `obter_texto_norma` para o texto consolidado em vigor. Cite o
   artigo exato (por exemplo, "Artigo 1305.º do Código Civil") e o ELI da norma.
3. **Confira a vigência antes de amparar.** O texto principal é o **vigente consolidado**. Uma
   norma revogada ou caducada não é apagada, mas sai MARCADA: sinalize explicitamente
   ("revogada por ...", "caducada") e nunca apresente uma norma fora de vigor como amparo atual.
   Se a resposta pedir o histórico de alterações por artigo, diga honestamente o que a tool
   devolveu e o que ainda não está disponível.

## Como citar (obrigatório)

- **Sempre** cite o **ELI** (ou o `link_completo`) devolvido - é o identificador estável da
  norma no Diário da República. **Nunca invente** número, artigo ou link.
- Cite o **tipo**, o **número**, a **data** e o **título** da norma, e o **artigo** exato que
  fundamenta a resposta.
- Preserve os acentos e o UTF-8 exatamente como na fonte.
- Se a norma **não** for localizada, **diga-o honestamente** - não cite um artigo plausível
  porém fabricado.

## Vocabulário (direito português)

Use: Diário da República, ELI, Decreto-Lei, Portaria, Decreto Regulamentar, vigência,
caducidade, revogação, norma consolidada. **Não** transponha terminologia, portais ou categorias
normativas de outros ordenamentos: a fonte é o Diário da República.

## Autenticação

O cliente MCP autentica por si: **login OAuth** (o navegador abre no primeiro uso) **ou** chave
`ik_*` no cabeçalho `Authorization: Bearer` (canal privado). Um **401** indica sessão/chave em
falta ou expirada. **Nunca** cole a chave em conversa nem em commit. Todas as tools são
somente-leitura.
