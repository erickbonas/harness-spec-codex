---
name: feature-spec
description: Primeiro passo do fluxo spec-driven. Monta a spec de produto da feature (docs/sessions/<slug>/requirements.md, espelho da issue) conversando com o usuário em rodadas de perguntas com opções; sugere abrir ou atualizar a issue. Use quando o usuário quiser começar uma feature, detalhar uma ideia, refinar uma issue ou corrigir um bug cuja regra ainda não está clara. Argumento: número/URL da issue, ideia em texto livre ou slug.
---

# $feature-spec

Monta a spec de produto da feature em `docs/sessions/<slug>/requirements.md`, conversando com o
usuário. O `requirements.md` é o espelho da issue: o conteúdo é o mesmo, e a issue é onde o time
acompanha.

```
$feature-spec → $feature-plan → $feature-work → $feature-pr
```

## Descobrir a sessão

-   O argumento pode ser URL ou número de issue, texto livre ou o slug de uma sessão.
-   Issue: leia com a descrição inteira e os comentários, pela CLI do rastreador indicado no
    `AGENTS.md`. O conteúdo dela é o ponto de partida.
-   Sem argumento: o slug sai da branch atual (regra no `AGENTS.md`). Não existe a pasta, ou a
    branch é a base → pergunte o que vai ser feito e comece a coletar.
-   Já existe `requirements.md`? Então é para refinar: leia e itere sobre ele, sem reescrever do
    zero.

Ideia solta já é ponto de partida. Não é preciso ter a feature pensada nem issue aberta.

## Fluxo

1. Leia a issue ou o pedido, `docs/specs/product/index.md`, o módulo afetado em
   `docs/specs/product/features/`, a regra em `docs/regras/` (se a feature mexe em cálculo ou regra
   com fonte externa), os ADRs de `docs/specs/technical/adr/` que tocam o assunto e o código que a
   feature toca. Pergunta que o código responde não vai ao usuário.
2. Colete, conversando, o que falta para ficar claro **por que** a feature existe, **o que** muda
   para cada perfil de usuário e o que fica **fora**. Se for bug: onde acontece e como reproduzir.
   Faça em rodadas: escolha as 3 a 5 dúvidas mais importantes, mostre antes o seu entendimento em
   poucas linhas e pergunte com opções (regra "Perguntas com opções" do `AGENTS.md`). **Pare e
   espere as respostas** antes da próxima rodada. Cada pergunta leva a sua hipótese ("acho que é X
   porque vi Y; confere?"). Vale perguntar sobre permissão (quem pode ver ou editar), sobre dados
   que já existem e são afetados (migração, registros sem configuração), sobre legado que ainda faz
   parte do fluxo e sobre como saber que ficou pronto.
3. Se o pedido contradiz uma spec, um ADR ou regra do `AGENTS.md`, diga antes de seguir.
4. Sem dúvidas pendentes, apresente o resumo (por que, o que muda, fora de escopo, critérios de
   aceite) e pergunte com as opções "aprovar" e "ajustar". Itere até aprovar. **Só grave o
   `requirements.md` depois da aprovação.**
5. Se ainda estiver na branch base, pergunte e crie a branch (`git switch -c feat/<issue>-<slug>`,
   a partir da base atualizada). Com issue, ofereça comentar nela o nome da branch. Se o usuário
   não quiser branch agora, pergunte o slug e avise que os outros passos vão precisar dele como
   argumento.
6. Grave o `requirements.md`:

```markdown
# <Nome da feature>

> Issue: <link ou "sem issue"> · Branch: `<branch>`

## Resumo

<um ou dois parágrafos curtos, do jeito que se explica para alguém do time no café: qual é o problema hoje, por que importa, para quem, e o que muda no dia a dia depois. Sem nome de arquivo nem de tabela>

## O QUE

<o que muda para cada perfil afetado: o fluxo na tela ou na API, o que já existe e é afetado, e as regras de negócio>

### Fora de escopo

-   <item>: <por quê / onde vai ser tratado>

## Critérios de aceite

-   [ ] <comportamento conferível na tela, na API ou no dado: "o operador aprova o pedido e ele aparece na lista de faturamento", não "aprovação funciona">

## Riscos e suposições

-   <o que pode dar errado, o que foi assumido sem confirmação, restrição técnica ou de negócio>

## COMO

<plano técnico de alto nível: arquivos, rotas, tabelas/migrations, padrões de referência e armadilhas encontradas no código>
```

**Sem quebra de linha manual.** Cada parágrafo e cada item de lista vai numa linha só: o texto vai
igual para a issue, e o rastreador respeita a quebra.

Quem lê é o time e um agente com o seu contexto. Conciso, sem perder regra de negócio. Termos por
extenso. Pule seção que não se aplica.

Os **critérios de aceite** são o contrato da feature: o `$feature-plan` reparte eles entre os
"Pronto quando" das sprints. Cada um tem que dar para marcar como sim ou não. Cubra o caminho
feliz, os casos de borda que a conversa levantou (lista vazia, sem permissão, valor vazio, registro
cancelado) e o que **não** pode mudar para quem não é afetado.

O checkbox é o progresso da feature para quem acompanha pela issue: o `$feature-work` marca o
critério quando a sprint que o cobre fica `Feito`.

## Issue: sugerir, não criar

-   **Sem issue:** sugira criar, em uma linha ("Quer que eu abra a issue com este conteúdo?"). Não
    crie sem pedido.
-   **Com issue, e o entendimento mudou** (escopo, título, fora de escopo): sugira atualizá-la. Não
    edite sem pedido.
-   **O usuário abriu a issue e passou o link:** atualize o cabeçalho do `requirements.md`.

Quando o usuário pedir, crie ou edite pela CLI do rastreador (procure issue parecida antes de
criar; mostre o texto antes de publicar). Descrição: o `requirements.md` inteiro, menos o título e
a linha de cabeçalho. Depois de criar, atualize o link no cabeçalho.

## Próximo passo

> "Spec gravada em `<caminho>`. Quando quiser, rode `$feature-plan`."
