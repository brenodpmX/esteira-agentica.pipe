# Incidente Produtivo

## Utilidade

Documentação de acompanhamento de problemas e incidentes reportados em produção. O mesmo arquivo cobre o ciclo inteiro do caso: da resposta ao incidente até o postmortem (a análise blameless que fecha o aprendizado). O postmortem é o capítulo final do mesmo caso, no mesmo diretório.

## Layout de Documentação

```markdown
# Incidente — <nome do incidente>

Status: registro | triagem | análise | tratamento | monitoramento | postmortem | encerrado | cancelado
Owner: tech-lead
Last updated: YYYY-MM-DD

## Registro
> Informações preliminares do incidente/problema.

### Descrição
- Data: <data e hora da ocorrência>
- Reportado por: <quem reportou>

<descrição do que foi reportado sobre o incidente>

### Evidências
<evidências; caso haja arquivos, adicione-os no mesmo diretório e vincule aqui>

### Impacto
<qual o impacto do incidente para o projeto>

## Triagem

### Classificação
<bug | configuração | operação | uso incorreto>

### Severidade
<severidade segundo a tabela de classificação>

### Prioridade
<prioridade>

### Workaround
<alternativas de contorno enquanto a correção não chega>

## Análise técnica
<causa provável, cadeia causa→sintoma, risco de manter e de intervir, custo/esforço da correção. Atualizada na reavaliação quando um hotfix falha, preservando o histórico do que não funcionou.>

## Ação proposta
<decisão de tratamento: correção imediata ou cancelamento, com a justificativa.>

### Hotfixes abertos
<lista dos hotfixes criados para estancar o sangramento (1 a 3): id, título e status. Cada hotfix anota /blocks para este incidente.>

# Postmortem
> Preenchido após o incidente ser mitigado e validado pelo humano. Cerimônia blameless: foco em sistema e processo, nunca em pessoas.

## Resumo
<síntese do ocorrido em poucas linhas>

## Detecção
<como o problema foi percebido (alerta, cliente, monitor) e o tempo até detectar>

## Linha do tempo
<eventos com timestamp: detecção → escalonamento → mitigação → resolução>

## Métricas
- TTD — tempo até detectar: <>
- TTM — tempo até mitigar: <>
- MTTR — tempo até resolver: <>

## Causa-raiz
<5 Porquês partindo do sintoma até as condições que o permitiram>

### Fatores contribuintes
<fatores que originaram, amplificaram ou atrasaram a resposta — normalmente mais de um; não force causa única>

## Balanço
### O que foi bem
<>
### O que foi mal
<>
### Onde tivemos sorte
<>

## Ações corretivas
<ações objetivas e acionáveis, cada uma endereçando um fator contribuinte>
- Prevenir recorrência: <>
- Melhorar detecção: <>
- Melhorar resposta: <>

## Épicos de melhoria
<épicos criados a partir das ações aprovadas: id, título e a ação que cada um endereça. Cada épico anota /blocks para este postmortem.>

## Encerramento
<resultado das melhorias entregues e confirmação de que os fatores contribuintes foram endereçados — ou por que conscientemente não foram>
```

## Classificação de severidade

| Severidade   | Descrição                                    | Exemplo                         |
| ------------ | -------------------------------------------- | ------------------------------- |
| P1 - Crítica | Sistema parado ou perda financeira relevante | Pagamentos indisponíveis        |
| P2 - Alta    | Funcionalidade importante comprometida       | Emissão de boletos falhando     |
| P3 - Média   | Problema com workaround                      | Relatório gera dados incorretos |
| P4 - Baixa   | Problema cosmético                           | Texto errado na tela            |

## Caminho do Arquivo

`doc/incidente/<slug-incidente>/ticket.md`
