# Test Results

## Utilidade

Registra o resultado da execução dos casos de teste de uma entrega (suíte pytest). Serve como evidência de qualidade e insumo para a decisão de avançar, devolver ao desenvolvimento (falha de código) ou revisar os casos de teste.

## Layout de Documentação

```markdown
# Resultados de Teste — <título da entrega>

Status: draft | approved
Owner: quality
Last updated: YYYY-MM-DD

## Inputs
- <test-cases utilizado>
- <issue relacionada (#id)>

## CT-001 — <título>

**Resultado:** passed | failed | blocked

**Observações:**
- ...

## Resumo

- Total: X
- Passou: X
- Falhou: X
- Bloqueado: X

## Veredito
aprovado | reprovado | parcialmente reprovado
```

## Caminho do Arquivo

`doc/quality/<slug-issue>/test-results.md`
