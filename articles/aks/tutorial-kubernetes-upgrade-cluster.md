---
title: Tutorial do Kubernetes no Azure - atualizar cluster
description: Tutorial do Kubernetes no Azure - atualizar cluster
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a293ebbd2ec07d9de53d168f79b8546576499bcb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-upgrade-kubernetes-in-azure-container-service-aks"></a>Tutorial: fazer upgrade do Kubernetes no Serviço de Contêiner do Azure (AKS)

Um cluster AKS (Serviço de Contêiner do Azure) pode ser atualizado usando a CLI do Azure. Durante o processo de upgrade, os nós Kubernetes são cuidadosamente [isolados e esvaziados][kubernetes-drain] para minimizar as interrupções nos aplicativos em execução.

Neste tutorial, parte oito de oito, é feito o upgrade de um cluster Kubernetes. As tarefas a serem concluídas incluem:

> [!div class="checklist"]
> * Identificar as versões atuais e disponíveis do Kubernetes
> * Fazer upgrade dos nós Kubernetes
> * Validar um upgrade bem-sucedido

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, essa imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado. Em seguida, o aplicativo foi executado no cluster Kubernetes.

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].


## <a name="get-cluster-versions"></a>Obter versões de cluster

Antes de atualizar um cluster, use o comando `az aks get-upgrades` para verificar quais versões do Kubernetes estão disponíveis para upgrade.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Aqui você pode ver que a versão atual do nó é `1.7.9`, e as versões de atualização disponíveis na coluna de atualizações.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>Fazer upgrade do cluster

Use o comando `az aks upgrade` para fazer upgrade dos nós de cluster. Os exemplos a seguir atualizam o cluster para a versão `1.8.2`.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Saída:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Validar o upgrade

Agora você pode confirmar se o upgrade obteve êxito com o comando `az aks show`.

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Saída:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez upgrade do Kubernetes em um cluster do AKS. Concluímos as seguintes tarefas:

> [!div class="checklist"]
> * Identificar as versões atuais e disponíveis do Kubernetes
> * Fazer upgrade dos nós Kubernetes
> * Validar um upgrade bem-sucedido

Siga este link para saber mais sobre o AKS.

> [!div class="nextstepaction"]
> [Visão geral do AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md