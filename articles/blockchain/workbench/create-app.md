---
title: Criar um aplicativo blockchain no Azure Blockchain Workbench
description: Como criar um aplicativo blockchain implantado no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: a7ca3f42874bc844bc0036e37a790ffebdc5f8d8
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240484"
---
# <a name="create-a-blockchain-application-in-azure-blockchain-workbench"></a>Criar um aplicativo blockchain no Azure Blockchain Workbench

Você pode usar o Azure Blockchain Workbench para criar aplicativos blockchain que representam os fluxos de trabalho de várias partes, definidos pela configuração e código de contrato inteligente.

Você aprenderá como:

> [!div class="checklist"]
> * Configurar um aplicativo blockchain
> * Criar um arquivo de código de contrato inteligente
> * Um aplicativo blockchain implantado no Blockchain Workbench
> * Adicionar membros ao aplicativo blockchain

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação do Blockchain Workbench. Para obter mais informações, consulte [implantação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implantação.
* Usuários do Azure Active Directory no locatário associado ao Blockchain Workbench. Para obter mais informações, consulte [adicionar usuários do Microsoft Azure Active Directory no Azure Blockchain Workbench](manage-users.md#add-azure-ad-users).
* Uma conta de administrador Blockchain Workbench. Para obter mais informações, consulte adicionar [administradores Blockchain Workbench no Azure Blockchain Workbench ](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Olá, Blockchain!

Vamos construir um aplicativo de solicitação-resposta básica em que um solicitante envia que uma solicitação e o respondente enviam uma resposta à solicitação. Por exemplo, pode ser uma solicitação, "Olá, como vai?" e a resposta possível, "Estou ótimo!". A solicitação e a resposta são registradas no blockchain subjacente. 

Siga as etapas para criar os arquivos de aplicativo, ou você pode [fazer o download do modelo do GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain). 

## <a name="configuration-file"></a>Arquivo de configuração

Metadados de configuração definem os fluxos de trabalho de alto nível e modelo de interação do aplicativo de blockchain. Os metadados de configuração definem as etapas de fluxos de trabalho e o modelo de interação do aplicativo blockchain.

1. No seu editor favorito, crie um arquivo nomeado `HelloBlockchain.json`.
2. Adicione o JSON a seguir para definir a configuração do aplicativo blockchain.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Salve o arquivo `HelloBlockchain.json`.

O arquivo de configuração tem várias seções. Detalhes sobre cada seção conforme a seguir:

### <a name="application-metadata"></a>Metadados do aplicativo

O início do arquivo de configuração contém informações sobre o aplicativo, incluindo a descrição e o nome do aplicativo.

### <a name="application-roles"></a>Funções de aplicativo

A seção de funções de aplicativo define as funções de usuário que podem agir ou participar do aplicativo blockchain. Você define um conjunto de funções diferentes com base na funcionalidade. No cenário de solicitação-resposta, há uma distinção entre a funcionalidade de um solicitante como uma entidade que produz solicitações e um respondente como uma entidade que gera respostas.

### <a name="workflows"></a>Fluxos de trabalho

Fluxos de trabalho definem uma ou mais etapas e ações do contrato. No cenário de solicitação-resposta, a primeira etapa (estado) do fluxo de trabalho é um solicitante (função) que executa uma ação (transição) para enviar uma solicitação (função). A próxima etapa (estado) é que um respondente (função) que executa uma ação (transição) para enviar uma resposta (função). O fluxo de trabalho de um aplicativo pode envolver propriedades, funções e estados necessários que descrevem o fluxo de um contrato. 

Para obter mais informações sobre o conteúdo dos arquivos de configuração, consulte [Referência de configuração do fluxo de trabalho do Azure Blockchain](configuration.md).

## <a name="smart-contract-code-file"></a>Arquivo de código de contrato inteligente

Contratos inteligentes representam a lógica de negócios do aplicativo blockchain. Atualmente, o Blockchain Workbench é compatível com Ethereum com o razão do blockchain. O Ethereum usa o [Solidity](https://solidity.readthedocs.io) como sua linguagem de programação para gravar uma lógico de auto-imposição para contratos inteligentes.

Contratos inteligentes no Solidity são semelhantes às classes em linguagens orientadas a objeto. Cada contrato contém o estado e as funções para implementar as etapas e as ações do contrato inteligente.

No seu editor favorito, crie um arquivo com o nome `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Pragma de versão

Como melhor prática, indica que a versão do Solidity de destino. Especificar a versão ajuda a evitar incompatibilidades com versões futuras do Solidity. 

Adicionar o pragma de versão a seguir na parte superior do `HelloBlockchain.sol` arquivo de código de contrato inteligente.


  ``` solidity
  pragma solidity ^0.4.20;
  ```

### <a name="base-class"></a>Classe base

A classe base **WorkbenchBase** permite que o Workbench do Blockchain crie e atualize o contrato. A classe base é necessária para o código de contrato inteligente específico do Blockchain Workbench. O contrato deve herdar da classe base do **WorkbenchBase**.

No `HelloBlockchain.sol`arquivo de código de contrato inteligente, adicione a classe do **WorkbenchBase** no início do arquivo. 

```
contract WorkbenchBase {
    event WorkbenchContractCreated(string applicationName, string workflowName, address originatingAddress);
    event WorkbenchContractUpdated(string applicationName, string workflowName, string action, address originatingAddress);

    string internal ApplicationName;
    string internal WorkflowName;

    function WorkbenchBase(string applicationName, string workflowName) internal {
        ApplicationName = applicationName;
        WorkflowName = workflowName;
    }

    function ContractCreated() internal {
        WorkbenchContractCreated(ApplicationName, WorkflowName, msg.sender);
    }

    function ContractUpdated(string action) internal {
        WorkbenchContractUpdated(ApplicationName, WorkflowName, action, msg.sender);
    }
}
```
A classe base inclui duas funções importantes:

|Função de classe base  | Finalidade  | Quando telefonar  |
|---------|---------|---------|
| ContractCreated() | Notifica o Blockchain Workbench que um contrato foi criado | Antes de sair do construtor de contrato |
| ContractUpdated() | Notifica o Blockchain Workbench que um estado de contrato foi atualizado | Antes de sair de uma função de contrato |

### <a name="configuration-and-smart-contract-code-relationship"></a>Configuração e relação de código de contrato inteligente

Blockchain Workbench usa o arquivo de configuração e o arquivo de código de contrato inteligente para criar um aplicativo blockchain. Há uma relação entre o que é definido na configuração e o código no contrato inteligente. Detalhes do contrato, funções, parâmetros e tipos devem corresponder para criar o aplicativo. O Blockchain Workbench verifica os arquivos antes da criação do aplicativo. 

### <a name="contract"></a>Contrato

Para o Blockchain Workbench, os contratos precisam herdar a classe base do **WorkbenchBase**. Ao declarar o contrato, é necessário passar o nome do aplicativo e o nome do fluxo de trabalho como argumentos.

Adicione o cabeçalho do **contrato** ao seu `HelloBlockchain.sol` arquivo de código de contrato inteligente. 

```
contract HelloBlockchain is WorkbenchBase('HelloBlockchain', 'HelloBlockchain') {
```

O contrato deve herdar a classe base do **WorkbenchBase** e passar nos parâmetros de **ApplicationName** e o fluxo de trabalho **Nome** conforme definido no arquivo de configuração. Neste caso, o nome do aplicativo e o nome do fluxo de trabalho são os mesmos.

### <a name="state-variables"></a>Variáveis de estado

As variáveis de estado armazenam valores de estado para cada instância do contrato. As variáveis de estado no seu contrato devem corresponder com as propriedades de fluxo de trabalho definidas no arquivo de configuração.

Adicione as variáveis de estado ao contrato no seu `HelloBlockchain.sol` arquivo de código de contrato inteligente. 

```
    //Set of States
    enum StateType { Request, Respond}
    
    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;
    
    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Construtor

O construtor define parâmetros de entrada para uma nova instância de contrato inteligente de um fluxo de trabalho. O construtor é declarado como uma função com o mesmo nome que o contrato. Parâmetros necessários para o construtor são definidos como parâmetros do construtor no arquivo de configuração. O número, ordem e o tipo de parâmetros devem corresponder em ambos os arquivos.

Na função de construtor, grave qualquer lógica de negócios que deseja executar antes de criar o contrato. Por exemplo, inicialize as variáveis de estado com valores iniciais.

Antes de sair da função de construtor, chame o `ContractCreated()` função. Esta função notifica o Blockchain Workbench que um contrato foi criado.

Adicione a função do construtor ao seu contrato no seu `HelloBlockchain.sol` arquivo de código de contrato inteligente. 

```
    // constructor function
    function HelloBlockchain(string message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    
        // call ContractCreated() to create an instance of this workflow
        ContractCreated();
    }
```

### <a name="functions"></a>Funções

Funções são as unidades executáveis de lógica de negócios dentro de um contrato. Parâmetros necessários para a função são definidos como parâmetros de função no arquivo de configuração. O número, ordem e o tipo de parâmetros devem corresponder em ambos os arquivos. As funções são associadas às transições em um fluxo de trabalho Blockchain Workbench no arquivo de configuração. Uma transição é uma ação executada para mover para a próxima etapa do fluxo de trabalho do aplicativo conforme determinado pelo contrato.

Grave qualquer lógica de negócios que você deseja executar na função. Por exemplo, modificando o valor de uma variável de estado.

Antes de sair da função de construtor, chame o `ContractUpdated()` função. A função notifica o Blockchain Workbench que um estado de contrato foi atualizado. Se você deseja desfazer as alterações de estado feitas na função, chame revert(). Reverter descarta as alterações de estado feitas desde a última chamada a ContractUpdated().

1. Adicione a função do construtor a seguir ao seu contrato no seu `HelloBlockchain.sol` arquivo de código de contrato inteligente. 

    ```
        // call this function to send a request
        function SendRequest(string requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
    
            // call ContractUpdated() to record this action
            ContractUpdated('SendRequest');
        }
    
        // call this function to send a response
        function SendResponse(string responseMessage) public
        {
            Responder = msg.sender;
    
            // call ContractUpdated() to record this action
            ResponseMessage = responseMessage;
            State = StateType.Respond;
            ContractUpdated('SendResponse');
        }
    }
    ```

2. Salve seu `HelloBlockchain.sol` arquivo de código de contrato inteligente.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Adicione o aplicativo blockchain ao Blockchain Workbench

Para adicionar um aplicativo de blockchain ao Blockchain Workbench, você pode carregar os arquivos de configuração e contrato inteligente para definir o aplicativo.

1. Em um navegador da web, navegue até o endereço da web do Blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` o aplicativo web é criado quando você implanta o Blockchain Workbench. Para obter informações sobre como localizar o endereço da web do Blockchain Workbench, consulte a URL da web do[Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Entre como um [administrador de Workbench do Blockchain](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecione **Aplicativos** > **Novo**. O painel **Novo aplicativo** painel é exibido.
4. Selecione **Carregar a configuração do contrato** > **Navegar** para localizar o arquivo de configuração **HelloBlockchain.json** que você criou. O arquivo de configuração é validado automaticamente. Selecione o link **Mostrar** para exibir erros de validação. Corrija os erros de validação antes de implantar o aplicativo.
5. Selecione **Carregar o código de contrato** > **Navegar** para localizar o arquivo do código de contrato inteligente **HelloBlockchain.sol**. O arquivo de código é validado automaticamente. Selecione o link **Mostrar** para exibir erros de validação. Corrija os erros de validação antes de implantar o aplicativo.
6. Selecione **Implantar** para criar o aplicativo blockchain com base na configuração e arquivos de contrato inteligente.

A implantação do aplicativo blockchain leva alguns minutos. Quando a implantação for concluída, o novo aplicativo é exibido em **Aplicativos**. 

> [!NOTE]
> Você também pode criar aplicativos blockchain usando o [API REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench). 

## <a name="add-blockchain-application-members"></a>Adicionar membros do aplicativo blockchain

Adicione membros do aplicativo ao seu aplicativo para iniciar e executar ações em contratos. Para adicionar membros do aplicativo, você precisa ser um [administrador Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

1. Selecione **Aplicativos** > **Hello, Blockchain!**.
2. O número de membros associados ao aplicativo é exibido no canto superior direito da página. Para um novo aplicativo, o número de membros será zero.
3. Selecione o link **membros** no canto superior direito da página. Uma lista atual dos membros para o aplicativo é exibida.
4. Na lista de membros, selecione **Adicionar membros**.
5. Selecione ou insira o nome do membro que deseja adicionar. Somente usuários do Microsoft Azure AD que existem no locatário Blockchain Workbench estão listados. Se o usuário não for encontrado, será necessário [adicionar usuários do Microsoft Azure AD](manage-users.md#add-azure-ad-users).
6. Selecione a **Função** para o membro. Para o primeiro membro, selecione **Solicitante** como a função.
7. Selecione **Adicionar** para adicionar o membro com a função associada ao aplicativo.
8. Adicionar outro membro para o aplicativo com a função **Respondente**.

Para obter mais informações sobre como gerenciar usuários Blockchain Workbench, consulte [gerenciamento de usuários no Azure Blockchain Workbench](manage-users.md)

## <a name="next-steps"></a>Próximas etapas

Neste artigo de procedimentos, você criou um aplicativo básico de solicitação e resposta. Para saber como usar o aplicativo, continue para o próximo artigo de instruções.

> [!div class="nextstepaction"]
> [Usando um aplicativo blockchain](use.md)
