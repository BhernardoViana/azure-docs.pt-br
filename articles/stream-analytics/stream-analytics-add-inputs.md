---
title: Adicionar uma entrada de dados aos trabalhos do Azure Stream Analytics
description: Saiba como conectar uma fonte de dados ao trabalho do Stream Analytics como fluxo de entrada de dados de Hubs de Eventos ou dados de referência armazenamento de Blob.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 13c3c948fbe24d5536b32967c8394060ee898377
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Adicionar um fluxo de entrada ou dados de referência a um trabalho do Stream Analytics
Saiba como conectar uma fonte de dados ao trabalho do Stream Analytics como fluxo de entrada de dados de Hubs de Eventos ou dados de referência do armazenamento de Blobs.

Os trabalhos do Stream Analytics do Azure podem ser conectados a uma ou mais entradas de dados, o que define uma conexão com uma fonte de dados existente. Como os dados são enviados a essa fonte de dados, eles são consumidos pelo trabalho do Stream Analytics e processados em tempo real como dados de streaming. O Stream Analytics tem integração de primeira classe com [Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e [Armazenamento de Blobs do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md) dentro e fora da assinatura do trabalho.

Este artigo é uma etapa do [roteiro de aprendizagem do Stream Analytics](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Entrada de dados: dados de transmissão e dados de referência
Há dois tipos de entradas diferentes no Stream Analytics: fluxos de dados e dados de referência.

* **Fluxos de dados**: os trabalhos do Stream Analytics devem incluir pelo menos uma entrada de fluxo de dados para ser consumida e transformada pelo trabalho. O Armazenamento de Blob do Azure e os Hubs de Eventos do Azure têm suporte como fontes de entrada de fluxo de dados. Os Hubs de Eventos do Azure são usados para coletar fluxos de eventos de dispositivos conectados, serviços e aplicativos. O armazenamento Blob do Azure pode ser usado como uma fonte de entrada para ingerir dados em massa como um fluxo.  
* **Dados de referência**: o Stream Analytics oferece suporte a um segundo tipo de entrada auxiliar chamado de dados de referência.  Ao contrário dos dados em movimento, esses dados são estáticos ou se modificam lentamente.  Geralmente, são usados para executar pesquisas e correlações com fluxos de dados a fim de criar um conjunto de dados mais rico.  O Armazenamento de Blob do Azure é a única fonte de entrada com suporte para dados de referência.  

Para adicionar uma entrada ao trabalho do Stream Analytics:

1. No portal do Azure, clique em **Entradas** e, em seguida, em **Adicionar uma Entrada** em seu trabalho do Stream Analytics.
   
    ![Portal do Azure – adicione uma entrada.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    No portal do Azure, clique no bloco **Entradas** no seu trabalho do Stream Analytics.  
   
    ![Portal do Azure - adicionar entrada de dados.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Especifique o tipo de entrada: **Fluxo de dados** ou **Dados de referência**.
   
    ![Adicionar a entrada de dados correta, transmitidos por streaming ou de referência](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Adicionar a entrada de dados correta, transmitidos por streaming ou de referência](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. Se criar uma entrada de Fluxo de Dados, especifique o tipo de fonte para a entrada.  Essa etapa pode ser ignorada durante a criação dos Dados de Referência, pois atualmente há suporte apenas para o armazenamento de Blob.
   
    ![Adicionar a entrada de dados do streaming de dados](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Adicionar a entrada de dados do streaming de dados](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Forneça um nome amigável para essa entrada na caixa Alias de Entrada.  Esse nome será usado na consulta do trabalho posteriormente para fazer referência à entrada.
   
    Preencha o restante das propriedades de conexão necessárias para se conectar à fonte de dados. 
   
    ![Adicionar entrada de dados do hub de eventos](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Especifique as configurações de serialização para os dados de entrada:
   
   * Para garantir que suas consultas funcionem da maneira esperada, especifique o **formato de serialização de evento** de dados de entrada.  Os formatos de serialização com suporte são JSON, CSV e Avro. Verifique se o formato JSON está alinhado com a especificação e não inclui um 0 à esquerda para números decimais.
   * Verifique a **Codificação** para os dados.  UTF-8 é o único formato de codificação com suporte no momento.
     
     ![Configurações de serialização de dados para entrada de dados](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Configurações de serialização de dados para entrada de dados](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. Depois de concluir a criação da entrada, o Stream Analytics verificará se ele pode se conectar à fonte de entrada.  Você pode exibir o status da operação Testar Conexão no Hub de notificação.
   
    ![Testar conexão da entrada de dados de streaming](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Testar conexão da entrada de dados de streaming](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Obter ajuda com a transmissão de entradas de dados
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

