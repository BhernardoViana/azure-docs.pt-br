---
title: Exemplo de script do Azure PowerShell – criar um cluster do Service Fabric | Microsoft Docs
description: Exemplo de script do Azure PowerShell – criar um cluster de teste do Service Fabric de três nós.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: a03362ebd4b8502f12b7c7bb9aadc558f6a073d2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Cria um cluster de teste do Service Fabric de três nós no Azure

Esse script de exemplo cria um cluster de teste do Service Fabric de três nós protegido com um certificado X.509. A configuração do cluster de três nós tem suporte para desenvolvimento e teste porque você pode fazer atualizações e resistir a falhas de nós individuais com segurança (desde que elas não ocorram simultaneamente). Cluster de produção exigem cinco ou mais nós para serem resilientes a falhas simultâneas.  

O comando cria um certificado autoassinado e carrega-o para um novo cofre de chaves, que é criado no mesmo grupo de recursos do cluster. O certificado também é copiado para um diretório local.  Defina o parâmetro *-OS* para escolher a versão do Windows ou Linux que é executada nos nós de cluster.  Personalize os parâmetros conforme necessário.

Se necessário, instale o Azure PowerShell usando as instruções encontradas no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Login-AzureRmAccount` para criar uma conexão com o Azure. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, o cluster e todos os recursos relacionados.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Cria um novo cluster do Service Fabric. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Mais exemplos do Azure PowerShell para o Azure Service Fabric podem ser encontrados nos [exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
