<!-- HARNESS-NAO-INICIALIZADO -->

# AGENTS.md

> **Harness ainda não inicializado.** Enquanto a linha `HARNESS-NAO-INICIALIZADO` estiver no topo
> deste arquivo, antes de qualquer outra tarefa diga ao usuário, em uma linha, que o projeto ainda
> não foi configurado e ofereça rodar a skill `$iniciar-projeto`. Ela entrevista o usuário e
> preenche este arquivo e a pasta `docs/specs/`. Se o usuário recusar, siga o pedido dele.

Instruções para o agente que trabalha neste repositório. Quem lê é o Codex (e qualquer agente que
siga o padrão AGENTS.md). Mantenha curto: o que for detalhe de produto ou de arquitetura mora em
`docs/specs/`, e este arquivo aponta para lá.

## Projeto

<!-- preenchido pelo $iniciar-projeto -->

-   **Nome:** <nome>
-   **O que é:** <uma frase>
-   **Para quem:** <usuários principais>
-   **Stack:** <linguagens, frameworks, banco>

Contexto de produto: [docs/specs/product/index.md](docs/specs/product/index.md). Contexto técnico:
[docs/specs/technical/index.md](docs/specs/technical/index.md).

## Comandos

<!-- preenchido pelo $iniciar-projeto. Um comando por linha, do jeito que roda no terminal da pessoa (PowerShell no Windows). -->

| Para quê                  | Comando                           |
| ------------------------- | --------------------------------- |
| Instalar dependências     | `<comando>`                       |
| Subir em desenvolvimento  | `<comando>`                       |
| Rodar um arquivo de teste | `<comando> <arquivo>`             |
| Rodar um teste pelo nome  | `<comando> <arquivo> -t "<nome>"` |
| Lint de arquivos          | `<comando> <arquivos>`            |
| Formatar arquivos         | `<comando> <arquivos>`            |
| Typecheck                 | `<comando>`                       |

## Testes

<!-- preenchido pelo $iniciar-projeto -->

-   **Frameworks:** <ex.: Vitest no front, pytest no back, Playwright no e2e>
-   **Onde ficam:** <ex.: `__tests__/` ao lado do código, `tests/` na raiz>
-   **Pastas de teste (para excluir do hash de conferência):** `<pathspec>`, `<pathspec>`
-   **Marca de "bug encontrado":** <ex.: `it.fails` (Vitest), `it.failing` (Jest), `test.fail()` (Playwright), `@pytest.mark.xfail(strict=True)` (pytest)>
-   **Mock obrigatório:** <serviços externos que nunca são chamados de verdade no teste>

## Fluxo de trabalho

Desenvolvimento guiado por spec, com as skills `$feature-*`:

```
$feature-spec → $feature-plan → $feature-work → $feature-pr
(ajuste pequeno ou bug com causa conhecida: $feature-quick)
(testes avulsos: $feature-test)

docs/sessions/<slug>/
├── requirements.md   o porquê, o que muda, critérios de aceite (espelho da issue)
├── plan.md           sprints com "Pronto quando" e status Pendente/Feito
└── verificacao.md    resultado do subagente verificador na última sprint
```

-   **Branch base:** `<develop | main>`. PR nunca vai direto para outra branch sem o usuário pedir.
-   **Branches:** `feat/<issue>-<slug>`, `fix/<slug>`, `chore/<slug>`. Feature grande com um PR por
    sprint: branch de integração `integra/<slug>` e sprints em `feat/<slug>/sprint-<N>`.
-   **Slug da sessão:** o nome da branch sem o prefixo (`feat/`, `fix/`, `chore/`, `integra/`) e sem
    o sufixo `/sprint-<N>`. `feat/123-exporta-csv/sprint-2` → `docs/sessions/123-exporta-csv/`.
-   **Ao começar uma sessão numa branch de feature:** leia o `plan.md` da sessão e diga em uma linha
    qual é a próxima sprint `Pendente`.
