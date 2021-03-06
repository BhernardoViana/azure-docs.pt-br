---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875518"
---
**Os destinos de computação podem ser reutilizados de um trabalho de treinamento para o próximo**. Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos.  Para pipelines de Machine Learning, use a [etapa de pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) apropriada para cada destino de computação.

|Treinamento &nbsp;destinos|[ML automatizado](../articles/machine-learning/service/concept-automated-ml.md) | [Pipelines de ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Designer de Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computador local](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Sim | &nbsp; | &nbsp; |
|[Azure Machine Learning cluster de computação](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Sim & <br/>ajuste de&nbsp;de hiperparâmetro | Sim | Sim |
|[VM remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | Sim & <br/>gráfico de ajuste de hiperparâmetro | Sim | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| Sim | Sim | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | Sim | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | Sim | &nbsp; |
|[Lote do Azure](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | Sim | &nbsp; |
