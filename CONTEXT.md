# Contexto — Repositório de Configuração da Esteira Agêntica

Este documento descreve **o que é este diretório** e **como a esteira que o
consome funciona**, para servir de introdução em conversas futuras. Não trata de
nenhum problema específico a corrigir — é apenas o retrato conceitual e
operacional do projeto como está.

## O que é este diretório

`esteira-agentica.pipe/` é o **repositório de configuração** de uma esteira
agêntica **em produção**. Ele não contém o código da esteira — contém apenas os
dois artefatos que a esteira lê para operar:

| Artefato | Papel |
|----------|-------|
| `pipe.yml` | Define agentes, boards, colunas, fluxos de git e o comportamento de cada etapa. É o "programa" que a esteira executa. |
| `contexts/` | Personas dos agentes e templates de documentos/issues usados nos prompts. |

Estrutura:

```
esteira-agentica.pipe/
├── pipe.yml                     # configuração principal (agentes + boards)
├── contexts/
│   ├── kiro-cli/                # persona de cada agente (contexto de execução)
│   │   ├── product.md, requirements.md, architecture.md, tech-lead.md,
│   │   │   reviewer.md, engineering-sr|pl|jr.md, quality.md, devops.md,
│   │   │   optimizer.md, generic.md
│   └── templates/
│       ├── docs/                # modelos de documentos (incidente, requisitos,
│       │                        #   arquitetura, casos/resultados de teste, etc.)
│       └── issues/              # modelos de issue (epic, user-story, task,
│                                #   bug, debito)
└── CONTEXT.md                   # este arquivo
```

## O que é a esteira (conceito)

A **Esteira Agêntica** é um orquestrador que automatiza um fluxo de
desenvolvimento de software inteiro usando agentes de IA. Cada **board** (no
GitHub Projects V2) representa um tipo de trabalho (épico, story, task, bug,
débito, incidente). Cada **coluna** de um board é uma **etapa** com:

- um **agente** responsável (persona + modelo, definidos em `pipe.yml` →
  `agents`), que recebe um `target-prompt` descrevendo o objetivo da etapa;
- **eventos de git** (`gitevents`: cria branch, usa branch existente, abre PR…);
- **regras de avanço** (`change`), que definem para qual coluna a issue vai
  quando o agente conclui (`advance`) ou quando decide um desvio nomeado
  (ex.: `correcao`, `falha`, `cancelar`).

Uma issue "anda" pelas colunas: o agente da coluna atua sobre ela (lê o corpo,
comenta, escreve documentos, mexe no código do repo alvo, move a issue) até a
issue chegar a uma coluna terminal (`encerrado`, arquivada).

Os agentes se comunicam com a issue por arquivos locais materializados pela
esteira (`<id>-<slug>-body.md`, `-history.md`, `-addcomment.md`) e por
**comandos `@---`** no corpo (parent, children, blocked_by, blocks, labels,
need_human, close, archive). Colunas humanas (com `on_in: [need_human]`) pausam a
issue esperando intervenção manual.

### Conceitos-chave do `pipe.yml`

- **`agents`**: por plataforma (`kiro-cli`), cada agente tem `name` e `model`.
  O contexto/persona vem de `contexts/kiro-cli/<agente>.md`.
- **`git.flow`**: define as branches (base `main` e flows como `hotfix`, `docs`,
  `epic`, `story`, `feature`) com `prefix`, `create` (origem) e `merge` (destino).
  O flow `docs` (main→main) versiona a documentação de incidente e postmortem.
- **`boards`**: `platform: github`; cada board tem `name`, `priority` (menor =
  mais prioritário), `todo` (coluna de entrada, cujas issues são auto-avançadas),
  `flow` e `columns`.
- **coluna**: `name`, `agent`, `agent-hub`/`override-agent` (roteia para outro
  agente conforme label `agent-hub-<valor>`), `gitevents`, `target-prompt`,
  `on_in`/`on_out` (eventos ao entrar/sair: labels, `need_human`, `archive`,
  `close`…) e `change` (mapa de destinos de avanço).

### Boards atuais (visão geral)

