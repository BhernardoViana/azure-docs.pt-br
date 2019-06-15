---
title: Criar experimentos de ML automatizados
titleSuffix: Azure Machine Learning service
description: O aprendizado de máquina automatizado escolhe um algoritmo para você e gera um modelo pronto para implantação. Saiba quais opções você pode usar para configurar experimentos de aprendizado de máquina automatizados.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: df05bd984667283b0ccc143ba14fff6b35d69144
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753166"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configurar automatizados experimentos do ML no Python

Neste guia, saiba como definir vários parâmetros de configuração de seu automatizado experimentos de machine learning com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). O aprendizado de máquina automatizado escolhe um algoritmo e hiperparâmetros para você e gera um modelo pronto para implantação. Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizados.

Para exibir exemplos de experimentos de aprendizado de máquina automatizados, confira [Tutorial: Treinar um modelo de classificação com aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [Treinar modelos com aprendizado de máquina automatizado na nuvem](how-to-auto-train-remote.md).

Opções de configuração disponíveis no aprendizado de máquina automatizado:

* Selecione o tipo de experimento: Classificação, regressão ou previsão da série temporal
* Fonte de dados, formatos e busca de dados
* Escolha o destino de computação: local ou remoto
* Configurações do experimento de aprendizado de máquina automatizado
* Executar um experimento de aprendizado de máquina automatizado
* Explorar as métricas do modelo
* Registro e implantação do modelo

Se você não preferir uma experiência de nenhum código, você também pode [criar experimentos no portal do Azure de aprendizado de máquina automatizada](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Selecionar o tipo de experimento

Antes de iniciar o experimento, determine o tipo de problema de aprendizado de máquina a ser resolvido. O aprendizado de máquina automatizado dá suporte a tipos de tarefa de classificação, regressão e previsão.

O aprendizado de máquina automatizado é compatível com os seguintes algoritmos durante o processo de automação e ajuste. Como usuário, não há necessidade de especificar o algoritmo. Embora os algoritmos DNN estejam disponíveis durante o treinamento, ML automatizado não compila modelos DNN.

classificação | Regressão | Previsão de série temporal
|-- |-- |--
[Regressão logística](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rede elástica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)|[GBM claro](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#regression)|[Árvore de decisões](https://scikit-learn.org/stable/modules/tree.html#regression)
[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K-ésimo vizinhos mais próximos](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC linear](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[SVC (Classificação de vetores de suporte do C)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Floresta aleatória](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Árvores extremamente aleatórias](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificador DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressor DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificador Linear de DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressor linear](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[SGD (Gradiente estocástico descendente)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Use o `task` parâmetro no `AutoMLConfig` construtor para especificar seu tipo de teste.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Fonte de dados e formato
O aprendizado de máquina automatizado é compatível com os dados que residem na área de trabalho local ou na nuvem no Armazenamento de Blobs do Azure. Os dados podem ser lidos nos formatos de dados compatíveis com scikit-learn. É possível ler os dados para:
* Matrizes numpy X (recursos) e y (variável de destino ou também conhecido como rótulo)
* Dataframe do Pandas

Exemplos:

*   Matrizes Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Dataframe do Pandas

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Buscar dados para executar o experimento em computação remota

Se você estiver usando computação remota para executar o experimento, a busca de dados precisará ser encapsulada em um script `get_data()` separado do Python. Esse script é executado na computação remota em que o experimento de aprendizado de máquina automatizado é executado. `get_data` elimina a necessidade de buscar os dados durante a transmissão para cada iteração. Sem `get_data`, o experimento falhará ao ser executado em computação remota.

Veja um exemplo de `get_data`:

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

No objeto `AutoMLConfig`, você especifica o parâmetro `data_script` e fornece o caminho para o arquivo de script `get_data` desta forma:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

O script `get_data` pode retornar:

Chave | Type | Mutuamente exclusivo com    | DESCRIÇÃO
---|---|---|---
X | Dataframe do Pandas ou matriz Numpy | data_train, rótulo, colunas |  Todos os recursos a serem treinados com
y | Dataframe do Pandas ou matriz Numpy |   label   | Rotular dados a serem treinados com. Para classificação, precisa ser uma matriz de inteiros.
X_valid | Dataframe do Pandas ou matriz Numpy   | data_train, rótulo | _Opcional_ dados que formam o conjunto de validação de recursos. Se não for especificado, X será dividido entre "treinar" e "validar"
y_valid |   Dataframe do Pandas ou matriz Numpy | data_train, rótulo | _Opcional_ Os dados de rótulo a serem validados com. Se não for especificado, y será dividido entre "treinar" e "validar"
sample_weight | Dataframe do Pandas ou matriz Numpy |   data_train, rótulo, colunas| _Opcional_ Um valor de peso para cada amostra. Use quando quiser atribuir pesos diferentes aos pontos de dados
sample_weight_valid | Dataframe do Pandas ou matriz Numpy | data_train, rótulo, colunas |    _Opcional_ Um valor de peso para cada amostra de validação. Se não for especificado, sample_weight será dividido entre "treinar" e "validar"
data_train |    Dataframe do Pandas |  X, y, X_valid, y_valid |    Todos os dados (recursos + rótulo) a serem treinados com
label | cadeia de caracteres  | X, y, X_valid, y_valid |  Qual coluna em data_train representa o rótulo
colunas | Matriz de cadeias de caracteres  ||  _Opcional_ Lista de permissões de colunas a serem usadas para recursos
cv_splits_indices   | Matriz de inteiros ||  _Opcional_ Lista de índices para dividir os dados para validação cruzada

## <a name="train-and-validation-data"></a>Dados de treinamento e validação

É possível especificar conjuntos de treinamento e validação por meio de get_data() ou diretamente no método `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Validação cruzada k-fold

Use a configuração `n_cross_validations` para especificar o número de validações cruzadas. O conjunto de dados de treinamento será dividido aleatoriamente em `n_cross_validations` partições de tamanho igual. Durante cada rodada de validação cruzada, uma das partições será usada para validar o modelo treinado nas partições restantes. Esse processo é repetido por `n_cross_validations` rodadas até que cada partição seja usada uma vez como conjunto de validação. As pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo cruzada validação (repetida subamostragem aleatória)

Use `validation_size` para especificar o percentual do conjunto de dados de treinamento que precisa ser usado para validação. Use `n_cross_validations` para especificar o número de validações cruzadas. Durante cada rodada de validação cruzada, um subconjunto de tamanho `validation_size` será selecionado aleatoriamente para validar o modelo treinado nos dados restantes. Por fim, as pontuações médias em todas as rodadas `n_cross_validations` serão relatadas e o modelo correspondente será treinado novamente em todo o conjunto de dados de treinamento. Monte Carlo, não há suporte para previsão de série temporal.

### <a name="custom-validation-dataset"></a>Conjunto de dados de validação personalizados

Use o conjunto de dados de validação personalizada se divisão aleatória não for aceitável, normalmente, dados de série temporal ou dados em desequilíbrio. É possível especificar seu próprio conjunto de dados de validação. O modelo será avaliado em relação ao conjunto de dados de validação especificado em vez de um conjunto de dados aleatórios.

## <a name="compute-to-run-experiment"></a>Computação para executar o experimento

Em seguida, determine onde o modelo será treinado. Um experimento de treinamento de aprendizado de máquina automatizado pode ser executado nas opções de computação a seguir:
*   O computador local, como a área de trabalho ou o laptop local – geralmente, quando o conjunto de dados é pequeno e você ainda está na fase de exploração.
*   Um computador remoto na nuvem – [Computação gerenciada do Azure Machine Learning](concept-compute-target.md#amlcompute) é um serviço gerenciado que permite a capacidade de treinar modelos de machine learning em clusters de máquinas virtuais do Azure.

Confira o [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) para obter exemplos de notebooks com destinos de computação locais e remotos.

*   Um cluster do Databricks do Azure em sua assinatura do Azure. Você pode encontrar mais detalhes aqui - [cluster de instalação do Azure Databricks para ML automatizada](how-to-configure-environment.md#azure-databricks)

Consulte a [site do GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) como blocos de anotações com o Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Configurar as definições do experimento

Há várias opções que você pode usar para configurar experimentos de aprendizado de máquina automatizado. Esses parâmetros são definidos pela instanciação de um objeto `AutoMLConfig`. Consulte a [classe AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) para obter uma lista completa de parâmetros.

Alguns exemplos incluem:

1.  Experimento de classificação usando AUC ponderada como a métrica principal com um tempo máximo de 12.000 segundos por iteração, com o experimento programado para terminar após 50 iterações e duas partições de validação cruzada.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Veja abaixo um exemplo de experimento de regressão programado para terminar após 100 iterações, em que cada iteração dura até 600 segundos com cinco partições de validação cruzada.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

Os três diferentes `task` valores de parâmetro de determinam a lista de algoritmos a ser aplicado.  Use os parâmetros `whitelist` ou `blacklist` para modificar ainda mais as iterações com os algoritmos disponíveis a serem incluídos ou excluídos. A lista de modelos com suporte pode ser encontrada no [SupportedAlgorithms classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Métrica principal
A principal métrica; conforme mostrado nos exemplos acima determina a métrica a ser usado durante o treinamento de modelo para otimização. A principal métrica que você pode selecionar é determinada pelo tipo de tarefa que você escolher. Abaixo está uma lista de métricas disponíveis.

|classificação | Regressão | Previsão de série temporal
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Pré-processamento de dados e personalização

Em cada computador automatizada de teste de aprendizado, seus dados estão [automaticamente dimensionado e normalizados](concept-automated-ml.md#preprocess) para ajudar a algoritmos funcionam bem.  No entanto, você também pode habilitar adicionais de pré-processamento/personalização, como ausência de imputação de valores, codificação e transformações. [Saiba mais sobre quais personalização está incluída](how-to-create-portal-experiments.md#preprocess).

Para habilitar essa personalização, especifique `"preprocess": True` para o [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Previsão de série temporal
Para o tipo de tarefa de previsão da série de tempo, você tem parâmetros adicionais para definir.
1. time_column_name - isso é um parâmetro obrigatório que define o nome da coluna na sua série de recipiente de dados de treinamento de data/hora.
1. max_horizon - isso define o período de tempo que você deseja prever horizontalmente com base na periodicidade dos dados de treinamento. Por exemplo se você tiver dados de treinamento com intervalos diários de agregação, você define por quanto nos dias que você deseja que o modelo para treinar para.
1. grain_column_names - isso define o nome das colunas que contêm dados de série de tempo individuais em seus dados de treinamento. Por exemplo, se a previsão de vendas de uma marca específica por loja, você definiria colunas store e o marca como suas colunas de detalhamento.

Veja um exemplo dessas configurações que estão sendo usadas abaixo, o exemplo de bloco de anotações está disponível [aqui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

## <a name="run-experiment"></a>Executar o experimento

Envie o experimento para ser executado e para gerar um modelo. Passe o `AutoMLConfig` para o método `submit` para gerar o modelo.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>As dependências são instaladas pela primeira vez em um novo computador.  Pode levar até dez minutos antes da saída ser exibida.
>Definir `show_output` como `True` faz a saída ser mostrada no console.

### <a name="exit-criteria"></a>Critérios de saída
Há algumas opções você pode definir para concluir seu experimento.
1. Nenhum critério - se você não definir nenhum sair parâmetros que o teste continuará até que nenhum progresso adicional é feito em sua métrica principal.
1. Número de iterações - você definir o número de iterações para executar o experimento. Você pode opcional adicionar iteration_timeout_minutes para definir um limite de tempo em minutos por cada iteração.
1. Sair após um período de tempo - experiment_timeout_minutes usando em suas configurações, que você pode definir quanto tempo em minutos um experimento que deve continuar em execução.
1. Feche após uma pontuação foi atingida - usando experiment_exit_score, que você pode escolher para concluir o teste após uma pontuação com base em sua métrica principal foi atingida.

### <a name="explore-model-metrics"></a>Explorar as métricas do modelo

Você pode exibir os resultados de treinamento em um widget ou embutido, se você estiver em um bloco de anotações. Confira [Track and evaluate models](how-to-track-experiments.md#view-run-details) (Rastrear e avaliar modelos) para obter mais detalhes.

## <a name="understand-automated-ml-models"></a>Compreender os modelos de ML automatizados

Qualquer modelo produzido usando ML automatizado inclui as seguintes etapas:
+ Automatizada de engenharia de recursos (se pré-processar = True)
+ Normalização/dimensionamento e o algoritmo com valores hypermeter

Podemos tornar transparente para obter essas informações na saída fitted_model do ML automatizado.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Engenharia de recursos automatizados

Consulte a lista de pré-processamento e [automatizada de engenharia de recursos](concept-automated-ml.md#preprocess) que acontece quando pré-processar = True.

Considere este exemplo:
+ Há 4 recursos de entrada: Um (numérico), B (numérico), o C (numérico), o D (DateTime)
+ Recurso numérico C é descartado porque ele é uma coluna de ID com todos os valores exclusivos
+ Recursos numéricos A e B tenham valores ausentes e, portanto, são inserir por média
+ O recurso de data e hora D é destacado em 11 de diferentes recursos de engenharia

Use esses 2 APIs na primeira etapa do modelo ajustado para entender mais.  Ver [este bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` retorna uma lista de nomes de recursos de engenharia.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Essa lista inclui todos os nomes de recurso de engenharia.

  >[!Note]
  >Use 'timeseriestransformer' para a tarefa = 'previsão', caso contrário, use 'datatransformer' para a tarefa 'regressão' ou 'classificação'.

+ A API 2: `get_featurization_summary()` retorna personalização resumo de todos os recursos de entrada.

  Uso:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Use 'timeseriestransformer' para a tarefa = 'previsão', caso contrário, use 'datatransformer' para a tarefa 'regressão' ou 'classificação'.

  Saída:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Em que:

   |Saída|Definição|
   |----|--------|
   |RawFeatureName|Nome de recurso/coluna de entrada do conjunto de dados fornecido.|
   |TypeDetected|Tipo de dados detectado do recurso de entrada.|
   |Descartado|Indica se o recurso de entrada foi descartado ou usado.|
   |EngineeringFeatureCount|Número de recursos gerados por meio de transformações de engenharia de recurso automatizado.|
   |Transformações|Lista de transformações aplicadas a recursos para gerar recursos de engenharia de entrada.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Normalização/dimensionamento e o algoritmo com valores hypermeter:

Para entender os valores de escala/normalização e o algoritmo/hiperparâmetro para um pipeline, use fitted_model.steps. [Saiba mais sobre dimensionamento/normalização](concept-automated-ml.md#preprocess). Aqui está uma amostra de saída:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Para obter mais detalhes, use essa função auxiliar mostrada na [este bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

A seguir está a saída de exemplo para um pipeline usando um algoritmo específico (LogisticRegression com RobustScalar, neste caso).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Explicar o modelo (possibilidade de interpretação)

O aprendizado de máquina automatizado permite que você entenda a importância do recurso.  Durante o processo de treinamento, é possível obter a importância global do recurso para o modelo.  Para cenários de classificação, também é possível obter a importância do recurso no nível da classe.  É necessário fornecer um conjunto de dados de validação (X_valid) para obter a importância do recurso.

Há duas maneiras de gerar a importância do recurso.

*   Quando um experimento é concluído, é possível usar o método `explain_model` em qualquer iteração.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Para exibir a importância do recurso para todas as iterações, defina o sinalizador `model_explainability` como `True` em AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    Quando terminar, será possível usar o método retrieve_model_explanation para recuperar a importância do recurso para uma iteração específica.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

É possível visualizar o gráfico de importância do recurso em seu workspace no portal do Azure. O gráfico também é mostrado ao usar o widget do Jupyter em um notebook. Para saber mais sobre os gráficos, veja o [artigo de notebooks de Serviço do Azure Machine Learning de exemplo.](samples-notebooks.md)

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![gráfico de importância do recurso](./media/how-to-configure-auto-train/feature-importance.png)

Para obter mais informações sobre como as explicações de modelo e a importância do recurso podem ser habilitados em outras áreas do SDK fora do aprendizado de máquina automatizados, consulte o [conceito](machine-learning-interpretability-explainability.md) artigo sobre a possibilidade de interpretação.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

Saiba mais sobre [como treinar um modelo de regressão com aprendizado de máquina automatizada](tutorial-auto-train-models.md) ou [como treinar usando automatizada de aprendizado de máquina em um recurso remoto](how-to-auto-train-remote.md).
