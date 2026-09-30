# Entrega

## Utilidade

Unidade única de trabalho da esteira de board único: uma mudança no motor da esteira que anda da ideia à `main` em uma só fila. É a issue que entra no `backlog` do board `entrega` e é conduzida por planejamento, casos de teste, desenvolvimento, execução de testes, documentação e envio à main. Não tem pai nem filhas — cada entrega é independente.

## Layout de Issue

```markdown
# <título da entrega>

## Descrição
<a mudança pedida no motor da esteira — objetiva e direta>

## Escopo
<o que está incluso>

## Fora de escopo
<limites claros>

## Critérios de aceitação
- Dado <contexto>, quando <ação>, então <resultado>
- ...

## Referências (obrigatório)
- **Branch desta issue**: `(ainda não criada)` — não preencher na criação. A esteira cria a branch na coluna "Casos de Teste" e registra o nome real aqui. A partir daí, todo agente que atuar nesta issue DEVE trabalhar nesta branch.

<adicionar tags aqui>
```

> O bloco "Escopo", "Fora de escopo" e "Critérios de aceitação" pode entrar cru na
> abertura; a coluna `planejamento` os fecha e reescreve o corpo antes de seguir.

## Board

`entrega` — coluna `backlog`
