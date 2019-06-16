---
title: Problemas conhecidos e soluções
titleSuffix: Azure Machine Learning service
description: Obter uma lista dos problemas conhecidos, soluções alternativas e solução de problemas do Serviço do Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 80bb7af0f7ed20336ab08d4f3ca9639057b9c67f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65149768"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemas conhecidos e solução de problemas do serviço Azure Machine Learning

Este artigo ajuda a localizar e corrigir os erros ou as falhas encontrados ao usar o serviço Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problemas da interface Visual

Interface visual para problemas do serviço de aprendizado de máquina.

### <a name="long-compute-preparation-time"></a>Tempo de preparação de computação longos

Criar nova computação ou evocar deixando computação leva tempo, talvez alguns minutos ou mais. A equipe está trabalhando para otimização.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Não é possível executar um experimento contém apenas conjunto de dados 

Você talvez queira executar um experimento contém apenas o conjunto de dados para visualizar o conjunto de dados. No entanto, ele não tem permissão para executar um experimento contém apenas conjunto de dados hoje mesmo. Estamos ativamente corrigindo o problema.
 
Antes da correção, é possível conectar o conjunto de dados a qualquer módulo de transformação de dados (Selecionar colunas no conjunto de dados, editar metadados, etc. de dados de divisão) e execute o teste. Em seguida, você pode visualizar o conjunto de dados. 

Abaixo da imagem mostra como: ![visulize dados](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: não é possível desinstalar 'PyYAML'**

SDK do Azure Machine Learning para Python: O PyYAML é um projeto de distutils instalado. Portanto, não é possível determinar com precisão quais arquivos pertencem a ele no caso de uma desinstalação parcial. Para continuar a instalação do SDK ignorando esse erro, use:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemas ao criar a computação do Azure Machine Learning

Há uma chance rara que alguns usuários que criaram seu espaço de trabalho do Machine Learning do Azure no portal do Azure antes da versão GA não consigam criar a computação do Azure Machine Learning nesse espaço de trabalho. Você pode gerar uma solicitação de suporte no serviço ou criar um novo espaço de trabalho por meio do Portal ou do SDK para desbloqueio imediato.

## <a name="image-building-failure"></a>Falha na criação da imagem

Falha na criação da imagem ao implantar o serviço Web. Uma solução alternativa é adicionar “pynacl==1.2.1” como uma dependência de pip para o arquivo Conda da imagem de configuração.

## <a name="deployment-failure"></a>Falha na implantação

Caso veja `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, altere o SKU das VMs usadas na implantação para uma que tenha mais memória.

## <a name="fpgas"></a>FPGAs

Não será possível implantar modelos em FPGAs até que você tenha solicitado e recebido aprovação para cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Machine Learning automatizado

Atualmente, o aprendizado de máquina do tensor Flow automatizada não oferece suporte para a versão do tensor flow 1.13. Instalar esta versão fará com que as dependências do pacote parar de funcionar. Estamos trabalhando para corrigir esse problema em uma versão futura. 

### <a name="experiment-charts"></a>Gráficos de teste

Gráficos de classificação binária (Obtenha o recolhimento de precisão, ROC, curva etc.) mostrados em iterações de teste automatizadas ML não são renderização corretamente na interface do usuário desde 4/12. Gráficos do gráfico estão mostrando resultados inversa, em que o melhor desempenho de modelos são mostrados com resultados inferiores. Uma resolução está sendo investigado.

## <a name="databricks"></a>Databricks

Problemas do Databricks e do Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Falha ao instalar os pacotes

Instalação do SDK de aprendizado de máquina do Azure falha no Azure Databricks quando mais pacotes são instalados. Alguns pacotes, como `psutil`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Esse problema está relacionado ao Databricks e não para o SDK do serviço de Azure Machine Learning. Você poderá enfrentar esse problema com outras bibliotecas, muito. Exemplo:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Como alternativa, você pode usar scripts de inicialização, se você manter enfrentando problemas de instalação com bibliotecas Python. Essa abordagem não é oficialmente suportada. Para obter mais informações, consulte [scripts de inicialização no escopo do Cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Cancelar uma execução de aprendizado de máquina automatizada

Quando você usa automatizado recursos de machine learning no Azure Databricks, para cancelar uma execução e iniciar um novo teste em execução, reinicie o cluster do Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterações de aprendizado de máquina automatizado

Na máquina automatizada aprendizado configurações, se você tiver mais de 10 iterações, defina `show_output` para `False` quando você envia a execução.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget para o aprendizado de máquina do Azure Machine Learning SDK/automatizada

O widget do SDK do Azure Machine Learning não tem suporte em um notebook do Databricks, porque os blocos de anotações não é possível analisar os widgets HTML. Você pode exibir o widget no portal usando esse código Python na célula de notebook do Databricks do Azure:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erro de importação: Nenhum módulo chamado 'pandas.core.indexes'

Se você vir esse erro quando você usa automatizada de aprendizado de máquina:

1. Execute este comando para instalar dois pacotes em seu cluster do Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Desanexar e anexar novamente o cluster para o bloco de anotações. 

Se essas etapas não resolverem o problema, tente reiniciar o cluster.

## <a name="azure-portal"></a>Portal do Azure

Se você for diretamente exibir seu espaço de trabalho a partir de um link de compartilhamento do SDK ou do portal, não poderá exibir a página de Visão Geral normal com as informações de assinatura na extensão. Você também não poderá alternar para outro espaço de trabalho. Se você precisar exibir outro workspace, a solução alternativa será ir diretamente para o [portal do Azure](https://portal.azure.com) e procurar o nome do workspace.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Para ver alguns logs, visite [portal do Azure](https://portal.azure.com) e vá para seu espaço de trabalho e selecione **espaço de trabalho > teste > Executar > Logs**.

## <a name="resource-quotas"></a>Cotas de recursos

Saiba mais sobre as [cotas de recursos](how-to-manage-quotas.md) que você pode encontrar ao trabalhar com o Azure Machine Learning.

## <a name="authentication-errors"></a>Erros de autenticação

Se executar uma operação de gerenciamento em um destino de computação de um trabalho remoto, você receberá um dos seguintes erros:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, você receberá um erro se tentar criar ou anexar um destino de computação de um Pipeline de ML que é enviado para execução remota.
