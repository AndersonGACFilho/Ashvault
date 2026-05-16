# Failure States Risk Metrics

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Consolida falhas, riscos e métricas de sucesso.

## High Concept

Falhas devem virar feedback e decisão, não frustração opaca. Riscos de produção precisam de mitigação explícita e métricas simples para saber se o projeto está saudável.

## Falhas do Player

- Morte.
- Overload.
- Perda de corpse.
- Falha de extração.
- Falência temporária da loja.
- Mutação ou toxicidade.

## Falhas de Sistema

- Save inválido.
- Run interrompida.
- Mercado inflacionado.
- Dungeon com softlock.
- Boss ilegível.
- Cross-input desequilibrado.

## Riscos de Produção

- Escopo sistêmico excessivo.
- VR comfort insuficiente.
- Multiplayer cedo demais.
- Economia difícil de balancear.
- Conteúdo procedural repetitivo.

## Métricas

- Taxa de extração.
- Duração média de run.
- Mortes por andar.
- Valor perdido por morte.
- Retenção pós-primeira morte.
- Dominância de build.
- Quebras de economia.

## Critérios de Aceite

- Toda falha importante tem feedback e próximo passo.
- Todo risco crítico tem mitigação de escopo.
- Métricas são usadas para decisão, não para vaidade.

## Failure Response Matrix

| Falha | Feedback | Próximo passo |
| --- | --- | --- |
| Morte por combate | causa, inimigo, valor perdido | recuperar corpse/preparar |
| Morte por queda | altura, peso, impacto | rota alternativa/carga menor |
| Falência da loja | caixa, estoque, demanda | vender diferente/reduzir custo |
| Toxicidade | fonte, intensidade | purificar/descansar/evitar |
| Softlock detectado | fallback técnico | regenerar sala/checkpoint |
| Save inválido | aviso claro | carregar backup |

## Risk Register

| Risco | Probabilidade | Impacto | Mitigação |
| --- | --- | --- | --- |
| Escopo excessivo | alta | alto | roadmap rígido e MVP fechado |
| VR desconfortável | média | alto | testes de conforto cedo |
| Economia quebrada | alta | alto | origem rastreável e sinks |
| IA ilegível | média | médio | debug de percepção |
| Procedural repetitivo | média | médio | biomas e micro-world states |
| Descoberta fragmentada | média | médio/alto | `KnowledgeRuntime` único |
| Progressão burlada | média | alto | `LayerLockValidator` central |
| Efeitos duplicados por sistema | média | alto | `UnifiedEffectsRuntime` obrigatório |
| Percepção inconsistente | média | alto | `NoiseAuthority` como fonte única |

## Métricas de Sucesso por Marco

| Marco | Target |
| --- | --- |
| MVP | 70% completam primeira extração após tutorial |
| MVP | morte entendida em menos de 10s via feedback |
| Alpha | nenhum item sem origem no mercado |
| Alpha | top build abaixo de 35% de dominância |
| Beta | delta VR/flatscreen dentro de tolerância definida |

## Non-Goals

- Esconder falha para proteger ego do jogador.
- Balancear por média sem olhar causa.
- Métrica de sucesso sem ação associada.

## Severity Levels

| Severidade | Definição | Exemplo |
| --- | --- | --- |
| S0 | bloqueia progresso ou corrompe save | softlock, save inválido |
| S1 | perda injusta de recursos | corpse inacessível |
| S2 | falha compreensível mas severa | morte por boss |
| S3 | fricção menor | UI pouco clara |
| S4 | polimento | timing de feedback |

## Protocolo de Falha Sistêmica

1. Detectar condição.
2. Interromper dano econômico adicional.
3. Preservar último estado válido.
4. Informar causa ao jogador, se visível.
5. Registrar telemetria.
6. Oferecer recuperação ou fallback.

## Examples

```text
SoftlockDetected:
  freeze room progression
  respawn traversal object
  mark seed for review
  do not grant free resources

MarketInvariantFailed:
  block transaction
  restore inventory snapshot
  log transaction_id
```

## Métricas de Qualidade

- Softlocks por 100 runs.
- Saves recuperados por crash.
- Deaths tagged "unclear" em playtest.
- Corpse inaccessible reports.
- Economy invariant failures.

## Critérios de Saída

- Falhas S0 têm fallback técnico.
- Falhas S1 têm logging e recuperação.
- Toda morte comum tem causa exibida.
- Riscos altos têm dono e mitigação.
