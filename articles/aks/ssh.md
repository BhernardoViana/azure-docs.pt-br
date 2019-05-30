---
title: SSH nos nós de cluster do AKS (Serviço de Kubernetes do Azure)
description: Saiba como criar uma conexão SSH com o nós de cluster do Serviço de Kubernetes do Azure (AKS) para tarefas de manutenção e solução de problemas.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 57eacca75d711c5125a2856a7b6219cd2ec5306b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242025"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Conectar com SSH aos nós de cluster do Serviço de Kubernetes do Azure (AKS) para manutenção ou solução de problemas

Em todo o ciclo de vida do seu cluster do Serviço de Kubernetes do Azure (AKS), você precisa acessar um nó do AKS. Esse acesso pode ser para manutenção, coleção de logs ou outras operações de solução de problemas. Você pode acessar nós do AKS usando o SSH, incluindo nós do Windows Server (atualmente em visualização no AKS). Você também pode [se conectar a nós do Windows Server usando conexões de protocolo de área de trabalho remota (RDP)][aks-windows-rdp]. Para fins de segurança, os nós do AKS não são expostos à internet.

Este artigo mostra como criar uma conexão SSH com um nó do AKS usando seus endereços IP privados.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

Você também precisa da CLI do Azure versão 2.0.64 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Adicionar sua chave SSH pública

Por padrão, as chaves SSH são obtidas, ou geradas e adicionadas a nós quando você cria um cluster do AKS. Se você precisar especificar chaves SSH diferentes daqueles usados quando você criou o cluster do AKS, adicione sua chave SSH pública para os nós do AKS do Linux. Se necessário, você pode criar uma chave de SSH usando [macOS ou Linux] [ ssh-nix] ou [Windows][ssh-windows]. Se você usar Gen PuTTY para criar o par de chaves, salve o par de chaves em um OpenSSH formato padrão em vez de formato chave privado PuTTy (arquivo. ppk).

