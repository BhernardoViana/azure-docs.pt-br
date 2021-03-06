---
title: Transferir dados com a biblioteca de movimentação de dados para .NET
titleSuffix: Azure Storage
description: Use a biblioteca de movimentação de dados para mover ou copiar dados de ou para conteúdo de BLOB e arquivo. Copie dados para o Armazenamento do Azure de arquivos locais ou copie dados dentro na mesma conta ou entre contas de armazenamento. Migre facilmente seus dados para o Armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 22dae518a45d5c4af20044d5f3eb88e764e92c8b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895123"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Transferir dados com a biblioteca de movimentação de dados

A biblioteca de movimentação de dados do armazenamento do Azure é uma biblioteca de software livre de plataforma cruzada projetada para carregamento de alto desempenho, download e cópia de BLOBs e arquivos. Essa biblioteca é a estrutura de movimentação de dados principal que capacita o [AzCopy](../storage-use-azcopy.md). A biblioteca de movimentação de dados fornece métodos convenientes que não estão disponíveis na biblioteca de cliente de armazenamento do Azure para .NET. Esses métodos fornecem a capacidade de definir o número de operações paralelas, acompanhar o progresso da transferência, retomar facilmente uma transferência cancelada e muito mais.

Essa biblioteca também usa o .NET Core, que significa que você pode usá-la ao criar aplicativos .NET para Windows, Linux e macOS. Para saber mais sobre o .NET Core, consulte a [documentação do .NET Core](https://dotnet.github.io/). Essa biblioteca também funciona para aplicativos tradicionais do .NET Framework para Windows.

Este documento demonstra como criar um aplicativo de console .NET Core que é executado no Windows, no Linux e no macOS e executa os seguintes cenários:

- Carregar arquivos e diretórios no Armazenamento de Blobs.
- Definir o número de operações paralelas durante a transferência de dados.
- Acompanhar o progresso de transferência de dados.
- Retomar a transferência de dados cancelada.
- Copiar o arquivo da URL para o Armazenamento de Blobs.
- Copiar do Armazenamento de Blobs para o Armazenamento de Blobs.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://code.visualstudio.com/)
- [Uma conta do Armazenamento do Azure](storage-quickstart-create-account.md)

## <a name="setup"></a>Configuração

1. Visite o [.NET Core Installation Guide](https://www.microsoft.com/net/core) (Guia de Instalação do .NET Core) para instalar o .NET Core. Ao selecionar o seu ambiente, escolha a opção de linha de comando.
2. Na linha de comando, crie um diretório para seu projeto. Navegue nesse diretório, digite `dotnet new console -o <sample-project-name>` para criar um projeto de console C#.
3. Abra esse diretório no Visual Studio Code. Esta etapa pode ser feita rapidamente por meio da linha de comando digitando `code .` no Windows.
4. Instale a [extensão do C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) do Marketplace do Visual Studio Code. Reinicie o Visual Studio Code.
5. Neste ponto, você deve ver dois prompts. Uma é para adicionar os "ativos necessários para criar e depurar". Clique em “sim”. Outro prompt é para restaurar dependências não resolvidas. Clique em “restaurar”.
6. Modificar `launch.json` em `.vscode` para usar o terminal externo como um console. Essa configuração deve ser lida como `"console": "externalTerminal"`
7. O Visual Studio Code permite que você depure aplicativos do .NET Core. Pressione `F5` para executar o aplicativo e verificar se a configuração está funcionando. Você deve ver o "Hello World!" impresso no console.

## <a name="add-the-data-movement-library-to-your-project"></a>Adicionar a biblioteca de movimentação de dados ao seu projeto

1. Adicione a versão mais recente da biblioteca de movimentação de dados à seção `dependencies` do arquivo de `<project-name>.csproj`. No momento da escrita, esta versão seria `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Deve ser exibido um prompt para restaurar seu projeto. Clique no botão "restaurar". Você também pode restaurar seu projeto na linha de comando digitando o comando `dotnet restore` na raiz do diretório do projeto.

Modifique `<project-name>.csproj`:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Configurar o esqueleto do aplicativo

A primeira coisa que fazemos é configurar o código de "esqueleto" do nosso aplicativo. Esse código nos solicita um nome de conta de armazenamento e uma chave de conta e usa essas credenciais para criar um objeto `CloudStorageAccount`. Esse objeto é usado para interagir com nossa conta de armazenamento em todos os cenários de transferência. O código também solicita a escolha do tipo de operação de transferência que gostaríamos de executar.

Modifique `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Carregar um arquivo local em um blob

Adicione os métodos `GetSourcePath` e `GetBlob` a `Program.cs`:

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Modifique o método `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Esse código nos solicita o caminho para um arquivo local, o nome de um contêiner de novo ou existente e o nome de um novo blob. O método `TransferManager.UploadAsync` realiza o upload usando essas informações.

Pressione `F5` para executar seu aplicativo. Você pode verificar se o upload ocorreu exibindo sua conta de armazenamento com o [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Definir o número de operações paralelas

Um recurso oferecido pela biblioteca de movimentação de dados é a capacidade de definir o número de operações paralelas para aumentar a taxa de transferência de transferência de dados. Por padrão, a biblioteca de movimentação de dados define o número de operações paralelas como 8 * o número de núcleos em seu computador.

Tenha em mente que muitas operações paralelas em um ambiente de baixa largura de banda podem sobrecarregar a conexão de rede e na verdade impedir que as operações sejam totalmente concluídas. Você precisará experimentar com essa configuração para determinar o que funciona melhor com base na largura de banda de rede disponível.

Vamos adicionar um código que nos permite definir o número de operações paralelas. Também vamos adicionar código cronometra quanto tempo demora para a transferência ser concluída.

Adicione o método `SetNumberOfParallelOperations` a `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Modifique o método `ExecuteChoice` para usar `SetNumberOfParallelOperations`:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Modifique o método `TransferLocalFileToAzureBlob` para usar um temporizador:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Acompanhar o progresso de transferência

Saber quanto tempo demorou para que os dados sejam transferidos é útil. No entanto, ser capaz de ver o progresso da transferência *durante* a operação de transferência seria ainda melhor. Para obter esse cenário, precisamos criar um objeto `TransferContext`. O objeto `TransferContext` vem em duas formas: `SingleTransferContext` e `DirectoryTransferContext`. O primeiro é para transferir um único arquivo e o último é para transferir um diretório de arquivos.

Adicione os métodos `GetSingleTransferContext` e `GetDirectoryTransferContext` a `Program.cs`:

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Modifique o método `TransferLocalFileToAzureBlob` para usar `GetSingleTransferContext`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Retomar uma transferência cancelada

Outro recurso conveniente oferecido pela biblioteca de movimentação de dados é a capacidade de retomar uma transferência cancelada. Vamos adicionar um pouco de código que permite cancelar temporariamente a transferência digitando `c` e, em seguida, retomar a transferência de 3 segundos mais tarde.

Modifique `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Até agora, nosso valor `checkpoint` sempre foi definido como `null`. Agora, se cancelarmos a transferência, recuperamos o último ponto de verificação da nossa transferência e usamos esse novo ponto de verificação em nosso contexto de transferência.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Transferir um diretório local para o armazenamento de BLOBs

Seria desapontador se a biblioteca de movimentação de dados pudesse transferir apenas um arquivo por vez. Felizmente, esse não é o caso. A biblioteca de movimentação de dados fornece a capacidade de transferir um diretório de arquivos e todos os seus subdiretórios. Vamos adicionar um pouco de código que nos permite fazer exatamente isso.

Primeiro, adicione o método `GetBlobDirectory` a `Program.cs`:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Em seguida, modifique `TransferLocalDirectoryToAzureBlobDirectory`:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Há algumas diferenças entre este método e o método de carregamento de um único arquivo. Agora estamos usando `TransferManager.UploadDirectoryAsync` e o método `getDirectoryTransferContext` criado anteriormente. Além disso, agora fornecemos um valor `options` para nossa operação de upload, o que permite indicar que queremos incluir subdiretórios em nosso upload.

## <a name="copy-a-file-from-url-to-a-blob"></a>Copiar um arquivo da URL para um blob

Agora, vamos adicionar o código que permite copiar um arquivo de uma URL para um Blob do Azure.

Modifique `TransferUrlToAzureBlob`:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Um caso de uso importante para esse recurso é quando você precisa mover dados de outro serviço de nuvem (por exemplo, AWS) para o Azure. Contanto que você tenha uma URL que fornece acesso ao recurso, pode mover facilmente esse recurso para Blobs do Azure usando o método `TransferManager.CopyAsync`. Esse método também introduz um novo parâmetro booliano. Configurar esse parâmetro como `true` indica que queremos fazer uma cópia assíncrona do lado do servidor. Configurar esse parâmetro para `false` indica uma cópia síncrona – o que significa que o recurso é baixado primeiramente para nosso computador local e então carregado para o Blob do Azure. No entanto, a cópia síncrona está disponível apenas para a cópia de um recurso do Armazenamento do Azure para outro.

## <a name="copy-a-blob"></a>Copiar um blob

Outro recurso que é fornecido exclusivamente pela biblioteca de movimentação de dados é a capacidade de copiar de um recurso de armazenamento do Azure para outro.

Modifique `TransferAzureBlobToAzureBlob`:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Neste exemplo, definimos o parâmetro booliano em `TransferManager.CopyAsync` como `false` para indicar que queremos fazer uma cópia síncrona. Isso significa que o recurso é baixado para o computador local primeiro e, em seguida, carregado no Blob do Azure. A opção de cópia síncrona é uma ótima maneira de garantir que a operação de cópia tenha uma velocidade consistente. Por outro lado, a velocidade de uma cópia assíncrona do lado do servidor é dependente da largura de banda de rede disponível no servidor, que pode flutuar. No entanto, a cópia síncrona pode gerar custo de saída adicional em comparação com a cópia assíncrona. A abordagem recomendada é usar essa a cópia síncrona na VM do Azure que está na mesma região que a sua conta de armazenamento de origem, a fim de evitar o custo de saída.

O aplicativo de movimentação de dados agora está concluído. [O exemplo de código completo está disponível no GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Próximos passos

[Documentação de referência da biblioteca de movimentação de dados do armazenamento do Azure](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
