---
name: iniciar-projeto
description: Configura o harness spec-driven num projeto novo ou existente. Lê o código que já houver, entrevista o usuário em rodadas curtas e preenche o AGENTS.md e a pasta docs/specs (produto, técnico, ADRs, módulos). Use quando o AGENTS.md tiver a marca HARNESS-NAO-INICIALIZADO, ou quando o usuário pedir para configurar, reconfigurar ou atualizar o contexto do projeto.
---

# Iniciar projeto

Monta o contexto que as skills `$feature-*` e os subagentes usam: o `AGENTS.md` da raiz e a pasta
`docs/specs/`. O resultado é o que um desenvolvedor novo (gente ou agente) precisa para trabalhar
no projeto sem perguntar de novo o que já foi respondido.

## Princípios

-   **Pesquise antes de perguntar.** Tudo o que o repositório responde (stack, comandos, pastas,
    branch base, remoto do git) você descobre sozinho e só **confirma** com o usuário.
-   **Rodadas curtas.** De 3 a 5 perguntas por rodada, pela ferramenta `request_user_input` quando
    existir (até 3 por chamada; faça duas chamadas seguidas se precisar), senão numeradas com
    opções `a)`, `b)`, `c)`. Mostre antes o que você já sabe, em poucas linhas. **Pare e espere** as
    respostas antes da próxima rodada.
