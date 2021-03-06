---
title: Preparar VMs do Hyper-V para avaliação/migração com as Migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs do Hyper-V com as Migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f93528e2a35661f8a233aea476a958a079d7cd59
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196253"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Preparar para a avaliação e a migração de VMs do Hyper-V para o Azure

Este artigo descreve como preparar a avaliação e a migração de VMs do Hyper-V locais para o Azure usando as [Migrações para Azure](migrate-services-overview.md).

As [Migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros.

Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs do Hyper-V para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Prepare o Azure. Configurar permissões para sua conta do Azure e recursos para trabalhar com as Migrações para Azure.
> * Preparar hosts do Hyper-V local e VMs para a avaliação do servidor.
> * Preparar hosts do Hyper-V local e VMs para a migração do servidor.


> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão quando possível e não mostram todas as configurações e todos os caminhos possíveis. Para obter instruções detalhadas, consulte as instruções para avaliação e migração do Hyper-V.


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

### <a name="azure-permissions"></a>Permissões do Azure

Você precisa configurar permissões para a implantação de Migrações para Azure.

- Permissões para sua conta do Azure para criar um projeto de Migrações para Azure.
- Permissões para sua para registrar o dispositivo de Migrações para Azure. O dispositivo é usado para descoberta e migração do Hyper-V. Durante o registro do dispositivo, as Migrações para Azure criam dois aplicativos Azure AD (Azure Active Directory) que identificam o dispositivo de forma exclusiva:
    - O primeiro aplicativo comunica-se com os pontos de extremidade de serviço das Migrações para Azure.
    - O segundo aplicativo acessa um Azure Key Vault criado durante o registro para armazenar informações de aplicativo do Azure AD e definições de configuração do dispositivo.



### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar o projeto

Verifique se você tem permissões para criar um projeto de Migrações para Azure.

1. No portal do Azure, abra a assinatura e selecione **Controle de acesso (IAM).**
2. Em **Verificar acesso**, localize a conta relevante e clique nela para exibir as permissões.
3. Você deve ter permissões de **Colaborador** e **Proprietário**.
    - Se você acaba de criar uma conta gratuita do Azure, você é o proprietário da assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.


### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Você pode atribuir permissões para as Migrações para Azure para criar os aplicativos do Azure AD desenvolvidos durante o registro do dispositivo usando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos (que tem as permissões) à conta.

Vale a pena observar que:

- Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
- Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo está configurado.


#### <a name="grant-account-permissions"></a>Conceder permissões da conta

O locatário/administrador global pode conceder permissões da seguinte maneira:

1. No Azure AD, o administrador de locatário/global deve navegar até **Azure Active Directory** > **Usuários** > **Configurações do Usuário**.
2. O administrador deve definir **Registros de aplicativo** como **Sim**.

    ![Permissões do Azure AD](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Essa é uma configuração padrão que não é confidencial. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Atribuir função de Desenvolvedor de Aplicativos

O locatário/administrador global pode atribuir a função de Desenvolvedor de Aplicativos a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>Preparar para a avaliação do Hyper-V

Para preparar para a avaliação do Hyper-V, faça o seguinte:

1. Verifique as configurações do host Hyper-V.
2. Configure a comunicação remota do PowerShell em cada host para que o dispositivo de Migrações para Azure possa executar comandos do PowerShell no host, em uma conexão do WinRM.
3. Se os discos da VM estiverem localizados no armazenamento SMB remoto, a delegação de credenciais será necessária.
    - Habilite a delegação de CredSSP para que o dispositivo de Migrações para Azure possa atuar como o cliente, delegando credenciais a um host.
    - Você permite que cada host atue como um delegado para o dispositivo, conforme descrito abaixo.
    - Posteriormente, ao configurar o dispositivo, você habilitará a delegação no dispositivo.
4. Examine os requisitos do dispositivo e o acesso à URL/porta necessário para o dispositivo.
5. Configure uma conta que será usada pelo dispositivo para descobrir VMs.
6. Configure o Integration Services do Hyper-V em cada VM que você deseja descobrir e avaliar.


Você pode definir essas configurações manualmente usando os procedimentos a seguir. Como alternativa, você executa o script de Configuração de Pré-requisitos do Hyper-V.

### <a name="hyper-v-prerequisites-configuration-script"></a>Script de Configuração de Pré-requisitos do Hyper-V

O script valida os hosts do Hyper-V e define as configurações necessárias para descobrir e avaliar as VMs do Hyper-V. Aqui está o que ele faz:

- Verifica se você está executando o script em uma versão do PowerShell compatível.
- Verifica se você (o usuário que está executando o script) tem privilégios administrativos no host Hyper-V.
- Permite que você crie uma conta de usuário local (não administrador) usada para o serviço de Migrações para Azure se comunicar com o host Hyper-V. Essa conta de usuário é adicionada a esses grupos no host:
    - Usuários do gerenciamento remoto
    - Administradores do Hyper-V
    - Usuários do monitor de desempenho
- Verifica se o host está executando uma versão compatível do Hyper-V e a função do Hyper-V.
- Habilita o serviço WinRM e abre as portas 5985 (HTTP) e 5986 (HTTPS) no host (necessárias para a coleta de metadados).
- Habilitar a comunicação remota do PowerShell no host.
- Verifica se o serviço de integração do Hyper-V está habilitado em todas as VMs gerenciadas pelo host.
- Habilita CredSSP no host, se necessário.

Crie o script da seguinte maneira:

1. Verifique se você tem o PowerShell versão 4.0 ou posterior instalado no host Hyper-V.
2. Baixe o script do [Centro de Download da Microsoft](https://aka.ms/migrate/script/hyperv). O script é assinado criptograficamente pela Microsoft.
3. Valide a integridade do script usando arquivos de hash MD5 ou SHA256. Os valores de hashtag são indicados abaixo. Execute o seguinte comando para gerar o hash para o script:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Exemplo de uso:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  Depois de validar a integridade do script, execute o script em cada host Hyper-V com este comando do PowerShell:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Valores de hashtag

Os valores de hash são:

| **Hash** | **Valor** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |

### <a name="verify-hyper-v-host-settings"></a>Verificar as configurações do host Hyper-V

1. Verifique os [requisitos do host do Hyper-V](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) para avaliação do servidor.
2. Verifique se as [portas necessárias](migrate-support-matrix-hyper-v.md#assessment-port-requirements) estão abertas nos hosts do Hyper-V.

### <a name="enable-powershell-remoting-on-hosts"></a>Habilitar a comunicação remota do PowerShell nos hosts

Configure a comunicação remota do PowerShell em cada host, da seguinte maneira:

1. Em cada host, abra um console do PowerShell como administrador.
2. Execute este comando:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Habilitar CredSSP em hosts

Se o host tiver VM com discos localizados em compartilhamentos SMB, complete esta etapa no host.

- Você pode executar esse comando remotamente em todos os hosts do Hyper-V.
- Se você adicionar novos nós de host a um cluster, eles serão adicionados automaticamente para descoberta, mas você precisará habilitar o CredSSP manualmente nos novos nós, se necessário.

Habilite da seguinte maneira:

1. Identifique os hosts do Hyper-V que executam VMs do Hyper-V com discos em compartilhamentos SMB.
2. Execute o seguinte comando em cada host do Hyper-V identificado:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Ao configurar o dispositivo, você conclui a configuração do CredSSP [habilitando-o no dispositivo](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Isso é descrito no próximo tutorial desta série.


### <a name="verify-appliance-settings"></a>Verificar configurações do dispositivo

Antes de configurar o dispositivo das Migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) os requisitos do dispositivo.
2. [Examine](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) as URLs do Azure que o dispositivo precisará acessar.
3. Examine os dados que o dispositivo coletará durante a descoberta e a avaliação.
4. [Observe](migrate-support-matrix-hyper-v.md#assessment-port-requirements) os requisitos de acesso da porta para o dispositivo.


### <a name="set-up-an-account-for-vm-discovery"></a>Configurar uma conta para descoberta de VM

As Migrações para Azure precisam de permissões para descobrir VMs locais.

- Configure uma conta de usuário local ou de domínio com permissões de administrador nos hosts/cluster do Hyper-V.

    - Você precisa de uma única conta para todos os hosts e clusters que deseja incluir na descoberta.
    - A conta pode ser local ou de domínio. Recomendamos que ela tenha permissões de Administrador nos hosts ou clusters do Hyper-V.
    - Como alternativa, se você não quiser atribuir permissões de administrador, as seguintes permissões serão necessárias:
        - Usuários do gerenciamento remoto
        - Administradores do Hyper-V
        - Usuários do monitor de desempenho

### <a name="enable-integration-services-on-vms"></a>Habilitar o Integration Services em VMs

O Integration Services deve ser habilitado em cada VM para que as Migrações para Azure possam capturar informações do sistema operacional na VM.

Habilite os [Integration Services do Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) em cada VM que você deseja descobrir e avaliar.

## <a name="prepare-for-hyper-v-migration"></a>Preparar para a migração do Hyper-V

1. [Examine](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) os requisitos de host do Hyper-V para migração.
2. [Examine](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) os requisitos para VMs do Hyper-V que você deseja migrar para o Azure.
3. [Observe](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) as URLs do Azure para as quais os hosts e clusters do Hyper-V precisam de acesso para migração da VM.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você irá:

> [!div class="checklist"]
> * Configurou permissões de conta do Azure.
> * Preparou VMs do Hyper-V para avaliação e migração.

Prossiga para o próximo tutorial para criar um projeto das Migrações para Azure e avaliar as VMs do Hyper-V para migração para o Azure

> [!div class="nextstepaction"]
> [Avaliar VMs do Hyper-V](./tutorial-assess-hyper-v.md)
