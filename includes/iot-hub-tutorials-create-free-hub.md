---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a7a86c6a2661a8a1f30491391fc76f4dc5d71f54
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66163716"
---
Para criar um Hub IoT usando o Portal do Azure:

1. Entre no [Portal do Azure](http://portal.azure.com).

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

    ![Selecione para instalar o Hub IoT](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Para criar o hub IoT gratuito, use os valores na tabela abaixo:

    | Configuração | Valor |
    | ------- | ----- |
    | Assinatura | Na lista suspensa, selecione sua assinatura do Azure. |
    | Grupo de recursos | Criar novo. Este tutorial usa o nome **tutoriais-iot hub rg**. |
    | Região | Este tutorial usa **Oeste dos EUA**. Escolha a região mais próxima a você. |
    | NOME | A captura de tela a seguir usa o nome **tutorials-iot-hub**. Você precisa escolher seu próprio nome exclusivo ao criar seu hub. |

    ![Configurações de hub 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo e escala de preço | F1 Gratuito. Você só pode ter um hub de camada gratuita em uma assinatura. |
    | Unidades do Hub IoT | 1 |

    ![Configurações de hub 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Clique em **Criar**. Pode demorar alguns minutos para que o hub seja criado.

    ![Configurações de hub 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Anote o nome do hub IoT escolhido. Você usará esse valor posteriormente no tutorial.