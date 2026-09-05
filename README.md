# Detecção de Fraudes em Transações com Python

Projeto educacional desenvolvido a partir do desafio **Detecção de Anomalias em Transações em Python**, do bootcamp **Bradesco — GenAI, Dados & Cyber**, na DIO.

O trabalho compara modelos de classificação para identificar transações fraudulentas em uma base pública altamente desbalanceada. Inclui preparação dos dados, avaliação de modelos, ajuste de hiperparâmetros, escolha do limiar de decisão e explicabilidade com SHAP.

Embora o desafio utilize o termo “anomalias”, a abordagem implementada é de **classificação supervisionada**, utilizando os rótulos de fraude disponíveis.

## Objetivo

Investigar o equilíbrio entre detectar fraudes e gerar falsos alarmes, sem depender apenas da acurácia.

Uma referência que sempre prevê “não fraudulenta” alcançou aproximadamente **99,83% de acurácia na validação**, mas não identificou nenhuma fraude.

## Dados utilizados

A base pública contém originalmente:

- **284.807 transações**;
- **492 fraudes**;
- **31 colunas**, incluindo a variável-alvo `Class`;
- nenhum valor ausente.

A classe `0` representa transações não fraudulentas e a classe `1`, fraudulentas.

O carregamento é realizado diretamente pelo endereço público utilizado no notebook, sem necessidade de incluir o CSV no repositório.

Fonte e contexto: [tutorial do TensorFlow sobre dados desbalanceados](https://www.tensorflow.org/tutorials/structured_data/imbalanced_data).

**Não foram utilizados dados internos do Bradesco nem dados de clientes acessados pelo autor.**

## Metodologia

1. Inspeção dos dados e da distribuição das classes.
2. Auditoria de 1.081 repetições completas.
3. Criação de uma cópia sem essas repetições, preservando a base original.
4. Divisão estratificada em aproximadamente 60% para treino, 20% para validação e 20% para teste.
5. Comparação de modelos e estratégias de balanceamento.
6. Busca de hiperparâmetros com validação cruzada somente no treino.
7. Seleção do limiar pelo maior F1 na validação.
8. Avaliação final no teste reservado.
9. Análise de importância das variáveis e explicabilidade.

A cópia de trabalho ficou com **283.726 transações e 473 fraudes**. A remoção de linhas idênticas foi uma decisão metodológica do experimento; sem identificadores únicos, não é possível afirmar que eram registros indevidos de transações reais.

A padronização e a reamostragem foram ajustadas exclusivamente nos dados de treino.

## Modelos e estratégias comparados

- Referência que sempre prevê a classe majoritária;
- regressão logística;
- regressão logística com undersampling;
- regressão logística com SMOTE;
- Random Forest com pesos por classe;
- XGBoost inicial;
- XGBoost com hiperparâmetros ajustados.

Foram utilizadas precisão, recall, F1, Average Precision (AP), ROC AUC e contagens de fraudes detectadas, fraudes não detectadas e falsos alarmes.

## Principais aprendizados

- A acurácia isolada pode esconder a incapacidade de detectar fraudes.
- Undersampling e SMOTE aumentaram o recall da regressão logística, mas produziram muitos falsos alarmes no limiar padrão.
- A Random Forest apresentou uma relação mais favorável entre detecção e falsos alarmes que as versões reamostradas da regressão logística.
- Ajustar o limiar altera o equilíbrio entre precisão e recall.
- A escolha do modelo depende do critério de avaliação e das necessidades de uso, não apenas de uma métrica.

## Resultado final no teste

A busca selecionou um XGBoost com **100 árvores**, **profundidade máxima de 5** e `scale_pos_weight=1`, utilizando a AP média na validação cruzada.

O limiar foi escolhido posteriormente na validação, pelo maior F1: aproximadamente **0,10628039**, mantendo o valor completo na execução.

| Indicador | Resultado |
|---|---:|
| Transações avaliadas | 56.746 |
| Fraudes existentes | 95 |
| Fraudes detectadas | 73 |
| Fraudes não detectadas | 22 |
| Falsos alarmes | 8 |
| Precisão | 90,12% |
| Recall | 76,84% |
| F1 | 0,8295 |
| Average Precision (AP) | 0,8140 |
| ROC AUC | 0,9698 |

O teste não foi utilizado para escolher os hiperparâmetros ou o limiar. O desempenho observado foi registrado sem novos ajustes com base nesse resultado.

## Explicabilidade

Foram exploradas duas perspectivas:

- **Importância por ganho:** contribuição das variáveis para as divisões das árvores;
- **SHAP:** magnitude das contribuições das variáveis às previsões em uma amostra de 1.000 transações do teste.

O SHAP foi calculado com `TreeExplainer`, utilizando `feature_perturbation="tree_path_dependent"` e `model_output="raw"`.

A amostra continha apenas **uma fraude**. Portanto, o gráfico representa principalmente o comportamento do modelo em transações não fraudulentas. A média dos valores absolutos SHAP indica magnitude de contribuição, não direção do efeito nem causalidade.

## Como executar

1. Abra o arquivo `deteccao_fraudes_transacoes.ipynb` no Google Colab.
2. Conecte um ambiente de execução.
3. Execute as células na ordem, do início ao fim.

É necessário acesso à internet para carregar a base. O notebook inclui as saídas do experimento e o registro das versões das bibliotecas utilizadas.

Tecnologias: **Python, pandas, NumPy, Matplotlib, scikit-learn, imbalanced-learn, XGBoost e SHAP**.

## Evidências

A pasta `evidencias` reúne capturas das etapas de preparação, treinamento, avaliação, explicabilidade e documentação do ambiente.

O notebook contém o código e os resultados detalhados.

## Limitações

- A divisão foi aleatória e estratificada, não uma avaliação cronológica de transações futuras.
- O teste contém apenas 95 fraudes, limitando a precisão das estimativas de desempenho.
- Não foram definidos custos financeiros de fraudes não detectadas e falsos alarmes.
- As variáveis anonimizadas limitam a interpretação de negócio.
- Não foram avaliados implantação, monitoramento, mudanças no padrão dos dados ou requisitos operacionais.

**Este é um projeto educacional, não um sistema antifraude pronto para produção.**

## Autor

**Antony Kennedy Ribeiro de Araújo**
