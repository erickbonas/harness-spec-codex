---
name: feature-work
description: Terceiro passo do fluxo spec-driven. Implementa a próxima sprint Pendente do plan.md, delega os testes ao subagente tester, verifica com o subagente verificador, sugere o commit e marca a sprint como Feito. Use quando o usuário pedir para trabalhar, continuar ou implementar a sprint de uma feature com plan.md.
---

# $feature-work

Trabalha na feature sprint por sprint, seguindo o `docs/sessions/<slug>/plan.md`: implementa,
delega os testes ao subagente `tester`, verifica com o subagente `verificador`, sugere o commit e
marca como `Feito`.

Você não commita por conta própria nem mexe no index (`git add`, `git commit`, `git restore
--staged`). A mudança chega ao usuário sem commit, já verificada, e ele revisa pelo diff.

## Subagentes

Os subagentes estão em `.codex/agents/` (`tester`, `verificador`). Peça ao Codex para dispará-los
pelo nome. Se a sessão não tiver subagentes disponíveis, avise o usuário em uma linha e siga você
mesmo as instruções do arquivo `.toml` correspondente, deixando claro que a conferência perdeu o
contexto limpo.

## Descobrir a sessão e a sprint

-   Argumento com slug → `docs/sessions/<slug>/`. Sem argumento: o slug sai da branch atual.
-   Sem `plan.md`: sugira `$feature-plan`.
-   A sprint é a primeira com `**Status:** Pendente`, salvo se o usuário disser outra.
-   Entrega com um PR por sprint e a branch atual não é a da sprint? Sugira criar
    `feat/<slug>/sprint-<N>` a partir de `integra/<slug>`, depois do merge da anterior. Pergunte.
-   Diga em uma linha qual sprint vai pegar e em que ponto ela está: sem código ainda, implementada
    esperando verificação, ou com verificação que falhou (ver `verificacao.md`).

## 1. Implementar

1. Releia a sprint, o `requirements.md`, o `AGENTS.md` (e o `AGENTS.md` da pasta que vai tocar, se
   houver) e o código de referência citado nas notas.
2. Implemente. **Os testes não são seus**: os itens `teste:` vão para o subagente `tester` na
   etapa seguinte. Não escreva nem edite arquivo de teste, nem "um rascunho para o agente partir".
   A documentação do código segue a skill `$docs-codigo`. Tela: siga a regra de tela do
   `$feature-quick`.
3. Achou um item errado, impossível ou ambíguo? **Pare e pergunte**, citando o item pelo texto e o
   que encontrou. Não reinterprete em silêncio. Se o usuário decidir mudar, edite a sprint e anote
   em `## Decisões`. O mesmo vale quando um pedido tira a conferência de um item (apagar um
   teste): o item continua; pergunte como ele passa a ser conferido. Nunca tire, junte ou mova
   item por conta própria.
4. Mostre um resumo curto: arquivos tocados (caminho + 1 linha) e o que foi feito para cada item.

## 2. Testes

Os testes da sprint são escritos por um subagente de contexto limpo, que não viu a implementação:
quem implementa testa o que escreveu e confirma o código em vez de conferi-lo. Sprint sem item
`teste:` pula esta etapa. Itens `tela:` continuam sendo do usuário.

1. **Propor.** Dispare o `tester` com só isto:

    ```
    Fase: propor. Sessão: docs/sessions/<slug>/, Sprint <N> do plan.md.
    Alvo: <arquivos e símbolos que a sprint criou ou mudou>.
    Itens do Pronto quando a cobrir, pelo texto:
    - <texto do item> → teste: <arquivo> › "<nome>"
    ```

    **Não** passe o raciocínio da implementação nem o que você acha que deve ser testado.

2. Mostre a proposta como veio, sem resumir. Confira que **todo item `teste:` tem ao menos um
   teste**; item sem teste, ou com teste que não cobre o item, volta ao agente. O usuário aprova,
   corta ou ajusta, e diz sim ou não a cada item de "Proposto, fora dos testes".
3. Item aprovado fora dos testes que muda código (extrair função, `aria-label`) é seu: faça antes
   da fase escrever.