-   **Rastreador de issues:** <GitHub (`gh`) | Gitea (`tea`) | GitLab (`glab`) | nenhum>. Endereço:
    <url do repositório>. Texto de issue e PR vai por arquivo temporário (`--body-file`), nunca
    na linha de comando: o PowerShell quebra aspas e acentos.
-   **Commit:** <idioma> · uma linha, `<prefixo>: <o que muda, em linguagem de usuário> (#<issue>)`.

## Regras do agente

-   **Perguntas com opções.** Decisão ou dúvida para o usuário vai pela ferramenta
    `request_user_input` quando ela existir na sessão: até 3 perguntas por chamada, 2 a 4 opções
    cada, a recomendada primeiro e com "(Recomendado)" no rótulo. Sem a ferramenta, escreva as
    perguntas numeradas com opções `a)`, `b)`, `c)`, a recomendada primeiro, e **pare** esperando a
    resposta. Cada pergunta se explica sozinha (cita o item e o que você achou no código) e leva a
    sua hipótese.
-   **Pesquise antes de perguntar.** Pergunta que o código ou `docs/specs/` respondem não vai ao
    usuário.
-   **Commit, push, PR e comentário em issue só com pedido explícito.** "Sim", "segue" ou "faça"
    autorizam o trabalho, não o commit. Antes de commitar, mostre o diff dos arquivos que entram;
    `git add <arquivos>`, nunca `git add .`.
-   **Lint, typecheck, testes e build:** peça antes e rode um de cada vez.
-   **Não amplie o escopo.** Viu outra coisa errada pelo caminho? Mencione no fim, não conserte junto.
-   **Edição de arquivo** é com a ferramenta de patch, não com `sed -i`, `Set-Content` ou script
    que regrava o arquivo: o patch mostra o que sai e o que entra.
-   **Terminal.** O usuário pode estar no Windows (PowerShell). Prefira comandos que rodam igual nos
    dois (`git`, a CLI do projeto). Evite `grep`, `sed`, `xargs`, `cat <<EOF` e `&&` (o PowerShell 5
    não tem): use `rg` se estiver instalado, ou `Select-String`, e comandos separados.
-   **Dado pessoal ou segredo** (documento, senha, token, chave) nunca entra em código, teste,
    fixture, log, issue ou PR.
-   Documentação de código segue a skill `$docs-codigo`. Documentação de produto e sistema
    (`docs/specs/`, `docs/regras/`) é atualizada no `$feature-pr`, pelo subagente `documentador`.

## Skills de apoio

O fluxo `$feature-*` chama estas skills no momento certo. Elas também podem ser chamadas direto.

| Skill                      | Quando                                                                 |
| -------------------------- | ---------------------------------------------------------------------- |
| `$arquitetura-assincrona`  | fila, worker, job agendado, evento, retentativa, integração externa    |
| `$differential-review`     | revisão de segurança do diff (obrigatória no PR que toca área sensível) |
| `$insecure-defaults`       | configuração, segredo, variável de ambiente, valor padrão de segurança |
| `$frontend-design`         | primeiras telas ou visual novo pedido; não em tela que já tem padrão   |
| `$webapp-testing`          | conferir itens `tela:` no navegador com Playwright antes do usuário    |

## Convenções do código

<!-- preenchido pelo $iniciar-projeto: só o que o lint não pega e que um agente erraria sem saber. -->

-   <convenção> — <por quê>

## Onde está cada coisa

-   Mapa do código: [docs/specs/technical/codebase-guide.md](docs/specs/technical/codebase-guide.md)
-   Decisões de arquitetura: [docs/specs/technical/adr/](docs/specs/technical/adr/README.md)
-   Módulos do produto: [docs/specs/product/features/](docs/specs/product/index.md)
-   Regras de domínio (cálculo, legislação, contrato): [docs/regras/](docs/regras/README.md)
