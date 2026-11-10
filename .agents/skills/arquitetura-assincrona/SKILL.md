---
name: arquitetura-assincrona
description: Checklist de arquitetura para trabalho assíncrono e distribuído — filas, workers, jobs, cron, eventos, retentativa, idempotência, integração com serviço externo, transação em vários passos. Dois modos, consultor (antes de projetar) e revisor (num diff). Use sempre que a feature ou o diff tocar fila, worker, job agendado, evento, chamada a API externa com retentativa ou fluxo que pode falhar no meio, mesmo que o usuário não fale em "arquitetura". O $feature-plan carrega esta skill nesses casos.
---

# Arquitetura assíncrona

Checklist para quando o trabalho sai do caminho requisição-resposta: fila, worker, job agendado,
evento, integração com serviço externo. Os princípios vêm, em grande parte, de *Fundamentals of
Software Architecture* (Richards & Ford, O'Reilly, 2020), caps. 9 (falácias da computação
distribuída), 14 (arquitetura orientada a eventos) e 17 (microsserviços: sagas, coreografia e
orquestração). A referência ao capítulo serve para quem quiser aprofundar; o conteúdo aqui é
resumo próprio.

Dois modos:

-   **Consultor**: antes de criar ou mudar um worker, uma fila ou uma integração, traga o princípio
    que se aplica e recomende o caminho.
-   **Revisor**: num diff ou arquivo, procure os cheiros abaixo e reporte com a ação.

Antes de tudo, leia `docs/specs/technical/` e os ADRs: o tipo de sistema (monólito, serviços com
banco compartilhado, microsserviços) e as ferramentas de fila já escolhidas mudam a recomendação.

## Duas formas de encadear passos

-   **Coreografia (broker):** cada passo publica o que fez e quem se interessa reage. Bom para
    fluxos independentes, do tipo "dispara e esquece" (notificação, consulta, sincronização).
-   **Orquestração (mediador):** um coordenador conhece os passos e a ordem. Bom quando um passo
    depende do outro e alguém precisa saber em que ponto o fluxo parou.

Para passos dependentes, comece pela opção mais leve:

1. **Máquina de estados na própria entidade.** O registro guarda o status
   (`PENDENTE → GRAVADO → COBRADO → CONFIRMADO`), cada job avança um passo e, na retentativa, pula
   o que já foi feito. Sem coordenador novo, fácil de auditar. Prefira quando o estado já vive no
   banco.
2. **Saga com compensação explícita**, por cima, só onde um passo precisa ser **desfeito**
   ativamente (estornar uma cobrança se o passo seguinte for rejeitado). Mapeie o desfazer de cada
   passo reversível.

Quando definir que um fluxo é coreografia e outro é orquestração, registre o porquê num ADR.

## Checklist

Cada item: o cheiro, o princípio e a ação. Severidade entre colchetes.

### 1. [CRÍTICO] Fila ou estado em memória do processo

-   **Cheiro:** lista em campo de classe ou variável de módulo drenada num laço (`shift`, `pop`).
    Se o processo reinicia, tudo o que estava na fila some.
-   **Princípio:** mensagem que não persiste se perde (cap. 14, prevenção de perda de dados).
-   **Ação:** fila persistente (Redis, banco, broker de mensagens), com prioridade e atraso da
    própria ferramenta.
-   **Dois agravantes que costumam vir junto:**
    -   Trava por flag sem `finally`: um `rodando = true` que protege o laço e só volta a `false` no
        fim. Uma exceção no meio deixa a flag presa e a fila para em silêncio.
    -   Não escala: com duas instâncias do worker, cada uma tem a sua fila, sem coordenação.

### 2. [ALTO] Retentativa manual num laço dentro do job

-   **Cheiro:** `while (tentativas < max) { ...; await esperar(2 ** tentativas) }`. Se o processo
    cai na terceira tentativa, ninguém retoma.
-   **Princípio:** a rede não é confiável, então precisa de retentativa (cap. 9), e ela tem que
    sobreviver a queda do processo (cap. 14).
-   **Ação:** retentativa no nível do job, com estado durável (número de tentativas e backoff
    exponencial da ferramenta de fila). Some com o laço.
