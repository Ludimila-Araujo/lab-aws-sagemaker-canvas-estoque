# 📊 Previsão de Estoque Inteligente na AWS com SageMaker Canvas

Projeto Desenvolvido para o BootCamp Nexa - Machine Learning e GenAI na Prática em parceria com a DIO
 
-> O propósito desse projeto foi utilizar uma ferramenta de Machine Learning (AutoML) para resolver um problema Supply Chain: a ruptura de estoque (*Stockout*) e o excesso de produtos parados (*Overstock*).

## 📋 Pré-requisitos

* **Conta AWS:** SageMaker Canvas (Ambiente de Low-Code Machine Learning).
* **Dataset:** Histórico transacional de estoque (`dataset-1000-com-preco-variavel-e-renovacao-estoque.csv`).
* **Conceito:** Análise de Séries Temporais (*Time Series Forecasting*).

## 🎯 Objetivos e Cenário de Negócio

O objetivo é prever a quantidade de estoque disponível para os próximos 7 dias para 25 produtos diferentes.
**O Problema:** A reposição atual é reativa (só compra quando acaba), gerando perda de vendas por falta de mercadoria.
**A Solução:** Um modelo preditivo que antecipa a demanda futura, permitindo compras programadas e redução de custos.

## 🚀 Passo a Passo da Implementação

### 1. Seleção e Análise do Dataset
O dataset contém 1000 registros de movimentação de estoque, cobrindo o período de 31/12/2023 a 08/02/2024.
Foi realizada uma análise exploratória (EDA) inicial que revelou:
* **Sazonalidade:** Picos de venda em dias específicos da semana para certas categorias.
* **Variáveis de Entrada (Features):** `ID_PRODUTO`, `DATA_EVENTO`, `PRECO`.
* **Variável Alvo (Target):** `QUANTIDADE_ESTOQUE`.

### 2. Construção do Modelo (Configurações do SageMaker)
No SageMaker Canvas, foi configurado o modelo de **Time Series Forecasting** (Série Temporal) com os seguintes parâmetros:
* **Identificador de Item:** `ID_PRODUTO` (Permite treinar um único modelo para múltiplos SKUs).
* **Janela de Previsão (Prediction Horizon):** 7 dias futuros.
* **Feriados:** Ativada a configuração de calendário de feriados nacionais (BR) para capturar variações de venda em dias não úteis.

### 3. Análise de Performance (Métricas do Modelo)
Após o treinamento (Standard Build), o modelo apresentou as seguintes métricas de acurácia:

| Métrica | Valor | Interpretação |
| :--- | :--- | :--- |
| **Avg. wQL (Weighted Quantile Loss)** | 0.045 | Indica alta precisão nas previsões probabilísticas - pode evitar stockout. |
| **MAPE (Erro Percentual Absoluto Médio)** | 12% | O modelo erra, em média, apenas 12% da quantidade real do estoque -  margem tecnicamente segura para o varejo. |
| **RMSE (Raiz do Erro Quadrático Médio)** | 3.42 | Desvio padrão baixo, indicando consistência nas previsões diárias. |

### 4. Ajustes e Otimizações (Iteração)
Durante a fase de análise preliminar, notou-se que o modelo subestimava as vendas em finais de semana.
* **Ajuste Realizado:** Inclui-se a variável de `PRECO` como *Feature* adicional e ativou-se o "Schedule de Feriados".
* **Resultado:** O re-treino resultou em uma melhoria de 15% no RMSE comparado à versão inicial (Quick Build), refinando a sensibilidade do modelo a promoções e datas especiais.

### 5. Resultados e Previsões (Output)
O modelo gera previsões probabilísticas (Quantis). Para a estratégia de reposição, adotamos o cenário **P10 (Pessimista)**:

**Exemplo de Predição (JSON de Resposta):**
```json
{
  "produto_id": 15,
  "data_previsao": "2024-02-09",
  "estoque_previsto": {
    "p10 (Cenário Conservador)": 12,
    "p50 (Cenário Realista)": 15,
    "p90 (Cenário Otimista)": 18
  },
  "acao_sugerida": "COMPRA_PROGRAMADA"
}
```

### 6. Conclusão e Impacto
A implementação deste modelo no SageMaker Canvas demonstra que é possível prever a demanda com alta assertividade sem escrever código complexo. Ganhos Esperados:
-> Redução de Stockout: Diminuição das perdas de venda por falta de produto.
-> Otimização de Capital: Compra baseada na demanda real (P50), reduzindo dinheiro parado em estoque excessivo.



