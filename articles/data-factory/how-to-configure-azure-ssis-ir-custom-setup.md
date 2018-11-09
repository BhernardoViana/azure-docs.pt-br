---
title: Personalizar a configuração para o tempo de execução de integração do Azure-SSIS | Microsoft Docs
description: Este artigo descreve como usar a interface de instalação personalizada para o tempo de execução de integração do Azure SSIS para instalar componentes adicionais ou alterar as configurações
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2edaea1cfb02b250b27c47d58b6c1d1ef6501480
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420261"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Personalizar configuração do tempo de execução de integração do Azure-SSIS

A interface de configuração personalizada do tempo de execução de integração do Azure-SSIS fornece uma interface para adicionar suas próprias etapas de instalação durante o provisionamento ou reconfigurar o IR do Azure-SSIS. A configuração personalizada permite alterar a configuração ou o ambiente operacional padrão (por exemplo, para iniciar serviços adicionais do Windows ou credenciais de acesso para compartilhamentos de arquivo) ou instalar componentes adicionais (por exemplo, assemblies, drivers ou extensões) em cada nó do IR do Azure-SSIS.

Você configura a configuração personalizada preparando um script e seus arquivos associados e carregando-os em um contêiner de blob em sua conta de Armazenamento do Azure. Você fornece uma assinatura de acesso compartilhado (SAS) URI (Uniform Resource Identifier) para seu contêiner quando provisiona ou reconfigura o IR do Azure-SSIS. Cada nó de IR do Azure-SSIS, em seguida, baixa o script e seus arquivos associados do seu contêiner e executa a configuração personalizada com privilégios elevados. Quando a instalação personalizada é concluída, cada nó carrega a saída padrão de execução e outros logs em seu contêiner.