-   **Jitter:** backoff exponencial sem aleatoriedade faz todos os jobs que falharam juntos voltarem
    juntos quando o serviço externo volta. Some um atraso aleatório.

### 3. [MÉDIO] Operação que não pode acontecer duas vezes, sem chave de idempotência

-   **Cheiro:** enfileirar uma emissão, cobrança ou envio sem um id determinístico. A mesma intenção
    vira dois jobs: duas cobranças, dois documentos.
-   **Princípio:** entrega "pelo menos uma vez" exige consumidor idempotente (cap. 9).
-   **Ação:** id previsível por operação (`cobranca:<cliente>:<pedido>`), usado para deduplicar na
    fila e conferido no consumidor antes de agir.

### 4. [MÉDIO] Falha que ninguém vê

-   **Cheiro:** job que esgota as tentativas fica como "falhou" e ninguém olha.
-   **Princípio:** erro em fluxo assíncrono precisa de um caminho até uma pessoa que corrija e
    reenvie (cap. 14, tratamento de erros).
-   **Ação:** fila de mensagens mortas (DLQ) e uma lista ou relatório de falhas com "reprocessar".
    Proporcional ao risco: para um job simples, alerta e log bastam.

### 5. [MÉDIO] Idempotência caso a caso

-   **Cheiro:** só alguns fluxos se protegem contra repetição, onde alguém lembrou.
-   **Ação:** padronize a chave de idempotência para toda operação crítica do projeto e registre o
    padrão no `AGENTS.md` ou num ADR.

### 6. [MÉDIO] Vários passos externos num job só, sem checkpoint

-   **Cheiro:** `grava → cobra → envia e-mail → confirma no serviço externo` num passo só. Na
    retentativa, refaz tudo (cobrança duplicada) ou deixa passos órfãos (cobrou, mas o documento foi
    rejeitado).
-   **Princípio:** transação distribuída pede checkpoint e, onde couber, compensação (cap. 17).
-   **Ação:** máquina de estados na entidade, um job por passo. Saga só onde um passo precisa ser
    desfeito.

### 7. [BAIXO] Decisão não registrada

-   **Ação:** coreografia ou orquestração, banco compartilhado ou isolado, a ferramenta de fila: um
    ADR curto em `docs/specs/technical/adr/` evita rediscutir a cada feature.

### 8. [BAIXO] Latência medida só pela média

-   **Cheiro:** chamada a serviço externo sem medição dos piores casos.
-   **Princípio:** latência não é zero, e a cauda longa é o que derruba (cap. 9).
-   **Ação:** medir p95 e p99 das chamadas externas e ter timeout explícito.

## O que não importar de microsserviços

Banco isolado por serviço, service mesh e sidecar custam caro e raramente cabem num monólito ou
num sistema de poucos serviços. Não recomende "migrar para microsserviços" porque o assunto é
interessante. De lá, se aproveitam sagas, coreografia e orquestração.

## Como agir

**Comece pela recomendação**, sem preâmbulo. Sustente cada ponto com o princípio, em uma linha.

**Proporcionalidade.** Padrão pesado (saga formal, DLQ com tela própria, máquina de estados
elaborada) só se paga onde o risco é real: duplicar cobrança, emitir documento duas vezes, perder
um pedido. Para um worker simples e de baixo risco, a retentativa da própria ferramenta de fila
resolve quase tudo. Diga quando algo é exagero para o caso.

**Consultor:**

1. Classifique o fluxo: independente (coreografia) ou com passos dependentes (máquina de estados;
   saga só se algum passo precisar ser desfeito).
2. Confira os itens 1 a 5 preventivamente.
3. No `$feature-plan`, o que sair daqui vira item do "Pronto quando" (ex.: "o mesmo pedido
   enfileirado duas vezes gera uma cobrança só → teste: ...") ou ponto de atenção nas "Notas para
   implementar".

**Revisor:**

1. Procure no diff os cheiros dos itens 1 a 8.
2. Para cada achado: `arquivo:linha` → o princípio → a ação.
3. Antes de dizer que um trecho está morto ou que falta algo, confira quem usa.
4. Ordene por severidade.
