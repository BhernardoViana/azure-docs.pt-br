---
title: 'Lição 13 do tutorial do Azure Analysis Services: implantar | Microsoft Docs'
description: Descreve como implantar o projeto do tutorial do Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f0528af5f3a6b7309d81c36ca5bc7a3faccfa293
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427103"
---
# <a name="deploy"></a>Implantar

Nesta lição, você configura as propriedades de implantação especificando um servidor do Azure Analysis Services para implantar e um nome para o modelo. Em seguida, você implanta o modelo para essa instância. Após o modelo ser implantado, os usuários podem se conectar a ele usando um aplicativo cliente de relatório. Para saber mais, confira [Implantar no Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy).  
  
Tempo estimado para conclusão desta lição: **5 minutos**  
  
## <a name="prerequisites"></a>Pré-requisitos  
Este artigo faz parte de um tutorial de modelagem tabular, que deve ser concluído na devida ordem. Antes de executar as tarefas nesta lição, você deve ter concluído a lição anterior: [Lição 12: analisar no Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> Você deve ter [permissões de Administrador](../analysis-services-server-admins.md) no servidor remoto do Analysis Services para poder implantar nele.  

> [!IMPORTANT]  
> Se você instalou o banco de dados de exemplo AdventureWorksDW2014 em um SQL Server local e está implantando seu modelo em um servidor do Azure Analysis Services, um [Gateway de dados local](../analysis-services-gateway.md) é necessário.
  
## <a name="deploy-the-model"></a>Implantar o modelo  
  
#### <a name="to-configure-deployment-properties"></a>Para configurar propriedades de implantação  

  
1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Vendas pela Internet da AW** e depois clique em **Propriedades**.  
  
2.  Na caixa de diálogo **Páginas de Propriedades de Vendas pela Internet da AW**, no **Servidor de Implantação**, na propriedade **Servidor**, digite o servidor completo.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  Na propriedade **Banco de Dados**, digite **Vendas pela Internet da Adventure Works**.  
  
4.  Na propriedade **Nome do Modelo**, digite **Modelo de Vendas pela Internet da Adventure Works**.  
  
5.  Verifique suas seleções e então clique em **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Para implantar as Vendas pela Internet da Adventure Works
  
1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Vendas pela Internet da AW** > **Compilar**.  

2.  Clique com o botão direito do mouse no projeto **Vendas pela Internet da AW** > **Implantar**.

    Ao implantar para os Azure Analysis Services, pode ser solicitado que você insira sua conta. Insira sua conta e senha organizacionais, por exemplo, nancy@adventureworks.com. Esta conta deve constar na lista Admins no servidor.
  
    A caixa de diálogo Implantar aparece e exibe o status da implantação dos metadados e cada tabela incluída no modelo.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Quando a implantação for concluída com êxito, vá em frente e clique em **Fechar**.  
  

Esta lição descreve o método mais fácil e mais comum para implantar um modelo tabular do SSDT. Opções de implantação avançadas, como o Assistente de Implantação ou automatização com XMLA e AMO fornecem maior flexibilidade, consistência e implantações agendadas. Para obter mais informações, confira [Implantação de solução de modelo tabular](https://docs.microsoft.com/sql/analysis-services/tabular-models/tabular-model-solution-deployment-ssas-tabular).

## <a name="conclusion"></a>Conclusão  
Parabéns! Você terminou de criar e implantar seu primeiro modelo tabular do Analysis Services. Este tutorial ajudou a orientá-lo a concluir as tarefas mais comuns na criação de um modelo tabular. Agora que seu modelo de vendas de Internet da Adventure Works está implantado, você pode usar o SQL Server Management Studio para gerenciar o modelo; crie scripts de processo e um plano de backup. Os usuários agora também podem se conectar ao modelo usando um aplicativo cliente de relatório, como o Microsoft Excel ou o Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>O que vem a seguir?
[Conecte-se com o Power BI Desktop](../analysis-services-connect-pbi.md)   
[Lição suplementar - Segurança dinâmica](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Lição suplementar - Linhas de detalhes](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Lição Suplementar – hierarquias desbalanceadas](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
