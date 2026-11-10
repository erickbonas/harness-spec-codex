---
name: feature-pr
description: Último passo do fluxo spec-driven. Abre o PR da feature ou da sprint — antes, roda o subagente documentador para atualizar docs/specs e docs/regras e pede revisão de segurança quando o diff toca área sensível. Com PR já aberto, trata os comentários da revisão. Use quando o usuário quiser abrir o PR, preparar o PR ou responder à revisão.
---

# $feature-pr

Abre o PR da feature. Se o PR já existe, trabalha nos comentários da revisão.

## Descobrir a sessão e o PR

-   Argumento com slug → `docs/sessions/<slug>/`. Sem argumento: o slug sai da branch atual.
-   Sem sessão (ajuste do `$feature-quick`): monte o PR a partir dos commits, do diff e da conversa.
    O "Como testar" sai do "Pronto quando" combinado no chat; se ele não estiver na conversa,
    proponha um a partir do diff.
-   Já existe PR aberto para a branch (CLI do rastreador do `AGENTS.md`)? Vá para **Revisão**.
-   Não existe → **Abrir**.

## Abrir

### Base e escopo

| Branch atual                | Base do PR       | O PR cobre                                |
| --------------------------- | ---------------- | ----------------------------------------- |
| `feat/<slug>` (PR único)    | a base           | a feature inteira                         |
| `feat/<slug>/sprint-<N>`    | `integra/<slug>` | só a sprint N                             |
| `integra/<slug>`            | a base           | o PR final, soma das sprints já revisadas |

Sem sessão, a base é a do `AGENTS.md`.

### Pré-checagem

1. PR da feature inteira com sprint ainda `Pendente`: liste e pergunte se abre mesmo assim.
2. **Documentação.** Dispare o subagente `documentador` (`.codex/agents/documentador.toml`):

    ```
    Diff: git diff <base>...HEAD. Sessão: docs/sessions/<slug>/ (ou 'sem sessão').
    ```

    Mostre a proposta. O que o usuário aprovar, aplique com patch e inclua na sugestão de commit.
    "Nada a atualizar" também se mostra, em uma linha.

3. **Revisões.** Olhe os arquivos do diff:
    - **Área sensível** (as que o `AGENTS.md` lista; em geral autenticação, permissão, senha,
      token, certificado, pagamento, dado pessoal): rode a skill `$differential-review` no diff
      antes de abrir, sem perguntar. Se o diff mexe em configuração, variável de ambiente, segredo
      ou valor padrão de segurança, rode também a `$insecure-defaults`. Achado vai ao usuário como
      pergunta com opções: corrigir agora, abrir issue ou aceitar com o porquê. O relatório das
      skills é rascunho: não entra no commit.
    - **Fila, worker, job ou integração externa no diff:** passe a `$arquitetura-assincrona` no
      modo revisor.
    - **Qualquer outro PR:** ofereça, em uma linha, a `$differential-review` no diff. Só rode se o
      usuário quiser: ela é demorada.
4. `git status`: arquivo fora de commit → liste e sugira a mensagem. Commit só com pedido
   explícito, depois de mostrar o diff.
5. Antes do PR, os testes do que foi tocado, o lint e o typecheck têm que passar. Ofereça rodar, um
   de cada vez, e só rode com autorização.

### Fluxo

1. Monte título e corpo e **mostre antes de publicar**:
    - Título: até 70 caracteres, no formato de commit do `AGENTS.md`. PR de sprint diz qual parte da
      feature é.
    - Corpo sem quebra de linha manual e sem dado pessoal:

```markdown
<link da issue>

## Resumo

-   <1-3 itens em linguagem de usuário, a partir do Resumo / O QUE do requirements.md>

## Como testar

-   [ ] <itens `tela:` das sprints que o PR cobre, com os passos>

## Decisões

-   <só as de `## Decisões` do plan.md que mudam o que o revisor espera ver; omita se não houver>

Closes #<issue>
```

PR final de `integra/<slug>`: no lugar de "Como testar", liste os PRs de sprint já revisados e os
**Critérios de aceite** do `requirements.md`.

2. Aprovado → pergunte antes do push: `git push -u origin <branch>`.
3. Crie o PR pela CLI do rastreador, com o corpo num arquivo temporário (não na linha de comando:
   o PowerShell quebra aspas e acentos).
4. Ofereça comentar o link do PR na issue e devolva o link.

## Revisão

1. Leia os comentários de linha e os gerais. Ignore os resolvidos e os seus.
2. Mostre a lista, um por linha: quem, onde (`arquivo:linha`), o que pede, e a sua leitura:
    - **ajuste**: dá para fazer como pedido
    - **discordo**: diga por quê, em uma frase
    - **dúvida**: o comentário não diz o que quer; sugira o que perguntar
    - **fora do escopo**: vira issue ou fica para depois
3. O usuário escolhe o que entra. Cada ajuste é tratado como um `$feature-quick`. Ajuste que muda o
   plano vai para `## Decisões` do `plan.md`.
4. Para cada comentário tratado, rascunhe a resposta. Mostre e só poste com pedido.
5. Push só depois do commit, e perguntando antes.

## Regras

-   Nunca dê push, crie PR ou poste comentário sem mostrar o conteúdo e pedir.
-   Nunca commite sem pedido explícito nem mexa no index fora do commit pedido.
