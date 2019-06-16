---
title: Criar oferta de máquina Virtual no Azure Marketplace
description: Lista as etapas necessárias para criar uma nova oferta de máquina virtual para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 4cd635c6f664a5260b79e62ea72bbb86fc4e1e4f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938367"
---
# <a name="create-virtual-machine-offer"></a>Criar uma oferta de Máquina Virtual

Esta seção lista as etapas necessárias para criar uma nova solicitação de oferta de máquina virtual (VM) para o Azure Marketplace.  Cada oferta aparece como sua própria entidade no Azure Marketplace e está associada uma ou mais SKUs.  Uma oferta VM é composta dos seguintes grupos de ativos e serviços de suporte: 

![Ativos para uma oferta de VM](./media/publishvm_002.png)

onde:

|  **Grupo de ativos**   |  **Descrição**  |
|  ---------------   |  ---------------  |
|    SKUs            |  Uma SKU é a menor unidade de compra de uma oferta. Uma oferta única (classe de produto) pode ter várias SKUs associadas a ele, para diferenciar entre os recursos com suporte, os tipos de imagem VM e modelos de cobrança. |
|  Marketplace       | Contém os ativos de marketing, jurídicos e de cliente potencial e especificações.  <ul><li> Ativos de marketing incluem nome da oferta, descrição e logotipos</li> <li> Ativos jurídicos incluem uma política de privacidade, termos de uso e outras documentações legais</li>  <li> A política de gerenciamento de cliente potencial permite que você especifique como manusear os clientes potenciais do portal do usuário final do Microsoft Azure Marketplace.</li> </ul> |
| Suporte            | Contém informações de contato e política de suporte |
| Test drive         | Define os ativos que permitem que os usuários finais testam sua oferta antes de eles comprá-la |
|  |  |


## <a name="new-offer-form"></a>Novo formulário de oferta

Ao fazer logon no [Portal do Cloud Partner](https://cloudpartner.azure.com/), clique no item **+ Nova oferta** na barra de menus à esquerda. No menu resultante, clique em **Máquinas Virtuais** para exibir o formulário de **Nova Oferta** e iniciar o processo de definição de ativos para uma nova oferta de máquina virtual. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![A nova oferta de máquina virtual oferce seleção de interface do usuário](./media/publishvm_003.png)

> [!WARNING]
> Se a opção das **Máquinas Virtuais** não for exibida ou não estiver habilitada, em seguida, sua conta não tem permissão para criar esse tipo de oferta.  Verifique se você atende a todos os [Pré-requisitos](./cpp-prerequisites.md) para esse tipo de oferta, incluindo o registro para uma conta de desenvolvedor.


## <a name="next-steps"></a>Próximas etapas

Os tópicos subsequentes nesta seção espelham as guias na página**Nova Oferta** (para um tipo de oferta VM).  Cada artigo explica como usar a guia associada para definir os grupos de ativos e serviços de suporte para sua nova oferta VM.

- [Guia Configurações da Oferta](./cpp-offer-settings-tab.md)
- [Guia SKUs](./cpp-skus-tab.md)
- [Guia Test Drive](./cpp-test-drive-tab.md)
- [Guia Marketplace](./cpp-marketplace-tab.md)
- [Guia Suporte](./cpp-support-tab.md)
