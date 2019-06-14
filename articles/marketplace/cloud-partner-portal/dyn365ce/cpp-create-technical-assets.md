---
title: Criar o Dynamics 365 para ativos técnicos do envolvimento do cliente | O Azure Marketplace
description: Crie os ativos técnicos para uma oferta de aplicativo do Dynamics 365 for Customer Engagement.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/29/2018
ms.author: pabutler
ms.openlocfilehash: eff175264677d6b8ffb885229b5e68b306424335
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943099"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Criar ativos técnicos para uma oferta de aplicativo do Azure

Normalmente, você desenvolve soluções usando o [SDK para aplicativos do Dynamics 365 for Customer Engagement](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  As soluções usam uma variedade de formas, conforme descrito em [Modelos de programação para aplicativos do Dynamics 365 for Customer Engagement](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models).  Escolha a forma que esteja em melhor conformidade com os requisitos de sua solução.  Ao desenvolver uma solução, há uma série de questões que você precisa abordar, como opções de extensibilidade, componentes da solução e compatibilidade de versão.  Para obter mais informações, confira [Introdução às soluções](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

A maioria das soluções do Dynamics 365 publicadas no AppSource são aplicativos gerenciados distribuídos como arquivos de pacote.


## <a name="creating-and-storing-the-package"></a>Criando e armazenando o pacote

A documentação paralela sobre a criação de ofertas do Dynamics 365 for Customer Engagement é encontrada na seção [Publicar seu aplicativo no AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  Os seguintes tópicos contidos fornecem detalhes de como criar o arquivo de pacote de solução e carregá-lo no Armazenamento do Azure:

- [Etapa 4: Criar um pacote do AppSource para seu aplicativo](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) – explica como criar um arquivo compactado (zip) que represente o aplicativo gerenciado e contenha: pasta de ativos da solução, DLL de código personalizado, arquivo de informações do tipo MIME, ícone de pacote do AppSource, arquivo de termos de licença (HTML) e arquivo de conteúdo (XML).
- [Etapa 5: Armazenar o pacote do AppSource no Armazenamento do Azure e gerar uma URL com a chave SAS](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) – explica como armazenar um arquivo de pacote do AppSource em uma conta do Armazenamento de Blobs do Microsoft Azure e usar uma chave SAS (Assinatura de Acesso Compartilhado) para compartilhar o arquivo de pacote. Seu arquivo de pacote é recuperado da localização do Armazenamento do Azure para certificação e, em seguida, para avaliações e publicação do AppSource.


## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso, [crie sua oferta do Dynamics 365 for Customer Engagement](./cpp-create-offer.md).  Em seguida, você estará pronto para [publicar sua oferta](./cpp-publish-offer.md).
