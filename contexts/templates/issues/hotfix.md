# Hotfix

## Utilidade

Correção pontual e cirúrgica de um defeito em produção, aberta a partir da decisão de tratamento de um incidente. O objetivo é **estancar o sangramento** de um bug específico — não reescrever arquitetura nem resolver dívida estrutural (isso é papel de épico). Nasce do incidente, mas é independente: bloqueia o incidente até concluir, por dependência (`/blocks`), não por parentesco.

## Layout de Issue

```markdown
# <título do hotfix>

## Defeito
<o bug específico e delimitado que este hotfix corrige>

## Sintoma em produção
<como o problema se manifesta para quem usa/opera>

## Correção pretendida
<a menor mudança que estanca o sangramento — objetiva e cirúrgica>

## Fora de escopo
<o que NÃO será feito aqui: refatoração, generalização, mudança de arquitetura>

## Validação
<como confirmar que o defeito foi corrigido sem regressão>

## Severidade
critical | high | medium | low

## Referências (obrigatório)
- **Branch desta issue**: `<branch>` — branch de correção, criada a partir da `main`. Todo agente que atuar nesta issue DEVE trabalhar nesta branch; não crie nem use outra.
- **Branch de origem**: `main` — o hotfix parte da main e o MR é aberto de volta para a main.
- **Incidente de origem**: #<id> — <nome do incidente que originou este hotfix>

## Comandos
/blocks #<id do incidente de origem>

<adicionar tags aqui>
```

## Board

`hotfix` — coluna `backlog`

## Notas de uso

- **Um bug por hotfix.** Se a correção exige mais de um defeito ou toca arquitetura, ela não é hotfix — é candidata a melhoria para o postmortem tratar como épico.
- **`/blocks #<incidente>`** é obrigatório: a esteira sincroniza o par recíproco e pausa o incidente até este hotfix concluir. Ao encerrar (coluna `concluido`), a remoção do bloqueio libera o incidente automaticamente.
- **Teto por incidente:** preferencialmente 1 hotfix, no máximo 3.
