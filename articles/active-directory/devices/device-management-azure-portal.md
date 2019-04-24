---
title: Como gerenciar dispositivos usando o portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para gerenciar dispositivos.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 516a2d3b0f81bcef1ed636be9d1da9a270cc7069
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60297201"
---
# <a name="how-to-manage-devices-using-the-azure-portal"></a>Como gerenciar dispositivos usando o portal do Azure


Com o gerenciamento de dispositivos no Azure AD (Azure Active Directory), você pode garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. 

Este artigo:

- Assuma que você está familiarizado com a [Introdução ao gerenciamento de dispositivos no Azure Active Directory](overview.md)

- Fornece informações sobre como gerenciar dispositivos usando o portal do Azure

## <a name="manage-devices"></a>Gerenciar dispositivos 

O portal do Azure fornece um local central para gerenciar seus dispositivos. Você pode acessar este local usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou seguindo estas etapas manuais:

1. Entre no [portal do Azure](https://portal.azure.com) como administrador.

2. Na barra de navegação à esquerda, clique em **Active Directory**.

    ![Definir configurações do dispositivo](./media/device-management-azure-portal/01.png)

3. Na seção **Gerenciar**, clique em **Dispositivos**.

    ![Definir configurações do dispositivo](./media/device-management-azure-portal/74.png)
 
A página de **Dispositivos** permite que você:

- Definir as configurações de gerenciamento de dispositivo

- Localizar dispositivos

- Realizar tarefas de gerenciamento de dispositivos

- Examinar os logs de auditoria relativos ao gerenciamento de dispositivos  
  

## <a name="configure-device-settings"></a>Definir configurações do dispositivo

Para gerenciar seus dispositivos usando o portal do Azure, eles precisam ser [registrados ou associados](overview.md#getting-devices-under-the-control-of-azure-ad) ao Azure Active Directory. Como administrador, você pode ajustar o processo de registro e ingresso de dispositivos definindo as configurações do dispositivo. 

![Definir configurações do dispositivo](./media/device-management-azure-portal/22.png)

A página de configurações do dispositivo permite que você configure:

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/21.png)


- **Os usuários podem associar seus dispositivos ao Azure Active Directory** – essas configurações permitem que você selecione os usuários que podem [associar dispositivos](overview.md#azure-ad-joined-devices) ao Azure Active Directory. O padrão é **Todos**. Essa configuração só é aplicável ao Ingresso do Microsoft Azure AD no Windows 10.

- **Outros administradores locais nos dispositivos associados ao Azure AD** – você pode selecionar os usuários que têm direitos de administrador local em um dispositivo. Os usuários adicionados aqui são adicionados à função *Administradores do dispositivo* no Azure AD. Os administradores globais no Azure AD e os proprietários do dispositivo recebem direitos de administrador local por padrão. Essa opção é uma funcionalidade Premium Edition disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite). 

- **Os usuários podem registrar seus dispositivos com o Azure Active Directory** – você precisa definir essa configuração para permitir que dispositivos sejam [registrados](overview.md#azure-ad-registered-devices) com o Azure Active Directory. Se você selecionar **Nenhum**, os dispositivos não terão permissão para registro quando eles não forem associados ao Azure AD ou não forem associados ao Azure AD híbrido. O registro com o Microsoft Intune ou o MDM (Gerenciamento de Dispositivo Móvel) para o Office 365 exige registro. Se você tiver configurado qualquer um desses serviços, a opção **TODOS** estará selecionada e **NENHUM** não estará disponível.

- **Exigir autenticação multifator para associar dispositivos** – é possível definir se os usuários devem precisar fornecer um segundo fator de autenticação para [associar](overview.md#azure-ad-joined-devices) seu dispositivo ao Azure Active Directory. O padrão é **Não**. É recomendável exigir a autenticação multifator ao registrar um dispositivo. Antes de habilitar a autenticação multifator para este serviço, você deve garantir que a autenticação multifator esteja configurada para os usuários que registram seus dispositivos. Para saber mais sobre os diferentes serviços de autenticação multifator do Azure, consulte [Introdução à autenticação multifator do Azure](../authentication/concept-mfa-whichversion.md). Essa configuração não afeta o ingresso híbrido para Windows 10 ou Windows 7. Isso só é aplicável à Junção do Azure AD no registro de dispositivos do Windows 10 e BYO para Windows 10, iOS e Android. 

- **Número máximo de dispositivos** – essa configuração permite selecionar o número máximo de dispositivos que um usuário pode ter no Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos. A cotação de dispositivo é contada para todos os dispositivos que estão associados ao Azure AD ou registrados no Azure AD atualmente. O valor padrão é **20**.

- **Os usuários podem sincronizar configurações e dados de aplicativo em dispositivos** – por padrão, essa configuração é definida como **NENHUM**. Selecionar usuários específicos ou grupos ou TODOS permite que as configurações do usuário e os dados de aplicativo sejam sincronizados em seus dispositivos Windows 10. Saiba mais sobre como a sincronização funciona no Windows 10.
Essa opção é uma funcionalidade premium disponível por meio de produtos como o Azure AD Premium ou o EMS (Enterprise Mobility Suite).





## <a name="locate-devices"></a>Localizar dispositivos

Você tem duas opções para localizar dispositivos registrados e associados:

- **Todos os dispositivos** na seção **Gerenciar** da página **Dispositivos**  

    ![Todos os dispositivos](./media/device-management-azure-portal/41.png)


- **Dispositivos** na seção **Gerenciar** de uma página **Usuário**
 
    ![Todos os dispositivos](./media/device-management-azure-portal/43.png)



Com as duas opções, você pode obter uma exibição que:


- Permite pesquisar dispositivos usando o nome de exibição como filtro.

- Fornece visão geral detalhada de dispositivos registrados e associados

- Permite que você execute tarefas comuns de gerenciamento de dispositivo
   

![Todos os dispositivos](./media/device-management-azure-portal/51.png)

Para alguns dispositivos iOS, os nomes de dispositivo que contém apóstrofos podem usar caracteres diferentes que se parecem com apóstrofos. Procurar esses dispositivos é um pouco confuso; se você não estiver vendo os resultados da pesquisa corretamente, verifique se a cadeia de caracteres de pesquisa contém um caractere de apóstrofo correspondente.

## <a name="device-management-tasks"></a>Tarefas de gerenciamento de dispositivos

Como um administrador global ou administrador do dispositivo de nuvem, você pode gerenciar os dispositivos registrados ou associados. Os administradores de Serviço do Intune podem:
 
- Atualizar dispositivos – exemplos são operações diárias, como habilitar/desabilitar dispositivos

- Excluir dispositivos – quando um dispositivo está desativado e deve ser excluído do Azure AD

Esta seção fornece informações sobre tarefas comuns de gerenciamento de dispositivo.



### <a name="manage-an-intune-device"></a>Gerenciar um dispositivo do Intune

Se você for um administrador do Intune, será possível gerenciar dispositivos marcados como **Microsoft Intune**. 

![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Habilitar/desabilitar um dispositivo do Azure Active Directory

Para ativar / desativar um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

    ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/71.png)

- A barra de ferramentas na página **Dispositivos**

    ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/32.png)


**Comentários:**

- Você precisa ser um administrador global ou administrador do dispositivo de nuvem no Azure AD para habilitar / desabilitar um dispositivo. 
- Desabilitar um dispositivo impede que um dispositivo se autenticar com êxito com o Azure AD, impedindo que o dispositivo de acessar seus recursos do Azure AD que estão protegidos pela autoridade de certificação de dispositivo ou usando suas credenciais de WH4B. 



### <a name="delete-an-azure-ad-device"></a>Excluir um dispositivo do Azure Active Directory

Para excluir um dispositivo, você tem duas opções:

- O menu de tarefas ("...") na página **Todos os dispositivos**

    ![Gerenciar um dispositivo do Intune](./media/device-management-azure-portal/72.png)

- A barra de ferramentas na página **Dispositivos**

    ![Excluir um dispositivo](./media/device-management-azure-portal/34.png)


**Comentários:**

- Você precisa ser um administrador global ou um administrador do Intune no Azure AD para excluir um dispositivo.

- Excluindo um dispositivo:
 
    - Impede que um dispositivo acesse seus recursos do Azure Active Directory. 

    - Remove todos os detalhes que estão relacionados ao dispositivo, por exemplo, chaves do BitLocker para dispositivos Windows.  

    - Representa uma atividade não recuperável e não é recomendável a menos que necessário.

Se um dispositivo for gerenciado por outra autoridade de gerenciamento (por exemplo, o Microsoft Intune), certifique-se de que o dispositivo foi apagado/desativado antes de excluir o dispositivo no Azure Active Directory.

 


### <a name="view-or-copy-device-id"></a>Exibir ou copiar a ID do dispositivo

Você pode usar uma ID de dispositivo para verificar os detalhes de ID de dispositivo no dispositivo ou usar o PowerShell durante a solução de problemas. Para acessar a opção de cópia, clique no dispositivo.

![Exibir uma ID do dispositivo](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Exibir ou copiar as chaves do BitLocker

É possível exibir e copiar as chaves do BitLocker para ajudar os usuários a recuperar sua unidade criptografada. Essas chaves só estão disponíveis para dispositivos Windows que são criptografados e têm suas chaves armazenadas no Azure AD. Você pode copiar essas chaves ao acessar detalhes do dispositivo.
 
![Exibir chaves do BitLocker](./media/device-management-azure-portal/36.png)

Para exibir ou copiar as chaves do BitLocker, você precisa ser o proprietário do dispositivo ou um usuário que tenha pelo menos uma das seguintes funções atribuídas:

- Administradores globais
- Administradores de assistência técnica
- Administradores de segurança
- Leitores de segurança
- Administradores de serviços do Intune

> [!NOTE]
> Os dispositivos do Windows 10 ingressados no Azure AD híbrido não têm um proprietário. Portanto, se você está procurando por um dispositivo pelo proprietário e não o encontrou, pesquise pela ID do dispositivo.


## <a name="audit-logs"></a>Logs de auditoria


As atividades de dispositivo estão disponíveis por meio dos logs de atividade. Isso inclui atividades acionadas pelo serviço de registro do dispositivo e pelos usuários:

- Criação de dispositivo e adição de usuários/proprietários no dispositivo

- Alterações nas configurações do dispositivo

- Operações de dispositivo como excluir ou atualizar um dispositivo
 
O ponto de entrada para os dados de auditoria é **Logs de auditoria**, na seção **Atividade** da página **Dispositivos**.

![Logs de auditoria](./media/device-management-azure-portal/61.png)


Um log de auditoria tem um modo de exibição de lista padrão que mostra:

- A data e a hora da ocorrência

- Os destinos

- O iniciador/ator (quem) de uma atividade

- A atividade (o quê)

![Logs de auditoria](./media/device-management-azure-portal/63.png)

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.
 
![Logs de auditoria](./media/device-management-azure-portal/64.png)


Para restringir os dados relatados a um nível que funciona para você, filtre os dados de auditoria usando os seguintes campos:

- Categoria
- Tipo de recurso de atividade
- Atividade
- Intervalo de datas
- Destino
- Iniciado por (ator)

Além dos filtros, você pode pesquisar itens específicos.

![Logs de auditoria](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](overview.md)



