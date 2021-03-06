---
title: Versão prévia da implantação do Azure Service Fabric Docker Compose
description: O Azure Service Fabric aceita Docker Compose formato para facilitar a orquestração de contêineres existentes usando Service Fabric. No momento, esse suporte está em versão prévia.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: d9d135136efea72017399b5888bc6591582ffe67
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553567"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Suporte à implantação do Docker Compose no Azure Service Fabric (versão prévia)

O Docker usa o arquivo [Docker-Compose. yml](https://docs.docker.com/compose) para definir aplicativos de vários contêineres. Para facilitar para os clientes familiarizados com o Docker para orquestrar os aplicativos de contêiner existentes no Azure Service Fabric, incluímos o suporte de visualização para Docker Compose implantação nativamente na plataforma. Service Fabric pode aceitar a versão 3 e posterior de arquivos `docker-compose.yml`. 

Como esse suporte está em visualização, há suporte para apenas um subconjunto de diretivas de composição.

Para usar essa visualização, crie seu cluster com a versão 5,7 ou superior do tempo de execução Service Fabric por meio do portal do Azure junto com o SDK correspondente. 

> [!NOTE]
> Este recurso está em visualização e não tem suporte na produção.
> Os exemplos a seguir são baseados na versão de tempo de execução 6,0 e na versão 2,8 do SDK.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Implantar um arquivo de Docker Compose no Service Fabric

Os comandos a seguir criam um aplicativo Service Fabric (chamado `fabric:/TestContainerApp`), que você pode monitorar e gerenciar como qualquer outro aplicativo Service Fabric. Você pode usar o nome do aplicativo especificado para consultas de integridade.
Service Fabric reconhece "Deploymentname" como o identificador da implantação de Compose.

### <a name="use-powershell"></a>Use o PowerShell

Crie uma implantação de redação de Service Fabric de um arquivo Docker-Compose. yml executando o seguinte comando no PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` e `RegistryPassword` consulte o nome de usuário e a senha do registro de contêiner. Depois de concluir a implantação, você pode verificar seu status usando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Para excluir a implantação de Compose por meio do PowerShell, use o seguinte comando:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Para iniciar uma atualização de implantação do Compose por meio do PowerShell, use o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Para reverter a atualização da implantação do Compose por meio do PowerShell, use o seguinte comando:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Após a atualização ser aceita, o andamento da atualização pode ser rastreado usando o seguinte comando:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Usar a CLI do Azure Service Fabric (sfctl)

Como alternativa, você pode usar o seguinte comando da CLI Service Fabric:

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Depois de criar a implantação, você pode verificar seu status usando o seguinte comando:

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Para excluir a implantação de Compose, use o seguinte comando:

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Para iniciar uma atualização da implantação do Compose, use o seguinte comando:

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Para reverter a atualização da implantação do Compose, use o seguinte comando:

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Após a atualização ser aceita, o andamento da atualização pode ser rastreado usando o seguinte comando:

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Diretivas de Compose suportadas

Essa visualização dá suporte a um subconjunto das opções de configuração do formato da versão 3 do Compose, incluindo os primitivos a seguir:

* Serviços > implantar > réplicas
* Serviços > implantar restrições de > de posicionamento de >
* Os serviços > implantar > recursos > limites
    * -CPU-compartilhamentos
    * -memória
    * -troca de memória
* Comandos de > de serviços
* Ambiente de > de serviços
* Portas de > de serviços
* Imagem de > de serviços
* Isolamento de > de serviços (somente para Windows)
* Driver de > de log de > de serviços
* Opções de > do driver de > de serviços de log >
* Volume & implantar > volume

Configure o cluster para impor limites de recursos, conforme descrito em [Service Fabric governança de recursos](service-fabric-resource-governance.md). Todas as outras diretivas de Docker Compose não têm suporte para essa versão prévia.

### <a name="ports-section"></a>Seção de portas

Especifique o protocolo http ou HTTPS na seção portas que será usada pelo ouvinte de serviço Service Fabric. Isso garantirá que o protocolo de ponto de extremidade seja publicado corretamente com o serviço de cadastramento para permitir que o proxy reverso encaminhe as solicitações:
* Para rotear para serviços de composição de Service Fabric não seguros, especifique **/http**. Por exemplo,- **"80:80/http"** .
* Para rotear para os serviços de composição de Service Fabric seguros, especifique **/https**. Por exemplo,- **"443:443/https"** .

> [!NOTE]
> A sintaxe da seção de portas/http e/HTTPS é específica para Service Fabric registrar a URL de ouvinte de Service Fabric correta.  Se a sintaxe do arquivo Docker Compose for validada programaticamente, isso poderá causar um erro de validação.

## <a name="servicednsname-computation"></a>Computação ServiceDnsName

Se o nome do serviço que você especificar em um arquivo de composição for um nome de domínio totalmente qualificado (ou seja, ele contiver um ponto [.]), o nome DNS registrado por Service Fabric será `<ServiceName>` (incluindo o ponto). Se não, cada segmento de caminho no nome do aplicativo se tornará um rótulo de domínio no nome DNS do serviço, com o primeiro segmento de caminho se tornando o rótulo de domínio de nível superior.

Por exemplo, se o nome do aplicativo especificado for `fabric:/SampleApp/MyComposeApp`, `<ServiceName>.MyComposeApp.SampleApp` será o nome DNS registrado.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Composição de implantação (definição de instância) versus modelo de aplicativo de Service Fabric (definição de tipo)

Um arquivo Docker-Compose. yml descreve um conjunto implantável de contêineres, incluindo suas propriedades e configurações.
Por exemplo, o arquivo pode conter variáveis de ambiente e portas. Você também pode especificar parâmetros de implantação, como restrições de posicionamento, limites de recursos e nomes DNS, no arquivo Docker-Compose. yml.

O [modelo de aplicativo Service Fabric](service-fabric-application-model.md) usa tipos de serviço e tipos de aplicativo, onde você pode ter muitas instâncias de aplicativo do mesmo tipo. Por exemplo, você pode ter uma instância de aplicativo por cliente. Esse modelo baseado em tipo dá suporte a várias versões do mesmo tipo de aplicativo registradas com o tempo de execução.

Por exemplo, o cliente A pode ter um aplicativo instanciado com o tipo 1,0 de AppTypeA, e o cliente B pode ter outro aplicativo instanciado com o mesmo tipo e versão. Você define os tipos de aplicativo nos manifestos do aplicativo e especifica o nome do aplicativo e os parâmetros de implantação ao criar o aplicativo.

Embora esse modelo ofereça flexibilidade, também estamos planejando oferecer suporte a um modelo de implantação baseado em instância mais simples, em que os tipos são implícitos no arquivo de manifesto. Nesse modelo, cada aplicativo obtém seu próprio manifesto independente. Estamos visualizando esse esforço adicionando suporte para Docker-Compose. yml, que é um formato de implantação baseado em instância.

## <a name="next-steps"></a>Próximos passos

* Leia sobre o [modelo de aplicativo Service Fabric](service-fabric-application-model.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
