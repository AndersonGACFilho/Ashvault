# Onboarding Tutorial System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define tutorial diegético, sequência obrigatória e failsafes.

## High Concept

Tutorial em Ashvault deve ensinar fazendo. O jogador aprende interagindo com objetos, riscos e consequências reais, não lendo uma lista de comandos fora da ficção.

## Sequência Obrigatória

1. Movimento e câmera.
2. Pegar e guardar item.
3. Usar ferramenta.
4. Gerar ruído e observar consequência.
5. Combate básico.
6. Coleta de recurso.
7. Inventário e peso.
8. Extração e venda.

## Failsafes

- Repetição contextual de prompt.
- Objeto substituto se item crítico for perdido.
- Saída de emergência do tutorial.
- Reset local sem resetar perfil.

## MVP

- Tutorial no hub e primeiro andar.
- Prompts diegéticos.
- Validação de ações essenciais.

## Critérios de Aceite

- O jogador completa uma mini-run sem menu explicativo longo.
- Falhas de tutorial não bloqueiam progresso.
- Prompts somem quando competência é demonstrada.

## Tutorial Beats

| Beat | Ação ensinada | Prova de competência |
| --- | --- | --- |
| Wake | olhar/mover | deslocar até porta |
| Hands | pegar/guardar | item em slot correto |
| Tool | usar picareta/faca | interação concluída |
| Noise | fazer barulho | inimigo investiga |
| Fight | atacar/bloquear | ameaça vencida ou evitada |
| Weight | carregar recurso | penalidade comunicada |
| Exit | extrair | run liquidada |
| Sell | vender | dinheiro/loja atualizada |

## Prompt Policy

Prompts são contextuais, curtos e removidos após sucesso. Repetição só ocorre se o jogador falhar, ficar parado ou tentar ação incorreta repetidamente.

## Failsafe Data

```json
{
  "tutorial_step": "first_mining",
  "required_action": "mine_node",
  "timeout_seconds": 90,
  "fallback_item": "training_pickaxe",
  "can_skip_after_failures": 3
}
```

## Integração com Notes

O tutorial deve criar primeiras notes reais, não páginas falsas. O jogador aprende que conhecimento registrado tem valor sistêmico.

## Non-Goals

- Tutorial de texto longo.
- Arena separada sem consequência.
- Bloquear jogador experiente por muito tempo.

## Filosofia de Ensino

Cada lição deve ter uma consequência real em miniatura. O tutorial de ruído precisa fazer algo ouvir. O tutorial de peso precisa alterar movimento. O tutorial de venda precisa mudar moeda e estoque. Sem consequência, o jogador aprende input, mas não sistema.

## Sequência Detalhada

| Ordem | Sistema | Cena | Falha permitida |
| --- | --- | --- | --- |
| 1 | Input | hub/quarto | sim, prompt repete |
| 2 | Inventário | bancada | item reaparece |
| 3 | Mineração | veio seguro | ferramenta reserva |
| 4 | Ruído | corredor | inimigo não letal investiga |
| 5 | Combate/fuga | sala curta | saída alternativa |
| 6 | Extração | escada/portal | prompt contextual |
| 7 | Economia | loja | venda reversível no tutorial |

## Gating

O tutorial não deve bloquear sistemas após competência demonstrada. Jogador experiente pode acelerar se executar ação correta antes do prompt.

## Métricas

- Tempo até primeira extração.
- Número de falhas por beat.
- Prompts repetidos.
- Abandono durante tutorial.
- Primeiro item vendido.

## Conteúdo Diegético

Prompts podem vir de bilhetes, marcações, ferramentas destacadas, voz curta, placas ou objetos. O sistema deve evitar painel explicativo longo, principalmente em VR.

## Critérios de Saída

- 80% dos testers completam tutorial sem ajuda externa.
- Menos de 10% ficam presos no mesmo beat por mais de 3 minutos.
- Jogador consegue explicar o loop: entrar, coletar, sair, vender.
