# <Nome do projeto> — Técnico

> O "como". Atualizar quando houver mudança material em stack, deploy ou arquitetura. Regras de como
> o agente trabalha ficam no `AGENTS.md` da raiz.

## Perfil

| Item                  | Valor                          |
| --------------------- | ------------------------------ |
| **Tipo**              | <monorepo, app único, API...>  |
| **Linguagens**        | <...>                          |
| **Frameworks**        | <...>                          |
| **Banco**             | <...>                          |
| **Gestor de pacotes** | <...>                          |
| **Ambiente de dev**   | <Docker, local; Windows/WSL/Mac/Linux> |
| **CI/CD**             | <...>                          |
| **Deploy**            | <...>                          |

## Mapa

```
<árvore de 2 níveis com uma anotação por pasta>
```

## Documentos

-   [Mapa do código](codebase-guide.md) — onde cada coisa mora e onde olhar primeiro
-   [Decisões de arquitetura](adr/README.md) — por que a stack é como é
-   [Lógica de negócio](business-logic.md) — regras que atravessam o sistema
-   [API](api-specification.md) — padrões de rota, autenticação, validação e erro
-   [Fluxo de desenvolvimento](development-workflow.md) — ambiente, branches, PR, deploy
-   [Solução de problemas](troubleshooting.md) — problemas de ambiente que voltam
