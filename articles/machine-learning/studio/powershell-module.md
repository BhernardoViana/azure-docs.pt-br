---
title: Módulo PowerShell para Machine Learning Studio - Azure | Microsoft Docs
description: O módulo PowerShell para Azure Machine Learning está disponível no modo de visualização pública. Use o PowerShell para criar e gerenciar os workspaces, os experimentos, os serviços Web e muito mais.
keywords: teste,regressão linear,algoritmos de aprendizado de máquina,tutorial de aprendizado de máquina,técnicas de modelos de previsão, teste de ciência de dados
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: previous-ms.author=haining, previous-author=hning86
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.openlocfilehash: 6539ec36c23feccfa52c8214784590106fa5a01e
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257515"
---
# <a name="powershell-module-for-azure-machine-learning-studio"></a>Módulo do PowerShell para o Estúdio de Aprendizagem de Máquina do Azure
O módulo PowerShell do Azure Machine Learning é uma ferramenta avançada que permite usar o Windows PowerShell para gerenciar workspaces, experimentos, conjuntos de dados, serviços Web Clássicos e muito mais.

Você pode exibir a documentação e fazer o download do módulo, juntamente com o código-fonte completo, de [https://aka.ms/amlps](https://aka.ms/amlps). 

> [!NOTE]
> O módulo PowerShell do Azure Machine Learning está atualmente no modo de visualização. O módulo continuará a ser aprimorado e expandido durante esse período de visualização. Fique atento ao [Blog do Cortana Intelligence e Machine Learning](https://blogs.technet.microsoft.com/machinelearning/) para obter mais informações e notícias.

## <a name="what-is-the-machine-learning-powershell-module"></a>O que é o módulo PowerShell do Machine Learning?
O módulo PowerShell do Machine Learning é um módulo DLL baseado no .NET que permite gerenciar por completo workspaces do Azure Machine Learning, experimentos, conjuntos de dados, serviços Web Clássicos e pontos de extremidade de serviço Web Clássico no Windows PowerShell. 

Junto com o módulo, é possível baixar o código-fonte completo, que inclui uma [camada de API do C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs) separada de forma limpa. É possível referenciar essa DLL no próprio projeto do .NET e gerenciar o Azure Machine Learning por meio do código .NET. Além disso, a DLL depende de APIs REST subjacentes que podem ser usadas diretamente em seu cliente favorito.

## <a name="what-can-i-do-with-the-powershell-module"></a>O que posso fazer com o módulo PowerShell?
Aqui estão algumas das tarefas que podem ser executadas com este módulo PowerShell. Confira a [documentação completa](https://aka.ms/amlps) dessas e de várias outras funções.

* Provisionar um novo workspace usando um certificado de gerenciamento ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* Exportar e importar um arquivo JSON que representa um grafo de experimento ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
* Executar um experimento ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* Criar um serviço Web de um experimento de previsão ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* Criar um ponto de extremidade em um serviço Web publicado ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* Invocar um ponto de extremidade de serviço Web RRS e/ou BES ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Veja um exemplo rápido de uso do PowerShell para executar um experimento existente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Para obter um caso de uso mais detalhado, confira este artigo sobre como usar o módulo do PowerShell para automatizar uma tarefa que é muito solicitada: [Crie vários modelos de machine learning e pontos de extremidade de serviço Web com base em um teste usando o PowerShell](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Como começar?
Para começar a usar o Machine Learning PowerShell, baixe o [pacote de versão](https://github.com/hning86/azuremlps/releases) do GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). As instruções explicam como desbloquear a DLL baixada/descompactada e importá-la para o ambiente do PowerShell. A maioria dos cmdlets exige que você forneça a ID do workspace, o token de autorização do workspace e a região do Azure em que o workspace está. A maneira mais simples de fornecer os valores é por meio de um arquivo config.json padrão. As instruções também explicam como configurar esse arquivo. 

Se você desejar, será possível clonar a árvore git, modificar o código e compilá-lo localmente usando o Visual Studio.

## <a name="next-steps"></a>Próximas etapas
A documentação completa do módulo PowerShell pode ser encontrada em [https://aka.ms/amlps](https://aka.ms/amlps). 

Para obter um exemplo de como usar o módulo em um cenário real, consulte o caso de uso mais detalhado [Criar vários modelos do Machine Learning e pontos de extremidade de serviço Web com base em um teste usando o PowerShell](create-models-and-endpoints-with-powershell.md).
