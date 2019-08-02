---
title: Guia de publicação da oferta de modelo de solução de aplicativos do Azure | Azure Marketplace
description: Este artigo descreve os requisitos para publicar um modelo de solução no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: 8d02d942fce7bd51a116cc4c19eac9faca0060ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561495"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplicativos Azure: Guia de Publicação de Oferta com Modelo de Solução

Os modelos de solução são uma das principais formas de publicar uma solução no Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Use o Azure App: oferta de modelo de solução quando a solução exigir mais automação de implantação e configuração do que um VM simples. Você pode automatizar o provisionamento de uma ou mais VMs usando Aplicativos do Azure: modelos de solução. Você também pode provisionar recursos de rede e armazenamento. O tipo de oferta Aplicativos do Azure: modelos de solução oferece benefícios de automação para VMs únicas e soluções inteiras baseadas em IaaS.

Esses modelos de solução são ofertas de transação, que são implantados e faturados através do Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".


## <a name="requirements-for-solution-templates"></a>Requisitos dos modelos de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. Máquinas virtuais pagas conforme o uso (PAYGO) serão negociadas com o cliente via Microsoft e cobradas por meio da assinatura do Azure do cliente (PAYGO).  <br/> No caso do modelo traga sua própria licença (BYOL), enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você negociará os valores referentes ao licenciamento de software diretamente com o cliente.   |
|VHD (disco rígido virtual) compatível com Azure  |   As VMs devem ser criadas em Windows ou Linux.  Para obter mais informações, [confira Criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Atribuição de uso do cliente | Habilitar a atribuição de uso do cliente é necessário em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre atribuição de uso do cliente e como habilitá-lo, consulte [atribuição de uso do cliente de parceiro do Azure](./azure-partner-customer-usage-attribution.md).  |
| Usar o Managed Disks | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) é a opção padrão para discos persistentes de VMs de IaaS no Azure. Você deve usar Managed Disks em modelos de solução. <br> <br> 1. Siga as [orientações](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) e [exemplos](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) para usar Managed DISKS nos modelos de ARM do Azure para atualizar seus modelos de solução. <br> <br> 2. Siga as instruções abaixo para importar o VHD subjacente do Managed Disks para uma conta de armazenamento para publicar o VHD como uma imagem no Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Próximas etapas
Se você ainda não fez isso, [registre](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em um existente, entre no [Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
