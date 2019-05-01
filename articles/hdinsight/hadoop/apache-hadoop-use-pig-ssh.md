---
title: Use o Apache Pig com SSH em um cluster do HDInsight - Azure
description: Aprenda como se conectar a um cluster do Apache Hadoop baseado em Linux com o SSH e, em seguida, use o comando Pig para executar instruções Pig Latin interativamente ou como um trabalho em lotes.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 503449e36b2b11e5663449dd732fdaf785417570
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727165"
---
# <a name="run-apache-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Executar tarefas do Apache Pig em um cluster baseado em Linux com o comando Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Aprenda a executar interativamente os trabalhos do Apache Pig a partir de uma conexão SSH para o cluster do HDInsight. A linguagem de programação Pig Latin permite descrever as transformações que são aplicadas aos dados de entrada para produzir a saída desejada.

> [!IMPORTANT]  
> As etapas deste documento exigem um cluster HDInsight baseado em Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Conexão com o SSH

Use o SSH para conectar-se ao cluster HDInsight. O exemplo a seguir se conecta a um cluster chamado **myhdinsight** como a conta denominada **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Usar o comando do Apache Pig

1. Uma vez conectado, inicie a CLI (interface de linha de comando) do Pig usando o comando a seguir:

    ```bash
    pig
    ```

    Após alguns instantes, o prompt muda para `grunt>`.

2. Insira a instrução a seguir:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    Esse comando carrega o conteúdo do arquivo sample.log em LOGS. Você pode exibir o conteúdo do arquivo usando a seguinte instrução:

    ```piglatin
    DUMP LOGS;
    ```

3. Em seguida, transforme os dados aplicando uma expressão regular para extrair apenas o nível de registro em log de cada registro usando a seguinte instrução:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Você pode usar **DUMP** para exibir os dados após a transformação. Nesse caso, use `DUMP LEVELS;`.

4. Continue a aplicar transformações usando as instruções na tabela a seguir:

    | Instrução de Pig Latin | O que a instrução faz |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Remove as linhas que contêm um valor nulo para o nível de log e armazena os resultados em `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Agrupa as linhas pelo nível de log e armazena os resultados em `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Cria um conjunto de dados que contém cada valor de nível de log exclusivo e quantas vezes ele ocorre. O conjunto de dados é armazenado em `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Ordena os níveis de log por contagem (decrescente) e armazena em `RESULT`. |

    > [!TIP]  
    > Use `DUMP` para exibir o resultado da transformação após cada etapa.

5. Você também pode salvar os resultados de uma transformação usando a instrução `STORE` . Por exemplo, a instrução a seguir salva o `RESULT` no diretório `/example/data/pigout` no armazenamento padrão para seu cluster:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]  
   > Os dados são armazenados no diretório especificado nos arquivos chamados `part-nnnnn`. Se o diretório já existir, você receberá um erro.

6. Para sair do prompt do grunt, insira a instrução a seguir:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Arquivos de lote do Pig Latin

Você também pode usar o comando Pig para executar o Pig Latin contido em um arquivo.

1. Depois de sair do prompt do Grunt, use o seguinte comando para criar um arquivo chamado `pigbatch.pig`:

    ```bash
    nano ~/pigbatch.pig
    ```

2. Digite ou cole as seguintes linhas:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    Quando terminar, pressione __Ctrl__  +  __X__, __Y__ e, em seguida, __Enter__ para salvar o arquivo.

3. Use o seguinte comando para executar o arquivo `pigbatch.pig` utilizando o comando Pig.

    ```bash
    pig ~/pigbatch.pig
    ```

    Quando o trabalho em lotes for concluído, você verá o seguinte resultado:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre como usar o Pig no HDInsight, consulte o seguinte documento:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

Para saber mais sobre outras maneiras de trabalhar com o Hadoop no HDInsight, consulte os seguintes documentos:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar MapReduce com Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
