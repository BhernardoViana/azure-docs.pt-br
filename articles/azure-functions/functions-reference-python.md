---
title: Referência de desenvolvedor do Python para o Azure Functions
description: Saiba como desenvolver funções usando Python
ms.topic: article
ms.date: 04/16/2018
ms.openlocfilehash: 7c8ce87fdf396bc488a7deaf576eea28f989e0e4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226638"
---
# <a name="azure-functions-python-developer-guide"></a>Guia do desenvolvedor de Python para o Azure Functions

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando Python. O conteúdo abaixo pressupõe que você já tenha lido o [Guia de desenvolvedores do Azure Functions](functions-reference.md). 

Para projetos de exemplo de função autônoma no Python, consulte os [exemplos de funções do Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modelo de programação

Azure Functions espera que uma função seja um método sem estado em seu script Python que processa a entrada e produz a saída. Por padrão, o tempo de execução espera que o método seja implementado como um método global chamado `main()` no arquivo de `__init__.py`. Você também pode [especificar um ponto de entrada alternativo](#alternate-entry-point).

Os dados de gatilhos e associações são associados à função por meio de atributos de método usando a propriedade `name` definida no arquivo *Function. JSON* . Por exemplo, a _Function. JSON_ abaixo descreve uma função simples disparada por uma solicitação HTTP chamada `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

O arquivo `__init__.py` contém o seguinte código de função:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Você também pode declarar explicitamente os tipos de atributo e o tipo de retorno na função usando anotações do tipo Python. Isso ajuda você a usar o IntelliSense e os recursos de preenchimento automático fornecidos por muitos editores de código Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Use as anotações do Python incluídas no pacote [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) para associar a entrada e as saídas a seus métodos.

## <a name="alternate-entry-point"></a>Ponto de entrada alternativo

Você pode alterar o comportamento padrão de uma função especificando opcionalmente as propriedades `scriptFile` e `entryPoint` no arquivo *Function. JSON* . Por exemplo, a _função. JSON_ a seguir informa o tempo de execução para usar o método `customentry()` no arquivo _Main.py_ , como o ponto de entrada para sua função do Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas recomendada para um projeto de funções do Python é semelhante ao exemplo a seguir:

```
 __app__
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 tests
```
A pasta principal do projeto (\_aplicativo \_\_\_) pode conter os seguintes arquivos:

* *local. Settings. JSON*: usado para armazenar configurações de aplicativo e cadeias de conexão ao executar localmente. Esse arquivo não é publicado no Azure. Para saber mais, consulte [local. Settings. File](functions-run-local.md#local-settings-file).
* *requirements. txt*: contém a lista de pacotes que o sistema instala ao publicar no Azure.
* *host. JSON*: contém opções de configuração global que afetam todas as funções em um aplicativo de funções. Esse arquivo é publicado do Azure. Nem todas as opções têm suporte ao serem executadas localmente. Para saber mais, consulte [host. JSON](functions-host-json.md).
* *funcignore*: (opcional) declara os arquivos que não devem ser publicados no Azure.
* *gitignore*: (opcional) declara os arquivos que são excluídos de um repositório git, como local. Settings. JSON.

Cada função possui seu próprio arquivo de código e arquivo de configuração de associação (function.json). 

O código compartilhado deve ser mantido em uma pasta separada em \_aplicativo \_\_\_. Para fazer referência a módulos na pasta SharedCode, você pode usar a seguinte sintaxe:

```python
from __app__.SharedCode import myFirstHelperFunction
```

Para fazer referência a módulos locais para uma função, você pode usar a sintaxe de importação relativa da seguinte maneira:

```python
from . import example
```

Ao implantar seu projeto em um aplicativo de funções no Azure, todo o conteúdo da pasta *FunctionApp* deve ser incluído no pacote, mas não na própria pasta. É recomendável que você mantenha os testes em uma pasta separada da pasta do projeto, neste exemplo `tests`. Isso impede que você implante o código de teste com seu aplicativo. Para obter mais informações, consulte [testes de unidade](#unit-testing).

## <a name="triggers-and-inputs"></a>Gatilhos e entradas

As entradas são divididas em duas categorias no Azure Functions: entrada do gatilho e entrada adicional. Embora sejam diferentes no arquivo `function.json`, o uso é idêntico no código Python.  Cadeias de conexão ou segredos para fontes de entrada e gatilho são mapeados para valores no arquivo de `local.settings.json` ao serem executados localmente e as configurações do aplicativo durante a execução no Azure. 

Por exemplo, o código a seguir demonstra a diferença entre os dois:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando a função é invocada, a solicitação HTTP é transmitida para a função como `req`. Uma entrada será recuperada do armazenamento de BLOBs do Azure com base na _ID_ na URL de rota e disponibilizada como `obj` no corpo da função.  Aqui, a conta de armazenamento especificada é a cadeia de conexão encontrada em, que é a mesma conta de armazenamento usada pelo aplicativo de funções.


## <a name="outputs"></a>Saídas

A saída pode ser expressa em parâmetros de saída e em valores retornados. Se houver apenas uma saída, recomendamos usar o valor retornado. Para múltiplas saídas, você precisará usar parâmetros de saída.

Para usar o valor retornado de uma função como o valor de uma associação de saída, a propriedade `name` da associação deve ser definida como `$return` em `function.json`.

Para produzir várias saídas, use o método `set()` fornecido pela interface [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) para atribuir um valor à associação. Por exemplo, a função a seguir pode enviar uma mensagem para uma fila e também retornar uma resposta HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Registro em log

O acesso ao agente do Azure Functions runtime está disponível por meio de um manipulador [`logging`](https://docs.python.org/3/library/logging.html#module-logging) raiz no seu aplicativo de funções. Esse agente é vinculado ao Application Insights e permite sinalizar avisos e erros encontrados durante a execução da função.

O exemplo a seguir registra uma mensagem de informações quando a função é invocada por meio de um gatilho HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Há outros métodos de registro em log disponíveis que permitem a gravação no console em níveis de rastreamento diferentes:

| Método                 | DESCRIÇÃO                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Grava uma mensagem com nível CRÍTICO no agente raiz.  |
| **`error(_message_)`**   | Grava uma mensagem com nível ERRO no agente raiz.    |
| **`warning(_message_)`**    | Grava uma mensagem com nível AVISO no agente raiz.  |
| **`info(_message_)`**    | Grava uma mensagem com nível INFORMAÇÕES no agente raiz.  |
| **`debug(_message_)`** | Grava uma mensagem com nível DEPURAR no agente raiz.  |

Para saber mais sobre registro em log, consulte [monitorar Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Gatilhos e associações HTTP

O gatilho HTTP é definido no arquivo function. Jon. O `name` da associação deve corresponder ao parâmetro nomeado na função. Nos exemplos anteriores, um nome de associação `req` é usado. Esse parâmetro é um objeto [HttpRequest] e um objeto [HttpResponse] é retornado.

No objeto [HttpRequest] , você pode obter cabeçalhos de solicitação, parâmetros de consulta, parâmetros de rota e o corpo da mensagem. 

O exemplo a seguir é do [modelo de gatilho http para Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Nessa função, o valor do parâmetro de consulta `name` é obtido do parâmetro `params` do objeto [HttpRequest] . O corpo da mensagem codificada em JSON é lido usando o método `get_json`. 

Da mesma forma, você pode definir o `status_code` e `headers` para a mensagem de resposta no objeto [HttpResponse] retornado.

## <a name="concurrency"></a>Simultaneidade

Por padrão, as funções de tempo de execução do Python só podem processar uma invocação de uma função por vez. Esse nível de simultaneidade pode não ser suficiente em uma ou mais das seguintes condições:

+ Você está tentando lidar com várias invocações sendo feitas ao mesmo tempo.
+ Você está processando um grande número de eventos de e/s.
+ Seu aplicativo está associado à e/s.

Nessas situações, você pode melhorar o desempenho executando de forma assíncrona e usando vários processos de trabalho de linguagem.  

### <a name="async"></a>Assíncrono

Recomendamos que você use a instrução `async def` para fazer sua função ser executada como uma corrotina assíncrona.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

Quando a função `main()` é síncrona (sem o qualificador de `async`), a função é executada automaticamente em um pool de threads `asyncio`.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Usar vários processos de trabalho de idioma

Por padrão, cada instância de host do Functions tem um processo de trabalho de idioma único. No entanto, há suporte para ter vários processos de trabalho de idioma por instância de host. As invocações de função podem ser distribuídas uniformemente entre esses processos de trabalho de linguagem. Use a configuração [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) aplicativo para alterar esse valor. 

## <a name="context"></a>Contexto

Para obter o contexto de invocação de uma função durante a execução, inclua o argumento [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) em sua assinatura. 

Por exemplo:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A classe de [**contexto**](/python/api/azure-functions/azure.functions.context?view=azure-python) tem os seguintes atributos de cadeia de caracteres:

`function_directory`  
O diretório no qual a função está em execução.

`function_name`  
Nome da função.

`invocation_id`  
ID da invocação de função atual.

## <a name="global-variables"></a>Variáveis globais

Não há garantia de que o estado do seu aplicativo será preservado para execuções futuras. No entanto, o tempo de execução de Azure Functions geralmente reutiliza o mesmo processo para várias execuções do mesmo aplicativo. Para armazenar em cache os resultados de uma computação cara, declare-o como uma variável global. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [as configurações de aplicativo](functions-app-settings.md), como cadeias de conexão de serviço, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações declarando `import os` e, em seguida, usando, `setting = os.environ["setting-name"]`.

O exemplo a seguir obtém a [configuração do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings), com a chave chamada `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Para o desenvolvimento local, as configurações do aplicativo são [mantidas no arquivo local. Settings. JSON](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Versão do Python 

Atualmente, Azure Functions dá suporte a Python 3.6. x e 3.7. x (distribuições oficiais do CPython). Ao executar localmente, o tempo de execução usa a versão disponível do Python. Para solicitar uma versão específica do Python ao criar seu aplicativo de funções no Azure, use a opção `--runtime-version` do comando [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) .  

## <a name="package-management"></a>Gerenciamento de pacote

Ao desenvolver localmente usando o Azure Functions Core Tools ou o Visual Studio Code, adicione os nomes e as versões dos pacotes necessários para os arquivos `requirements.txt` e instale-os usando `pip`. 

Por exemplo, o arquivo de requisitos e comando pip a seguir podem ser usados para instalar o pacote `requests` do PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicando no Azure

Quando você estiver pronto para publicar, verifique se todas as suas dependências disponíveis publicamente estão listadas no arquivo Requirements. txt, localizado na raiz do diretório do projeto. 

Arquivos de projeto e pastas que são excluídos da publicação, incluindo a pasta de ambiente virtual, são listados no arquivo. funcignore.

Há três ações de compilação com suporte para publicar seu projeto Python no Azure:

+ Compilação remota: as dependências são obtidas remotamente com base no conteúdo do arquivo Requirements. txt. A [compilação remota](functions-deployment-technologies.md#remote-build) é o método de compilação recomendado. Remoto também é a opção de compilação padrão das ferramentas do Azure. 
+ Compilação local: as dependências são obtidas localmente com base no conteúdo do arquivo Requirements. txt. 
+ Dependências personalizadas: seu projeto usa pacotes não disponíveis publicamente para nossas ferramentas. (Requer o Docker.)

Para criar suas dependências e publicar usando um sistema de entrega contínua (CD), [use Azure pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Build remoto

Por padrão, o Azure Functions Core Tools solicita um Build remoto quando você usa o seguinte comando [Func Azure functionapp Publish](functions-run-local.md#publish) para publicar seu projeto Python no Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

A [extensão Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) também solicita uma compilação remota por padrão. 

### <a name="local-build"></a>Build local

Você pode impedir fazer uma compilação remota usando o seguinte comando [Func functionapp Publish do Azure](functions-run-local.md#publish) para publicar com uma compilação local. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure. 

Usando a opção `--build local`, as dependências do projeto são lidas no arquivo Requirements. txt e esses pacotes dependentes são baixados e instalados localmente. Os arquivos e as dependências do projeto são implantados do computador local no Azure. Isso resulta em um pacote de implantação maior sendo carregado no Azure. Se, por algum motivo, as dependências no arquivo Requirements. txt não puderem ser adquiridas pelas ferramentas principais, você deverá usar a opção dependências personalizadas para publicação. 

### <a name="custom-dependencies"></a>Dependências personalizadas

Se seu projeto usa pacotes que não estão publicamente disponíveis para nossas ferramentas, você pode disponibilizá-los para seu aplicativo, colocando-os no \_aplicativo \_\_\_/. python_packages Directory. Antes de publicar, execute o seguinte comando para instalar as dependências localmente:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Ao usar dependências personalizadas, você deve usar a opção de publicação `--no-build`, já que já instalou as dependências.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

## <a name="unit-testing"></a>Testes de unidade

As funções escritas em Python podem ser testadas como outros códigos Python usando estruturas de teste padrão. Para a maioria das associações, é possível criar um objeto de entrada fictício criando uma instância de uma classe apropriada do pacote de `azure.functions`. Como o pacote de [`azure.functions`](https://pypi.org/project/azure-functions/) não está disponível imediatamente, certifique-se de instalá-lo por meio do arquivo `requirements.txt`, conforme descrito na seção [Gerenciamento de pacotes](#package-management) acima. 

Por exemplo, a seguir está um teste fictício de uma função disparada por HTTP:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Aqui está outro exemplo, com uma função disparada por fila:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>Arquivos temporários

O método `tempfile.gettempdir()` retorna uma pasta temporária, que no Linux é `/tmp`. Seu aplicativo pode usar esse diretório para armazenar arquivos temporários gerados e usados por suas funções durante a execução. 

> [!IMPORTANT]
> Os arquivos gravados no diretório temporário não têm garantia de persistir entre invocações. Durante o scale out, os arquivos temporários não são compartilhados entre instâncias. 

O exemplo a seguir cria um arquivo temporário nomeado no diretório temporário (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Recomendamos que você mantenha os testes em uma pasta separada da pasta do projeto. Isso impede que você implante o código de teste com seu aplicativo. 

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Todos os problemas conhecidos e solicitações de recursos são controlados usando a lista [Problemas do GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se você tiver um problema e não for possível localizá-lo no GitHub, abra um novo problema e inclua uma descrição detalhada dele.

### <a name="cross-origin-resource-sharing"></a>Compartilhamento de recursos entre origens

O Azure Functions dá suporte ao compartilhamento de recursos entre origens (CORS). O CORS é configurado [no portal](functions-how-to-use-azure-function-app-settings.md#cors) e por meio do [CLI do Azure](/cli/azure/functionapp/cors). A lista de origens permitidas pelo CORS aplica-se ao nível do aplicativo de funções. Com o CORS habilitado, as respostas incluem o cabeçalho `Access-Control-Allow-Origin`. Para obter mais informações, consulte [Compartilhamento de recursos entre origens](functions-how-to-use-azure-function-app-settings.md#cors).

Atualmente, a lista de origens permitidas [não tem suporte](https://github.com/Azure/azure-functions-python-worker/issues/444) para aplicativos de funções do Python. Devido a essa limitação, você deve definir expressamente o cabeçalho de `Access-Control-Allow-Origin` em suas funções HTTP, conforme mostrado no exemplo a seguir:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Certifique-se de também atualizar seu function. JSON para dar suporte ao método HTTP OPTIONS:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Esse método é usado pelo navegador Chrome para negociar a lista de origens permitidas. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Documentação da API do pacote Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* [Associações do armazenamento de blobs](functions-bindings-storage-blob.md)
* [Associações HTTP e webhook](functions-bindings-http-webhook.md)
* [Associações de Armazenamento de Filas](functions-bindings-storage-queue.md)
* [Gatilho de temporizador](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
