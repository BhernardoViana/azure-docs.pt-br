---
title: Guia de configurações de oferta de máquina virtual no Portal do Cloud Partner para Azure Marketplace
description: Descreve a guia Configurações da oferta usada na criação de uma oferta de VM do Marketplace do Azure.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: d361b6b8b08f9556cd57215ebdf82c1bf69d372d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938163"
---
# <a name="virtual-machine-offer-settings-tab"></a>Guia de configuração da oferta de máquina virtual

A página **Nova oferta** para máquinas virtuais é aberta na primeira guia chamada **Offer Settings**.  

![Nova página de oferta para máquinas virtuais](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Campos de Configurações da Oferta

No **oferecer configurações** guia, você deve fornecer os campos a seguir.  Um asterisco anexado (*) no nome do campo indica que isso é necessário. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***   | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador ficará visível em URLs de produtos, modelos do Azure Resource Manager e relatórios de faturamento. Ele tem um comprimento máximo de 50 caracteres, só pode ser composto de caracteres alfanuméricos minúsculos e traços (-), mas não pode terminar em um traço. Este campo não pode ser alterado depois que uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID de oferta **sample-vm**, ela é atribuída ao URL do Azure Marketplace`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Publisher\***  | Identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas a sua ID do publicador. Este valor não pode ser modificado quando a oferta é salva. |
| **Nome\***       | Nome de exibição da sua oferta. Este nome será exibido no Azure Marketplace e no Portal do Cloud Partner. Ele pode ter um máximo de 50 caracteres. A orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que seja assim que é comercializado. Se você estiver promovendo essa oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|   |   |
 
Clique em **salvar** depois que você tiver fornecido a todos os campos. 


## <a name="next-steps"></a>Próximas etapas

A próxima guia, você adicionará [SKUs](./cpp-skus-tab.md) para sua oferta.
