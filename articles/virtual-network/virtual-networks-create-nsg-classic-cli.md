---
title: Criar um grupo de segurança de rede (clássico) usando o CLI clássico do Azure | Microsoft Docs
description: Aprenda a criar e implantar um grupo de segurança de rede (clássico) usando a CLI clássica do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 5e81fa709589e5fa4a109cdf7709b87bc446ac3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947227"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Criar um grupo de segurança de rede (clássico) usando a CLI clássica do Azure
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Também é possível [criar NSGs no modelo de implantação do Gerenciador de Recursos](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Os exemplos de comandos da CLI do Azure a seguir esperam um ambiente simples já criado com base no cenário. Se você quiser executar os comandos da forma como eles aparecem neste documento, primeiro crie o ambiente de teste [criando uma VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Criar um NSG para a sub-rede de front-end

1. Se você nunca utilizou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure ](/cli/azure/install-cli-version-1.0).
2. Alterne para o modo clássico:

    ```azurecli
    azure config mode asm
    ```   

3. Crie um NSG:
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Crie uma regra de segurança que permita o acesso à porta 3389 (RDP) da Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Crie uma regra que permita o acesso à porta 80 (HTTP) da Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Associe o NSG à sub-rede de front-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Criar o NSG para a sub-rede de back-end

1. Criar o NSG:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Crie uma regra que permita acesso à porta 1433 (SQL) da sub-rede front-end:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Crie uma regra que negue acesso à Internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Associe o NSG à sub-rede de back-end:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```