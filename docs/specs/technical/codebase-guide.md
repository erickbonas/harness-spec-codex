# Mapa do código

> Onde cada coisa mora. O `$feature-plan` e o `$feature-work` leem este arquivo antes de tocar no
> código; o `documentador` propõe atualização quando uma pasta nova aparece.

## Estrutura

```
<árvore anotada>
```

## <Parte do sistema (ex.: API, front, worker)>

-   **Onde:** `<pasta>`
-   **Padrão de referência:** `<arquivo que mostra o jeito certo de fazer>`
-   **Como um recurso novo é montado:** <arquivos que nascem juntos: rota, serviço, schema, teste>

## Fluxo de dados

<Um cenário típico do começo ao fim: requisição → validação → regra → banco → resposta.>

## Onde olhar primeiro quando...

| Quando...                     | Olhe          |
| ----------------------------- | ------------- |
| <uma tela mostra valor errado> | `<pasta>`    |
