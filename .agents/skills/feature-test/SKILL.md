---
name: feature-test
description: Escreve testes para um alvo com o subagente tester, de contexto limpo — ele propõe a lista com o bug que cada teste pega, o usuário aprova, ele escreve e prova que cada teste falha sabotando o código. Use quando o usuário pedir testes para um arquivo, função, serviço ou fluxo de tela. Também é o fluxo de testes usado pelo $feature-work e pelo $feature-quick.
---

# $feature-test

Testes escritos por um subagente de contexto limpo, que não viu o código ser feito. Quem
implementa tende a testar o que escreveu e a confirmar a implementação; o subagente parte do
contrato e de onde os bugs costumam estar.

Monte o prompt com o alvo (caminho e símbolo) e, se houver sessão (`docs/sessions/<slug>/`, pela
branch), o caminho do `requirements.md` e do `plan.md` e os itens do "Pronto quando" a cobrir.
**Não** passe o raciocínio da implementação nem o que "deveria" ser testado.

Alvo que pede mais de um tipo de teste (uma regra nova e a tela dela): rode um por vez, a regra
primeiro.

## 1. Propor

Dispare o subagente `tester` (`.codex/agents/tester.toml`):

```
Fase: propor. Alvo: <...>. Sessão: <...>.
```

Mostre a proposta como veio, sem resumir. O usuário aprova, corta ou ajusta, e diz sim ou não a
cada item de "Proposto, fora dos testes".

## 2. Escrever

Depois da aprovação (que já cobre rodar os testes do alvo e as sabotagens):

**Conferência do código.** A prova do subagente sabota o código e desfaz. Para pegar uma sabotagem
que tenha ficado (agente interrompido, desfeito pela metade), tire uma impressão do código antes do
disparo e outra depois do relatório. Funciona igual em PowerShell e bash; troque `<pastas de teste>`
pelos pathspecs da seção "Testes" do `AGENTS.md` (ex.: `':!**/__tests__/**'`):

```
git diff -- . <pastas de teste> | git hash-object --stdin
git ls-files -o --exclude-standard -- . <pastas de teste> | git hash-object --stdin-paths
```

O primeiro resume as mudanças em arquivos que já estão no git; o segundo, um hash por arquivo novo
fora dos testes (comum numa sprint). Saída diferente entre antes e depois: mostre ao usuário o
`git diff` dos arquivos fora dos testes e não siga até ele decidir. Mudança sua entre as duas
impressões (item aprovado fora dos testes) entra antes da primeira, nunca no meio.

```
Fase: escrever. Alvo: <...>. Lista aprovada:
<os testes aprovados, como vieram, com os ajustes do usuário>
Fora dos testes, aprovado: <itens, ou 'nada'>.
```

Item aprovado fora dos testes que muda código (extrair função, `aria-label`, mock) é seu, não do
subagente: faça antes da fase escrever.

## 3. Depois

Mostre o relatório. Para cada sabotagem que não derrubou teste, pergunte se vale nova rodada de
proposta.

Bug encontrado (teste com a marca de bug do `AGENTS.md`): o subagente traz onde está e a correção
provável. Mostre um por linha, com o fluxo sugerido:

```
- <o bug, em uma frase> — <correção provável> → <fluxo sugerido>
```

-   **`$feature-quick`**: causa conhecida e regra clara.
-   **`$feature-spec`**: falta decidir a regra, ou a correção muda algo que o usuário final vai
    notar.
-   **Issue**: fora do que o usuário está mexendo agora.

Chamado de dentro do `$feature-work` ou do `$feature-quick`, o bug entra no plano que já está
rodando, como cada um desses diz.

Não commite sem pedido explícito: sugira a mensagem e espere.
