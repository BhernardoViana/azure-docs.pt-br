---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141220"
---
Devido ao desenvolvimento contínuo, a versão do SDK do Android instalada no Android Studio pode não corresponder à versão no código. O SDK do Android referenciado neste tutorial é a versão 26, a mais recente no momento desta edição. O número da versão pode aumentar à medida que aparecem novas versões do SDK e é recomendável usar a versão mais recente disponível.

Dois sintomas da incompatibilidade de versão são:

- Quando você criar ou recriar o projeto, pode receber mensagens de erro de Gradle como `Gradle sync failed: Failed to find target with hash string 'android-XX'`.
- Os objetos Android padrão no código que deve ser resolvido com base em instruções `import` podem gerar mensagens de erro.

Se um deles for exibido, a versão do SDK do Android instalado no Android Studio poderá não coincidir com o destino do SDK do projeto baixado. Para verificar a versão, faça as seguintes alterações:

1. No Android Studio, clique em **Ferramentas** > **Android** > **Gerenciador de SDK**. Se você não tiver instalado a versão mais recente da plataforma do SDK, clique para instalá-la. Anote o número da versão.

2. Na guia **Gerenciador de Projetos**, em **Scripts Gradle**, abra o arquivo **build.gradle (módulo: aplicativo)**. Verifique se **compileSdkVersion** e **targetSdkVersion** estão definidos para a versão do SDK mais recente instalada. O `build.gradle` pode ter esta aparência:

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
