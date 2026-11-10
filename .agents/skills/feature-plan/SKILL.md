---
name: feature-plan
description: Segundo passo do fluxo spec-driven. Monta ou ajusta o docs/sessions/<slug>/plan.md a partir do requirements.md — sprints com status Pendente/Feito e "Pronto quando" conferível por teste, tela ou comando. Use depois do $feature-spec, ou quando o usuário pedir para replanejar, quebrar ou reordenar sprints.
---

# $feature-plan

Monta, ou ajusta, o plano em `docs/sessions/<slug>/plan.md`, a partir do `requirements.md`. O
`plan.md` é o único lugar com o estado das sprints.

## Descobrir a sessão

-   Argumento com slug → `docs/sessions/<slug>/`. Sem argumento: o slug sai da branch atual (regra
    no `AGENTS.md`).
-   Sem `requirements.md`: sugira `$feature-spec` antes. Se o usuário quiser seguir mesmo assim,
    use o que ele passar como brief.
-   Já existe `plan.md`? Então é ajuste: aplique o pedido com patch pontual e registre em
    `## Decisões`. Não reescreva o arquivo.

## Fluxo

1. Leia o `requirements.md` e o código que ele cita. `docs/specs/technical/codebase-guide.md` diz
   onde cada coisa mora, e o `AGENTS.md` diz como se escreve. Cada item dos **Critérios de aceite**
   tem que cair no "Pronto quando" de alguma sprint. Se algum não couber, diga ao usuário.
   Feature que mexe em fila, worker, job agendado, evento, retentativa ou integração externa:
   carregue a skill `$arquitetura-assincrona` antes de fatiar as sprints. Idempotência,
   duplicidade e o que acontece quando o job falha no meio viram item do "Pronto quando" ou ponto
   de atenção nas "Notas para implementar".
2. Escreva o `plan.md` no formato abaixo.
3. Mostre um resumo (as sprints e o "Pronto quando" de cada uma) e itere até aprovar. Aprovação e
   decisões (faseamento, o que fica de fora, como conferir um item) vão como perguntas com opções
   (regra do `AGENTS.md`).

```markdown
# Plano — <Nome da feature>

> Issue: <link> · Branch: `<branch>` · Requisitos: [requirements.md](requirements.md)
> Entrega: <PR único para a base | um PR por sprint em `integra/<slug>`>

## Sprint 1 — <o que entrega, em linguagem de usuário>

**Status:** Pendente

**Para quê:** <1-3 frases: por que esta sprint existe e por que nessa ordem>

**Pronto quando**

-   [ ] <comportamento observável>
        → teste: `<arquivo de teste>` › "<nome do teste>"
-   [ ] <comportamento observável>
        → tela: <onde entrar, o que fazer, o que tem que aparecer>
-   [ ] <comportamento observável>
        → comando: `<comando>` <o que tem que sair>

**Fora:** <o que fica para outra sprint ou outra issue>

**Notas para implementar**
<arquivos, padrões de referência, pontos de atenção. Guia para quem implementa, não é critério.>

## Sprint 2 — ...

## Decisões

-   <AAAA-MM-DD> — <o que mudou no plano> — <por quê>
```

`**Status:**` só assume `Pendente` ou `Feito`. Quem troca para `Feito` é o `$feature-work`, depois
da verificação.

## Como escrever o "Pronto quando"

É a parte que o usuário lê para dizer se acabou. Cada item responde, sozinho, **"como eu sei que
está pronto?"**, sem precisar abrir código.

-   **Comportamento, não implementação.** "Cliente sem endereço: o pedido continua indo para a fila
    manual" ✓. "`PedidoService` tem o método `rotear`" ✗: isso vai para as "Notas para implementar".
-   **Toda conferência é de um destes três tipos:**
    -   `teste:` um teste com nome, que já existe ou vai ser escrito na sprint. O verificador roda.
    -   `tela:` passos concretos no sistema rodando em desenvolvimento. O usuário confere.
    -   `comando:` um comando com a saída esperada. O verificador roda.
        "Ler o arquivo e confirmar que..." não é conferência: reescreva pelo efeito ou mova para as
        notas.
-   **O critério vale mais que a conferência.** Se a conferência deixa de existir (o teste foi
    apagado), o item fica e só a linha `→` muda: outra conferência, ou `→ teste: a escrever`.
-   **Tirar, juntar ou mover item é mudança de escopo.** Só com pedido explícito, e sempre com
    entrada em `## Decisões`.
-   **3 a 6 itens por sprint.** Mais que isso quase sempre é sprint grande demais: quebre.
-   **Não entram:** lint, typecheck, "não tocou no arquivo X". O verificador confere isso sempre.
-   **Teste só pela API pública.** Comportamento que só se alcança com mock pesado (serviço
    externo, certificado, fila) é `tela:` ou `comando:`.
-   Item não leva número nem ID. Quem se refere a um item cita o texto dele.

## Faseamento

-   Sprint é uma entrega que dá para conferir sozinha. Não misture mudança de cálculo com mudança de
    layout: se um número mudar, tem que dar para saber o motivo.
-   Migration com dado vai numa sprint que dá para conferir antes do código que depende dela.
-   Se o usuário impôs uma ordem, ela manda.
-   Sprint pequena é melhor que sprint "completa".
-   Mudança de plano depois de começar: edite a sprint ou crie uma nova, e anote em `## Decisões`.
    Nada de sprint "1b". Em sprint `Feito`, os critérios não saem: o que pode mudar é a conferência.

## Entrega: um PR ou vários

O padrão é **PR único para a branch base**: o humano revisa a feature inteira de uma vez.

Quando o PR vai ficar grande demais para revisar com atenção (várias sprints com mudança de
cálculo, áreas diferentes do sistema), sugira **um PR por sprint numa branch de integração**:

```
<base>
└── integra/<slug>                    criada da base
    ├── feat/<slug>/sprint-1  ──PR──▶ integra/<slug>
    ├── feat/<slug>/sprint-2  ──PR──▶ integra/<slug>   (depois do merge da sprint 1)
    └── ...
integra/<slug>  ──PR final──▶ <base>
```

Proponha, explique em uma frase e deixe o usuário decidir. Grave a escolha na linha `Entrega:`.

## Próximo passo

> "Plano aprovado. Rode `$feature-work` para começar a Sprint 1."
