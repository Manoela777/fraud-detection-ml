# Fraud Detection ML

Projeto de Machine Learning para detecção de fraudes em transações de cartão de crédito.

## Objetivo

Construir e comparar modelos de classificação em uma base altamente desbalanceada, utilizando métricas adequadas para a classe rara de fraude.

O projeto foi desenvolvido como parte de um desafio de Machine Learning da DIO.

## Dataset

O dataset utilizado é o **Credit Card Fraud Detection**, carregado diretamente no notebook por meio da URL utilizada na aula:

```text
https://storage.googleapis.com/download.tensorflow.org/data/creditcard.csv
````

O dataset não é armazenado neste repositório.

A base contém 284.807 transações e 31 colunas: `Time`, `Amount`, `Class` e as variáveis `V1` a `V28`.

As variáveis `V1` a `V28` são componentes obtidas por PCA para preservar a confidencialidade das informações originais.

## Desbalanceamento

O principal desafio da base é o forte desbalanceamento entre as classes. A quantidade de transações legítimas é muito maior que a quantidade de fraudes.

Por isso, a acurácia isolada pode ser enganosa. Um modelo poderia classificar praticamente todas as transações como normais e ainda apresentar uma acurácia elevada, mesmo deixando de identificar as fraudes.

As principais métricas utilizadas foram:

* Precision
* Recall
* F1-score
* ROC-AUC
* PR-AUC

O **recall da classe fraude** recebeu atenção especial, pois representa a proporção das fraudes reais que foram identificadas pelo modelo.

## Preparação dos dados

Foram realizadas as seguintes etapas:

* inspeção da estrutura e qualidade dos dados;
* análise da distribuição das classes;
* criação da variável `Amount_log` utilizando `log1p`;
* padronização das variáveis com `StandardScaler`;
* divisão dos dados em treino, validação e teste utilizando estratificação;
* tratamento do desbalanceamento por meio de pesos de classe.

A separação em treino, validação e teste permitiu utilizar o conjunto de validação para analisar o threshold sem utilizar o conjunto de teste nessa etapa.

## Modelos

Foram comparados três modelos:

* Regressão Logística;
* Random Forest;
* XGBoost.

A comparação inicial foi realizada utilizando threshold de decisão igual a `0,50`.

## Resultados

### Comparação com threshold 0,50

Os modelos foram inicialmente avaliados no conjunto de teste utilizando threshold de `0,50`.

| Modelo              | Precision | Recall |     F1 | ROC-AUC | PR-AUC |
| ------------------- | --------: | -----: | -----: | ------: | -----: |
| Logistic Regression |    0.0610 | 0.9184 | 0.1144 |  0.9731 | 0.7202 |
| Random Forest       |    0.9195 | 0.8163 | 0.8649 |  0.9556 | 0.8605 |
| XGBoost             |    0.8469 | 0.8469 | 0.8469 |  0.9759 | 0.8697 |

Os resultados mostram a importância de analisar mais de uma métrica. A Regressão Logística apresentou recall elevado para fraude, mas sua precisão foi baixa, resultando em um F1-score consideravelmente menor.

Random Forest e XGBoost apresentaram um equilíbrio maior entre precisão e recall.

## Ajuste do threshold

Após a comparação inicial, foi analisado o efeito de diferentes thresholds sobre as previsões do XGBoost.

O threshold selecionado foi **0,90**, pois apresentou o maior F1-score para a classe fraude no conjunto de validação.

No conjunto de validação:

* Precision: `0.9390`
* Recall: `0.7778`
* F1-score: `0.8508`

Com o threshold de `0,90`, o modelo final foi então avaliado no conjunto de teste:

* Precision: `0.9302`
* Recall: `0.8163`
* F1-score: `0.8696`
* ROC-AUC: `0.9759`
* PR-AUC: `0.8697`

No conjunto de teste, foram identificadas **80 das 98 transações fraudulentas**, correspondendo a um recall de aproximadamente `81,63%`.

O ajuste do threshold demonstra que a escolha do ponto de decisão pode alterar significativamente o equilíbrio entre identificar fraudes e evitar classificações incorretas como fraude.

## Explicabilidade com SHAP

Foi utilizada a biblioteca SHAP para analisar a contribuição das variáveis nas previsões do XGBoost.

No `summary_plot`, as variáveis que apresentaram maior influência global foram:

`V4`, `V14`, `V12`, `V10`, `V3`, `V11`, `V8`, `V1`, `V21` e `V5`.

A análise também permitiu observar como os valores das diferentes componentes contribuíram para aumentar ou diminuir a saída do modelo.

Como `V1` a `V28` são componentes transformadas por PCA, elas não devem ser interpretadas diretamente como características originais do negócio, como localização, estabelecimento ou tipo de compra.

## O que foi diferente da aula

Além do fluxo apresentado nas aulas, este projeto foi organizado com:

1. divisão dos dados em treino, validação e teste;
2. comparação estruturada entre três modelos;
3. análise de diferentes thresholds;
4. seleção do threshold utilizando o conjunto de validação;
5. avaliação final do modelo no conjunto de teste;
6. explicação das previsões utilizando SHAP.

## Possíveis extensões

Como próximos passos, o projeto pode ser ampliado com:

* comparação entre undersampling e oversampling;
* ajuste de hiperparâmetros com `GridSearchCV`;
* criação de variáveis relacionadas ao comportamento das transações ao longo do tempo;
* teste de outros algoritmos de classificação;
* comparação dos modelos utilizando diferentes estratégias de balanceamento.

## Tecnologias

* Python
* Pandas
* NumPy
* Matplotlib
* Seaborn
* Scikit-learn
* XGBoost
* SHAP
* Jupyter Notebook

## Como executar

1. Clone ou baixe este repositório.
2. Abra `fraud_detection_ml.ipynb` no Google Colab ou em um ambiente Jupyter.
3. Instale as bibliotecas necessárias, caso ainda não estejam disponíveis.
4. Execute as células na ordem.
5. O dataset será carregado automaticamente pela URL utilizada no projeto.
6. Confira as tabelas, métricas e gráficos gerados pelo notebook.