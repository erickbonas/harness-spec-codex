---
name: docs-codigo
description: Regras da documentação de código (docstring/JSDoc de classe, método, tabela, coluna, schema, componente) — o que quem chama não recupera lendo, sem prolixidade. Com um alvo, faz a passada só nele sem mudar código. O $feature-work e o $feature-quick seguem estas regras ao escrever código.
---

# $docs-codigo

Serve de dois jeitos:

-   **Passada num alvo** (um arquivo, uma classe, um método): a entrega é a doc desse alvo e nada
    mais. O código não muda.
-   **Regra ao escrever código**: a doc do que você cria ou muda segue estas regras, e a doc que a
    sua mudança deixou falsa é sua para consertar. Não faça passada nos vizinhos que não tocou.

Isto é só a doc **de código**. A documentação de produto e de sistema (`docs/specs`,
`docs/regras`) é do subagente `documentador`.

## Para que serve uma doc

A assinatura já diz o que a coisa é: tipos, nome, schema. A doc ganha o lugar dizendo o que quem
lê não recupera lendo: pré e pós-condição, caso de borda, a regra de negócio por trás, o que quebra
se mudar.

**Função, método, classe e interface sempre têm doc**, mesmo quando é óbvio: uma linha que diz
para que serve, nas palavras de quem chama. Um item sem doc no meio de documentados parece
esquecido.

## Ordem fixa

1. **Para que serve**, em uma linha.
2. **Como as peças se usam juntas**, quando há mais de uma.
3. **O observável que não é óbvio**: pré-condição, pós-condição, caso de borda, o que é filtrado
   ou normalizado, o que lança, o que um `null` quer dizer.
4. **`Implementação:`**, num parágrafo próprio, com tudo o que é interno. Quem só quer chamar sabe
   onde parar.

**Histórico só com o número ou a restrição junto.** "Antes era uma query por item", sozinho, não é
doc. O que ganha lugar é o resultado que impede o trabalho de ser refeito: a medição, a regra
legal, a issue que decidiu.

**Não documente o que a coisa não é.** "Isto não é um cache" põe o leitor no quadro errado.

## Recupere o porquê antes de escrever

Nesta ordem, parando quando tiver a resposta: quem chama (`rg -n '<nome>'`), o contrato (schema,
controller, tabela); o histórico (`git log -S '<nome>' --oneline` e `git show`); a regra de negócio
(`docs/specs/product/features/`, `docs/regras/`, ADRs, `requirements.md`/`plan.md` da sessão); as
regras do `AGENTS.md`.

## O que leva doc

-   **Classe/serviço**: para que serve; como as peças se usam juntas, quando há.
-   **Tipo exportado**: o que o valor representa, em uma linha.
-   **Tabela**: o que guarda em termos de negócio, se é por cliente ou global, se tem vigência, e o
    porquê dessas escolhas.
-   **Coluna, campo, membro de enum**: só o não óbvio (unidade, o que `null` quer dizer, formato,
    quem pode mudar) — mas veja "leia o bloco".
-   **Função e método**: uma linha mais o observável não óbvio. Não descreva o óbvio pelo nome.
-   **Constante**: o que quebra se o número mudar, e com o que tem que concordar.
-   **Componente de tela**: o que devolve ou emite; a prop cujo nome não diz o efeito.
-   **Obsoleto**: sempre com o substituto.

## Leia o bloco, não o item

Colunas de uma tabela, membros de um enum, campos de um schema e métodos irmãos são lidos juntos.
**Se um item do bloco tem doc, todos têm** (uma linha nos óbvios basta), e nenhum com mais de umas
três linhas: o motivo que não cabe nisso costuma ser da tabela ou da classe, e vai lá.

## Orçamento

Uma frase é o padrão; duas ou três quando há uma troca a nomear. Nunca: repetir a assinatura,
narrar o corpo, `@param`/`@returns` que só repetem nome e tipo, abrir com "Este método…", tempo de
processo ("adicionado na Sprint 2"). Diga o óbvio uma vez ou nenhuma.

## Diga o motivo quando a escolha parece estranha

Tabela global onde o resto é por cliente; leitura que não filtra apagados; `''` e `null` com
sentidos diferentes; coluna que outro sistema também grava; valor que tem que concordar com outro;
código no padrão antigo num módulo que já migrou. Cada uma é desfeita pelo próximo que passar, a
não ser que o motivo esteja do lado.

## Afirmações

Não escreva "rápido", "sem N+1" ou "idempotente" só de ler o código: siga a chamada até o fim ou
meça. Doc que deixou de ser verdade é bug.

## Quando o porquê não se recupera

Não invente. Escreva só a linha do óbvio, termine o resto e **pergunte no fim**, todas juntas, uma
por linha, com o seu palpite:

```
- `retemNaMesmaCidade`: vale só quando as duas partes estão na mesma cidade?
```

Ninguém sabe → fica a linha do óbvio. Nada de `TODO` ou "motivo desconhecido".

## A passada não muda código

Nem renomear, nem reordenar, nem extrair função. Código errado vai no relatório. Doc e comentário
no idioma que o `AGENTS.md` define.

Antes de reportar: confira com `git diff` que só comentário mudou e rode lint e formatação nos
arquivos tocados (com autorização). Relatório curto, uma linha por arquivo, as perguntas e o que
ficou quebrado. Sugira o commit; commit só com pedido.