| Board | priority | flow | Papel |
|-------|----------|------|-------|
| `hotfix` | 0 | hotfix | Correção pontual/cirúrgica em produção (estancar o sangramento) |
| `incidente` | 1 | docs | Coordenação do incidente: registro → triagem → análise → decisão → monitoramento |
| `epic` | 1 | epic | Épicos: negócio → requisitos → arquitetura → stories → integração |
| `story` | 2 | story | User stories: planejamento técnico → tasks → integração |
| `debito` | 3 | (dinâmico) | Lacunas de definição (negocial/arquitetural/humana) |
| `bug` | 4 | (dinâmico) | Defeitos achados em teste/review |
| `task` | 5 | feature | Unidade de desenvolvimento: planning-poker → teste → dev → review |
| `postmortem` | 10 | docs | Análise blameless pós-incidente e ações preventivas |

> `incidente` e `epic` compartilham priority 1 (empate na ordenação, sem quebra).

### Ciclo de incidente (incidente → hotfix → postmortem)

O tratamento de incidente é dividido em três boards com responsabilidades e
cadências distintas:

- **`incidente`** enxuto (não escreve código): documenta e decide. A decisão de
  tratamento abre de 1 a 3 **hotfixes** independentes e fica pausado
  (`blocked_by`) até eles concluírem; um `need_human` de monitoramento valida se
  a correção resolveu. Ao encerrar, cria a issue de **postmortem**.
- **`hotfix`** (priority 0, main→main): quem de fato estanca o sangramento —
  correção curta e cirúrgica, com reteste, homologação humana e code-review antes
  do merge na main. Nasce do incidente, mas é independente; bloqueia-o via
  `/blocks` (não por parentesco).
- **`postmortem`** (reflexivo, priority baixa): cerimônia blameless (linha do
  tempo, métricas TTD/TTM/MTTR, causa-raiz/5 Porquês, o que foi bem/mal/sorte,
  ações corretivas). As ações que exigem mudança estrutural viram **épicos**
  independentes, que bloqueiam o postmortem até concluírem.

Templates de apoio: `contexts/templates/issues/hotfix.md` e as seções de
postmortem em `contexts/templates/docs/incidente.md` (mesmo `doc/incidente/<slug>/`
do incidente).

## Onde está a esteira (o código que consome esta configuração)

O **código** da esteira é um projeto separado, versionado no repositório apontado
por `pipe.yml` → `git.repo.main`:

```
git@github.com:brenodpmX/esteira-agentica.git
```

No ambiente local, esse código-fonte fica em:

```
/home/breno/code/esteira-agentica/
```

Esse diretório tem sua própria documentação técnica detalhada em
`/home/breno/code/esteira-agentica/CONTEXT.md` (arquitetura hexagonal, fluxo de
sincronização, filas, snapshots, adapters de board e de agente, incidentes
históricos e changelog de versões).

### Arquitetura da esteira (resumo)

Arquitetura hexagonal em Python:

```
src/
├── core/        # domínio: config, board, agent, sync, change_queue,
│                #   snapshot, session, commands, log
├── adapters/    # github_board.py (GitHub Projects V2), kiro_cli_agent.py
└── __main__.py  # orquestração (loop principal)
```

Fluxo do processo: `check_config → startup → board_full_sync → loop`. No loop:
descoberta local (up) em todos os boards + sync remoto (down) do board da vez →
processa a fila de mudanças → seleciona a tarefa elegível (`keep_task`, boards
por prioridade) → executa o agente (`kiro-cli`) → dorme se nada aconteceu.

### Como a esteira roda

Distribuída via Docker (build canônico com `Dockerfile` + `docker-compose.yml`
na raiz do repo de código). O container roda como usuário não-root `pipe`
(uid 1000), executa `python -m src`, e recebe **esta configuração** por bind
mount:

- `pipe.yml` e `contexts/` entram como bind **read-only**;
- estado/logs/repositórios clonados ficam em volumes (`.pipe`, `repo`, `logs`,
  `~/.kiro`).

Credenciais via `.env`: `GH_TOKEN`, `KIRO_API_KEY` e a chave SSH (Docker secret).
O agente é executado via `kiro-cli chat`, com a persona da coluna concatenada ao
início do input.

## Relação entre os dois repositórios

```
/home/breno/code/esteira-agentica/        → CÓDIGO da esteira (o executor)
/home/breno/code/esteira-agentica.pipe/   → CONFIGURAÇÃO consumida (este dir)
```

Alterar arquivos **aqui** muda o comportamento da esteira em produção sem tocar
no código: novos boards, novas etapas, novos prompts e personas. Alterar o
comportamento do próprio motor (sincronização, seleção de tarefa, adapters)
exige mexer no repositório de código.
