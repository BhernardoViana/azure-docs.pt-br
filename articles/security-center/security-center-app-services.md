---
title: Proteger seus aplicativos Web e APIs do serviço de Azure App
description: Este artigo ajuda você a começar a proteger seus aplicativos Web e APIs do serviço de Azure App na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: be9331ccd548628bfc27172c4f6e625bdba1632c
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158901"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteger seus aplicativos Web e APIs do serviço de Azure App

Azure App serviço é uma plataforma totalmente gerenciada para criar e hospedar seus aplicativos Web e APIs sem se preocupar em ter de gerenciar a infraestrutura. Ele fornece gerenciamento, monitoramento e informações operacionais para atender aos requisitos de desempenho, segurança e conformidade de nível empresarial. Para obter mais informações, consulte [Azure app Service](https://azure.microsoft.com/services/app-service/).

Para habilitar a proteção avançada contra ameaças para seu plano de serviço Azure App, você deve:

* Assinar o tipo de preço padrão da central de segurança do Azure
* Habilite o plano do serviço de aplicativo, conforme mostrado abaixo. A central de segurança é integrada nativamente com o serviço de aplicativo, eliminando a necessidade de implantação e integração-o é transparente.
* Ter um plano do serviço de aplicativo associado a computadores dedicados. Os planos com suporte são: básico, Standard, Premium, isolado ou Linux. A central de segurança não dá suporte aos planos gratuito, compartilhado ou de consumo. Para obter mais informações, consulte [planos de Serviço de Aplicativo do Azure](https://azure.microsoft.com/pricing/details/app-service/plans/).

Com o plano do serviço de aplicativo habilitado, a central de segurança avalia os recursos cobertos pelo seu plano do serviço de aplicativo e gera recomendações de segurança com base em suas descobertas. A central de segurança protege a instância de VM na qual seu serviço de aplicativo está em execução e a interface de gerenciamento. Ele também monitora solicitações e respostas enviadas para e de seus aplicativos em execução no Serviço de Aplicativo.

A central de segurança aproveita a escala da nuvem e a visibilidade que o Azure tem como um provedor de nuvem para monitorar ataques comuns de aplicativos Web. A central de segurança pode descobrir ataques em seus aplicativos e identificar ataques emergentes – mesmo enquanto os invasores estão na fase de reconhecimento, a verificação para identificar vulnerabilidades em vários aplicativos hospedados no Azure. Como um serviço nativo do Azure, a central de segurança também está em uma posição exclusiva para oferecer análise de segurança baseada em host que abrange os nós de computação subjacentes para essa PaaS, permitindo que a central de segurança detecte ataques contra aplicativos Web que já foram explorados.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Ativando o monitoramento e a proteção do Serviço de Aplicativo

1. Na portal do Azure, escolha central de segurança.
2. Vá para **preços & configurações** e escolha uma assinatura.
3. Em **Nível de preços**, na linha **Serviço de aplicativos**, alterne seu plano para **Ativado**.

    [![habilitar serviços de aplicativos na sua assinatura de camada Standard](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> O número de instâncias listadas para a **quantidade de recursos** representa o número total de instâncias de computação, em todos os planos do serviço de aplicativo nesta assinatura, em execução no momento em que você abriu a folha tipo de preço.
>
> O Azure App Service oferece uma variedade de planos. Seu plano do serviço de aplicativo define o conjunto de recursos de computação para um aplicativo Web a ser executado. Eles são equivalentes aos farms de servidores na hospedagem Web convencional. Um ou mais aplicativos podem ser configurados para ser executado nos mesmos recursos de computação (ou no mesmo plano de Serviço de Aplicativo).
>
>Para validar a contagem, vá para "planos do serviço de aplicativo" no portal do Azure, no qual você pode ver o número de instâncias de computação usadas por cada plano. 






Para desativar o monitoramento e as recomendações do seu serviço de aplicativos, repita esse processo e ative o plano do **Serviço de Aplicativo do Azure** para **Desativado**.



## <a name="see-also"></a>Consulte também
Neste artigo, você aprendeu como usar os recursos de monitoramento na Central de segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, leia os seguintes artigos:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md): saiba como configurar políticas de segurança na Central de Segurança do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Serviços de aplicativos](security-center-virtual-machine-protection.md#app-services): exiba uma lista de seus ambientes de serviço de aplicativo com resumos de integridade.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure.
