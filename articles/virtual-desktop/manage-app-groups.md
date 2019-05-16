---
title: Gerenciar grupos de aplicativos para a Versão Prévia da Área de Trabalho Virtual do Windows – Azure
description: Descreve como configurar locatários da Versão Prévia da Área de Trabalho Virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f0cdd28be8c6e7390aa26fdc2dfbf32ec5542c2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233913"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Tutorial: Gerenciar grupos de aplicativos para a Versão Prévia da Área de Trabalho Virtual do Windows

O grupo de aplicativos padrão criado para um novo pool de hosts da Versão Prévia da Área de Trabalho Virtual do Windows também publica a área de trabalho completa. Além disso, você pode criar um ou mais grupos de aplicativos do RemoteApp para o pool de hosts. Siga este tutorial para criar um grupo de aplicativos do RemoteApp e publicar aplicativos individuais do menu Iniciar.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo do RemoteApp.
> * Permitir acesso a RemoteApps.

Antes de começar, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), o módulo da Área de Trabalho Virtual do Windows a ser usado na sessão do PowerShell, caso ainda não tenha feito isso.

## <a name="create-a-remoteapp-group"></a>Criar um grupo do RemoteApp

1. Execute o seguinte cmdlet do PowerShell para criar um grupo de aplicativos vazio do RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcional) Para verificar se o grupo de aplicativos foi criado, execute o cmdlet a seguir para ver uma lista de todos os grupos de aplicativos para o pool de hosts.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Execute o cmdlet a seguir para obter uma lista de aplicativos do menu Iniciar na imagem de máquina virtual do pool de hosts. Anote os valores de **FilePath**, **IconPath**, **IconIndex** e outras informações importantes do aplicativo que deseja publicar.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Execute o cmdlet a seguir para instalar o aplicativo com base no alias do aplicativo. O alias do aplicativo fica visível quando você executa a saída da etapa 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Opcional) Execute o cmdlet a seguir para publicar um novo RemoteApp no grupo de aplicativos criado na etapa 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Para verificar se o aplicativo foi publicado, execute o cmdlet a seguir.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Repita as etapas 1 a 5 para cada aplicativo que deseja publicar para esse grupo de aplicativos.
8. Execute o cmdlet a seguir para permitir aos usuários acesso aos RemoteApps no grupo de aplicativos.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar grupos de aplicativos, populá-los com o RemoteApp e atribuir usuários ao grupo de aplicativos. Para saber mais sobre como entrar na Área de Trabalho Virtual do Windows, continue com as instruções de Conectar-se à Área de Trabalho Virtual do Windows.

- [Conectar-se ao cliente da Área de Trabalho Remota no Windows 7 e no Windows 10](connect-windows-7-and-10.md)
- [Conectar-se ao cliente Web da Versão Prévia da Área de Trabalho Virtual do Windows](connect-web.md)
