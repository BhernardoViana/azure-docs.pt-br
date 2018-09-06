---
title: Desenvolver localmente com o Emulador Azure Cosmos DB | Microsoft Docs
description: Usando o Emulador do Azure Cosmos DB, você pode desenvolver e testar seu aplicativo no local gratuitamente, sem criar uma assinatura do Azure.
services: cosmos-db
keywords: Emulador do Azure Cosmos DB
author: David-Noble-at-work
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: danoble
ms.openlocfilehash: 355f80479ba7c6d6399bb25f7ba1511c6b18599b
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285220"
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Usar o Emulador do Azure Cosmos DB para desenvolvimento e teste locais

<table>
<tr>
  <td><strong>Binários</strong></td>
  <td>[Baixar MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Hub de Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Fonte de Docker</strong></td>
  <td>[Github](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>
  
O Emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço Azure Cosmos DB para fins de desenvolvimento. Com o Emulador do Azure Cosmos DB, você pode desenvolver e testar seu aplicativo localmente sem criar uma assinatura Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento de seu aplicativo no Emulador do Azure Cosmos DB, você poderá passar a usar uma conta do Azure Cosmos DB na nuvem.

> [!NOTE]
> No momento o Data Explorer no emulador dá suporte completo apenas a coleções de API do SQL e de coleções do MongoDB. Tabela, gráfico e contêineres de Cassandra não têm suporte total. 

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Instalar o Emulador
> * Autenticar solicitações
> * Usar o Data Explorer no Emulador
> * Exportar certificados SSL
> * Como chamar o Emulador na linha de comando
> * Executar o Emulador no Docker para Windows
> * Coletar arquivos de rastreamento
> * solução de problemas

## <a name="how-the-emulator-works"></a>Como o emulador funciona

O Emulador do Azure Cosmos DB fornece uma emulação de alta fidelidade do serviço Azure Cosmos DB. Ele dá suporte a uma funcionalidade idêntica ao Azure Cosmos DB, incluindo suporte para criar e consultar documentos JSON, provisionar e dimensionar coleções, bem como executar procedimentos armazenados e gatilhos. Desenvolva e teste aplicativos usando o Emulador do Azure Cosmos DB e implante-os no Azure em escala global, apenas fazendo uma única alteração de configuração no ponto de extremidade de conexão do Azure Cosmos DB.

Embora a emulação do serviço do Azure Cosmos DB seja fiel, a implementação do Emulador é diferente do serviço. Por exemplo, o Emulador usa componentes padrão do SO, como sistema de arquivos local para persistência e pilha do protocolo HTTPS para conectividade. A funcionalidade que depende da infraestrutura do Azure, como replicação global, latência de único dígito em milissegundos para leituras/gravações e níveis de consistência ajustáveis, não está disponível.

## <a name="differences-between-the-emulator-and-the-service"></a>Diferenças entre o Emulador e o serviço 
Como o Emulador do Azure Cosmos DB fornece um ambiente emulado em execução em uma estação de trabalho do desenvolvedor local, há algumas diferenças na funcionalidade entre o emulador e a conta do Azure Cosmos DB na nuvem:

* Atualmente o Data Explorer no emulador oferece suporte apenas a coleções de API do SQL e de coleções do MongoDB. As APIs Tabela, Gráfico e Cassandra não possuem suporte.  
* O Emulador do Azure Cosmos DB dá suporte apenas uma única conta fixa e uma chave mestra conhecida.  Não é possível regenerar uma chave no Emulador do Azure Cosmos DB.
* O Emulador do Azure Cosmos DB não é um serviço escalonável e não dará suporte a um grande número de coleções.
* O Emulador do Azure Cosmos DB não simula diferentes [níveis de consistência do Azure Cosmos DB](consistency-levels.md).
* O Emulador do Azure Cosmos DB não simula [replicação de várias regiões](distribute-data-globally.md).
* O Emulador do Azure Cosmos DB não dá suporte às substituições de cota de serviço que estão disponíveis no serviço Azure Cosmos DB (por exemplo, limites de tamanho de documento, aumento do armazenamento de coleção particionado).
* Como a sua cópia do Emulador do Azure Cosmos DB pode não ser atualizada com as alterações mais recentes com o serviço do Azure Cosmos DB, você deve usar o [planejador de capacidade do Azure Cosmos DB](https://www.documentdb.com/capacityplanner) para estimar as necessidades de taxa de transferência (RUs) de seu aplicativo.

## <a name="system-requirements"></a>Requisitos do sistema
O Emulador do Azure Cosmos DB tem os seguintes requisitos de hardware e software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
*   Requisitos mínimos de hardware
  * 2 GB de RAM
  * Espaço disponível no disco rígido de 10 GB

## <a name="installation"></a>Instalação
Você pode baixar e instalar o Emulador do Azure Cosmos DB no [Centro de Download da Microsoft](https://aka.ms/cosmosdb-emulator) ou você pode executar o emulador no Docker para Windows. Para obter instruções sobre como usar o emulador no Docker para Windows, consulte [Em execução no Docker](#running-on-docker). 

> [!NOTE]
> Para instalar, configurar e executar o Emulador do Azure Cosmos DB, você deve ter privilégios administrativos no computador.

## <a name="running-on-windows"></a>Em execução no Windows

Para iniciar o Emulador do Azure Cosmos DB, selecione o botão Iniciar ou pressione a tecla Windows. Comece a digitar **Emulador do Azure Cosmos DB** e selecione o emulador na lista de aplicativos. 

![Selecionar o botão Iniciar ou pressionar a tecla Windows, começar a digitar **Emulador do Azure Cosmos DB** e selecionar o emulador na lista de aplicativos](./media/local-emulator/database-local-emulator-start.png)

Quando o emulador estiver em execução, você verá um ícone na área de notificação da barra de tarefas do Windows. ![Notificação na barra de tarefas do emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

O Emulador do Azure Cosmos DB, por padrão, é executado no computador local ("localhost") escutando na porta 8081.

O Emulador do Azure Cosmos DB é instalado em `C:\Program Files\Azure Cosmos DB Emulator` por padrão. Você também pode iniciar e parar o emulador pela linha de comando. Para saber mais, veja a [referência da ferramenta de linha de comando](#command-line).

## <a name="start-data-explorer"></a>Iniciar o Data Explorer

Quando o emulador do Azure Cosmos DB é iniciado, ele abre automaticamente o Data Explorer do Azure Cosmos DB no seu navegador. O endereço é exibido como [ https://localhost:8081/_explorer/index.html ](https://localhost:8081/_explorer/index.html). Se você fechar o Explorer e quiser reabri-lo mais tarde, é possível abrir a URL no navegador ou iniciá-lo no Emulador do Azure Cosmos DB no Ícone de Bandeja do Windows, como mostrado abaixo.

![Iniciador do Data Explorer do emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Procurando atualizações
O Data Explorer indica se há uma nova atualização disponível para download. 

> [!NOTE]
> Não há garantias de que os dados criados em uma versão do Emulador do Azure Cosmos DB possam ser acessados em outras versões. Se você precisar persistir seus dados por longo prazo, é recomendável armazená-los em uma conta do Azure Cosmos DB e não no Emulador do Azure Cosmos DB. 

## <a name="authenticating-requests"></a>Autenticar solicitações
Assim como no Azure Cosmos DB na nuvem, cada solicitação feita no Emulador do Azure Cosmos DB deve ser autenticada. O Emulador do Azure Cosmos DB dá suporte a uma única conta fixa e a uma chave de autenticação conhecida para autenticação de chave mestra. Essa conta e a chave são as únicas credenciais permitidas para uso com o Emulador do Azure Cosmos DB. Eles são:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> A chave mestra com suporte do Emulador do Azure Cosmos DB destina-se ao uso somente com o emulador. Você não pode usar sua conta do Azure Cosmos DB de produção e a chave com o Emulador do Azure Cosmos DB. 

> [!NOTE] 
> Se você tiver iniciado o emulador com a opção /Key, use a chave gerada em vez de "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="

Além disso, assim como o serviço Azure Cosmos DB, o Emulador do Azure Cosmos DB tem suporte apenas à comunicação segura por SSL.

## <a name="running-on-a-local-network"></a>Em execução em uma rede local

Você pode executar o emulador em uma rede local. Para habilitar o acesso à rede, especifique a opção /AllowNetworkAccess na [linha de comando](#command-line-syntax), o que também requer que você especifique /Key=key_string ou /KeyFile=file_name. Você pode usar /GenKeyFile=file_name para gerar um arquivo com uma chave aleatória antecipadamente.  Em seguida, você pode passá-la para /KeyFile=file_name ou /Key=contents_of_file.

Para habilitar o acesso de rede pela primeira vez, o usuário deve desligar o emulador e excluir o diretório de dados do emulador (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Desenvolver com o Emulador
Depois que o Emulador do Azure Cosmos DB estiver em execução na área de trabalho, você poderá usar qualquer [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md) com suporte ou a [API REST do Azure Cosmos DB](/rest/api/cosmos-db/) para interagir com o Emulador. O Emulador do Azure Cosmos DB também inclui um Data Explorer interno que permite criar coleções para as APIs do MongoDB e SQL e exibir e editar documentos sem precisar escrever qualquer código.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Se você estiver usando o [suporte ao protocolo do Azure Cosmos DB para MongoDB](mongodb-introduction.md), use a seguinte cadeia de conexão:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Você pode usar ferramentas existentes, como o [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio), para se conectar ao Emulador do Azure Cosmos DB. Também é possível migrar dados entre o Emulador do Azure Cosmos DB e o serviço Azure Cosmos DB usando a [Ferramenta de Migração de Dados do Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE] 
> Se você tiver iniciado o emulador com a opção /Key, use a chave gerada em vez de "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="

Usando o emulador do Azure Cosmos DB, por padrão, você pode criar até 25 coleções de partição única ou uma coleção particionada. Para saber mais sobre como alterar esse valor, veja [Definição do valor de PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Exportar o certificado SSL

As linguagens e o tempo de execução .NET usam o Repositório de Certificados do Windows para a conexão segura com o emulador local do Azure Cosmos DB. Outras linguagens têm seu próprio método de gerenciar e usar certificados. O Java usa seu próprio [repositório de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) enquanto o Python usa [wrappers de soquete](https://docs.python.org/2/library/ssl.html).

Para obter um certificado para uso com linguagens e tempos de execução que não se integram ao Repositório de Certificados do Windows, você precisará exportá-lo usando o Gerenciador de Certificados do Windows. Você pode iniciá-lo executando certlm.msc ou seguir as instruções passo a passo em [Exportar os certificados do Emulador do Azure Cosmos DB](./local-emulator-export-ssl-certificates.md). Depois que o gerenciador de certificados estiver em execução, abra os Certificados Pessoais, conforme mostrado abaixo, e exporte o certificado com o nome amigável "DocumentDBEmulatorCertificate" como um arquivo X.509 codificado em BASE-64 (.cer).

![Certificado SSL do emulador local do Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

O certificado X.509 pode ser importado no repositório de certificados Java seguindo as instruções em [Adicionando um certificado ao repositório de certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Depois que o certificado é importado para o repositório de certificados, os aplicativos Java e MongoDB podem se conectar ao Emulador do Azure Cosmos DB.

Durante a conexão do emulador de SDKs Python e Node.js, a verificação de SSL é desabilitada.

## <a id="command-line"></a>Referência da ferramenta de linha de comando
No local da instalação, você pode usar a linha de comando para iniciar e interromper o emulador, configurar opções e executar outras operações.

### <a name="command-line-syntax"></a>Sintaxe da linha de comando

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Para exibir a lista de opções, digite `CosmosDB.Emulator.exe /?` no prompt de comando.

<table>
<tr>
  <td><strong>Opção</strong></td>
  <td><strong>Descrição</strong></td>
  <td><strong>Comando</strong></td>
  <td><strong>Argumentos</strong></td>
</tr>
<tr>
  <td>[No arguments]</td>
  <td>Inicia o Emulador do Azure Cosmos DB com as configurações padrão.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Ajuda]</td>
  <td>Exibe a lista de argumentos de linha de comando com suporte.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>Obtém o status do Emulador do Azure Cosmos DB. O status é indicado pelo código de saída: 1 = Iniciando, 2 = Em execução, 3 = Parado. Um código de saída negativo indica que ocorreu um erro. Nenhum outro resultado é produzido.</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>Shutdown</td>
  <td>Desliga o Emulador do Azure Cosmos DB.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Especifica o caminho no qual armazenar os arquivos de dados. O padrão é %LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: um caminho acessível</td>
</tr>
<tr>
  <td>Porta</td>
  <td>Especifica o número da porta a ser usada para o emulador.  O padrão é 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;porta&gt;</td>
  <td>&lt;port&gt;: único número de porta</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Especifica o número da porta a ser usada para API de compatibilidade do MongoDB. O padrão é 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: único número de porta</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Especifica as portas a serem usadas para conectividade direta. Os padrões são 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: lista de 4 portas delimitadas por vírgula</td>
</tr>
<tr>
  <td>Chave</td>
  <td>Chave de autorização para o emulador. A chave deve ser a codificação de base 64 de um vetor de 64 bytes.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;chave&gt;</td>
  <td>&lt;key&gt;: a chave de ser a codificação base-64 de um vetor de 64 bytes</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Especifica que o comportamento de limitação da taxa de solicitação está habilitado.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Especifica que o comportamento de limitação da taxa de solicitação está desabilitado.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Não mostra o emulador de interface do usuário.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Não mostra o Data Explorer na inicialização.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Especifica o número máximo de coleções particionadas. Veja [Alterar o número de coleções](#set-partitioncount) para saber mais.</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;contagemdepartições&gt;: número máximo permitido de coleções de partição única. O padrão é 25. O máximo permitido é 250.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>Especifica o número padrão de partições para uma coleção particionada.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; O padrão é 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Permite o acesso para o emulador através de uma rede. Você também deve passar /Key=&lt;key_string&gt; ou /KeyFile=&lt;file_name&gt; para habilitar o acesso à rede.</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;key_string&gt;<br><br>ou o<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>Não ajuste as regras de firewall quando /AllowNetworkAccess for usado.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Gere uma nova chave de autorização e salve no arquivo especificado. A chave gerada pode ser usada com as opções /Key ou /KeyFile.</td>
  <td>CosmosDB.Emulator.exe  /GenKeyFile=&lt;caminho até o arquivo da chave&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Consistência</td>
  <td>Defina o nível de consistência padrão da conta.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistência&gt;</td>
  <td>&lt;consistência&gt;: o valor deve ser um dos seguintes [níveis de consistência](consistency-levels.md): Sessão, Forte, Eventual ou BoundedStaleness.  O valor padrão é Sessão.</td>
</tr>
<tr>
  <td>?</td>
  <td>Mostre a mensagem de ajuda.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Alterar o número de coleções

Por padrão, você pode criar até 25 coleções de partição única ou uma coleção particionada usando o Emulador do Azure Cosmos DB. Modificando o valor **PartitionCount**, você pode criar até 250 coleções de partição única ou 10 coleções particionadas ou qualquer combinação dos dois que não excedam 250 partições únicas (onde uma coleção particionada = 25 coleções de partição única).

Se você tentar criar uma coleção depois que a contagem de partição atual tiver sido excedida, o emulador lançará uma exceção de ServiceUnavailable, com a mensagem de erro a seguir.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Para alterar o número de coleções disponíveis para o Emulador do Azure Cosmos DB, faça o seguinte:

1. Exclua todos os dados locais do Emulador do Azure Cosmos DB clicando com o botão direito do mouse no ícone **Emulador do Azure Cosmos DB** na bandeja do sistema e clicando em **Redefinir Dados…**.
2. Exclua todos os dados de emulador desta pasta C:\Users\user_name\AppData\Local\CosmosDBEmulator.
3. Saia de todas as instâncias abertas clicando com o botão direito do mouse no ícone do **Emulador do Azure Cosmos DB** na bandeja do sistema e clicando em **Sair**. Pode levar um minuto para que todas as instâncias saiam.
4. Instale a versão mais recente do [Emulador Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).
5. Inicie o emulador com o sinalizador PartitionCount definindo um valor <= 250. Por exemplo: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Controlar o Emulador

O Emulador vem com um módulo PowerShell para iniciar, parar, desinstalar e recuperar o status do serviço. Para usá-lo:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

ou colocar o `PSModules` diretório em `PSModulesPath` e importá-lo assim:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Aqui é apresentado um resumo dos comandos para controlar o emulador do PowerShell:

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>Sintaxe

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>Comentários

Retorna um desses valores ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running ou ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>Sintaxe

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>]  [<CommonParameters>]`

#### <a name="remarks"></a>Comentários

Inicia o emulador. Por padrão, o comando aguarda até que o emulador esteja pronto para aceitar solicitações. Use a opção -NoWait, se quiser que o cmdlet seja retornado assim que ele iniciar o emulador.

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>Sintaxe

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>Comentários

Parada do emulador. Por padrão, esse comando aguarda até que o emulador seja completamente desligado. Use a opção -NoWait, se quiser que o cmdlet seja retornado assim que o emulador iniciar o desligamento.

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>Sintaxe

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>Comentários

Desinstala o emulador e, opcionalmente, remove o conteúdo completo do $env:LOCALAPPDATA\CosmosDbEmulator.
O cmdlet garante que o emulador seja parado antes de desinstalá-lo.

## <a name="running-on-docker"></a>Em execução no Docker

O Emulador do Azure Cosmos DB pode ser executado no Docker para Windows. O emulador não funciona no Docker para Oracle Linux.

Uma vez que você tenha o [Docker para Windows](https://www.docker.com/docker-windows) instalado, mude para contêineres do Windows clicando com o botão direito do mouse no ícone do Docker na barra de ferramentas e selecionando **Mudar para contêineres do Windows**.

Em seguida, efetue pull da imagem do emulador do Hub do Docker, executando o comando a seguir do seu shell favorito.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
Para iniciar a imagem, execute os comandos a seguir.

Na linha de comando:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

Do PowerShell:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:C:\CosmosDB.Emulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

A resposta tem aparência semelhante à seguinte:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Agora use o ponto de extremidade e a chave mestra da resposta no seu cliente e importe o certificado SSL no seu host. Para importar o certificado SSL, faça o seguinte em um prompt de comando do administrador:

Na linha de comando:
```cmd 
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

Do PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Fechar o shell interativo depois de o emulador ter sido iniciado desligará o contêiner do emulador.

Para abrir o Data Explorer, navegue até a URL a seguir no seu navegador. O ponto de extremidade do emulador é fornecido na mensagem de resposta mostrada acima.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>solução de problemas

Use as dicas a seguir para ajudar a solucionar problemas encontrados com o Emulador do Azure Cosmos DB:

- Se você tiver instalado uma nova versão do emulador e se houver erros, redefina os dados. Você pode redefinir seus dados clicando com o botão direito do mouse no ícone do Emulador do Azure Cosmos DB na bandeja do sistema e clicando em Redefinir Dados.... Se isso não resolver os erros, você pode desinstalar e reinstalar o aplicativo. Veja [Desinstalar o emulador local](#uninstall) para obter instruções.

- Se o Emulador do Azure Cosmos DB falhar, colete os arquivos de despejo na pasta c:\Users\user_name\AppData\Local\CrashDumps, compacte-os e anexe-os a um email que deve ser enviado para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Se você enfrentar falhas em CosmosDB.StartupEntryPoint.exe, execute o seguinte comando em um prompt de comando de administrador: `lodctr /R` 

- Se você encontrar um problema de conectividade, [colete os arquivos de rastreamento](#trace-files), compacte-os e anexe-os a um email que deve ser enviado para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Se você receber uma mensagem de **Serviço Indisponível**, o emulador poderá estar falhando em inicializar a pilha de rede. Verifique se você tem o cliente seguro Pulse ou o cliente de redes Juniper instalado, uma vez que seus drivers de filtro de rede podem causar o problema. Desinstalar os drivers de filtro de rede de terceiros geralmente corrige o problema.

### <a id="trace-files"></a>Coletar arquivos de rastreamento

Para coletar rastreamentos de depuração, execute os seguintes comandos em um prompt de comando administrativo:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Confira a bandeja do sistema para ter certeza de que o programa foi desligado. Isso pode levar um minuto. Você também pode simplesmente clicar em **Sair** na interface do usuário do Emulador do Azure Cosmos DB.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduza o problema. Se o Data Explorer não está funcionando, você só precisa aguardar que o navegador abra por alguns segundos para capturar o erro.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Navegue até `%ProgramFiles%\Azure Cosmos DB Emulator` e localize o arquivo docdbemulator_000001.etl.
7. Envie o arquivo .etl junto com as etapas reproduzidas para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) para fins de depuração.

### <a id="uninstall"></a>Desinstalar o Emulador local

1. Saia de todas as instâncias abertas do Emulador local clicando com o botão direito do mouse no ícone do Emulador do Azure Cosmos DB na bandeja do sistema e clicando em Sair. Pode levar um minuto para que todas as instâncias saiam.
2. Na caixa de pesquisa do Windows, digite **Aplicativos e recursos** e clique no resultado de **Aplicativos e recursos (Configurações do sistema)**.
3. Na lista de aplicativos, vá para **Emulador do Azure Cosmos DB**, selecione-o, clique em **Desinstalar** e então confirme e clique em **Desinstalar** novamente.
4. Quando o aplicativo for desinstalado, navegue até C:\Users\<usuário > \AppData\Local\CosmosDBEmulator e exclua a pasta. 

## <a name="change-list"></a>Lista de alterações

Você pode verificar o número da versão clicando com o botão direito no ícone do emulador local na barra de tarefas e clicando no item de menu Sobre.

### <a name="1220-released-on-april-20-2018"></a>1.22.0. Lançado em 20 de março de 2018

Além de atualizar os serviços do Emulador para paridade com os serviços de nuvem do Azure Cosmos DB, incluímos uma documentação aprimorada do PowerShell e algumas correções de bugs diversos.

### <a name="12106-released-on-march-27-2018"></a>1.21.0.6 Lançado em 27 de março de 2018

Além de atualizar os serviços do Emulador para paridade com os serviços de nuvem do Azure Cosmos DB, incluímos um novo recurso e duas correções de bugs nesta versão.

#### <a name="features"></a>Recursos

1. O comando Start-CosmosDbEmulator agora inclui opções de inicialização.

#### <a name="bug-fixes"></a>Correções de bug

1. O módulo do PowerShell Microsoft.Azure.CosmosDB.Emulator agora garante que a enumeração `ServiceControllerStatus` seja carregada.

2. O módulo do PowerShell Microsoft.Azure.CosmosDB.Emulator PowerShell agora inclui um manifesto; uma omissão do primeiro lançamento.

### <a name="1201084-released-on-february-14-2018"></a>1.20.108.4 Lançado em 14 de fevereiro de 2018

Há um novo recurso e duas correções de bugs nessa versão. Graças aos clientes que nos ajudaram a localizar e corrigir esses problemas.

#### <a name="bug-fixes"></a>Correções de bug

1. O emulador agora funciona em computadores com 1 ou 2 núcleos (ou CPUs virtuais)

   O Cosmos DB aloca tarefas para executar vários serviços. O número de tarefas alocadas é um múltiplo do número de núcleos em um host. O padrão múltiplo funciona bem em ambientes de produção onde o número de núcleos é grande. No entanto, em computadores com 1 ou 2 processadores, nenhuma tarefa é alocada para executar esses serviços quando esse múltiplo é aplicado.

   Corrigimos isso, adicionando uma substituição de configuração ao emulador. Agora aplicamos um múltiplo de 1. O número de tarefas alocadas para executar vários serviços agora é igual ao número de núcleos em um host.

   Se não fizéssemos nada para essa versão, seria para resolver esse problema. Nós descobrimos que muitos ambientes de teste/desenvolvimento hospedando o emulador têm 1 ou 2 núcleos.

2. O emulador não exige mais o Pacote Redistribuível do Microsoft Visual C++ 2015 para ser instalado.

   Nós constatamos que as instalações recentes do Windows (Server Edition e Desktop Edition) não incluem esse pacote redistribuível. Portanto, agora agrupamos os binários redistribuíveis com o emulador.

#### <a name="features"></a>Recursos

Muitos clientes com quem conversamos nos disseram: seria bom se o Emulador fosse passível de script. Por isso, nessa versão adicionamos alguma capacidade de script. O Emulador agora inclui um módulo PowerShell para iniciar, parar, obter status e desinstalar-se: `Microsoft.Azure.CosmosDB.Emulator`. 

### <a name="120911-released-on-january-26-2018"></a>1.20.91.1 Lançado em 26 de janeiro de 2018

* Habilitado o pipeline de agregação do MongoDB por padrão.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Instalou o Emulador local
> * Executou o Emulador no Docker para Windows
> * Autenticou solicitações
> * Usou o Data Explorer no Emulador
> * Exportou certificados SSL
> * Chamou o Emulador na linha de comando
> * Coletou arquivos de rastreamento

Neste tutorial, você aprendeu como usar o Emulador local para o desenvolvimento local gratuito. Agora, você pode seguir para o próximo tutorial e aprender como exportar certificados SSL do Emulador. 

> [!div class="nextstepaction"]
> [Exportar os certificados do Emulador do Azure Cosmos DB](local-emulator-export-ssl-certificates.md)