> [!NOTE]
> Can de chaves SSH no momento apenas ser adicionados a nós do Linux usando a CLI do Azure. Se você usar nós de servidor Windows, use as chaves SSH fornecidas quando você criou o cluster do AKS e pule para a etapa em [como obter o endereço do nó AKS](#get-the-aks-node-address). Ou, [se conectar a nós do Windows Server usando conexões de protocolo de área de trabalho remota (RDP)][aks-windows-rdp].

As etapas para obter o endereço IP de nós do AKS é diferente com base no tipo de cluster do AKS executar:

* Para a maioria dos clusters AKS, siga as etapas a serem [obter o endereço IP para clusters AKS regulares](#add-ssh-keys-to-regular-aks-clusters).
* Se você usa os recursos de visualização no AKS que usam conjuntos de dimensionamento de máquina virtual, como vários pools de nós ou suporte de contêiner do Windows Server, [siga as etapas para dimensionar da máquina virtual baseada em conjunto clusters AKS](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Adicionar chaves SSH em clusters AKS regulares

Para adicionar sua chave SSH para um nó do AKS de Linux, conclua as seguintes etapas:

1. Obtenha o nome do grupo de recursos para os recursos de cluster do AKS usando [az aks show][az-aks-show]. Forneça seu próprio grupo de recursos de núcleo e o nome do cluster AKS. O nome do cluster é atribuído à variável chamada *CLUSTER_RESOURCE_GROUP*:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Liste as VMs no grupo de recursos de cluster do AKS usando o comando [az vm list][az-vm-list]. Essas VMs são seus nós do AKS:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    O exemplo a seguir mostra a saída dos nós do AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Para adicionar suas chaves SSH ao nó, use o comando [az vm user update][az-vm-user-update]. Forneça o nome do grupo de recursos e, em seguida, um de nós do AKS obtidos na etapa anterior. Por padrão, o nome de usuário para os nós do AKS é *azureuser*. Forneça o local do seu próprio local de chave pública do SSH, como *~/.ssh/id_rsa.pub* ou cole o conteúdo da sua chave pública SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Adicionar chaves SSH para clusters do AKS de baseada em conjunto de dimensionamento de máquina virtual

Para adicionar sua chave SSH para um nó do AKS de Linux que faz parte de um conjunto de dimensionamento de máquina virtual, conclua as seguintes etapas:

1. Obtenha o nome do grupo de recursos para os recursos de cluster do AKS usando [az aks show][az-aks-show]. Forneça seu próprio grupo de recursos de núcleo e o nome do cluster AKS. O nome do cluster é atribuído à variável chamada *CLUSTER_RESOURCE_GROUP*:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Em seguida, obtenha o dimensionamento de máquinas virtuais definido para seu cluster AKS usando o [lista de vmss az] [ az-vmss-list] comando. O nome de conjunto de dimensionamento de máquina virtual é atribuído à variável chamada *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Para adicionar suas chaves SSH para os nós em um conjunto de dimensionamento de máquina virtual, use o [conjunto de extensão do az vmss] [ az-vmss-extension-set] comando. O grupo de recursos de cluster e o nome do conjunto de dimensionamento de máquina virtual são fornecidas nos comandos anteriores. Por padrão, o nome de usuário para os nós do AKS é *azureuser*. Se necessário, atualize o local do seu próprio local de chave pública do SSH, como *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Aplicar a chave SSH para nós usando o [az vmss update-instances] [ az-vmss-update-instances] comando:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Obter o endereço do nó do AKS

Os nós do AKS não estão expostos publicamente à internet. Em relação ao SSH para os nós do AKS, você deve usar o endereço IP privado. A próxima etapa, você cria um pod de auxiliar no cluster do AKS que permite que você SSH para esse endereço IP do nó. As etapas para obter o endereço IP de nós do AKS é diferente com base no tipo de cluster do AKS executar:

* Para a maioria dos clusters AKS, siga as etapas a serem [obter o endereço IP para clusters AKS regulares](#ssh-to-regular-aks-clusters).
* Se você usa os recursos de visualização no AKS que usam conjuntos de dimensionamento de máquina virtual, como vários pools de nós ou suporte de contêiner do Windows Server, [siga as etapas para dimensionar da máquina virtual baseada em conjunto clusters AKS](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>SSH em clusters AKS regulares

Exiba o endereço IP privado de um nó de cluster do AKS usando o comando [az vm list-ip-addresses][az-vm-list-ip-addresses]. Forneça seu próprio nome do grupo de recursos do AKS cluster obtido na etapa [az-aks-show][az-aks-show] anterior:

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

A saída de exemplo a seguir mostra os endereços IP privados dos nós do AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>SSH para clusters do AKS de baseada em conjunto de dimensionamento de máquina virtual

Lista o endereço IP interno de nós usando o [kubectl obter o comando][kubectl-get]:

```console
kubectl get nodes -o wide
```

A saída de exemplo a seguir mostra os endereços IP internos de todos os nós no cluster, incluindo um nó do Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

O endereço IP interno do nó que você deseja solucionar problemas de registro. Você usará esse endereço em uma etapa posterior.

## <a name="create-the-ssh-connection"></a>Criar a conexão SSH

Para criar uma conexão SSH para um nó do AKS, execute um pod auxiliar no cluster do AKS. Este pod auxiliar fornece acesso de SSH no cluster e depois acesso ao nó SSH adicional. Para criar e usar esse podo auxiliar, conclua as seguintes etapas:

1. Execute uma imagem de contêiner `debian` e anexe uma sessão de terminal a ela. Esse contêiner pode ser usado para criar uma sessão SSH com qualquer nó no cluster do AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Se você usar nós do Windows Server (atualmente em visualização no AKS), adicione um seletor de nó para o comando para agendar o contêiner em um nó do Linux Debian da seguinte maneira:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. A imagem base do Debian não inclui componentes SSH. Depois que a sessão de terminal estiver conectada ao contêiner, instale um cliente SSH usando `apt-get` da seguinte maneira:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Em uma nova janela de terminal não conectada ao seu contêiner, liste os pods no seu cluster do AKS usando o comando [kubectl get pods][kubectl-get]. O pod criado na etapa anterior começa com o nome *aks-ssh*, conforme mostrado no exemplo a seguir:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Na primeira etapa deste artigo, você adicionou sua chave SSH pública no nó do AKS. Agora, copie sua chave SSH privada no pod. Essa chave privada é usada para criar o SSH em nós do AKS.

    Forneça seu próprio nome do pod *aks-ssh* obtido na etapa anterior. Se necessário, altere *~/.ssh/id_rsa* para o local de sua chave SSH privada:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Novamente na sessão de terminal para o seu contêiner, atualize as permissões na chave SSH privada `id_rsa` para que seja somente leitura para o usuário:

    ```console
    chmod 0600 id_rsa
    ```

1. Agora, crie uma conexão SSH para o nó do AKS. Novamente, o nome de usuário para os nós do AKS é *azureuser*. Aceite o prompt para continuar com a conexão quando a chave SSH for definida como confiável pela primeira vez. Você receberá o prompt de bash do seu nó do AKS:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Remover o acesso SSH

Quando terminar, `exit` da sessão SSH e, em seguida, `exit` da sessão interativa do contêiner. Quando essa sessão do contêiner for fechada, o pod usado para acesso de SSH do cluster do AKS será excluído.

## <a name="next-steps"></a>Próximas etapas

Se precisar de dados adicionais de solução de problemas, você poderá [exibir os logs de kubelet][view-kubelet-logs] ou [exibir os logs do nó mestre de Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
