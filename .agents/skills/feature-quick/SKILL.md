---
name: feature-quick
description: Ajuste rápido ou bug — plano curto no chat, implementa, testa com o subagente tester, verifica e sugere o commit, sem requirements.md nem plan.md. Use para corrigir um campo, um vínculo, um bug com causa conhecida ou que se reproduz rápido. Bug sem causa passa por "5 a 7 causas → 2 mais prováveis → provar 1" antes de corrigir.
---

# $feature-quick

Para ajuste rápido: corrigir um campo, consertar um vínculo, adicionar o que faltava num contrato,
corrigir um bug. O plano fica no chat: não nasce sessão em `docs/sessions/`.

## Cabe aqui?

O que decide é **a natureza da mudança**, não quantos arquivos ela toca. Migration, contrato de API
e cálculo podem ser ajuste rápido, quando o comportamento certo já está claro.

Não cabe, e vale sugerir `$feature-spec`, quando:

-   **falta decidir regra de negócio:** "como deveria funcionar?" não tem resposta, ou tem mais de
    uma razoável;
-   **a mudança de cálculo não é correção**, e sim comportamento novo que o usuário final vai notar;
-   **a ideia cresceu durante a investigação** e exige mais de uma entrega para conferir.

Se perceber isso no meio, pare e diga o que mudou na leitura. O usuário decide.

## 1. Entender

-   **Bug com causa indicada pelo usuário:** comece por ela e confirme no código. Não refaça a
    investigação que ele já fez.
-   **Bug sem causa:** reproduza ou ache onde acontece, e afunile antes de mexer em código:
    1.  Liste de 5 a 7 causas possíveis, sem filtrar.
    2.  Reduza às 2 mais prováveis, com o porquê.
    3.  Prove 1 com log, consulta no banco ou teste que falha hoje.

    Mostre as hipóteses. Se as 2 continuarem abertas, pergunte com opções. Só corrija com a causa
    provada.
-   **Ajuste:** leia o código que ele toca e o padrão vizinho.
-   Pergunte só o que o código não responde.

## 2. Plano no chat

```
**O que muda:** <1-2 frases, linguagem de quem usa>
**Onde:** <arquivos ou áreas>
**Pronto quando:**
- <comportamento> → teste: / tela: / comando: <como conferir>
**Não mexe em:** <o que fica de fora, se houver risco de confusão>
```

O "Pronto quando" segue as regras do `$feature-plan`. Bug → um dos itens é "reproduz antes, não
reproduz depois".

Na branch base (ou numa de outro assunto), pergunte se cria a branch (`fix/<slug>` ou
`feat/<slug>`) a partir da base atualizada.

## 3. Issue, se fizer sentido

Sugira abrir issue quando o ajuste muda algo que o usuário final ou o time vai notar, corrige dado
em produção ou mexe em migration. Uma linha basta. Só crie com pedido.

## 4. Implementar, testar e verificar

**Regra de tela.** Tela nova ou alterada usa os componentes que já existem e o padrão de uma tela
vizinha (layout, espaçamento, ordem dos campos). Nada de visual novo sem o usuário pedir. A skill
`$frontend-design` cria uma estética própria por tela: ela só entra quando o projeto ainda não tem
padrão visual (primeiras telas, landing page) ou quando o usuário pede um visual novo.

**Os testes não são seus.** Os itens `teste:` vão para o subagente `tester` no fluxo do
`$feature-test` (propor, o usuário aprova, escrever). O prompt leva o alvo e o texto dos itens;
**não** leva o raciocínio da implementação.

**Bug:** o tester vem **antes** da correção. O teste que reproduz o bug é escrito contra o código
ainda errado e fica com a marca de bug do `AGENTS.md`: é o "reproduz antes".

```
Fase: propor. Alvo: <arquivos e símbolos>.
Itens a cobrir, pelo texto: - <texto do item>
[Bug ainda não corrigido: <o que acontece> contra <o que deveria acontecer>.]
```

1. Bug: tester (propor, aprovar, escrever) e confirme que o teste reproduz o bug. Ajuste: passo 2.
2. Implemente seguindo o `AGENTS.md` e o código de referência. Documentação pela `$docs-codigo`.
3. Ajuste: agora o tester. Bug: tire a marca de bug do teste (sua única edição em teste).
4. Resumo: arquivos tocados (caminho + 1 linha) e os testes escritos.
5. **Peça autorização** para lint, formatação, typecheck e o teste citado, um de cada vez. Teste que
   falha se resolve no código; se o problema é o teste, ele volta ao tester. Bug que o tester achou
   **fora** do ajuste não entra: mostre com a correção provável e sugira o caminho.
6. Mostre o resultado item por item, citando o texto. Itens `tela:` ficam com o usuário.
7. Mudança delicada (migration com dado, cálculo): ofereça o subagente `verificador`.

## 5. Sugerir o commit

Mensagem no formato do `AGENTS.md`, uma linha. Commit só com pedido explícito, depois de mostrar o
diff completo. Para abrir o PR: `$feature-pr`.

## Regras

-   Nunca rode lint, typecheck, teste ou build sem pedir; um de cada vez.
-   Nunca escreva nem reescreva teste; só tire a marca de bug depois de corrigir.
-   Ajuste rápido não pula regra do `AGENTS.md`.
-   Não amplie o escopo. Viu outra coisa errada? Mencione no fim.
