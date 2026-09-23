# Test Cases

## Utilidade

Define os casos de teste derivados dos critérios de aceitação de uma entrega. Serve como contrato entre QA e engenharia — o que será validado após a implementação. Os testes são da suíte Python do motor (pytest, em `tests/`).

## Layout de Documentação

```markdown
# Casos de Teste — <título da entrega>

Status: draft | approved | deprecated
Owner: quality
Last updated: YYYY-MM-DD

## Inputs
- <issue relacionada (#id)>
- <critérios de aceitação de referência>

## CT-001 — <título>

**Tipo:** unitário | integração
**Critério de aceitação:** <referência>

**Pré-condição:**
- ...

**Passos:**
1. ...

**Resultado esperado:**
- ...
```

## Caminho do Arquivo

`doc/quality/<slug-issue>/test-cases.md`
