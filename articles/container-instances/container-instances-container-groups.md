---
title: Introdução aos grupos de contêineres
description: Saiba mais sobre grupos de contêineres em instâncias de contêiner do Azure, uma coleção de instâncias que compartilham um ciclo de vida e recursos como armazenamento e rede
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: c4d5217fe96ca2669397bb7f2a94c6394c002534
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896591"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contêineres em Instâncias de Contêiner do Azure

O recurso de nível superior em Instâncias de Contêiner do Azure é um *grupo de contêineres*. Este artigo descreve o que são os grupos de contêineres e os tipos de cenários que eles permitem.

## <a name="what-is-a-container-group"></a>O que é um grupo de contêineres?

Um grupo de contêineres é uma coleção de contêineres que são agendados no mesmo computador host. Os contêineres em um grupo de contêineres compartilham um ciclo de vida, recursos, rede local e volumes de armazenamento. Ele é semelhante em conceito a um *Pod* em [kubernetes][kubernetes-pod].

O diagrama a seguir mostra um exemplo de um grupo de contêineres que inclui vários contêineres:

![Diagrama de grupos de contêineres][container-groups-example]

Este grupo de contêineres de exemplo:

* Está agendado em um único computador host.
* É atribuído um rótulo de nome DNS.
* Expõe um único endereço IP público, com uma porta exposta.
* Consiste em dois contêineres. Um contêiner escuta na porta 80, enquanto o outro escuta na porta 5000.
* Inclui dois compartilhamentos de arquivos do Azure como montagens de volume e cada contêiner monta um dos compartilhamentos localmente.

> [!NOTE]
> Atualmente, os grupos de vários contêineres dão suporte apenas a contêineres do Linux. Para contêineres do Windows, as instâncias de contêiner do Azure dão suporte apenas à implantação de uma única instância. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers)do serviço.

## <a name="deployment"></a>Implantação

Aqui estão duas maneiras comuns de implantar um grupo de vários contêineres: Use um [modelo do Resource Manager][resource-manager template] ou um [arquivo YAML][yaml-file]. Um modelo do Resource Manager é recomendado quando você precisa implantar recursos adicionais de serviço do Azure (por exemplo, um [compartilhamento de arquivos do Azure][azure-files]) ao implantar as instâncias de contêiner. Devido à natureza mais concisa do formato YAML, um arquivo YAML é recomendado quando sua implantação inclui apenas instâncias de contêiner. Para obter detalhes sobre as propriedades que você pode definir, consulte a [referência de modelo do Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) ou a documentação de [referência do YAML](container-instances-reference-yaml.md) .

Para preservar a configuração de um grupo de contêineres, você pode exportar a configuração para um arquivo YAML usando o comando de CLI do Azure [AZ contêiner Export][az-container-export]. A exportação permite que você armazene as configurações do grupo de contêineres no controle de versão para "configuração como código". Ou então, usar o arquivo exportado como ponto de partida ao desenvolver uma nova configuração em YAML.



## <a name="resource-allocation"></a>Alocação de recurso

As instâncias de contêiner do Azure alocam recursos como CPUs, memória e, opcionalmente, [GPUs][gpus] (visualização) a um grupo de contêineres adicionando as [solicitações de recurso][resource-requests] das instâncias no grupo. Usando recursos de CPU como exemplo, se você criar um grupo de contêineres com duas instâncias, cada uma solicitando 1 CPU, o grupo de contêineres tem 2 CPUs alocadas.

### <a name="resource-usage-by-instances"></a>Uso de recursos por instâncias

Cada instância de contêiner é alocada aos recursos especificados em sua solicitação de recurso. No entanto, o uso de recursos por uma instância de contêiner em um grupo depende de como você configura sua propriedade de [limite de recurso][resource-limits] opcional. O limite de recursos deve ser menor que a propriedade obrigatória de [solicitação de recursos][resource-requests] .

* Se você não especificar um limite de recurso, o uso máximo de recursos da instância será o mesmo que sua solicitação de recurso.

* Se você especificar um limite de recurso para uma instância, poderá ajustar o uso de recursos da instância para sua carga de trabalho, reduzindo ou aumentando o uso relativo à solicitação de recurso. O limite máximo de recursos que você pode definir é o total de recursos alocados para o grupo.
    
Por exemplo, em um grupo com duas instâncias solicitando 1 CPU, um de seus contêineres pode executar uma carga de trabalho que exige mais CPUs para execução do que a outra.

Nesse cenário, você pode definir um limite de recursos de CPU de 0,5 para uma instância e um limite de 2 CPUs para o segundo. Essa configuração limita o uso de recursos do primeiro contêiner a 0,5 de CPU, permitindo que o segundo contêiner use até as 2 CPUs completas, se disponíveis.

Para obter mais informações, consulte a propriedade [ResourceRequirements][resource-requirements] na API REST dos grupos de contêineres.

### <a name="minimum-and-maximum-allocation"></a>Alocação mínima e máxima

* Aloque um **mínimo** de 1 CPU e 1 GB de memória para um grupo de contêineres. As instâncias de contêiner individuais dentro de um grupo podem ser provisionadas com menos de 1 CPU e 1 GB de memória. 

* Para obter o **máximo** de recursos em um grupo de contêineres, consulte [disponibilidade de recursos][region-availability] para instâncias de contêiner do Azure na região de implantação.

## <a name="networking"></a>Rede

Os grupos de contêineres podem compartilhar um endereço IP voltado para o externo e um namespace de porta nesse endereço IP. Para permitir que clientes externos alcancem um contêiner dentro do grupo, você deve expor a porta no endereço IP e do contêiner. Como os contêineres no grupo compartilham um namespace de porta, o mapeamento de porta não tem suporte. 

Dentro de um grupo de contêineres, as instâncias de contêineres podem chegar umas às outras por meio de localhost em qualquer porta, mesmo se essas portas não estiverem expostas externamente no endereço IP do grupo ou no contêiner.

Opcionalmente, implante grupos de contêineres em uma [rede virtual do Azure][virtual-network] (versão prévia) para permitir que os contêineres se comuniquem com segurança com outros recursos na rede virtual.

## <a name="storage"></a>Armazenamento

Você pode especificar volumes externos para montar dentro de um grupo de contêineres. Você pode mapear os volumes para caminhos específicos dentro dos contêineres individuais em um grupo.

## <a name="common-scenarios"></a>Cenários comuns

Grupos de vários contêineres são úteis quando você deseja dividir uma única tarefa funcional em um pequeno número de imagens de contêiner. Essas imagens podem, então, ser fornecidas por equipes diferentes e têm requisitos de recursos separados.

Os exemplos de uso podem incluir:

* Um contêiner que atende a um aplicativo Web e um contêiner efetuando pull do conteúdo mais recente do controle do código-fonte.
* Um contêiner de aplicativo e um contêiner de log. O contêiner de log coleta logs e métricas de saída do aplicativo principal e grava-as em armazenamento de longo prazo.
* Um contêiner de aplicativo e um contêiner de monitoramento. O contêiner de monitoramento faz uma solicitação periódica ao aplicativo para garantir que ele esteja em execução e respondendo corretamente e emite um alerta em caso negativo.
* Um contêiner de front-end e um contêiner de back-end. O front-end pode servir a um aplicativo Web, com o back-end executando um serviço para recuperar dados. 

## <a name="next-steps"></a>Próximos passos

Saiba como implantar um grupo de contêineres com vários contêineres com um modelo do Azure Resource Manager:

> [!div class="nextstepaction"]
> [Implantar um grupo de contêineres][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
