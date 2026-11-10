# Harness spec-driven para o Codex

Um fluxo de desenvolvimento guiado por spec para o [Codex](https://developers.openai.com/codex):
o agente pergunta antes de construir, grava o combinado em arquivos e confere a entrega contra
esse combinado.

```
$iniciar-projeto   uma vez: entrevista sobre o projeto e monta AGENTS.md + docs/specs
$feature-spec      conversa e grava docs/sessions/<slug>/requirements.md
$feature-plan      quebra em sprints com "Pronto quando" conferível
$feature-work      implementa a sprint, testa com subagente, verifica com subagente
$feature-pr        atualiza a documentação e abre o PR
$feature-quick     ajuste pequeno ou bug, sem sessão
$feature-test      testes avulsos com o subagente tester
$docs-codigo       regras de documentação de código
```

Skills de apoio, chamadas pelo fluxo na hora certa (ou direto, com `$nome`):

| Skill                     | O que faz                                                                  | Chamada por               |
| ------------------------- | -------------------------------------------------------------------------- | ------------------------- |
| `$arquitetura-assincrona` | checklist de fila, worker, retentativa, idempotência, saga                 | `$feature-plan`, `$feature-pr` |
| `$differential-review`    | revisão de segurança do diff, com raio de impacto e relatório              | `$feature-pr`             |
| `$insecure-defaults`      | segredo fixo, autenticação fraca, padrão permissivo                        | `$feature-pr`             |
| `$frontend-design`        | telas com identidade visual própria, para quando ainda não há padrão       | `$feature-quick`, `$feature-work` |
| `$webapp-testing`         | abre a aplicação no navegador (Playwright) e confere os itens `tela:`      | `$feature-work`           |
| `migration`               | criada pelo `$iniciar-projeto` quando o projeto tem banco                  | `$feature-plan`, `$feature-work` |

## Começar

### Projeto novo

No PowerShell:

```powershell
git clone <url deste repositório> meu-projeto
cd meu-projeto
Remove-Item -Recurse -Force .git
git init
codex
```

### Projeto que já existe

Copie para a raiz do seu repositório as pastas `.agents`, `.codex`, `docs` e os arquivos
`AGENTS.md` e `.gitattributes`. Se o projeto já tiver um `AGENTS.md`, renomeie o seu antes
(por exemplo `AGENTS.antigo.md`): o `$iniciar-projeto` lê o antigo e aproveita o que houver.

```powershell
$h = "C:\caminho\para\harness-spec-codex"
Copy-Item -Recurse "$h\.agents", "$h\.codex", "$h\docs" .
Copy-Item "$h\AGENTS.md", "$h\.gitattributes" .
codex
```

### Primeira sessão

1. Quando o Codex perguntar, **marque o projeto como confiável**: os subagentes de `.codex/agents/`
   só carregam em projeto confiável.
2. O `AGENTS.md` vem com a marca `HARNESS-NAO-INICIALIZADO`. Na primeira mensagem o Codex vai
   oferecer o `$iniciar-projeto`; se não oferecer, digite `$iniciar-projeto`.
3. Ele lê o código que houver e faz quatro rodadas curtas de perguntas (produto, técnico, fluxo,
   regras), mostra o resumo e só grava depois que você aprovar.
4. Depois: `$feature-spec <ideia ou número da issue>`.

## Requisitos

-   Codex (CLI, extensão de IDE ou app) com suporte a skills e subagentes.
-   Git. Recomendado: [ripgrep](https://github.com/BurntSushi/ripgrep) (`winget install
    BurntSushi.ripgrep.MSVC`), que os agentes usam para buscar no código.
-   GitHub CLI (`winget install GitHub.cli`, depois `gh auth login`), para issue e PR. Gitea
    (`tea`) e GitLab (`glab`) também servem: o `$iniciar-projeto` pergunta.
-   Para a `$webapp-testing`: Python, `pip install playwright` e `playwright install chromium`.

## Como está organizado

```
AGENTS.md                      regras do agente, comandos, fluxo (lido em toda sessão)
.agents/skills/<nome>/SKILL.md uma skill por passo do fluxo ($nome para chamar)
.codex/agents/*.toml           subagentes: tester, verificador, documentador
docs/specs/                    produto (para quem, por quê) e técnico (como)
docs/specs/product/features/   um arquivo por área do produto
docs/specs/technical/adr/      decisões de arquitetura
docs/regras/                   regras com fonte externa (cálculo, norma, contrato)
docs/sessions/<slug>/          requirements.md, plan.md, verificacao.md de cada feature
```

## Ideias que sustentam o fluxo

-   **Perguntar com opções, em rodadas.** De 3 a 5 dúvidas por vez, cada uma com a hipótese do
    agente e a opção recomendada primeiro. Nada é gravado sem aprovação do resumo.
-   **"Pronto quando" conferível.** Cada item da sprint é um comportamento observável com um jeito
    de conferir: `teste:`, `tela:` ou `comando:`.
-   **Quem implementa não testa nem verifica.** Testes saem do subagente `tester`, que não viu a
    implementação e prova cada teste sabotando o código. A sprint só fica `Feito` depois do
    subagente `verificador`.
-   **A documentação acompanha o código.** No PR, o subagente `documentador` aponta o que ficou
    desatualizado em `docs/specs` e propõe o módulo novo quando a área ainda não tem arquivo.
-   **Commit, push e PR só com pedido explícito.**

## Licença

MIT (ver [LICENSE](LICENSE)), exceto as pastas de terceiros abaixo.

### Licenças de terceiros

Quatro skills vêm de outros repositórios, com pequenas adaptações descritas no `SOURCE.md` de cada
pasta:

| Skill                                    | Origem                                                    | Licença      |
| ---------------------------------------- | --------------------------------------------------------- | ------------ |
| `differential-review`, `insecure-defaults` | [trailofbits/skills](https://github.com/trailofbits/skills) | CC BY-SA 4.0 |
| `frontend-design`, `webapp-testing`      | [anthropics/skills](https://github.com/anthropics/skills) | Apache 2.0   |

A `arquitetura-assincrona` é texto próprio. Ela se baseia em *Fundamentals of Software
Architecture* (Richards & Ford, O'Reilly, 2020) e cita o livro só como referência.

## Windows

Tudo foi escrito para rodar no PowerShell sem WSL: não há hooks em bash, e as skills pedem
comandos que funcionam igual nos dois terminais. O `.gitattributes` mantém os `.md` com quebra de
linha LF, para o diff não acusar o arquivo inteiro como alterado entre Windows e Linux.