4. **Escrever.** Faça a **conferência do código** do `$feature-test` em volta do disparo:

    ```
    Fase: escrever. Sessão: docs/sessions/<slug>/, Sprint <N>. Alvo: <...>.
    Lista aprovada: <os testes, como vieram, com os ajustes do usuário>.
    Fora dos testes, aprovado: <itens, ou 'nada'>.
    ```

    Um tester de cada vez, nunca em paralelo: a sabotagem de um chegaria ao código que o outro
    testa.

5. Mostre o relatório. O nome do teste escrito difere do que o `plan.md` cita? Ajuste só a linha
   `→` do item e avise.
6. Teste deixado como bug encontrado (marca de bug do `AGENTS.md`): a implementação está errada.
   Corrija o **código**, troque a marca pelo teste normal (só isso no arquivo de teste) e rode
   aquele teste, com autorização. Se discorda do teste, não mexa nele: mostre ao usuário o teste,
   o que o código faz e por quê. Bug em código que a sprint **não** tocou: mostre com a correção
   provável e pergunte onde entra (sprint atual, sprint nova ou issue).
7. Sabotagem que não derrubou teste: mostre e pergunte se vale nova rodada de proposta.

## 3. Verificar

A verificação roda sobre o que ainda não foi commitado (`git diff HEAD` e os arquivos novos do
`git status`). Por isso vem antes do commit.

1. **Peça autorização.** A verificação roda lint, formatação, typecheck e os testes citados, e
   demora. Diga o que vai rodar e espere o "pode".
2. Dispare o `verificador`:

    ```
    Sessão: docs/sessions/<slug>/. Verifique a Sprint <N> do plan.md.
    A sprint está no working tree, ainda sem commit.
    ```

3. Mostre o resultado **citando o texto de cada item**, em três grupos: conferido (evidência em uma
   linha), falhou (o que e a causa provável), para o usuário conferir (os itens `tela:`, com os
   passos). E os achados de revisão do diff.
   Itens `tela:` de aplicação web: ofereça, em uma linha, passar os passos antes pela skill
   `$webapp-testing` (abre o navegador, segue os passos e tira print). Se o usuário quiser, mostre
   os prints e o que apareceu. A confirmação final continua sendo dele.
4. Falhou algo → corrija só o que falhou e verifique de novo (pedindo autorização de novo). Se o
   problema é o próprio teste, ele volta ao tester; você não edita o teste.
5. Tudo conferido e o usuário confirmou os itens de tela → marque os checkboxes e troque o
   `**Status:**` para `Feito`.
6. Marque no `requirements.md` os **Critérios de aceite** cobertos: só quando todas as sprints que
   o cobrem estão `Feito`. Com issue, ofereça sincronizar a descrição dela.
7. Ofereça, em uma linha, uma passada de simplificação no diff da sprint (reuso, código duplicado,
   o que dá para enxugar). Só faça se o usuário quiser; se mudar código que a verificação
   conferiu, diga e ofereça verificar de novo.

## 4. Sugerir o commit

Sugira a mensagem (formato do `AGENTS.md`, uma linha, sem citar item nem sprint) e os arquivos que
entram. Normalmente é um commit por sprint.

Commit só com pedido explícito ("pode commitar"). Pedido o commit: mostre o diff completo dos
arquivos que entram, espere o "ok", e só então `git add <arquivos>` (nunca `git add .`) e
`git commit`.

## 5. Próximo passo

Espere o commit. Pergunte antes de começar a próxima sprint.

-   Um PR por sprint: "Sprint <N> feita. Rode `$feature-pr` para abrir o PR em `integra/<slug>`."
-   Nenhuma `Pendente`: "Todas as sprints estão feitas. Rode `$feature-pr` para abrir o PR."

## Regras

-   Nunca rode lint, typecheck, teste ou build sem pedir; um de cada vez.
-   Nunca escreva nem reescreva teste. A única edição sua em arquivo de teste é tirar a marca de bug
    depois de corrigir o código.
-   Nunca pergunte "o item 3 passou?": a pergunta traz o texto do item, o que foi encontrado e a sua
    hipótese.
-   Não troque status para `Feito` sem a verificação ter passado e o usuário ter confirmado a tela.
-   Edite o `plan.md` com patch pontual, preservando o que o usuário ajustou à mão.