-   **Toda pergunta leva a sua hipótese** ("vi `vitest` no package.json, então os testes são
    Vitest; confere?"), e a opção recomendada vem primeiro.
-   **Não invente.** O que o usuário não souber ou não quiser responder fica marcado como
    `_a confirmar_` no documento. Nada de métrica, persona, limite ou decisão inventada.
-   **Nada é gravado antes da aprovação do resumo** (passo 4).

## 1. Ler o que existe

Sem perguntar nada ainda:

-   `git remote -v` e `git branch -a`: endereço do repositório (GitHub, Gitea, GitLab), branch base
    (`develop` ou `main`), padrão de nome de branch já usado.
-   Arquivos de projeto na raiz e um nível abaixo: `package.json`, `pnpm-workspace.yaml`,
    `pyproject.toml`, `requirements.txt`, `*.csproj`, `*.sln`, `go.mod`, `Cargo.toml`, `pom.xml`,
    `build.gradle`, `composer.json`, `Gemfile`, `docker-compose.yml`/`compose.yml`, `Makefile`,
    `justfile`. Deles saem a stack e os comandos (scripts de test, lint, build).
-   Configuração de teste e lint: `vitest.config.*`, `jest.config.*`, `playwright.config.*`,
    `pytest.ini`, `.eslintrc*`, `eslint.config.*`, `ruff.toml`, `.prettierrc*`.
-   `README.md`, `CONTRIBUTING.md`, `CLAUDE.md`, `.cursorrules`, `.github/copilot-instructions.md`,
    `docs/`: regras e contexto que já foram escritos. Regra que já existe vira item do `AGENTS.md`
    (citando de onde veio), não pergunta.
-   A árvore de pastas (2 níveis, sem `node_modules`, `.git`, `dist`, `bin`, `obj`, `.venv`).
-   `git log --oneline -30`: idioma e formato dos commits.

Repositório vazio (só o harness): é projeto novo. A entrevista vai mais para produto e decisões, e
os comandos ficam `_a confirmar_` até o código existir.

Resuma ao usuário o que encontrou em até 8 linhas antes da primeira rodada.

## 2. Entrevistar

Quatro rodadas, nesta ordem. Pule a pergunta que a leitura já respondeu com segurança (e diga que
pulou); confirme a que respondeu com dúvida.

**Rodada 1 — Produto**

-   O que o sistema faz, em uma frase, e qual problema resolve.
-   Quem usa (perfis: cliente final, operador interno, administrador...) e o que cada um faz nele.
-   As áreas ou módulos principais do produto, do jeito que o usuário fala delas (ex.: "cadastro de
    clientes", "faturamento", "relatórios"). Cada uma vira um arquivo em
    `docs/specs/product/features/`.
-   Existe regra de domínio que precisa ser exata e tem fonte externa (lei, norma, cálculo de
    imposto, contrato, tabela de preço)? Se sim, quais tópicos: cada um vira um arquivo em
    `docs/regras/`.

**Rodada 2 — Técnico**

-   Confirmar stack, estrutura de pastas e onde cada coisa mora (o que você inferiu).
-   Como sobe em desenvolvimento (Docker? local?) e o sistema operacional do time (Windows nativo,
    WSL, Mac, Linux): decide como os comandos são escritos no `AGENTS.md`.
-   Comandos de teste (um arquivo e um teste pelo nome), lint, formatação e typecheck.
-   Banco e migrations (ferramenta, onde ficam, regra de nome, como gerar, aplicar e desfazer), se
    houver.
-   O projeto tem tela web? Sem tela, as skills `frontend-design` e `webapp-testing` podem sair do
    repositório (pergunte antes de apagar).

**Rodada 3 — Fluxo**

-   Branch base (`develop` ou `main`) e se o padrão `feat/`/`fix/`/`chore/` serve.
-   Rastreador de issues e CLI. Recomende GitHub com `gh` (o padrão deste harness) salvo se o
    `git remote` mostrar outro (Gitea com `tea`, GitLab com `glab`), ou nenhum. Com GitHub,
    confira se o `gh` está instalado e autenticado (`gh auth status`); se não estiver, diga como
    (`winget install GitHub.cli` e `gh auth login`, que a pessoa roda), sem pedir token.
-   Idioma e formato de commit (sugira o que o `git log` mostrou).
-   `docs/sessions/` vai para o git (o time vê a spec e o plano no PR) ou fica local (só rascunho
    de trabalho, `.gitignore`)? Recomende versionar se o time revisa plano junto.

**Rodada 4 — Regras e decisões**

-   Decisões de arquitetura já tomadas que um agente precisa respeitar (ex.: "ORM X em código
    novo", "não usar biblioteca Y", "legado em Z não recebe código novo"). Cada uma vira um ADR
    curto.
-   Convenções que o lint não pega e que um agente erraria (idioma dos comentários, como tratar
    data e dinheiro, padrão de validação, padrão de tela).
-   Áreas sensíveis: dado pessoal, autenticação, permissão, pagamento, certificado. O `$feature-pr`
    pede revisão de segurança quando o diff toca nelas.
-   Serviços externos que o teste nunca chama de verdade (devem ir mockados).

## 3. Rodada extra, se precisar

Resposta que abriu dúvida nova ("tem um legado em PHP" → o legado recebe código novo?) vira mais
uma rodada curta. Pare quando o que falta não muda o que vai ser escrito.

## 4. Resumo e aprovação

Mostre o resumo do que vai ser gravado, por arquivo, em poucas linhas cada:

-   `AGENTS.md`: projeto, comandos, testes, fluxo, convenções.
-   Skills: a `migration` que vai ser criada (se houver banco) e as que saem (se não houver tela).
-   `docs/specs/product/index.md` e um arquivo por módulo em `features/`.
-   `docs/specs/technical/index.md`, `codebase-guide.md`, `development-workflow.md` e os ADRs.
-   `docs/regras/<tópico>.md`, se houver.
-   O que ficou `_a confirmar_`.

Pergunte "aprovar" ou "ajustar". Itere até aprovar.

## 5. Gravar

1. **`AGENTS.md`**: preencha todas as seções marcadas `<!-- preenchido pelo $iniciar-projeto -->`,
   apague esses comentários e **apague a primeira linha `<!-- HARNESS-NAO-INICIALIZADO -->` e o
   bloco "Harness ainda não inicializado"**. Mantenha as seções "Fluxo de trabalho" e "Regras do
   agente", ajustando só branch base, rastreador e formato de commit. O arquivo tem que caber em
   ~200 linhas: detalhe vai para `docs/specs/` com link.
2. **`docs/specs/product/index.md`**: perfil do produto, perfis de usuário, catálogo de módulos
   com link e uma linha cada, princípios que o usuário citou.
3. **`docs/specs/product/features/<area>.md`**: um por módulo, a partir de `_modelo.md`, nome em
   português com hífen, pela área do produto (`faturamento.md`, `cadastro-clientes.md`), não pela
   feature. Projeto existente: descreva o que o módulo faz hoje, lendo o código; o que não deu
   para confirmar fica `_a confirmar_`. Projeto novo: só o "O que é" e "Para quem importa"; o resto
   cresce com as features. Não apague o `_modelo.md`.
4. **`docs/specs/technical/`**: `index.md` (perfil e mapa), `codebase-guide.md` (árvore anotada e
   "onde olhar primeiro quando..."), `development-workflow.md` (ambiente, branches, PR, deploy) e
   `api-specification.md`, `business-logic.md`, `troubleshooting.md` só com o que se sabe (o
   resto fica `_a confirmar_`; não preencha por preencher).
5. **ADRs**: um por decisão da rodada 4, `docs/specs/technical/adr/NNNN-titulo-curto.md` a partir
   de `_modelo.md`, numerados de `0001`, status `Aceito`, e a linha no índice do `README.md` da
   pasta.
6. **`docs/regras/<tópico>.md`**: um por tópico da rodada 1, no modelo de seis seções do
   `docs/regras/README.md`. A **base legal ou fonte** é obrigatória: se o usuário não souber a
   fonte, a seção fica `_a confirmar_`, nunca preenchida de memória.
7. **`docs/specs/index.md`**: o mapa geral, com o catálogo de módulos e os ADRs.
8. **Skill `migration`**, se o projeto tem banco com migrations: crie
   `.agents/skills/migration/SKILL.md` com `name: migration` e uma `description` que dispare em
   "criar, alterar ou revisar migration, tabela, coluna ou índice". O corpo traz só o que a
   rodada 2 e o código mostraram: a ferramenta, onde ficam os arquivos, como gerar, aplicar e
   desfazer (comandos do `AGENTS.md`), a regra de nome, e o checklist que vale para qualquer
   banco: modelo/schema atualizado junto com a migration, `NOT NULL` sem default em tabela que já
   tem dado, `down` que desfaz o `up`, migration já aplicada nunca é editada (cria-se outra),
   dado migrado conferível antes do código que depende dele. Acrescente a skill na lista do
   `AGENTS.md`. Sem banco, não crie.
9. **`docs/sessions/`**: se ficar local, confirme que `.gitignore` tem `docs/sessions/*` e
   `!docs/sessions/README.md`; se for versionado, tire essas duas linhas.

Grave com a ferramenta de patch. Não crie arquivo vazio nem seção só com título.

## 6. Fechar

Diga em poucas linhas o que foi gravado, o que ficou `_a confirmar_` e o próximo passo:

> "Projeto configurado. Para a primeira feature, rode `$feature-spec <ideia ou issue>`."

Não commite: sugira a mensagem (`chore: configura o harness spec-driven`) e espere o pedido.

## Reconfigurar

Com o `AGENTS.md` já preenchido, esta skill atualiza: leia o que existe, pergunte só o que o
usuário quer mudar (ou o que está `_a confirmar_`) e edite pontualmente. Não reescreva arquivo
que o time já ajustou à mão.