Você pode instalar componentes livres ou não licenciados e componentes pagos ou licenciados. Se você for um ISV, consulte [Como desenvolver componentes pagos ou licenciados para o IR do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Limitações atuais

-   Se você quiser usar `gacutil.exe` para instalar assemblies no GAC (Cache de Assembly Global), será necessário fornecer `gacutil.exe` como parte da configuração personalizada ou usar a cópia fornecida no contêiner da Visualização Pública.

-   Se você quiser referenciar uma subpasta no script, `msiexec.exe` não dará suporte `.\` à notação para referenciar a pasta raiz. Use um comando como `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` em vez de `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Se você precisa unir o IR do Azure-SSIS com a instalação personalizada para uma rede virtual, há suporte apenas para a rede virtual do Azure Resource Manager. Não há suporte à rede virtual clássica.

-   Atualmente, não há suporte para o compartilhamento administrativo no IR do Azure-SSIS.

## <a name="prerequisites"></a>Pré-requisitos

Para personalizar o IR do Azure-SSIS, você precisa do seguinte:

-   [Assinatura do Azure](https://azure.microsoft.com/)

-   [Um Banco de Dados SQL do Azure ou servidor de Instância Gerencia](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Provisionar o IR do Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Uma conta de Armazenamento do Azure](https://azure.microsoft.com/services/storage/). Para instalação personalizada, você carrega e armazena seu script de instalação personalizada e seus arquivos associados em um contêiner de blob. O processo de instalação personalizado também carrega seus logs de execução no mesmo contêiner de blob.

## <a name="instructions"></a>Instruções

1.  Baixe e instale o [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (versão 5.4 ou posterior).

1.  Prepare o script de instalação personalizado e seus arquivos associados (por exemplo, arquivos .bat, .cmd, .exe, .dll, .msi ou .ps1).

    1.  Você deve ter um arquivo de script chamado `main.cmd`, que é o ponto de entrada de sua instalação personalizada.

    1.  Se você quiser logs adicionais gerados por outras ferramentas (por exemplo, `msiexec.exe`) para ser carregado em seu contêiner, especifique a variável de ambiente predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR` como a pasta de log em seus scripts (por exemplo, `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1.  Baixe, instale e execute o [Azure Storage Explorer](http://storageexplorer.com/).

    1.  No **(Local e Anexo)**, selecione com o botão de **Contas de Armazenamento** e selecione **Conecte-se ao Armazenamento do Azure**.

       ![Conectar-se ao armazenamento do Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    1.  Selecione **Usar um nome e uma chave da conta de armazenamento** e selecione **Próximo**.

       ![Usar um nome e uma chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    1.  Digite seu nome e a chave da conta de armazenamento do Azure, selecione **Próximo**e, em seguida, selecione **Conectar**.

       ![Forneça o nome e a chave da conta de armazenamento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    1.  Em sua conta de armazenamento do Azure conectada, clique duas vezes em **Contêineres de Blob**, selecione **Criar contêiner de Blob**e nomeie o novo contêiner.

       ![Criar um contêiner de blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    1.  Selecione o novo contêiner e carregue o script de instalação personalizado e seus arquivos associados. Certifique-se de carregar `main.cmd` no nível superior do contêiner, não em qualquer pasta. 

       ![Carregar arquivos no contêiner de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    1.  Clique com o botão direito no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**.

       ![Obter Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    1.  Crie o URI de SAS para seu contêiner com um período de expiração suficientemente longo e leitura + escrita + lista de permissões. É necessário que o URI de SAS baixe e execute o script de instalação personalizado e seus arquivos associados, sempre que qualquer nó de IR do Azure-SSIS for refeita a imagem/recriada. Você precisa gravar permissão para carregar os logs de execução da instalação.

        > [!IMPORTANT]
        > Certifique-se de que o URI SAS não expirará e os recursos de instalação personalizada estejam sempre disponíveis durante o ciclo de vida inteiro de o IR do Azue-SSIS da criação à exclusão, especialmente se você regularmente parar e iniciar o IV SSIS do Azure durante esse período.

       ![Gerar Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    1.  Copie e salve o URI de SAS do seu contêiner.

       ![Copie e salve a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    1.  Ao provisionar ou reconfigurar o Azure-SSIS IR com a IU do Data Factory, antes de iniciar o Azure-SSIS IR, insira o URI de SAS do seu contêiner no campo apropriado no painel **Configurações Avançadas**:

       ![Insira a Assinatura de Acesso Compartilhado](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

       Quando você provisionar ou reconfigurar o IR do Azure-SSIS com o PowerShell, antes de iniciar o IR do Azure-SSIS, execute o cmdlet `Set-AzureRmDataFactoryV2IntegrationRuntime` com o URI de SAS do seu contêiner como o valor para o novo parâmetro `SetupScriptContainerSasUri`. Por exemplo: 

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    1.  Depois de concluir a configuração personalizada e iniciar o IR do Azure-SSIS, você pode encontrar a saída padrão de `main.cmd` e outros logs de execução na pasta `main.cmd.log` do seu contêiner de armazenamento.

1.  Para obter outros exemplos de instalação personalizada, conecte-se ao contêiner de visualização pública com o Azure Storage Explorer.

    a.  Em **(Local e conectado)**, clique com botão direito em **Contas de Armazenamento**, selecione **Conectar ao armazenamento do Azure**, selecione **Usar uma cadeia de caracteres de conexão ou uma URI de assinatura de acesso compartilhado** e, em seguida, selecione **Próximo**.

       ![Conecte-se ao armazenamento do Azure com a Assinatura de Acesso Compartilhado](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Selecione **Usar um URI de SAS** e insira o seguinte URI de SAS para o contêiner de Visualização Pública. Selecione **Próximo**e selecione **Conectar**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Fornecer a Assinatura de Acesso Compartilhado para o contêiner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Selecione o contêiner de Visualização Pública conectado e clique duas vezes na pasta `CustomSetupScript`. Nesta pasta, você encontrará os seguintes itens:

       1. Uma pasta `Sample`, que contém uma instalação personalizada para instalar uma tarefa básica em cada nó do seu IR do Azure-SSIS. A tarefa apenas executará a suspensão por alguns segundos. A pasta também contém um `gacutil` pasta, que contém `gacutil.exe`. Além disso, `main.cmd` contém comentários para manter as credenciais de acesso para compartilhamentos de arquivos.

       1. Uma pasta `UserScenarios`, que contém várias configurações personalizadas para cenários de usuário real.

    ![Conteúdo do contêiner de visualização pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Clique duas vezes na pasta `UserScenarios`. Nesta pasta, você encontrará os seguintes itens:

       1. Um `.NET FRAMEWORK 3.5` pasta, que contém uma instalação personalizada para instalar uma versão anterior do .NET Framework que podem ser necessária para componentes personalizados em cada nó do seu IR do Azure-SSIS.

       1. Uma pasta `AAS`, que contém uma instalação personalizada para instalar bibliotecas de cliente em cada nó do Azure-SSIS IR que permitem que as tarefas do Analysis Services se conectem à instância do AAS (Azure Analysis Services) usando a autenticação de entidade de serviço. Baixe os instaladores do Windows/bibliotecas de cliente **MSOLAP (amd64)** e **AMO** mais recentes, por exemplo, `x64_15.0.900.108_SQL_AS_OLEDB.msi` e `x64_15.0.900.108_SQL_AS_AMO.msi`, [aqui](https://docs.microsoft.com/en-us/azure/analysis-services/analysis-services-data-providers), e carregue-os juntos com `main.cmd` em seu contêiner.  

       1. Uma pasta `BCP`, que contém uma instalação personalizada para instalar utilitários de linha de comando do SQL Server (`MsSqlCmdLnUtils.msi`), incluindo o programa de cópia em massa (`bcp`), em cada nó do seu IR do Azure-SSIS.

       1. Uma pasta `EXCEL`, que contém uma instalação personalizada para instalar assemblies de código-fonte aberto (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` e `ExcelDataReader.dll`) em cada nó do seu IR do Azure-SSIS.

       1. Uma pasta `MSDTC`, que contém uma instalação personalizada para modificar a rede e as configurações de segurança para o serviço do Coordenador de Transações Distribuídas (MSDTC) em cada modo do seu IR Azure-SSIS. Para garantir que o MSDTC seja iniciado, adicione a tarefa do processo de executar no início do fluxo de controle em seus pacotes para executar o comando a seguir: `%SystemRoot%\system32\cmd.exe /c powershell -Command "Start-Service MSDTC"` 

       1. Uma pasta `ORACLE ENTERPRISE`, que contém um script de instalação personalizada (`main.cmd`) e o arquivo de configuração de instalação silenciosa (`client.rsp`) para instalar o driver OCI e os conectores Oracle em cada nó do Azure-SSIS IR Enterprise Edition. Essa configuração permite o uso do Gerenciador de Conexão do Oracle, Origem e Destino. Baixe o Microsoft Connectors v5.0 para Oracle (`AttunitySSISOraAdaptersSetup.msi` e `AttunitySSISOraAdaptersSetup64.msi`) no [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) e o cliente Oracle mais recente, por exemplo, `winx64_12102_client.zip`, no [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), e carregue-os junto com `main.cmd` e `client.rsp` em seu contêiner. Se você usar TNS para se conectar ao Oracle, você também precisa baixar `tnsnames.ora`, editá-lo e carregá-lo no seu contêiner, portanto ele pode ser copiado para a pasta de instalação do Oracle durante a instalação.

       1. Uma pasta `ORACLE STANDARD`, que contém um script de instalação personalizada (`main.cmd`) para instalar o driver do Oracle ODP.NET em cada nó do seu IR do Azure-SSIS. Essa configuração permite o uso do Gerenciador de Conexão ADO.NET, Origem e Destino. Primeiro, baixe o último cliente do Oracle ODP.NET – por exemplo, `ODP.NET_Managed_ODAC122cR1.zip` – na [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) e, em seguida, faça upload dele junto com `main.cmd` no contêiner.

       1. Uma pasta `SAP BW`, que contém um script de instalação personalizada (`main.cmd`) para instalar o assembly de conector do SAP .NET (`librfc32.dll`) em cada nó do IR do Azure-SSIS Enterprise Edition. Essa configuração permite o uso do Gerenciador de Conexão do SAP BW, Origem e Destino. Primeiro, carregue a versão 64 bits ou 32 bits de `librfc32.dll` da pasta de instalação do SAP em seu contêiner, junto com `main.cmd`. O script, em seguida, copia o assembly do SAP para a pasta `%windir%\SysWow64` ou `%windir%\System32` durante a instalação.

       1. Uma pasta `STORAGE`, que contém uma instalação personalizada para instalar o Azure PowerShell em cada nó do seu IR do Azure-SSIS. Essa configuração permite que você implante e execução de pacotes SSIS que executam [scripts do PowerShell para manipular a sua conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copie `main.cmd`, uma amostra `AzurePowerShell.msi` (ou instale a versão mais recente), e `storage.ps1` para seu contêiner. Use PowerShell.dtsx como um modelo para seus pacotes. O modelo de pacote combina uma [Tarefa de Download de Blob do Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), que baixa `storage.ps1` como um script do PowerShell pode ser modificado e uma [Tarefa de Executar Processo](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) que executa o script em cada nó.

       1. Uma pasta `TERADATA`, que contém um script de instalação personalizada (`main.cmd`), seu arquivo associado (`install.cmd`) e pacotes do instalador (`.msi`). Esses arquivos instalam o driver ODBC, a API TPT e os conectores Teradata em cada nó do IR do Azure-SSIS Enterprise Edition. Essa configuração permite o uso do Gerenciador de Conexão do Terada, Origem e Destino. Primeiro, baixe o arquivo zip Teradata Tools and Utilities (TTU) 15.x (por exemplo, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) de [Teradata](http://partnerintelligence.teradata.com) e, em seguida, carregue-o junto com os aquivos `.cmd` e `.msi` acima em seu contêiner.

    ![Pastas na pasta de cenários de usuário](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Para testar esses exemplos de instalação personalizada, copie e cole o conteúdo da pasta selecionada em seu contêiner. Quando você provisionar ou reconfigurar o IR do Azure-SSIS com o PowerShell, execute o cmdlet `Set-AzureRmDataFactoryV2IntegrationRuntime` com o URI de SAS do seu contêiner como o valor para o novo parâmetro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Próximas etapas

-   [Enterprise Edition do Tempo de Execução de Integração do Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Como desenvolver componentes personalizados pagos ou licenciados para o Tempo de Execução de Integração do Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
