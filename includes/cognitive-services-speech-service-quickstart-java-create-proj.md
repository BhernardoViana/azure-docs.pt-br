---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 89db2c9e1cac7337b0f3bcd9fdf733e8db410d02
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675539"
---
1. Inicie o Eclipse.

1. No Inicializador do Eclipse, no campo **Espaço de Trabalho**, insira o nome de um novo diretório de workspace. Em seguida, selecione **Iniciar**.

   ![Captura de tela do Inicializador do Eclipse](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Em alguns instantes, a janela principal do IDE do Eclipse aparece. Feche a tela de **boas-vindas** caso haja uma.

1. Na barra de menus do Eclipse, crie um novo projeto escolhendo **Arquivo** > **Novo** > **Projeto**.

1. A caixa de diálogo **Novo Projeto** aparecerá. Selecione **Projeto Java** e selecione **Avançar**.

   ![Captura de tela da caixa de diálogo Novo Projeto, com Projeto Java realçado](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. O assistente **Novo Projeto em Java** é iniciado. No campo **Nome do projeto**, insira **quickstart** e escolha **JavaSE-1.8** como o ambiente de execução. Selecione **Concluir**.

   ![Captura de tela do assistente de Novo Projeto Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Se a janela **Abrir Perspectiva Associada?** aparecer, selecione **Abrir Perspectiva**.

1. No **Explorador de pacotes**, clique com o botão direito do mouse no projeto **quickstart**. Escolha **Configurar** > **Converter para Projeto Maven** no menu de contexto.

   ![Captura de tela do Explorador de pacotes](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. A janela **Criar novo POM** é exibida. No campo **ID do Grupo**, insira *com.microsoft.cognitiveservices.speech.samples* e, no campo **ID do Artefato**, insira *início rápido*. Em seguida, selecione **Concluir**.

   ![Captura de tela da janela Criar novo POM](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Abra o arquivo *pom.xml* e edite-o.

   * No final do arquivo, antes da marca de fechamento `</project>`, crie um elemento `repositories` com uma referência ao repositório Maven para o SDK de Fala, como mostrado aqui:

     [!code-xml[POM repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

   * Adicione também um elemento `dependencies`, com o SDK de Fala versão 1.7.0 como uma dependência:

     [!code-xml[POM dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Salve as alterações.
