# Desempenho de um Sistema Web em Duas Camadas com Redes de Petri Estocásticas

Trabalho 07 — Modelagem e Avaliação de Desempenho com SPN (UFPI) · João Vitor Santana Lima

**Contexto 1 — Servidor Web:** como a demanda e o número de servidores afetam o desempenho?

O sistema tem um balanceador com fila de `K_WEB` vagas, uma camada web com `N_WEB` servidores e uma
camada de banco de dados (BD) com `N_BD` servidores. 40% das requisições são respondidas pelo cache; as
outras 60% consultam o BD, e o servidor web fica ocupado esperando a resposta. O modelo foi construído
e simulado na ferramenta **Jupiter** (LabPASID/UFPI).

- Vídeo: https://youtu.be/84_uo6u3v_U

## Conteúdo

```
modelo/
  servidor_web.json                 modelo SPN exportado do Jupiter (File > Import (Local) para abrir)
configuracao/
  configuracao_simulacoes.json      configuração das simulações e de cada experimento
resultados/
  E1.csv, E2.csv, E2BD.csv, E3.csv, E4.csv, SENS.csv, DIM.csv   resultados de cada experimento
figuras/                            gráficos do artigo e figura do modelo
```

## Configuração das simulações

Todos os resultados foram obtidos com a **simulação estacionária** do Jupiter, com as mesmas opções
(`configuracao/configuracao_simulacoes.json`). Unidade de tempo: **milissegundos**.

| Opção (Unique Simulation / Advanced Options) | Valor |
|----------------------------------------------|-------|
| Execution Type | Stationary Simulation |
| Simulation Time | 200000 ms |
| Warmup Time | 20000 ms |
| Nível de confiança | 95% |
| Max Rel. Error | 0.02 (2%) |
| Min Samples / Max Samples | 30 / 200 |
| Max Events | 1000000000 |
| Seed | 20249006849 |

Parâmetros do modelo (definições do Jupiter):

| Parâmetro | Referência | Significado |
|-----------|-----------|-------------|
| `AR` | 0,05 req/ms | taxa de chegada (a transição `Chegada` tem tempo médio `1/AR`) |
| `N_WEB` | 12 | servidores web (6 máquinas × 2 núcleos) |
| `N_BD` | 15 | servidores de BD (5 máquinas × 3 núcleos) |
| `K_WEB` | 20 | vagas na fila do balanceador |
| `D_WEB`, `D_BD` | 100 ms | tempo médio de processamento em cada camada (fixos) |
| `P_CACHE` | 0,4 | pesos das transições imediatas `Responde_Cache` (0,4) e `Consulta_BD` (0,6) |

## Experimentos

Cada experimento muda só os parâmetros indicados; os demais ficam na referência. Nas varreduras, a taxa
de chegada vai de 0,01 a 0,20 req/ms com passo de 0,01 (Multi Simulation → Scenarios: `AR`, Start 0.01,
End 0.2, Steps 20).

| Exp. | O que varia | Taxas de chegada (req/ms) | Resultados |
|------|-------------|---------------------------|------------|
| E1 | demanda (configuração de referência) | 0,01 a 0,20 (níveis destacados: 0,01; 0,05; 0,10; 0,15; 0,20) | `E1.csv` |
| E2 | servidores web: 4, 8, 12, 16 (BD = 15) | 0,01 a 0,20 | `E2.csv` |
| E2BD | servidores de BD: 6, 9, 12, 15 (web = 16) | 0,01 a 0,20 | `E2BD.csv` |
| E3 | vagas na fila `K_WEB`: 10, 20, 40 | 0,01 a 0,20 | `E3.csv` |
| E4 | pico com 2 máquinas a mais: +2 web (16/15), +2 BD (12/21), +1 web +1 BD (14/18) | 0,08 | `E4.csv` |
| SENS | cache: pesos 0,2/0,8; 0,4/0,6; 0,6/0,4 | 0,01 a 0,20 | `SENS.csv` |
| DIM | 4 a 10 máquinas web × 2 a 4 máquinas de BD | 0,05; 0,08; 0,10 | `DIM.csv` |