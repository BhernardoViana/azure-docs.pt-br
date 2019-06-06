---
title: Exibir logs do controlador de serviço de Kubernetes do Azure (AKS)
description: Saiba como ativar e visualizar os logs do nó mestre do Kubernetes no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: iainfou
ms.openlocfilehash: 256101cce5588f56a8094a7a9a98e5fe69e6ec73
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497240"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Habilitar e revisar os logs do nó mestre do Kubernetes no Azure Kubernetes Service (AKS)

Com o Serviço Azerbas do Kubernetes (AKS), os componentes principais, como o *kube-apiserver* e o *kube-controller-manager*, são fornecidos como um serviço gerenciado. Você cria e gerencia os nós que executam o *kubelet* e o tempo de execução do contêiner e implementa seus aplicativos por meio do servidor gerenciado do Kubernetes API. Para ajudar a solucionar problemas de seu aplicativo e serviços, talvez seja necessário visualizar os logs gerados por esses componentes principais. Este artigo mostra como usar os logs do Azure Monitor para ativar e consultar os logs dos componentes principais do Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer um cluster AKS existente em execução na sua conta do Azure. Se você ainda não tiver um cluster AKS, crie um usando o [Portal do Azure][cli-quickstart] ou [do Portal do Azure][portal-quickstart]. Os logs do Azure Monitor trabalham com clusters AKS habilitados para RBAC e não RBAC.

## <a name="enable-diagnostics-logs"></a>Habilitar logs de diagnóstico

Para ajudar a coletar e analisar dados de várias origens, os logs do Azure Monitor disponibilizam uma linguagem de consulta e um mecanismo de análise que fornecem insights ao ambiente. Um workspace é usado para agrupar e analisar os dados e pode se integrar a outros serviços do Azure, como o Application Insights e o Security Center. Para usar uma plataforma diferente para analisar os logs, você pode optar por enviar logs de diagnóstico para uma conta de armazenamento do Azure ou um hub de eventos. Para obter mais informações, veja [O que são os logs do Azure Monitor?][log-analytics-overview].

Os logs do Azure Monitor estão habilitados e gerenciados no portal do Azure. Para habilitar a coleta de log para os componentes principais do Kubernetes no seu cluster AKS, abra o portal do Azure em um navegador da Web e conclua as etapas a seguir:

1. Selecione o grupo de recursos para seu cluster AKS, como *myResourceGroup*. Não selecione o grupo de recursos que contém seus recursos individuais de cluster do AKS, como *MC_myResourceGroup_myAKSCluster_eastus*.
1. No lado esquerdo, escolha **Diagnostic settings**.
1. Selecione o cluster AKS, como *myAKSCluster*, em seguida, escolher **Adicionar configuração de diagnóstico**.
1. Digite um nome, assim como *myAKSClusterLogs*, e selecione a opção para **Enviar para o Log Analytics**.
1. Selecione um espaço de trabalho existente ou crie um novo. Se você criar um espaço de trabalho, forneça um nome de espaço de trabalho, um grupo de recursos e um local.
1. Na lista de logs disponíveis, selecione os logs que deseja habilitar. Logs comuns incluem o *kube apiserver*, *Gerenciador do controlador de kube*, e *kube-Agendador*. Você pode habilitar logs adicionais, tais como *kube-audit* e *cluster-autoscaler*. Você pode retornar e alterar os logs coletados depois que os espaços de trabalho do Log Analytics estiverem ativados.
1. Quando estiver pronto, selecione **Salvar** para ativar a coleta dos logs selecionados.

> [!NOTE]
> O AKS captura somente logs de auditoria para clusters criados ou atualizados depois que um sinalizador de recursos está habilitado em sua assinatura. Para registrar o sinalizador de recursos *AKSAuditLog*, use o comando [az feature register][az-feature-register] conforme mostrado no exemplo a seguir:
>
> `az feature register --name AKSAuditLog --namespace Microsoft.ContainerService`
>
> Aguarde até que o status mostre *Registrado*. Você pode verificar o status de registro usando o comando [az feature list][az-feature-list]:
>
> `az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAuditLog')].{Name:name,State:properties.state}"`
>
> Quando estiver pronto, atualize o registro do provedor de recursos AKS usando o comando [az provider register][az-provider-register]:
>
> `az provider register --namespace Microsoft.ContainerService`

A captura de tela de portal de exemplo a seguir mostra a *as configurações de diagnóstico* janela:

![Habilitar o espaço de trabalho do Log Analytics para os logs do Azure Monitor de cluster AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Agendar um pod de teste no cluster do AKS

Para gerar alguns logs, crie um novo pod no seu cluster AKS. O seguinte exemplo de manifesto YAML pode ser usado para criar uma instância básica do NGINX. Crie um arquivo chamado `nginx.yaml` em um editor de sua escolha e cole o seguinte conteúdo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Crie o pod com o comando [kubectl create][kubectl-create] e especifique seu arquivo YAML, conforme mostrado no exemplo a seguir:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Exibir logs coletados

Pode levar alguns minutos para que os logs de diagnósticos sejam habilitados e apareçam no espaço de trabalho do Log Analytics. No portal do Azure, selecione o grupo de recursos para seu espaço de trabalho do Log Analytics, como *myResourceGroup*, escolha o recurso do log analytics, como *myAKSLogs*.

![Selecione o espaço de trabalho do Log Analytics para seu cluster AKS](media/view-master-logs/select-log-analytics-workspace.png)

No lado esquerdo, escolha **Logs**. Para visualizar o *kube-apiserver*, digite a seguinte consulta na caixa de texto:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Provavelmente, muitos logs são retornados para o servidor de API. Para fazer o escopo da consulta para visualizar os logs sobre o pod NGINX criado na etapa anterior, adicione uma instrução adicional *onde* para pesquisar *pods/nginx*, conforme mostrado na consulta de exemplo a seguir:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Os logs específicos do seu pod NGINX são exibidos, conforme mostrado na captura de tela de exemplo a seguir:

![Resultados da consulta do Log Analytics para amostra de pod NGINX](media/view-master-logs/log-analytics-query-results.png)

Para visualizar logs adicionais, é possível atualizar a consulta do nome *Categoria* para *kube-controller-manager* ou *kube-scheduler*, dependendo de quais logs adicionais você ativar . *adicional, onde as declarações* podem ser usadas para refinar os eventos que você está procurando.

Para obter mais informações sobre como consultar e filtrar seus dados de log, consulte [Visualizar ou analisar dados coletados com a pesquisa de registros da análise de logs][analyze-log-analytics].

## <a name="log-event-schema"></a>Esquema do log de eventos

Para ajudar a analisar os dados de log, a tabela a seguir fornece detalhes sobre o esquema usado para cada evento:

| Nome do campo               | DESCRIÇÃO |
|--------------------------|-------------|
| *resourceId*             | Recursos do Azure que produziu o log |
| *time*                   | Carimbo de data / hora de quando o log foi carregado |
| *category*               | Nome do contêiner / componente que gera o log |
| *operationName*          | Sempre *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | Texto completo do log do componente |
| *properties.stream*      | *stderr* ou *stdout* |
| *Properties.pod*         | Nome do pod que vieram de log |
| *properties.containerID* | ID do contêiner docker que esse log veio |

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como ativar e revisar os logs dos componentes principais do Kubernetes no seu cluster AKS. Para monitorar e solucionar problemas adicionais, você também pode [visualizar os logs do Kubelet][kubelet-logs] e [ativar o acesso ao nó do SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
