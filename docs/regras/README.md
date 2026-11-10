# Regras de domínio

Regras que precisam ser exatas e têm fonte externa: cálculo de imposto, norma, contrato, tabela de
preço, legislação. Aqui fica a **regra** e **onde ela vive no código**; não é documentação de API.

Pasta opcional: projeto sem regra desse tipo pode apagar.

## Índice

| Tópico | Arquivo |
| ------ | ------- |

## Modelo de um tópico

Todo tópico segue as mesmas seis seções:

1. **Visão geral** — o que é, em 2-3 linhas.
2. **Fonte** — lei, artigo, norma, contrato ou documento oficial, com a data em que foi conferido.
   Sem fonte, a seção fica `_a confirmar_`: regra não se escreve de memória.
3. **Entradas** — o que alimenta o cálculo ou a decisão.
4. **Passo a passo** — fórmulas e ordem, com um exemplo cuja conta dá para fazer de cabeça.
5. **Onde está no código** — arquivo e símbolo (não número de linha).
6. **Casos de borda** — situações especiais e como o sistema trata.

## Convenções

-   Separe o que a fonte diz do que é interpretação do time.
-   Sem referência a sprint ou fase: o quando fica no git e na issue.
