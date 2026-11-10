# Sessões de feature

Uma pasta por feature em andamento, com o nome da branch sem o prefixo:
`feat/123-exporta-csv` → `docs/sessions/123-exporta-csv/`.

```
docs/sessions/<slug>/
├── requirements.md   $feature-spec — por quê, o que muda, critérios de aceite (espelho da issue)
├── plan.md           $feature-plan — sprints, "Pronto quando", status Pendente/Feito, decisões
└── verificacao.md    $feature-work — resultado do verificador na última sprint
```

Ajuste rápido (`$feature-quick`) não cria sessão: o plano fica no chat.

O que vale guardar depois que a feature entra (regra nova, decisão de arquitetura) vai para
`docs/specs/` e `docs/regras/` pelo `$feature-pr`. A sessão é rascunho de trabalho.
