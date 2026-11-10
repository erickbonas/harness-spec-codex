# <Nome do projeto> — Produto

> O "para quem" e o "por quê". Pareia com [technical/](../technical/index.md), que é o "como".

## Perfil

| Item                 | Valor                                     |
| -------------------- | ----------------------------------------- |
| **O que é**          | <uma frase>                               |
| **Problema que resolve** | <uma frase>                           |
| **Modelo**           | <interno, SaaS, cliente único...>         |
| **Estágio**          | <ideia, MVP, em produção>                 |

## Quem usa

| Perfil     | O que faz no sistema |
| ---------- | -------------------- |
| <perfil>   | <uma linha>          |

## Módulos

Cada área do produto tem um arquivo em [features/](features/), escrito para quem acabou de chegar
entender o que ela faz. Modelo: [features/\_modelo.md](features/_modelo.md).

-   <[Nome da área](features/<area>.md) — uma linha>

## Princípios

<!-- O que vale para qualquer feature: "o operador nunca perde trabalho digitado", "todo valor monetário tem duas casas". Só o que o time disse. -->

1. <princípio> — <por quê>

## Como atualizar

| Mudou…                             | Atualize                          |
| ---------------------------------- | --------------------------------- |
| Área nova ou descontinuada         | `features/<area>.md` + este índice |
| Comportamento de uma área          | `features/<area>.md`              |
| Perfil de usuário ou princípio     | este arquivo                      |
