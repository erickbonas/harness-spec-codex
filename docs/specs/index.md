# <Nome do projeto> — Especificações

> Fonte de verdade do projeto, lida por gente e por agentes. Preenchido pelo `$iniciar-projeto` e
> mantido pelo subagente `documentador` a cada PR.

## Mapa

| Área          | Conteúdo                                         | Entrada                                  |
| ------------- | ------------------------------------------------ | ---------------------------------------- |
| **Produto**   | Para quem, por quê, o que cada área faz          | [product/index.md](product/index.md)     |
| **Técnico**   | Stack, mapa do código, decisões, fluxo de dev    | [technical/index.md](technical/index.md) |
| **Regras**    | Cálculos e regras com fonte externa              | [../regras/README.md](../regras/README.md) |
| **Sessões**   | Features em andamento (spec, plano, verificação) | [../sessions/README.md](../sessions/README.md) |

## Módulos do produto

-   <[Nome da área](product/features/<area>.md) — uma linha>

## Decisões de arquitetura

-   <[0001 — Título](technical/adr/0001-titulo.md)>

## Como atualizar

-   **Comportamento do produto mudou** → o módulo em `product/features/` (o `$feature-pr` propõe).
-   **Stack ou arquitetura mudou** → `technical/`, ou um ADR novo em `technical/adr/`.
-   **Regra de domínio mudou** → `docs/regras/<tópico>.md`, com a fonte.
-   **Feature em andamento** → `docs/sessions/<slug>/` (fluxo `$feature-*`).
