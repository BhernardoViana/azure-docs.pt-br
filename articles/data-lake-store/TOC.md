# [Documentação do Armazenamento do Data Lake Gen1](index.md)

# Visão geral
## [Visão Geral do Armazenamento do Data Lake Gen1](data-lake-store-overview.md)
## [Comparar o Armazenamento do Data Lake Gen1 com o Armazenamento do Azure](data-lake-store-comparison-with-blob-storage.md)
## [Armazenamento do Data Lake Gen1 para o processamento de big data](data-lake-store-data-scenarios.md)
## [Aplicativos de fonte aberta que funcionam com o Armazenamento do Data Lake Gen1](data-lake-store-compatible-oss-other-applications.md)
## [Práticas recomendadas para usar o Armazenamento do Data Lake Gen1](data-lake-store-best-practices.md)

# Introdução
## [Usar o Portal](data-lake-store-get-started-portal.md)
## [Usando o PowerShell](data-lake-store-get-started-powershell.md)
## [Usando a CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)


# Como
## Carregar e mover dados
### [Usar o Azure Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Como usar o Gerenciador de Armazenamento](data-lake-store-in-storage-explorer.md)
### [Como usar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [Como usar DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Como usar o Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Carregar dados de fontes offline](data-lake-store-offline-bulk-data-upload.md)
### [Migrar Data Lake Store entre regiões](data-lake-store-migration-cross-region.md)

## Proteger dados
### [Visão geral da segurança](data-lake-store-security-overview.md)
### [Controle de acesso no Armazenamento do Data Lake Gen1](data-lake-store-access-control.md)
### [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
### [Criptografia](data-lake-store-encryption.md)

## Autenticar com o Armazenamento do Data Lake Gen1
### [Opções de autenticação](data-lakes-store-authentication-using-azure-active-directory.md)
### [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [Usando o Java](data-lake-store-end-user-authenticate-java-sdk.md)
#### [Usar o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
#### [Usar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
#### [Usando o Python](data-lake-store-end-user-authenticate-python.md)
### [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [Usando o Java](data-lake-store-service-to-service-authenticate-java.md)
#### [Usar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [Usar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Usando o Python](data-lake-store-service-to-service-authenticate-python.md)

## Trabalhar com o Armazenamento do Data Lake Gen1
### Operações de gerenciamento de conta
#### [Usar o SDK .NET](data-lake-store-get-started-net-sdk.md)
#### [Usar a API REST](data-lake-store-get-started-rest-api.md)
#### [Usando o Python](data-lake-store-get-started-python.md)
### Operações de sistema de arquivos
#### [Usar o SDK .NET](data-lake-store-data-operations-net-sdk.md)
#### [Usar o SDK do Java](data-lake-store-get-started-java-sdk.md)
#### [Usar a API REST](data-lake-store-data-operations-rest-api.md)
#### [Usando o Python](data-lake-store-data-operations-python.md)

## Desempenho
### [Diretrizes de ajuste do desempenho do Armazenamento do Azure Data Lake Gen1](data-lake-store-performance-tuning-guidance.md)
### [Diretrizes de ajuste do desempenho para usar o PowerShell com o Armazenamento do Data Lake Gen1](data-lake-store-performance-tuning-powershell.md)
### [Diretrizes de ajuste do desempenho para o Spark no HDInsight e no Armazenamento do Data Lake Gen1](data-lake-store-performance-tuning-spark.md)
### [Diretrizes de ajuste do desempenho para o Hive no HDInsight e no Armazenamento do Data Lake Gen1](data-lake-store-performance-tuning-hive.md)
### [Diretrizes de ajuste do desempenho para o MapReduce no HDInsight e no Armazenamento do Data Lake Gen1](data-lake-store-performance-tuning-mapreduce.md)
### [Diretrizes de ajuste do desempenho para o Storm no HDInsight e no Armazenamento do Data Lake Gen1](data-lake-store-performance-tuning-storm.md)

## Integrar-se aos Serviços do Azure
### Com o HDInsight
#### [Usar o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Usando o Azure PowerShell (armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Usando o Azure PowerShell (armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Usando o modelo do Azure](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Acesso de VMs na VNET do Azure](data-lake-store-connectivity-from-vnets.md)
### [Usar com Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Usar com os Hubs de Eventos do Azure](data-lake-store-archive-eventhub-capture.md)
### [Usar com Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Usar com Stream Analytics](data-lake-store-stream-analytics.md)
### [Usar com Power BI](data-lake-store-power-bi.md)
### [Usar com Catálogo de Dados](data-lake-store-with-data-catalog.md)
### [Usar com o PolyBase no SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Usar com o SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [Mais opções de integração do Azure](data-lake-store-integrate-with-other-services.md)

## Gerenciar
### [Acessar logs de diagnóstico](data-lake-store-diagnostic-logs.md)
### [Projetando para alta disponibilidade](data-lake-store-disaster-recovery-guidance.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [PowerShell do Azure](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (Gerenciamento de Conta)](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)
## [Python (Gerenciamento do sistema de arquivos)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [CLI do Azure](https://docs.microsoft.com/cli/azure/dls)

# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/)
## [Blog do Data Lake Store](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Fornecer comentários sobre o UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Preços](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Fórum Stack Overflow](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
