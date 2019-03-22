---
title: Ferramentas de desenvolvimento de Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs
description: Saiba mais sobre as ferramentas e ambientes de desenvolvimento integrado que são previamente instalados na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 7983169c2b1123c57a48471e3f4d9ad6f19c84dc
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314715"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Ferramentas de desenvolvimento na Máquina Virtual de Ciência de Dados

A DSVM (Máquina Virtual de Ciência de Dados) oferece um ambiente produtivo para seu desenvolvimento agrupando várias ferramentas populares e IDE. Aqui estão algumas ferramentas oferecidas na DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de Propósito Geral      |
| Versões do DSVM com suporte      |  Windows      |
| Usos típicos      | Desenvolvimento de software    |
| Como é configurado/instalado no DSVM?      | Carga de Trabalho de Ciência de Dados (ferramentas Python e R), carga de trabalho do Azure (Hadoop, Data Lake), Node.js, ferramentas do SQL Server, [Azure Machine Learning para o Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Como usar/executar?      | Atalho da Área de Trabalho (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Ferramentas relacionadas ao DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE de Propósito Geral      |
| Versões do DSVM com suporte      | Windows, Linux     |
| Usos típicos      | Editor de código e a integração de Git   |
| Como usar/executar?      | Atalho da Área de Trabalho (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) no Windows, atalho da área de trabalho ou terminal (`code`) no Linux    |
| Ferramentas relacionadas ao DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>Área de trabalho do RStudio 

|    |           |
| ------------- | ------------- |
| O que é?   | Cliente IDE para R    |
| Versões do DSVM com suporte      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento em R     |
| Como usar/executar?      | Atalho da Área de Trabalho (`C:\Program Files\RStudio\bin\rstudio.exe`) no Windows, Atalho da Área de Trabalho (`/usr/bin/rstudio`) no Linux      |
| Ferramentas relacionadas ao DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE baseado na Web para R    |
| Versões do DSVM com suporte      | Linux      |
| Usos típicos      |  Desenvolvimento em R     |
| Como usar/executar?      | Habilite o serviço com _systemctl enable rstudio-server_ e inicie o serviço com _systemctl start rstudio-server_. Você pode, em seguida, fazer logon RStudio Server em http:\// seu-vm-ip:8787.       |
| Ferramentas relacionadas ao DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| O que é?   | IDE do cliente para a linguagem Julia   |
| Versões do DSVM com suporte      | Windows, Linux      |
| Usos típicos      |  Desenvolvimento em Julia     |
| Como usar/executar?      | Atalho da Área de Trabalho (`C:\JuliaPro-0.5.1.1\Juno.bat`) no Windows, Atalho da Área de Trabalho (`/opt/JuliaPro-VERSION/Juno`) no Linux      |
| Ferramentas relacionadas ao DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| O que é?   | IDE do cliente para a linguagem Python    |
| Versões do DSVM com suporte      | Linux      |
| Usos típicos      |  Desenvolvimento em Python     |
| Como usar/executar?      | Atalho da Área de Trabalho (`/usr/bin/pycharm`) em Linux      |
| Ferramentas relacionadas ao DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 

|    |           |
| ------------- | ------------- |
| O que é?   | Visualização de Dados Interativa e Ferramenta de BI    |
| Versões do DSVM com suporte      |  Windows  |
| Usos típicos      |  Visualização de Dados e criação de Painéis   |
| Como usar/executar?      | Atalho da Área de Trabalho (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Ferramentas relacionadas ao DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

