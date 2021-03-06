---
title: Instale as Ferramentas do Azure Data Lake para Visual Studio
description: Este artigo descreve como instalar o Azure Data Lake Tools para Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914062"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalar Ferramentas do Data Lake para Visual Studio

Saiba como usar o Visual Studio para criar contas de Azure Data Lake Analytics. Você pode definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md) e enviar trabalhos para o serviço de data Lake Analytics. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: Todas as edições, exceto Express, têm suporte.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **SDK do Microsoft Azure para .NET** versão 2.7.1 ou posterior. Instale-o usando o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Uma conta do **Data Lake Analytics**. Para criar uma conta, confira [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Instalar o Ferramentas do Azure Data Lake para Visual Studio 2017 ou o Visual Studio 2019

Ferramentas do Azure Data Lake para Visual Studio tem suporte no Visual Studio 2017 15,3 ou posterior. A ferramenta faz parte das cargas de trabalho de **processamento e armazenamento de dados** e de **desenvolvimento do Azure** . Habilite qualquer uma dessas duas cargas de trabalho como parte da instalação do Visual Studio.

Habilite a carga de trabalho de **Armazenamento e processamento de dados** conforme mostrado:

![Habilitar o armazenamento de dados e o processamento de carga de trabalho](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Habilite a carga de trabalho de **Desenvolvimento do Azure** conforme mostrado:

![Selecionar carga de trabalho de desenvolvimento do Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instale as Ferramentas do Azure Data Lake para Visual Studio 2013 e 2015

Baixe e instale o [Microsoft Azure data Lake e as ferramentas de Stream Analytics para Visual Studio ](https://aka.ms/adltoolsvs). Após a instalação, o Visual Studio tem as seguintes alterações:

* O nó do **Gerenciador de Servidores** > do **Azure** contém um nó do **Data Lake Analytics**.
* O menu de **ferramentas** tem um item do **Data Lake**.

## <a name="next-steps"></a>Próximas etapas

* Para registrar em log as informações de diagnóstico, veja [Acessando os logs de diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
* Para usar o modo de exibição de execução de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
