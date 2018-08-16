---
title: Alterar configurações de cluster do Azure Service Fabric | Microsoft Docs
description: Este artigo descreve as configurações de malha e as políticas de atualização de malha que você pode personalizar.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2018
ms.author: aljo
ms.openlocfilehash: 9e4d65875085ec293813e2683acde095ae112b75
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503699"
---
# <a name="customize-service-fabric-cluster-settings"></a>Saiba como personalizar algumas das configurações de cluster do Service Fabric
Este artigo descreve como personalizar as várias configurações de malha e a política de atualização para o cluster do Service Fabric. Para clusters hospedados no Azure, você pode personalizá-los através do [portal do Azure](https://portal.azure.com) ou utilizando um modelo do Azure Resource Manager. Para clusters autônomos, você pode personalizar as configurações atualizando o arquivo ClusterConfig.json e executar uma atualização de configuração em seu cluster. 

> [!NOTE]
> Nem todas as configurações estão disponíveis no portal. No caso de uma configuração listada abaixo não está disponível por meio do portal, personalize-a usando um modelo do Azure Resource Manager.
> 

## <a name="description-of-the-different-upgrade-policies"></a>Descrição das diferentes políticas de atualização

- **Dinâmico** - As alterações em uma configuração dinâmica não causam nenhuma reinicialização de processo dos processos do Service Fabric ou de seus processos de host de serviço. 
- **Estático** - Alterações em uma configuração estática farão com que o nó do Service Fabric seja reiniciado para consumir a mudança. Os serviços nos nós serão reiniciados.
- **NotAllowed** - Essas configurações não podem ser modificadas. Alterar essas configurações requer que o cluster seja destruído e um novo cluster seja criado. 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalize as configurações do cluster, utilizando os modelos do Resource Manager
As etapas abaixo ilustram como adicionar uma nova configuração *MaxDiskQuotaInMB* à seção *Diagnóstico* usando o Azure Resource Explorer.

1. Acesse https://resources.azure.com
2. Navegue até sua assinatura expandindo **assinaturas** -> **\<Sua assinatura >** -> **resourceGroups** -> **\<Seu grupo de recursos>** -> **provedores** -> **Microsoft.ServiceFabric** -> **clusters** -> **\<Nome do seu cluster>**
3. No canto superior direito, selecione **Leitura/Gravação.**
4. Selecione **Editar** e atualize o elemento `fabricSettings` JSON e adicione um novo elemento:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Você também pode personalizar as configurações de cluster em uma das maneiras a seguir com o Azure Resource Manager:

- Use o [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Use o [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Use a [CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) para exportar e atualizar o modelo do Gerenciador de Recursos.
- Usar os comandos do PowerShell do Azure RM [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) e [Remove-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) para modificar a configuração diretamente.
- Usar os comandos de configuração do cluster[az sf da CLI do Azure](https://docs.microsoft.com/cli/azure/sf/cluster/setting) para modificar a configuração diretamente.

## <a name="customize-cluster-settings-for-standalone-clusters"></a>Personalizar as configurações de cluster para clusters autônomos
Clusters autônomos são configurados por meio do arquivo ClusterConfig.json. Para saber mais, consulte [Definições de configuração para um cluster do Windows](./service-fabric-cluster-manifest.md).

Você pode adicionar, atualizar ou remover as configurações na `fabricSettings` seção na seção Propriedades do [Cluster](./service-fabric-cluster-manifest.md#cluster-properties) em ClusterConfig.json. 

Por exemplo, o JSON a seguir adiciona uma nova configuração *MaxDiskQuotaInMB* para a *seção de diagnóstico*  em `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Depois de modificar as configurações no arquivo ClusterConfig.json, siga as instruções em [Atualizar a configuração do cluster](./service-fabric-cluster-upgrade-windows-server.md#upgrade-the-cluster-configuration) para aplicar as configurações para seu cluster. 


A seguir, é apresentada uma lista de configurações de Malha que você pode personalizar, organizada por seção.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|cadeia de caracteres, o padrão é "None"|estático| Isso não valida o certificado do servidor; suceder o pedido. Consulte config ServiceCertificateThumbprints para a lista separada por vírgula de impressões digitais dos certificados remotos nos quais o proxy reverso pode confiar. Refira a configuração ServiceCommonNameAndIssuer para o nome da assunto e impressão digital do emissor dos certificados remotos que o proxy reverso pode confiar. Para obter mais informações, consulte [Inverter conexão segura de proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, o padrão é 16384 |Dinâmico| Fornece o tamanho da parte em bytes usado para ler o corpo. |
|CrlCheckingFlag|uint, o padrão é 0x40000000 |Dinâmico| Sinalizadores para validação de cadeia de certificados de serviço/aplicativo, por exemplo, verificação da CRL 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Configurar para 0 desabilita a verificação de CRL A lista completa de valores com suporte é documentada por dwFlags de CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tempo em segundos. O padrão é 120 |Dinâmico|Especifique o intervalo de tempo em segundos.  Fornece o tempo limite da solicitação padrão para as solicitações de http que estão sendo processadas no gateway de aplicativo http. |
|ForwardClientCertificate|bool, o padrão é FALSE|Dinâmico|Quando configurado como false, o proxy reverso não solicitará o certificado do cliente.Quando definido como true, o proxy reverso solicitará o certificado do cliente durante o handshake SSL e encaminhará a cadeia de caracteres de formato PEM com codificação base64 para o serviço em um cabeçalho chamado X-Client -Certificado.O serviço pode falhar na solicitação com o código de status apropriado após inspecionar os dados do certificado. Se isso for verdade e o cliente não apresentar um certificado, o proxy reverso encaminhará um cabeçalho vazio e permitirá que o serviço manipule o caso. O proxy reverso atuará como uma camada transparente. Para obter mais informações, consulte [configurar a autenticação de certificado de cliente](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |cadeia de caracteres, o padrão é "None" |estático| Indica o tipo de credenciais de segurança para usar nos ponto de extremidade do gateway do aplicativo Os valores válidos são “None/ X509. |
|GatewayX509CertificateFindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico| Indica como pesquisar o certificado no repositório especificado pelo valor GatewayX509CertificateStoreName com suporte: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | cadeia de caracteres, o padrão é "" |Dinâmico| Valor do filtro de pesquisa usado para localizar o certificado de gateway de aplicativo http. Esse certificado é configurado no ponto de extremidade https e também pode ser usado para verificar a identidade do aplicativo, se necessário, para os serviços. FindValue é pesquisado primeiro; se ele não existir, FindValueSecondary será pesquisado. |
|GatewayX509CertificateFindValueSecondary | cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado de gateway de aplicativo http. Esse certificado é configurado no ponto de extremidade https e também pode ser usado para verificar a identidade do aplicativo, se necessário, para os serviços. FindValue é pesquisado primeiro; se ele não existir, FindValueSecondary será pesquisado.|
|GatewayX509CertificateStoreName |cadeia de caracteres, o padrão é "My" |Dinâmico| Nome do repositório de certificados X.509 que contém o certificado do gateway de aplicativo http. |
|HttpRequestConnectTimeout|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(5)|Dinâmico|Especifique o intervalo de tempo em segundos.  Fornece o tempo limite de conexão para as solicitações de http que estão sendo enviadas no gateway de aplicativo http.  |
|IgnoreCrlOfflineError|bool, o padrão é TRUE|Dinâmico|Se ignorar ou não o erro offline de CRL para verificação do certificado de serviço/aplicativo. |
|IsEnabled |Bool, o padrão é false |estático| Habilita/desabilita o HttpApplicationGateway. HttpApplicationGateway está desabilitado por padrão e essa configuração precisa ser definida para habilitá-lo. |
|NumberOfParallelOperations | Uint, o padrão é 5000 |estático|Número de leituras a serem postadas na fila de servidor http. Isso controla o número de solicitações simultâneas que podem ser atendidas pelo HttpGateway. |
|RemoveServiceResponseHeaders|Cadeia de caracteres, o padrão é "Date; Servidor"|estático|Lista de cabeçalhos de resposta separados por vírgula/ponto e vírgula que serão removidos da resposta do serviço antes de encaminhá-la ao cliente. Se isso estiver definido como cadeia de caracteres vazia, passe todos os cabeçalhos retornados pelo serviço no estado em que se encontram. ou seja não sobrescreva Date e Server |
|ResolveServiceBackoffInterval |Tempo em segundos, o padrão é 5 |Dinâmico|Especifique o intervalo de tempo em segundos.  Fornece o intervalo de retirada padrão antes de tentar uma operação de serviço de resolução falido novamente. |
|SecureOnlyMode|bool, o padrão é FALSE|Dinâmico| SecureOnlyMode: true: o proxy reverso só encaminhará para serviços que publicam pontos de extremidade seguros. false: o proxy reverso pode encaminhar solicitações para pontos de extremidade seguros/não seguros. Para obter mais informações, consulte [reverter a lógica de seleção de ponto de extremidade de proxy](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|cadeia de caracteres, o padrão é ""|Dinâmico|A lista separada por vírgulas das impressões digitais dos certificados remotos em que o proxy reverso pode confiar. Para obter mais informações, consulte [Inverter conexão segura de proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico| Nome do assunto e impressão digital do emissor dos certificados remotos em que o proxy reverso pode confiar. Para obter mais informações, consulte [Inverter conexão segura de proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, o padrão é 0|estático|O MinReplicaSetSize para BackupRestoreService |
|PlacementConstraints|cadeia de caracteres, o padrão é ""|estático|  O PlacementConstraints para o serviço de BackupRestore |
|SecretEncryptionCertThumbprint|cadeia de caracteres, o padrão é ""|Dinâmico|Impressão digital do certificado de criptografia de segredo X509 |
|SecretEncryptionCertX509StoreName|cadeia de caracteres, o padrão é "Meu"|   Dinâmico|    Isso indica que o certificado a ser usado para criptografia e descriptografia de nome de credenciais do repositório de certificados X.509 que é usado para criptografar as credenciais do repositório de criptografia descriptografia usadas pelo serviço de restauração de Backup |
|TargetReplicaSetSize|int, o padrão é 0|estático| O TargetReplicaSetSize para BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | Bool, o padrão é false |Dinâmico|Habilite a atualização de serviços padrão durante a atualização do aplicativo. Descrições de serviço padrão serão substituídas após a atualização. |
|FabricUpgradeHealthCheckInterval |Tempo em segundos, o padrão é de 60 |Dinâmico|A frequência de verificação do status de integridade durante uma atualização de Malha monitorada |
|FabricUpgradeStatusPollInterval |Tempo em segundos, o padrão é de 60 |Dinâmico|A frequência de sondagem do status de atualização de malha. Esse valor determina a taxa de atualização para qualquer chamada GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Tempo em segundos, o padrão é 3 |Dinâmico|Especifique o intervalo de tempo em segundos. A quantidade de tempo para permitir que erros de tempo limite específico do Image Builder para retornar ao cliente. Se esse buffer for pequeno demais, então o cliente atingirá o tempo limite antes do servidor e receberá um erro de tempo limite genérico. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo em segundos, o padrão é de 60 |Dinâmico|Especifique o intervalo de tempo em segundos. A quantidade de tempo para tentar novamente verificações de integridade com falha ao pós-processar uma tarefa de infraestrutura. Observar uma verificação de integridade aprovada redefinirá esse temporizador. |
|InfrastructureTaskHealthCheckStableDuration | Tempo em segundos, o padrão é 0|Dinâmico| Especifique o intervalo de tempo em segundos. A quantidade de tempo para observar verificações de integridade passadas consecutivas antes que o pós-processamento de uma tarefa de infraestrutura seja encerrado com êxito. Observar uma verificação de integridade com falha redefinirá esse temporizador. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo em segundos, o padrão é 0|Dinâmico| Especifique o intervalo de tempo em segundos. A quantidade de tempo de espera antes de iniciar verificações de integridade após pós-processar uma tarefa de infraestrutura. |
|InfrastructureTaskProcessingInterval | Tempo em segundos, o padrão é de 10 |Dinâmico|Especifique o intervalo de tempo em segundos. O intervalo de processamento usado pela tarefa de infraestrutura que está processando o computador de estado. |
|MaxCommunicationTimeout |Tempo em segundos, o padrão é 600 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite máximo para comunicações internas entre ClusterManager e outros serviços do sistema (ou seja, Serviço de Cadastramento; Gerenciador de Failover, etc). Esse tempo limite deve ser menor que MaxOperationTimeout global (como pode haver várias comunicações entre componentes do sistema para cada operação de cliente). |
|MaxDataMigrationTimeout |Tempo em segundos, o padrão é 600 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite máximo para operações de recuperação de migração de dados depois que ocorreu uma atualização do Fabric. |
|MaxOperationRetryDelay |Tempo em segundos, o padrão é 5|Dinâmico| Especifique o intervalo de tempo em segundos. O atraso máximo para as repetições internas quando são encontradas falhas. |
|MaxOperationTimeout |Tempo em segundos, o padrão é MaxValue |Dinâmico| Especifique o intervalo de tempo em segundos. O tempo limite global máximo para processar internamente operações no ClusterManager. |
|MaxTimeoutRetryBuffer | Tempo em segundos, o padrão é 600 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite máximo da operação ao tentar novamente internamente devido a tempos limite é <Original Time out> + <MaxTimeoutRetryBuffer>. Tempo limite adicional é adicionado em incrementos de MinOperationTimeout. |
|MinOperationTimeout | Tempo em segundos, o padrão é de 60 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite global mínimo para processar internamente operações no ClusterManager. |
|MinReplicaSetSize |Int, o padrão é 3 |Não Permitido|O MinReplicaSetSize para ClusterManager. |
|PlacementConstraints | cadeia de caracteres, o padrão é "" |Não Permitido|O PlacementConstraints para ClusterManager. |
|QuorumLossWaitDuration |Tempo em segundos, o padrão é MaxValue |Não Permitido| Especifique o intervalo de tempo em segundos. O QuorumLossWaitDuration para ClusterManager. |
|ReplicaRestartWaitDuration |Tempo em segundos, o padrão é (60,0 * 30)|Não Permitido|Especifique o intervalo de tempo em segundos. O ReplicaRestartWaitDuration para ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo em segundos, o padrão é 1200 |Dinâmico| Especifique o intervalo de tempo em segundos. Se ReplicaSetCheckTimeout for definido como o valor máximo de DWORD, ele será substituído pelo valor dessa configuração para fins de reversão. O valor usado para avanço nunca é substituído. |
|SkipRollbackUpdateDefaultService | Bool, o padrão é false |Dinâmico|O CM ignorará a inversão dos serviços padrão atualizados durante a reversão da atualização do aplicativo. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é (3600,0 * 2)|Não Permitido|Especifique o intervalo de tempo em segundos. O StandByReplicaKeepDuration para ClusterManager. |
|TargetReplicaSetSize |Int, o padrão é 7 |Não Permitido|O TargetReplicaSetSize para ClusterManager. |
|UpgradeHealthCheckInterval |Tempo em segundos, o padrão é de 60 |Dinâmico|A frequência de verificação do status de integridade durante atualizações de um aplicativo monitorado |
|UpgradeStatusPollInterval |Tempo em segundos, o padrão é de 60 |Dinâmico|A frequência de sondagem do status de atualização do aplicativo. Esse valor determina a taxa de atualização para qualquer chamada GetApplicationUpgradeProgress |

## <a name="common"></a>Comum
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tempo em segundos, o padrão é 1 |Dinâmico|Especifique o intervalo de tempo em segundos. Intervalo de monitoramento de desempenho. Definir como 0 ou valor negativo desabilita o monitoramento. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parâmetro** | **Valores permitidos** |**Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, o padrão é None|Dinâmico|Especifica a política seguida pela desfragmentação ao esvaziar nós. Para uma determinada métrica, 0 indica que SF deve tentar desfragmentar nós uniformemente entre UDs e FDs; 1 indica que apenas os nós devem ser desfragmentados |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parâmetro** | **Valores permitidos** |**Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, o padrão é None|Dinâmico|Determina o conjunto de métricas que devem ser usadas para desfragmentação e não para balanceamento de carga. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parâmetro** | **Valores permitidos** |**Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, o padrão é None|Dinâmico|Determina o número de nós livres que são necessários para considerar o cluster desfragmentado especificando o percentual no intervalo [0.0-1.0) ou o número de nós vazios como um número >= 1.0 |

## <a name="diagnostics"></a>Diagnósticos
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, o padrão é true | Dinâmico |Se a representação é necessária ou não ao acessar repositórios de diagnóstico em nome do aplicativo. |
|AppEtwTraceDeletionAgeInDays |Int, o padrão é 3 | Dinâmico |O número de dias após os quais é possível excluir arquivos ETL antigos que contêm rastreamentos ETW do aplicativo. |
|ApplicationLogsFormatVersion |Int, o padrão é 0 | Dinâmico |A versão do formato de logs de aplicativo. Os valores com suporte são 0 e 1. A versão 1 inclui mais campos do registro de eventos ETW que a versão 0. |
|ClusterId |Cadeia de caracteres | Dinâmico |A ID exclusiva do cluster. Ela é gerada quando o cluster é criado. |
|ConsumerInstances |Cadeia de caracteres | Dinâmico |A lista de instâncias de consumidor DCA. |
|DiskFullSafetySpaceInMB |Int, o padrão é 1024 | Dinâmico |Espaço em disco restante em MB para proteger contra o uso pelo DCA. |
|EnableCircularTraceSession |Bool, o padrão é false | estático |O sinalizador indica se as sessões de rastreamento circular devem ser usadas. |
|EnableTelemetry |Bool, o padrão é true | Dinâmico |Isso vai habilitar ou desabilitar a telemetria. |
|MaxDiskQuotaInMB |Int, o padrão é 65536 | Dinâmico |Cota de disco em MB para arquivos de log do Windows Fabric. |
|ProducerInstances |Cadeia de caracteres | Dinâmico |A lista de instâncias de produtor DCA. |

## <a name="dnsservice"></a>DnsService
| **Parâmetro** | **Valores permitidos** |**Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|InstanceCount|int, o padrão é -1|estático|o valor padrão é -1, o que significa que o DnsService está sendo executado em todos os nós. O OneBox precisa que isso seja definido como 1, já que o DnsService usa a porta conhecida 53, portanto, não pode ter várias instâncias na mesma máquina.|
|IsEnabled|bool, o padrão é FALSE|estático|Habilita/desabilita DnsService. O DnsService está desativado por padrão e essa configuração precisa ser definida para ativá-lo. |
|PartitionPrefix|cadeia de caracteres, o padrão é "-"|estático|Controla o valor da cadeia de caracteres do prefixo da partição em consultas DNS para serviços particionados. O valor: <ul><li>Deve ser compatível com RFC, pois fará parte de uma consulta DNS.</li><li>Não deve conter um ponto, '.', Pois o ponto interfere no comportamento do sufixo DNS.</li><li>Não deve ter mais que 5 caracteres.</li><li>Não pode ser uma cadeia de caracteres vazia.</li><li>Se a configuração PartitionPrefix for substituída, o PartitionSuffix deverá ser substituído e vice-versa.</li></ul>Para obter mais informações, consulte [serviço de DNS do Service Fabric.](service-fabric-dnsservice.md).|
|PartitionSuffix|cadeia de caracteres, o padrão é ""|estático|Controla o valor da sequência do sufixo da partição em consultas DNS para serviços particionados. O valor: <ul><li>Deve ser compatível com RFC, pois fará parte de uma consulta DNS.</li><li>Não deve conter um ponto, '.', Pois o ponto interfere no comportamento do sufixo DNS.</li><li>Não deve ter mais que 5 caracteres.</li><li>Se a configuração PartitionPrefix for substituída, o PartitionSuffix deverá ser substituído e vice-versa.</li></ul>Para obter mais informações, consulte [serviço de DNS do Service Fabric.](service-fabric-dnsservice.md). |

## <a name="fabricclient"></a>FabricClient
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tempo em segundos, o padrão é 2 |Dinâmico|Especifique o intervalo de tempo em segundos. Intervalo do tempo limite de conexão para que cada cliente tente abrir uma conexão com o gateway.|
|HealthOperationTimeout |Tempo em segundos, o padrão é 120 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite para uma mensagem de relatório enviada ao Gerenciador de Integridade. |
|HealthReportRetrySendInterval |Tempo em segundos, o padrão é de 30 |Dinâmico|Especifique o intervalo de tempo em segundos. O intervalo no qual o componente de relatório reenvia relatórios de integridade acumulados ao Gerenciador de Integridade. |
|HealthReportSendInterval |Tempo em segundos, o padrão é de 30 |Dinâmico|Especifique o intervalo de tempo em segundos. O intervalo no qual o componente de relatório envia relatórios de integridade acumulados ao Gerenciador de Integridade. |
|KeepAliveIntervalInSeconds |Int, o padrão é 20 |estático|O intervalo no qual o transporte FabricClient envia mensagens keep alive para o gateway. Para 0, keepAlive está desabilitado. Deve ser um valor positivo. |
|MaxFileSenderThreads |Uint, o padrão é 10 |estático|O número máximo de arquivos transferidos paralelamente. |
|NodeAddresses |cadeia de caracteres, o padrão é "" |estático|Uma coleção de endereços (cadeias de conexão) em diferentes nós que podem ser usados para se comunicar com o serviço de nomenclatura. Inicialmente, o cliente se conecta selecionando um dos endereços aleatoriamente. Se mais de uma cadeia de conexão for fornecida e uma conexão falhar devido a um erro de comunicação ou de tempo limite, o cliente usará o próximo endereço na sequência. Consulte a seção de repetição do Endereço do Serviço de Cadastramento para obter detalhes sobre semântica de repetição. |
|PartitionLocationCacheLimit |Int, o padrão é 100000 |estático|Número de partições em cache para a resolução do serviço (definida como 0 para nenhum limite). |
|RetryBackoffInterval |Tempo em segundos, o padrão é 3 |Dinâmico|Especifique o intervalo de tempo em segundos. O intervalo de retirada antes de repetir a operação. |
|ServiceChangePollInterval |Tempo em segundos, o padrão é 120 |Dinâmico|Especifique o intervalo de tempo em segundos. O intervalo entre pesquisas consecutivas para que o serviço seja alterado do cliente para o gateway para retornos de chamada de notificações de alteração do serviço registrado. |

## <a name="fabrichost"></a>FabricHost
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, o padrão é 10 |Dinâmico|Essa é a contagem máxima para a qual o sistema tentará novamente a ativação com falha antes de desistir. |
|ActivationMaxRetryInterval |Tempo em segundos, o padrão é de 300 |Dinâmico|Especifique o intervalo de tempo em segundos. Máx. intervalo de repetição de ativação. Em cada falha contínua, o intervalo de repetição é calculado como Min( ActivationMaxRetryInterval; Contagem de falha contínua * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tempo em segundos, o padrão é 5 |Dinâmico|Especifique o intervalo de tempo em segundos. Intervalo de retirada em cada falha de ativação; em toda falha de ativação contínua, o sistema tentará novamente realizar a ativação até a MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto da falha de ativação contínua e do intervalo de retirada de ativação. |
|EnableRestartManagement |Bool, o padrão é false |Dinâmico|Isso é para habilitar a reinicialização do servidor. |
|EnableServiceFabricAutomaticUpdates |Bool, o padrão é false |Dinâmico|Isso é para habilitar a atualização automática da malha por meio do Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, o padrão é false |Dinâmico|Isso é para habilitar a atualização de base de servidor. |
|StartTimeout |Tempo em segundos, o padrão é de 300 |Dinâmico|Especifique o intervalo de tempo em segundos. Tempo limite para inicialização do fabricactivationmanager. |
|StopTimeout |Tempo em segundos, o padrão é de 300 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite para a ativação; desativação e atualização do serviço hospedado. |

## <a name="fabricnode"></a>FabricNode
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico|Indica como pesquisar o certificado no repositório especificado pelo valor ClientAuthX509StoreName com suporte: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |cadeia de caracteres, o padrão é "" | Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado para a função de administrador padrão FabricClient. |
|ClientAuthX509FindValueSecondary |cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado para a função de administrador padrão FabricClient. |
|ClientAuthX509StoreName |cadeia de caracteres, o padrão é "My" |Dinâmico|Nome do repositório de certificados X.509 que contém o certificado da função de administrador padrão FabricClient. |
|ClusterX509FindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico|Indica como pesquisar o certificado do cluster no repositório especificado pelos valores com suporte ClusterX509StoreName: "FindByThumbprint"; "FindBySubjectName" With "FindBySubjectName"; quando houver várias correspondências, a que tiver a data de expiração mais afastada será usada. |
|ClusterX509FindValue |cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado do cluster. |
|ClusterX509FindValueSecondary |cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado do cluster. |
|ClusterX509StoreName |cadeia de caracteres, o padrão é "My" |Dinâmico|Nome do repositório de certificados X.509 que contém o certificado do cluster para proteger a comunicação dentro do cluster. |
|EndApplicationPortRange |Int, o padrão é 0 |estático|Fim (não inclusive) das portas do aplicativo gerenciadas pelo subsistema de hospedagem. Necessário se EndpointFilteringEnabled for true na hospedagem. |
|ServerAuthX509FindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico|Indica como pesquisar o certificado do servidor no repositório especificado pelo valor ServerAuthX509StoreName com suporte: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado do servidor. |
|ServerAuthX509FindValueSecondary |cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado do servidor. |
|ServerAuthX509StoreName |cadeia de caracteres, o padrão é "My" |Dinâmico|Nome do repositório de certificados X.509 que contém o certificado do servidor para o serviço de entrada. |
|StartApplicationPortRange |Int, o padrão é 0 |estático|Início das portas do aplicativo gerenciadas pelo subsistema de hospedagem. Necessário se EndpointFilteringEnabled for true na hospedagem. |
|StateTraceInterval |Tempo em segundos, o padrão é de 300 |estático|Especifique o intervalo de tempo em segundos. O intervalo para rastrear o status do nó em cada nó e até nós em FM/FMM. |
|UserRoleClientX509FindType |cadeia de caracteres, o padrão é "FindByThumbprint" |Dinâmico|Indica como pesquisar o certificado no repositório especificado pelo valor UserRoleClientX509StoreName com suporte: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado para a função de usuário padrão FabricClient. |
|UserRoleClientX509FindValueSecondary |cadeia de caracteres, o padrão é "" |Dinâmico|Valor do filtro de pesquisa usado para localizar o certificado para a função de usuário padrão FabricClient. |
|UserRoleClientX509StoreName |cadeia de caracteres, o padrão é "My" |Dinâmico|Nome do repositório de certificados X.509 que contém o certificado da função de usuário padrão FabricClient. |

## <a name="failovermanager"></a>FailoverManager
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(3600)|Dinâmico|Especifique o intervalo de tempo em segundos. O limite de tempo para criar uma réplica com estado, depois do qual um relatório de integridade de aviso será iniciado |
|ClusterPauseThreshold|int, o padrão é 1|Dinâmico|Se o número de nós no sistema ficarem abaixo desse valor, o posicionamento, o balanceamento de carga e o failover serão interrompidos. |
|CreateInstanceTimeLimit|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(300)|Dinâmico|Especifique o intervalo de tempo em segundos. O limite de tempo para criar uma instância sem monitoração de estado, depois do qual um relatório de integridade de aviso será iniciado |
|ExpectedClusterSize|int, o padrão é 1|Dinâmico|Quando o cluster é inicializado pela primeira vez, o FM aguardará que essa quantidade de nós relatem informações antes de começar a posicionar outros serviços, inclusive serviços do sistema como o de nomenclatura. Aumentar esse valor aumenta o tempo que leva para um cluster iniciar, mas impede que os nós iniciais sejam sobrecarregados e impede também as movimentações adicionais necessárias conforme mais nós ficam online. Geralmente, esse valor deve ser definido para uma pequena fração do tamanho do cluster inicial. |
|ExpectedNodeDeactivationDuration|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmico|Especifique o intervalo de tempo em segundos. Esta é a duração esperada para a conclusão da desativação de um nó. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmico|Especifique o intervalo de tempo em segundos. Esta é a duração esperada para um nó a ser atualizado durante a atualização do Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(60.0 * 30)|Dinâmico|Especifique o intervalo de tempo em segundos. Esta é a duração esperada para todas as réplicas a serem atualizadas em um nó durante a atualização do aplicativo. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, o padrão é TRUE|Dinâmico|Se definido como true; réplicas com um tamanho 1 de conjunto de réplicas de destino terão permissão para mover durante a atualização. |
|MinReplicaSetSize|int, o padrão é 3|Não Permitido|Esse é o tamanho mínimo do conjunto de réplicas para o FM. Se o número de réplicas ativas do FM cair abaixo desse valor; o FM rejeitará as alterações para o cluster até que pelo menos o número mínimo de réplicas seja recuperado |
|PlacementConstraints|cadeia de caracteres, o padrão é ""|Não Permitido|Quaisquer restrições de posicionamento para as réplicas do gerenciador de failover |
|PlacementTimeLimit|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(600)|Dinâmico|Especifique o intervalo de tempo em segundos. O limite de tempo para atingir a contagem de réplicas, depois do qual um relatório de integridade de aviso será iniciado |
|QuorumLossWaitDuration |Tempo em segundos, o padrão é MaxValue |Dinâmico|Especifique o intervalo de tempo em segundos. Essa é a duração máxima para a qual nós permitimos que uma partição esteja em um estado de perda de quorum. Se a partição ainda estiver em perda de quorum após essa duração, ela será recuperada da perda de quorum considerando as réplicas inativas como perdidas. Observe que isso eventualmente pode resultar em perda de dados. |
|ReconfigurationTimeLimit|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(300)|Dinâmico|Especifique o intervalo de tempo em segundos. O limite de tempo de reconfiguração; depois do qual um relatório de integridade de aviso será iniciado |
|ReplicaRestartWaitDuration|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(60.0 * 30)|Não Permitido|Especifique o intervalo de tempo em segundos. Esse é o ReplicaRestartWaitDuration para o FMService |
|StandByReplicaKeepDuration|Timespan, o padrão é Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Não Permitido|Especifique o intervalo de tempo em segundos. Esse é o StandByReplicaKeepDuration para o FMService |
|TargetReplicaSetSize|int, o padrão é 7|Não Permitido|Este é o número de destino de réplicas FM que serão mantidas pelo Windows Fabric. Um número mais alto resulta em maior confiabilidade dos dados FM, mas como compensação, há uma pequena redução de desempenho. |
|UserMaxStandByReplicaCount |Int, o padrão é 1 |Dinâmico|O número máximo padrão de réplicas Em Espera que o sistema mantém para serviços de usuário. |
|UserReplicaRestartWaitDuration |Tempo em segundos, o padrão é 60,0 * 30 |Dinâmico|Especifique o intervalo de tempo em segundos. Quando uma réplica persistente fica inativa, o Windows Fabric aguarda a duração para que a réplica fique ativa novamente antes de criar novas réplicas de posicionamento (o que exigiria uma cópia do estado). |
|UserStandByReplicaKeepDuration |Tempo em segundos, o padrão é 3600,0 * 24 * 7 |Dinâmico|Especifique o intervalo de tempo em segundos. Quando uma réplica persistente voltar de um estado inativo, talvez ela já tenha sido substituída. Este temporizador determina por quanto tempo o FM manterá a réplica em espera antes de descartá-la. |

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, o padrão é 604800 |estático| Isso é aproximadamente por quanto tempo serão mantidas as ações em um estado terminal. Isso também depende de StoredActionCleanupIntervalInSeconds, uma vez que o trabalho de limpeza é realizado somente nesse intervalo. 604800 é 7 dias. |
|DataLossCheckPollIntervalInSeconds|int, o padrão é 5|estático|Este é o tempo entre as verificações que o sistema executa enquanto aguarda a perda de dados ocorrer. O número de vezes que o número de perda de dados será verificado por iteração interna é DataLossCheckWaitDurationInSeconds/esse valor. |
|DataLossCheckWaitDurationInSeconds|int, o padrão é 25|estático|A quantidade total de tempo em segundos pelo qual o sistema aguardará que a perda de dados ocorra. Isso é usado internamente quando a API StartPartitionDataLossAsync() é chamada. |
|MinReplicaSetSize |Int, o padrão é 0 |estático|O MinReplicaSetSize para FaultAnalysisService. |
|PlacementConstraints | cadeia de caracteres, o padrão é ""|estático| O PlacementConstraints para FaultAnalysisService. |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |estático|Especifique o intervalo de tempo em segundos. O QuorumLossWaitDuration para FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, o padrão é 600|estático|Esse parâmetro é usado quando a API de perda de dados é chamada. Após remove replica ser invocado internamente nele, controla por quanto tempo o sistema aguardará que uma réplica seja descartada. |
|ReplicaRestartWaitDuration |Tempo em segundos, o padrão é 60 minutos|estático|Especifique o intervalo de tempo em segundos. O ReplicaRestartWaitDuration para FaultAnalysisService. |
|StandByReplicaKeepDuration| Tempo em segundos, o padrão é (60*24*7) minutos |estático|Especifique o intervalo de tempo em segundos. O StandByReplicaKeepDuration para FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, o padrão é 3600 |estático|Essa é a frequência com que o repositório será limpo. Somente ações em um estado terminal; e a concluída há pelo menos CompletedActionKeepDurationInSeconds será removida. |
|StoredChaosEventCleanupIntervalInSeconds | Int, o padrão é 3600 |estático|Essa é a frequência com que o repositório será auditado para limpeza; se o número de eventos for maior do que 30000, a limpeza começará. |
|TargetReplicaSetSize |Int, o padrão é 0 |estático|NOT_PLATFORM_UNIX_START O TargetReplicaSetSize para FaultAnalysisService. |

## <a name="federation"></a>Federação
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|GlobalTicketLeaseDuration|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(300)|estático|Especifique o intervalo de tempo em segundos. Nós no cluster precisam manter uma concessão global com os eleitores. Os votantes enviam seus arrendamentos globais para serem propagados através do cluster por esse período. Se a duração expirar; então a concessão é perdida. Perda de quorum de concessões faz com que um nó abandone o cluster; por não receber comunicação com um quorum de nós neste período de tempo.  Esse valor precisa ser ajustado com base no tamanho do cluster. |
|LeaseDuration |Tempo em segundos, o padrão é de 30 |Dinâmico|Duração de uma concessão entre um nó e seus vizinhos. |
|LeaseDurationAcrossFaultDomain |Tempo em segundos, o padrão é de 30 |Dinâmico|Duração de uma concessão entre um nó e seus vizinhos entre domínios de falha. |

## <a name="filestoreservice"></a>FileStoreService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|AcceptChunkUpload|bool, o padrão é TRUE|Dinâmico|Config para determinar se o serviço de armazenamento de arquivos aceita o upload de arquivo baseado em partes ou não durante o pacote do aplicativo de cópia. |
|AnonymousAccessEnabled | Bool, o padrão é true |estático|Habilitar/desabilitar o acesso anônimo aos compartilhamentos do FileStoreService. |
|CommonName1Ntlmx509CommonName|cadeia de caracteres, o padrão é ""|estático| O nome comum do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName1Ntlmx509StoreLocation|cadeia de caracteres, o padrão é "LocalMachine"|estático|O local do repositório do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName1Ntlmx509StoreName|cadeia de caracteres, o padrão é "MY"| estático|O nome do repositório do certificado X509 usado para gerar HMAC no CommonName1NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName2Ntlmx509CommonName|cadeia de caracteres, o padrão é ""|estático|O nome comum do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName2Ntlmx509StoreLocation|cadeia de caracteres, o padrão é "LocalMachine"| estático|O local do repositório do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonName2Ntlmx509StoreName|cadeia de caracteres, o padrão é "MY"|estático| O nome do repositório do certificado X509 usado para gerar HMAC no CommonName2NtlmPasswordSecret ao usar a autenticação NTLM |
|CommonNameNtlmPasswordSecret|SecureString, o padrão é Common::SecureString("")| estático|O segredo de senha usado como semente para gerar a mesma senha gerada ao usar a autenticação NTLM |
|GenerateV1CommonNameAccount| bool, o padrão é TRUE|estático|Especifica se deve gerar uma conta com o algoritmo de geração V1 de nome de usuário. A partir do Service Fabric versão 6.1; uma conta com a geração v2 sempre será criada. A conta V1 é necessária para atualizações de/para as versões que não dão suporte à geração V2 (antes da 6.1).|
|MaxCopyOperationThreads | Uint, o padrão é 0 |Dinâmico| O número máximo de arquivos paralelos que os secundários podem copiar do primário. '0' == número de núcleos. |
|MaxFileOperationThreads | Uint, o padrão é 100 |estático| O número máximo de threads paralelos permitidos para executar FileOperations (Copiar/Mover) no primário. '0' == número de núcleos. |
|MaxRequestProcessingThreads | Uint, o padrão é 200 |estático|O número máximo de threads paralelos permitidos para processar solicitações no primário. '0' == número de núcleos. |
|MaxSecondaryFileCopyFailureThreshold | Uint, o padrão é 25|Dinâmico|O número máximo de repetições de cópia de arquivo no secundário antes de desistir. |
|MaxStoreOperations | Uint, o padrão é 4096 |estático|O número máximo de operações de transação de repositório paralelas permitidas no primário. '0' == número de núcleos. |
|NamingOperationTimeout |Tempo em segundos, o padrão é de 60 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite para executar uma operação de nomenclatura. |
|PrimaryAccountNTLMPasswordSecret | SecureString, o padrão é vazio |estático| O segredo de senha usado como semente para gerar a mesma senha gerada ao usar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreLocation | cadeia de caracteres, o padrão é "LocalMachine"|estático| O local de repositório do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|PrimaryAccountNTLMX509StoreName | cadeia de caracteres, o padrão é "MY"|estático| O nome de repositório do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|PrimaryAccountNTLMX509Thumbprint | cadeia de caracteres, o padrão é ""|estático|A impressão digital de repositório do certificado X509 usado para gerar HMAC no PrimaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|PrimaryAccountType | cadeia de caracteres, o padrão é "" |estático|O AccountType primário da entidade para colocar os compartilhamentos do FileStoreService em ACL. |
|PrimaryAccountUserName | cadeia de caracteres, o padrão é "" |estático|O nome de usuário da conta primária da entidade para colocar os compartilhamentos do FileStoreService em ACL. |
|PrimaryAccountUserPassword | SecureString, o padrão é vazio |estático|O nome de usuário da conta primária da entidade colocar os compartilhamentos do FileStoreService em ACL. |
|QueryOperationTimeout | Tempo em segundos, o padrão é de 60 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite para executar a operação de consulta. |
|SecondaryAccountNTLMPasswordSecret | SecureString, o padrão é vazio |estático| O segredo de senha usado como semente para gerar a mesma senha gerada ao usar a autenticação NTLM. |
|SecondaryAccountNTLMX509StoreLocation | cadeia de caracteres, o padrão é "LocalMachine" |estático|O local de repositório do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|SecondaryAccountNTLMX509StoreName | cadeia de caracteres, o padrão é "MY" |estático|O nome de repositório do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|SecondaryAccountNTLMX509Thumbprint | cadeia de caracteres, o padrão é ""| estático|A impressão digital do certificado X509 usado para gerar HMAC no SecondaryAccountNTLMPasswordSecret ao usar a autenticação NTLM. |
|SecondaryAccountType | cadeia de caracteres, o padrão é ""|estático| O AccountType secundário da entidade para colocar os compartilhamentos do FileStoreService em ACL. |
|SecondaryAccountUserName | cadeia de caracteres, o padrão é ""| estático|O Nome de Usuário da conta secundária da entidade para colocar os compartilhamentos do FileStoreService em ACL. |
|SecondaryAccountUserPassword | SecureString, o padrão é vazio |estático|A senha da conta secundária da entidade para colocar os compartilhamentos do FileStoreService em ACL. |

## <a name="healthmanager"></a>HealthManager
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool, o padrão é false |estático|Política de avaliação de integridade do cluster: habilitar avaliação de integridade de tipo por aplicativo. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, o padrão é false |estático|Política de avaliação de integridade do cluster: os avisos são tratados como erros. |
|MaxPercentUnhealthyApplications | Int, o padrão é 0 |estático|Política de avaliação de integridade do cluster: a porcentagem máxima de aplicativos não íntegros permitida para que o cluster seja íntegro. |
|MaxPercentUnhealthyNodes | Int, o padrão é 0 |estático|Política de avaliação de integridade do cluster: a porcentagem máxima de nós não íntegros permitida para que o cluster seja íntegro. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, o padrão é 10|estático|Política de avaliação de integridade de atualização do cluster: a porcentagem máxima de nós delta não íntegros permitida para que o cluster seja íntegro |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, o padrão é 15|estático|Política de avaliação de integridade de atualização do cluster: a porcentagem máxima de delta de nós não íntegros em um domínio de atualização permitida para que o cluster seja íntegro |

## <a name="hosting"></a>Hosting
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Número inteiro, o padrão é 10 |Dinâmico|Número de vezes que o sistema tenta realizar novamente a ativação que falhou antes de desistir |
|ActivationMaxRetryInterval |Tempo em segundos, o padrão é de 300 |Dinâmico|Em cada falha de ativação contínua, o sistema tenta novamente realizar a ativação até a ActivationMaxFailureCount. ActivationMaxRetryInterval especifica o intervalo de tempo de espera antes de tentar novamente após cada falha de ativação |
|ActivationRetryBackoffInterval |Tempo em segundos, o padrão é de 5 |Dinâmico|Intervalo de retirada em cada falha de ativação; em cada falha de ativação contínua, o sistema tenta novamente realizar a ativação até a MaxActivationFailureCount. O intervalo de repetição em cada tentativa é um produto da falha de ativação contínua e do intervalo de retirada de ativação. |
|ActivationTimeout| TimeSpan, o padrão é Common::TimeSpan::FromSeconds(180)|Dinâmico| Especifique o intervalo de tempo em segundos. O tempo limite para a ativação; desativação e atualização do aplicativo. |
|ApplicationHostCloseTimeout| TimeSpan, o padrão é Common::TimeSpan::FromSeconds(120)|Dinâmico| Especifique o intervalo de tempo em segundos. Quando a saída do Fabric é detectado em um self processos ativado; FabricRuntime fecha todas as réplicas no processo de host (applicationhost) do usuário. Esse é o tempo limite para a operação de fechamento. |
|ApplicationUpgradeTimeout| TimeSpan, o padrão é Common::TimeSpan::FromSeconds(360)|Dinâmico| Especifique o intervalo de tempo em segundos. O tempo limite para a atualização do aplicativo. Se o tempo limite for menor que o "ActivationTimeout", o implantador falhará. |
|ContainerServiceArguments|cadeia de caracteres, o padrão é "-H localhost:2375 -H npipe: / /"|estático|O Service Fabric (SF) gerencia o daemon do docker (exceto em máquinas cliente do Windows como Win10). Essa configuração permite que o usuário especifique argumentos personalizados que devem ser passados para o daemon do docker ao iniciá-lo. Ao especificar argumentos personalizados, o Service Fabric não passa outro argumento para o mecanismo do Docker, com exceção do argumento '--pidfile'. Portanto, os usuários não devem especificar o argumento '--pidfile' como parte de seus argumentos de cliente. Além disso, o argumento de cliente deve garantir que o daemon do docker escuta no pipe de nome padrão no Windows (ou um soquete de domínio do Unix no Linux) para o Service Fabric poder comunicar-se com ele.|
|CreateFabricRuntimeTimeout|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(120)|Dinâmico| Especifique o intervalo de tempo em segundos. O valor de tempo limite para chamada de sincronização de FabricCreateRuntime |
|DefaultContainerRepositoryAccountName|cadeia de caracteres, o padrão é ""|estático|Credenciais padrão usadas em vez de credenciais especificadas no applicationmanifest. XML |
|DefaultContainerRepositoryPassword|cadeia de caracteres, o padrão é ""|estático|Credenciais de senha padrão usadas em vez de credenciais especificadas no applicationmanifest. XML|
|DeploymentMaxFailureCount|int, o padrão é 20| Dinâmico|A implantação de aplicativo será repetida por DeploymentMaxFailureCount vezes antes que a implantação desse aplicativo no nó falhe.| 
|DeploymentMaxRetryInterval| TimeSpan, o padrão é Common::TimeSpan::FromSeconds(3600)|Dinâmico| Especifique o intervalo de tempo em segundos. Máx. intervalo de repetição para a implantação. Em cada falha contínua, o intervalo de repetição é calculado como Min( DeploymentMaxRetryInterval; Contagem de falha contínua * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, o padrão é Common::TimeSpan::FromSeconds(10)|Dinâmico|Especifique o intervalo de tempo em segundos. Intervalo de retirada da falha de implantação. Em toda falha de implantação contínua o sistema fará até MaxDeploymentFailureCount novas tentativas de implantação. O intervalo de repetição é um produto da falha de implantação contínua e do intervalo de retirada de implantação. |
|EnableActivateNoWindow| bool, o padrão é FALSE|Dinâmico| O processo de ativação é criado em segundo plano sem nenhum console. |
|EnableDockerHealthCheckIntegration|bool, o padrão é TRUE|estático|Permite a integração de eventos de verificação de integridade do docker com o relatório de integridade do sistema do Service Fabric |
|EnableProcessDebugging|bool, o padrão é FALSE|Dinâmico| Permite iniciar hosts de aplicativo sob o depurador |
|EndpointProviderEnabled| bool, o padrão é FALSE|estático| Habilita o gerenciamento de recursos de ponto de extremidade pelo Fabric. Requer a especificação do intervalo de portas de aplicativo inicial e final em FabricNode. |
|FabricContainerAppsEnabled| bool, o padrão é FALSE|estático| |
|FirewallPolicyEnabled|bool, o padrão é FALSE|estático| Habilita a abertura de portas de firewall para os recursos de ponto de extremidade com portas explícitas especificadas no ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(120)|Dinâmico|Especifique o intervalo de tempo em segundos. O valor de tempo limite para as chamadas CodePackageActivationContext. Isso não é aplicável a serviços ad hoc. |
|IPProviderEnabled|bool, o padrão é FALSE|estático|Habilita o gerenciamento de endereços IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, o padrão é FALSE|estático|Se o DefaultContainerRepositoryPassword é criptografada ou não.|
|LinuxExternalExecutablePath|cadeia de caracteres, o padrão é "/ usr/bin /" |estático|O diretório principal do comandos executáveis externos no nó.|
|NTLMAuthenticationEnabled|bool, o padrão é FALSE|estático| Habilita o suporte ao uso de NTLM pelos pacotes de código que estão executando como outros usuários para que os processos entre computadores possam se comunicar com segurança. |
|NTLMAuthenticationPasswordSecret|SecureString, o padrão é Common::SecureString("")|estático|É um has criptografado que é usado para gerar a senha para usuários NTLM. Deverá ser definido se NTLMAuthenticationEnabled for true. Validado pelo implantador. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, o padrão é Common::TimeSpan::FromMinutes(3)|Dinâmico|Especifique o intervalo de tempo em segundos. Configurações específicas do ambiente. O intervalo periódico no qual a hospedagem procura novos certificados a serem usados para a configuração NTLM do FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, o padrão é Common::TimeSpan::FromMinutes(4)|Dinâmico| Especifique o intervalo de tempo em segundos. O tempo limite para configurar usuários NTLM usando nomes comuns do certificado. Os usuários NTLM são necessários para compartilhamentos de FileStoreService. |
|RegisterCodePackageHostTimeout|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(120)|Dinâmico| Especifique o intervalo de tempo em segundos. O valor de tempo limite para a chamada de sincronização de FabricRegisterCodePackageHost. Isso é aplicável apenas a hosts de aplicativo com vários pacotes de códigos, tais como o FWP |
|RequestTimeout|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(30)|Dinâmico| Especifique o intervalo de tempo em segundos. Isso representa o tempo limite para a comunicação entre o host de aplicativo do usuário e o processo do Fabric para várias operações relacionadas à hospedagem, tais como o registro de fábrica e o registro de tempo de execução. |
|RunAsPolicyEnabled| bool, o padrão é FALSE|estático| Permite a execução de pacotes de código como um usuário local que não seja o usuário sob o qual o processo do Fabric está sendo executado. Para habilitar essa política, o Fabric deve estar executando como sistema ou usuário que tenha SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, o padrão é Common::TimeSpan::FromSeconds(120)|Dinâmico|Especifique o intervalo de tempo em segundos. O valor de tempo limite para a chamada de sincronização de Register(Stateless/Stateful)ServiceFactory |
|ServiceTypeDisableFailureThreshold |Número inteiro, o padrão é 1 |Dinâmico|Esse é o limite para a contagem de falhas após o qual o FailoverManager (FM) é notificado para desabilitar o tipo de serviço nesse nó e tentar um nó diferente para posicionamento. |
|ServiceTypeDisableGraceInterval|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(30)|Dinâmico|Especifique o intervalo de tempo em segundos. Intervalo de tempo após o qual o tipo de serviço pode ser desabilitado |
|ServiceTypeRegistrationTimeout |Tempo em segundos, o padrão é de 300 |Dinâmico|Tempo máximo permitido para o ServiceType a ser registrado com a malha |
|UseContainerServiceArguments|bool, o padrão é TRUE|estático|Essa configuração ordena que o hosting ignore os argumentos de passagem (especificados na configuração ContainerServiceArguments) para o daemon do docker.|

## <a name="httpgateway"></a>HttpGateway
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, o padrão é 50 |estático| Número de leituras a serem postadas na fila de servidor http. Isso controla o número de solicitações simultâneas que podem ser atendidas pelo HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tempo em segundos, o padrão é de 30 |estático|Especifique o intervalo de tempo em segundos. O intervalo no qual o Gateway HTTP envia relatórios de integridade acumulados ao Gerenciador de Integridade. |
|IsEnabled|Bool, o padrão é false |estático| Habilita/desabilita o HttpGateway. O HttpGateway é desabilitado por padrão. |
|MaxEntityBodySize |Uint, o padrão é 4194304 |Dinâmico|Fornece o tamanho máximo do corpo que se espera de uma solicitação http. O valor padrão é 4 MB. Uma solicitação falhará no httpgateway se ele tiver um corpo de tamanho maior que esse valor. O tamanho mínimo da parte de leitura é 4096 bytes. Portanto, isso tem que ser >= 4096. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ClientCopyTimeout | Tempo em segundos, o padrão é 1800 |Dinâmico| Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de cópia de nível superior para o Serviço de Repositório de Imagens. |
|ClientDefaultTimeout | Tempo em segundos, o padrão é 180 |Dinâmico| Especifique o intervalo de tempo em segundos. Valor de tempo limite para todas as solicitações que não são de upload/que não são de download (por exemplo, existe; excluir) para o Serviço de Repositório de Imagens. |
|ClientDownloadTimeout | Tempo em segundos, o padrão é 1800 |Dinâmico| Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de download de nível superior para o Serviço de Repositório de Imagens. |
|ClientListTimeout | Tempo em segundos, o padrão é 600 |Dinâmico|Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de lista de nível superior para o Serviço de Repositório de Imagens. |
|ClientUploadTimeout |Tempo em segundos, o padrão é 1800 |Dinâmico|Especifique o intervalo de tempo em segundos. Valor de tempo limite para solicitação de upload de nível superior para o Serviço de Repositório de Imagens. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|habilitado |Bool, o padrão é false |estático|O sinalizador Enabled para ImageStoreService. Padrão: false |
|MinReplicaSetSize | Int, o padrão é 3 |estático|O MinReplicaSetSize para ImageStoreService. |
|PlacementConstraints | cadeia de caracteres, o padrão é "" |estático| O PlacementConstraints para ImageStoreService. |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |estático| Especifique o intervalo de tempo em segundos. O QuorumLossWaitDuration para ImageStoreService. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é 60,0 * 30 |estático|Especifique o intervalo de tempo em segundos. O ReplicaRestartWaitDuration para ImageStoreService. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 3600,0 * 2 |estático| Especifique o intervalo de tempo em segundos. O StandByReplicaKeepDuration para ImageStoreService. |
|TargetReplicaSetSize | Int, o padrão é 7 |estático|O TargetReplicaSetSize para ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, o padrão é 1 |Dinâmico|Sinalizador que indica se as configurações de memória devem ser configuradas dinâmica e automaticamente. Se for zero, as definições de configuração de memória serão usadas diretamente e não serão alteradas com base nas condições do sistema. Se for um, as definições da memória serão configuradas automaticamente e poderão ser alteradas com base nas condições do sistema. |
|MaximumDestagingWriteOutstandingInKB | Int, o padrão é 0 |Dinâmico|O número de KB que o log compartilhado tem permissão para ficar à frente do log dedicado. Use 0 para não indicar nenhum limite.
|SharedLogId |cadeia de caracteres, o padrão é "" |estático|Guid exclusivo para o contêiner de log compartilhado. Use "" se usar o caminho padrão na raiz de dados de malha. |
|SharedLogPath |cadeia de caracteres, o padrão é "" |estático|O caminho e o nome de arquivo do local onde será inserido o contêiner do log compartilhado. Use "" para usar o caminho padrão na raiz de dados de malha. |
|SharedLogSizeInMB |Int, o padrão é 8192 |estático|O número de MB a ser alocado no contêiner do log compartilhado. |
|WriteBufferMemoryPoolMaximumInKB | Int, o padrão é 0 |Dinâmico|O número de KB até o qual o pool de memória do buffer de gravação tem permissão para crescer. Use 0 para não indicar nenhum limite. |
|WriteBufferMemoryPoolMinimumInKB |Int, o padrão é 8388608 |Dinâmico|O número de KB para serem inicialmente alocados para o pool de memória do buffer de gravação. Use 0 para indicar que nenhum padrão de limite deve ser consistente com SharedLogSizeInMB abaixo. |

## <a name="management"></a>Gerenciamento
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int, o padrão é 5000 |Dinâmico|O número máximo de conexões simultâneas para o armazenamento do Azure. |
|AzureStorageMaxWorkerThreads | Int, o padrão é 25 |Dinâmico|O número máximo de threads de trabalho em paralelo. |
|AzureStorageOperationTimeout | Tempo em segundos, o padrão é 6000 |Dinâmico|Especifique o intervalo de tempo em segundos. Tempo limite para a operação xstore ser concluída. |
|CleanupApplicationPackageOnProvisionSuccess|bool, o padrão é FALSE |Dinâmico|Essa configuração habilita ou desabilita a limpeza automática de pacote de aplicativo em provisão com êxito. |
|DisableChecksumValidation | Bool, o padrão é false |estático| Essa configuração permite habilitar ou desabilitar a validação de soma de verificação durante o provisionamento de aplicativo. |
|DisableServerSideCopy | Bool, o padrão é false |estático|Essa configuração habilita ou desabilita a cópia do lado do servidor do pacote de aplicativos no ImageStore durante o provisionamento de aplicativo. |
|ImageCachingEnabled | Bool, o padrão é true |estático|Essa configuração permite habilitar ou desabilitar o cache. |
|ImageStoreConnectionString |SecureString |estático|Cadeia de conexão para a raiz do ImageStore. |
|ImageStoreMinimumTransferBPS | Int, o padrão é 1024 |Dinâmico|A taxa de transferência mínima entre o cluster e o ImageStore. Esse valor é usado para determinar o tempo limite ao acessar o ImageStore externo. Altere este valor somente se a latência entre o cluster e o ImageStore for alta para dar mais tempo para o cluster baixar do ImageStore externo. |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parâmetro** | **Valores permitidos** |**Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, o padrão é None|Dinâmico|Determina o conjunto de MetricActivityThresholds para as métricas no cluster. O balanceamento funcionará se maxNodeLoad for maior que MetricActivityThresholds. Para métricas de desfragmentação, ele define a quantidade de carga na qual ou abaixo da qual o Service Fabric considerará o nó vazio |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parâmetro** | **Valores permitidos** |**Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, o padrão é None|Dinâmico|Determina o conjunto de MetricBalancingThresholds para as métricas no cluster. O balanceamento funcionará se maxNodeLoad/minNodeLoad é maior do que MetricBalancingThresholds. A desfragmentação funcionará se maxNodeLoad/minNodeLoad em pelo menos um FD ou UD for menor do que MetricBalancingThresholds. |

## <a name="namingservice"></a>NamingService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, o padrão é 0 |estático|O número máximo de entradas mantidas no cache de descrição do serviço LRU no Gateway de Cadastramento (defina como 0 para sem limite). |
|MaxClientConnections |Int, o padrão é 1000 |Dinâmico|O número máximo permitido de conexões do cliente por gateway. |
|MaxFileOperationTimeout |Tempo em segundos, o padrão é de 30 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite máximo permitido para a operação de serviço de repositório de arquivos. As solicitações que especificarem um tempo limite maior serão rejeitadas. |
|MaxIndexedEmptyPartitions |Int, o padrão é 1000 |Dinâmico|O número máximo de partições vazias que permanecerão indexadas no cache de notificação para sincronizar clientes de reconexão. As partições vazias acima desse número serão removidas do índice em ordem crescente de versão de pesquisa. Os clientes de reconexão ainda podem sincronizar e receber atualizações de partição vazias perdidas, mas o protocolo de sincronização se torna mais caro. |
|MaxMessageSize |Int, o padrão é 4\*1024\*1024 |estático|O tamanho máximo da mensagem para a comunicação do nó do cliente ao usar a nomenclatura. Atenuação do ataque ao DOS, o valor padrão é 4 MB. |
|MaxNamingServiceHealthReports | Int, o padrão é 10 |Dinâmico|O número máximo de operações lentas que o Serviço do Repositório de Cadastramento reporta como não íntegras de uma vez. Se for 0, todas as operações lentas serão enviadas. |
|MaxOperationTimeout |Tempo em segundos, o padrão é 600 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite máximo permitido para operações do cliente. As solicitações que especificarem um tempo limite maior serão rejeitadas. |
|MaxOutstandingNotificationsPerClient |Int, o padrão é 1000 |Dinâmico|O número máximo de notificações pendentes antes que o registro de um cliente seja fechado à força pelo gateway. |
|MinReplicaSetSize | Int, o padrão é 3 |Não Permitido| O número mínimo de réplicas do Serviço de Cadastramento necessárias para serem escritas a fim de concluir uma atualização. Se houver menos réplicas que essas ativas no sistema, o Sistema de Confiabilidade recusará atualização do Repositório do Serviço de Cadastramento até que as réplicas sejam restauradas. Esse valor nunca deve ser maior que o TargetReplicaSetSize. |
|PartitionCount |Int, o padrão é 3 |Não Permitido|O número de partições do repositório do Serviço de Cadastramento a serem criadas. Cada partição tem uma chave de partição única que corresponde ao seu índice. Portanto, chaves de partição então [0; PartitionCount) existem. O aumento no número de partições do Serviço de Cadastramento aumenta a escala na qual o Serviço de Cadastramento pode ser realizado diminuindo a quantidade média de dados mantidos por qualquer conjunto de réplicas de suporte, a um custo do aumento da utilização de recursos (uma vez que as réplicas de serviço PartitionCount*ReplicaSetSize devem ser mantidas).|
|PlacementConstraints | cadeia de caracteres, o padrão é "" |Não Permitido| Restrição de posicionamento para o Serviço de Cadastramento. |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |Não Permitido| Especifique o intervalo de tempo em segundos. Quando um Serviço de Cadastramento entra em perda de quorum, esse temporizador é iniciado. Quando ele expirar, o FM considerará as réplicas inativas como perdidas e tentará recuperar o quorum. Observe que isso pode resultar em perda de dados. |
|RepairInterval | Tempo em segundos, o padrão é 5 |estático| Especifique o intervalo de tempo em segundos. Intervalo em que o reparo de inconsistência de nomenclatura entre o proprietário da autoridade e o proprietário do nome será iniciado. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é (60,0 * 30)|Não Permitido| Especifique o intervalo de tempo em segundos. Quando uma réplica do Serviço de Cadastramento ficar inativa, esse temporizador será iniciado. Quando ele expirar, o FM começará a substituir as réplicas inativas (ele não ainda não as considerará perdidas). |
|ServiceDescriptionCacheLimit | Int, o padrão é 0 |estático| O número máximo de entradas mantidas no cache de descrição do serviço LRU no Serviço do Repositório de Cadastramento (defina como 0 para sem limite). |
|ServiceNotificationTimeout |Tempo em segundos, o padrão é de 30 |Dinâmico|Especifique o intervalo de tempo em segundos. O tempo limite usado ao entregar notificações de serviço ao cliente. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 3600,0 * 2 |Não Permitido| Especifique o intervalo de tempo em segundos. Quando uma réplica do Serviço de Cadastramento volta de um estado inativo, talvez ela já tenha sido substituída. Este temporizador determina por quanto tempo o FM manterá a réplica em espera antes de descartá-la. |
|TargetReplicaSetSize |Int, o padrão é 7 |Não Permitido|O número de conjuntos de réplicas para cada partição do repositório do Serviço de Cadastramento. O aumento no número de conjuntos de réplicas aumenta o nível de confiabilidade das informações no Repositório do Serviço de Cadastramento; a diminuição da alteração de que as informações serão perdidas como resultado de falhas do nó, a um custo do aumento de carga no Windows Fabric e da quantidade de tempo que leva para realizar atualizações nos dados de nomenclatura.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parâmetro** | **Valores permitidos** |**Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, o padrão é None|Dinâmico|Percentual da capacidade de nó por nome da métrica, usado como um buffer para manter algum lugar livre em um nó para o caso de failover. |

## <a name="nodecapacities"></a>NodeCapacities
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |estático|Uma coleção de capacidades de nó para diferentes métricas. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |estático|Descreve os domínios de falha aos quais o nó pertence. O domínio de falha é definido por meio de um URI que descreve o local do nó no data center.  Os URIs do domínio de falha estão no formato fd:/fd/ seguidos por um segmento de caminho do URI.|
|UpgradeDomainId |cadeia de caracteres, o padrão é "" |estático|Descreve o domínio de atualização ao qual o nó pertence. |

## <a name="nodeproperties"></a>NodeProperties
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |estático|Uma coleção de pares chave-valor da cadeia de caracteres para propriedades do nó. |

## <a name="paas"></a>Paas
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ClusterId |cadeia de caracteres, o padrão é "" |Não Permitido|Repositório de certificados X509 usado pela malha para proteção da configuração. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|Contadores |Cadeia de caracteres | Dinâmico |Lista separada por vírgulas dos contadores de desempenho a serem coletados. |
|IsEnabled |Bool, o padrão é true | Dinâmico |O sinalizador indica se a coleta do contador de desempenho no nó local está habilitada. |
|MaxCounterBinaryFileSizeInMB |Int, o padrão é 1 | Dinâmico |Tamanho máximo (em MB) para cada arquivo binário de contador de desempenho. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, o padrão é 10 | Dinâmico |Intervalo máximo (em segundos) após o qual um novo arquivo binário de contador de desempenho é criado. |
|SamplingIntervalInSeconds |Int, o padrão é 60 | Dinâmico |Intervalo de amostragem dos contadores de desempenho que estão sendo coletados. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, o padrão é 0 | Dinâmico|Determina a prioridade da restrição de afinidade: 0: rígida; 1: flexível; negativa: ignorar. |
|ApplicationCapacityConstraintPriority | Int, o padrão é 0 | Dinâmico|Determina a prioridade da restrição de capacidade: 0: difícil; 1: flexível; negativo: ignorar. |
|AutoDetectAvailableResources|bool, o padrão é TRUE|estático|Essa configuração disparará a detecção automática de recursos disponíveis no nó (CPU e memória) quando essa configuração for definida como true – leremos capacidades reais e as corrigiremos se o usuário tiver especificado capacidades de nó incorretas ou não as tiver definido. Se essa configuração for definida como false – rastrearemos um aviso de que o usuário especificou capacidades de nó incorretas, mas não as corrigiremos; isso significa que o usuário deseja ter as capacidades especificadas como maiores do que o nó realmente tem ou, se as capacidades estão indefinidas, ela assumirá capacidade ilimitada |
|BalancingDelayAfterNewNode | Tempo em segundos, o padrão é 120 |Dinâmico|Especifique o intervalo de tempo em segundos. Não inicie atividades de balanceamento nesse período depois de adicionar um novo nó. |
|BalancingDelayAfterNodeDown | Tempo em segundos, o padrão é 120 |Dinâmico|Especifique o intervalo de tempo em segundos. Não inicie atividades de balanceamento nesse período depois de um evento de nó inativo. |
|CapacityConstraintPriority | Int, o padrão é 0 | Dinâmico|Determina a prioridade da restrição de capacidade: 0: difícil; 1: flexível; negativo: ignorar. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, o padrão é 20 | Dinâmico|Define o número de vezes consecutivas que os movimentos emitidos por ResourceBalancer são soltos antes que o diagnóstico seja realizado e os avisos de integridade sejam emitidos. Negativo: nenhum aviso emitido sob essa condição. |
|ConstraintFixPartialDelayAfterNewNode | Tempo em segundos, o padrão é 120 |Dinâmico| Especifique o intervalo de tempo em segundos. Não corrija as violações de restrições FaultDomain e UpgradeDomain nesse período depois de adicionar um novo nó. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo em segundos, o padrão é 120 |Dinâmico| Especifique o intervalo de tempo em segundos. Não corrija violações de restrição FaultDomain e UpgradeDomain nesse período depois de um evento de nó inativo. |
|ConstraintViolationHealthReportLimit | Int, o padrão é 50 |Dinâmico| Define o número de vezes que a réplica de violação de restrição precisa ser persistentemente não fixa antes que os diagnósticos sejam realizados e os relatórios de integridade sejam emitidos. |
|DetailedConstraintViolationHealthReportLimit | Int, o padrão é 200 |Dinâmico| Define o número de vezes que a réplica de violação de restrição precisa ser persistentemente não fixa antes que os diagnósticos sejam realizados e os relatórios detalhados de integridade sejam emitidos. |
|DetailedDiagnosticsInfoListLimit | Int, o padrão é 15 |Dinâmico| Define o número de entradas de diagnóstico (com informações detalhadas) por restrição a serem incluídas antes do truncamento em Diagnósticos.|
|DetailedNodeListLimit | Int, o padrão é 15 |Dinâmico| Define o número de nós por restrição a serem incluídos antes do truncamento nos relatórios de Réplica Não Alocada. |
|DetailedPartitionListLimit | Int, o padrão é 15 |Dinâmico| Define o número de partições por entrada de diagnóstico para uma restrição a serem incluídas antes do truncamento em Diagnósticos. |
|DetailedVerboseHealthReportLimit | Int, o padrão é 200 | Dinâmico|Define o número de vezes que uma réplica não alocada tem que ser persistentemente não alocada antes que os relatórios detalhados de integridade sejam emitidos. |
|FaultDomainConstraintPriority | Int, o padrão é 0 |Dinâmico| Determina a prioridade da restrição de domínio de falha: 0: rígida; 1: flexível; negativa: ignorar. |
|GlobalMovementThrottleCountingInterval | Tempo em segundos, o padrão é 600 |estático| Especifique o intervalo de tempo em segundos. Indica o comprimento do intervalo passado para o qual rastrear movimentos de réplica por domínio (usado juntamente com GlobalMovementThrottleThreshold). Pode ser definido como 0 para ignorar a limitação global completamente. |
|GlobalMovementThrottleThreshold | Uint, o padrão é 1000 |Dinâmico| Número máximo de movimentos permitidos na fase de balanceamento no último intervalo indicado por GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, o padrão é 0 | Dinâmico|Número máximo de movimentos permitidos na fase de balanceamento no último intervalo indicado por GlobalMovementThrottleCountingInterval. 0 indica nenhum limite. |
|GlobalMovementThrottleThresholdForPlacement | Uint, o padrão é 0 |Dinâmico| O número máximo de movimentos permitidos na fase de posicionamento no último intervalo indicado por GlobalMovementThrottleCountingInterval.0 não indica nenhum limite.|
|GlobalMovementThrottleThresholdPercentage|double, o padrão é 0|Dinâmico|Número máximo de movimentações totais permitidas nas fases de balanceamento e posicionamento (expresso como percentual do número total de réplicas no cluster) no intervalo anterior indicado por GlobalMovementThrottleCountingInterval. 0 indica nenhum limite. Se essa configuração e GlobalMovementThrottleThreshold forem especificadas, será usado o limite mais conservador.|
|GlobalMovementThrottleThresholdPercentageForBalancing|double, o padrão é 0|Dinâmico|Número máximo de movimentações permitidas na fase de balanceamento (expresso como percentual do número total de réplicas no PLB) no intervalo anterior indicado por GlobalMovementThrottleCountingInterval. 0 indica nenhum limite. Se essa configuração e GlobalMovementThrottleThresholdForBalancing forem especificadas, será usado o limite mais conservador.|
|InBuildThrottlingAssociatedMetric | cadeia de caracteres, o padrão é "" |estático| O nome da métrica associada para essa limitação. |
|InBuildThrottlingEnabled | Bool, o padrão é false |Dinâmico| Determine se a limitação no build está habilitada. |
|InBuildThrottlingGlobalMaxValue | Int, o padrão é 0 |Dinâmico|O número máximo de réplicas no build permitidas globalmente. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, o padrão é false | Dinâmico|Determina se qualquer tipo de atualização de unidade de failover deve interromper a execução de balanceamento rápida ou lenta. Com o balanceamento “false” especificado, a execução será interrompida se FailoverUnit: for criado/excluído; tiver réplicas ausentes; o local de réplica primário tiver sido alterado ou a quantidade de réplicas tiver sido alterada. A execução de balanceamento NÃO será interrompida em outros casos – se FailoverUnit: tiver réplicas extras; qualquer outro sinalizador de réplica tiver sido alterado; somente a versão de partição ou qualquer outro caso tiver sido alterado. |
|MinConstraintCheckInterval | Tempo em segundos, o padrão é 1 |Dinâmico| Especifique o intervalo de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de dois turnos de verificação de restrição consecutivos. |
|MinLoadBalancingInterval | Tempo em segundos, o padrão é 5 |Dinâmico| Especifique o intervalo de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de dois turnos de balanceamento consecutivos. |
|MinPlacementInterval | Tempo em segundos, o padrão é 1 |Dinâmico| Especifique o intervalo de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes de dois turnos de posicionamento consecutivos. |
|MoveExistingReplicaForPlacement | Bool, o padrão é true |Dinâmico|Configuração que determina se a réplica existente será movida durante o posicionamento. |
|MovementPerPartitionThrottleCountingInterval | Tempo em segundos, o padrão é 600 |estático| Especifique o intervalo de tempo em segundos. Indica o comprimento do intervalo passado para o qual rastrear movimentos de réplica para cada partição (usado juntamente com MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, o padrão é 50 |Dinâmico| Nenhum movimento relacionado a balanceamento ocorrerá para uma partição se o número de movimentos relacionados a balanceamento para réplicas dessa partição tiver alcançado ou excedido MovementPerFailoverUnitThrottleThreshold no último intervalo indicado por MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, o padrão é false |Dinâmico| Configuração que determina se as réplicas pai podem ser movidas para corrigir restrições de afinidade.|
|PartiallyPlaceServices | Bool, o padrão é true |Dinâmico| Determina se todas as réplicas de serviço no cluster serão posicionadas em “tudo ou nada” dados os nós adequados limitados para elas.|
|PlaceChildWithoutParent | Bool, o padrão é true | Dinâmico|Configuração que determinará se a réplica de serviço filho poderá ser posicionada se nenhuma réplica pai estiver ativa. |
|PlacementConstraintPriority | Int, o padrão é 0 | Dinâmico|Determina a prioridade da restrição de posicionamento: 0: rígida; 1: flexível; negativa: ignorar. |
|PlacementConstraintValidationCacheSize | Int, o padrão é 10000 |Dinâmico| Limita o tamanho da tabela usada para rápida validação e cache de Expressões de Restrição de Posicionamento. |
|PlacementSearchTimeout | Tempo em segundos, o padrão é 0,5 |Dinâmico| Especifique o intervalo de tempo em segundos. Ao posicionar os serviços, pesquise por, no máximo, essa duração antes de retornar um resultado. |
|PLBRefreshGap | Tempo em segundos, o padrão é 1 |Dinâmico| Especifique o intervalo de tempo em segundos. Define a quantidade mínima de tempo que deve passar antes que o PLB atualize o estado novamente. |
|PreferredLocationConstraintPriority | Int, o padrão é 2| Dinâmico|Determina a prioridade da restrição de local preferencial: 0: Rígida; 1: flexível; 2: Otimização; negativo: Ignorar |
|PreventTransientOvercommit | Bool, o padrão é false | Dinâmico|Determina se o PLB conta imediatamente com recursos que serão liberados pelos movimentos iniciados. Por padrão, o PLB pode iniciar movimentos para fora e para dentro no mesmo nó que pode criar compromissos excessivos transitórios. Configurar esse parâmetro como true impedirá que esses tipos de compromissos excessivos e a desfragmentação sob demanda (conhecida como placementWithMove) sejam desabilitados. |
|ScaleoutCountConstraintPriority | Int, o padrão é 0 |Dinâmico| Determina a prioridade da restrição de contagem de dimensionamento: 0: rígida; 1: flexível; negativa: ignorar. |
|SwapPrimaryThrottlingAssociatedMetric | cadeia de caracteres, o padrão é ""|estático| O nome da métrica associada para essa limitação. |
|SwapPrimaryThrottlingEnabled | Bool, o padrão é false|Dinâmico| Determine se a limitação de troca primária está habilitada. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, o padrão é 0 |Dinâmico| O número máximo de réplicas de troca primárias permitidas globalmente. |
|TraceCRMReasons |Bool, o padrão é true |Dinâmico|Especifica se os motivos devem ser rastreados para movimentos emitidos pelo CRM para o canal de eventos operacionais. |
|UpgradeDomainConstraintPriority | Int, o padrão é 1| Dinâmico|Determina a prioridade da restrição de domínio de atualização: 0: rígida; 1: flexível; negativa: ignorar. |
|UseMoveCostReports | Bool, o padrão é false | Dinâmico|Instrui o LB a ignorar o elemento de custo da função de pontuação, resultando em uma quantidade de movimentos potencialmente grande para obter posicionamento balanceado. |
|UseSeparateSecondaryLoad | Bool, o padrão é true | Dinâmico|Configuração que determina se uma carga secundária diferente será usada. |
|ValidatePlacementConstraint | Bool, o padrão é true |Dinâmico| Especifica se a expressão PlacementConstraint para um serviço é validada ou não quando um ServiceDescription do serviço é atualizado. |
|VerboseHealthReportLimit | Int, o padrão é 20 | Dinâmico|Define o número de vezes que uma réplica precisa estar não alocada antes que um aviso de integridade seja comunicado para ela (caso o relatório de integridade detalhado esteja habilitado). |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmico|Especifique o intervalo de tempo em segundos. A duração pela qual o sistema aguardará antes de encerrar os hosts de serviço que tiverem réplicas presas no fechamento durante a atualização do aplicativo.|
|FabricUpgradeMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmico| Especifique o intervalo de tempo em segundos. A duração pela qual o sistema aguardará antes de encerrar os hosts de serviço que tenham réplicas presas no fechamento durante a atualização da malha. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(120)|Dinâmico|Especifique o intervalo de tempo em segundos. A duração para a qual o sistema aguardará antes de encerrar os hosts de serviço que têm réplicas presas no fechamento. Se este valor for definido como 0, as réplicas não serão instruídas a fechar.|
|NodeDeactivationMaxReplicaCloseDuration | Tempo em segundos, o padrão é 900 |Dinâmico|Especifique o intervalo de tempo em segundos. A duração pela qual o sistema aguardará antes de encerrar os hosts de serviço que tiverem réplicas presas no fechamento durante a desativação do nó. |
|PeriodicApiSlowTraceInterval | Tempo em segundos, o padrão é 5 minutos |Dinâmico| Especifique o intervalo de tempo em segundos. PeriodicApiSlowTraceInterval define o intervalo durante o qual as chamadas à API lentas serão retraçadas pelo monitor da API. |
|ReplicaChangeRoleFailureRestartThreshold|int, o padrão é 10|Dinâmico| Inteiro. Especifique o número de falhas de API durante a promoção primária após a qual a ação de mitigação automática (reinicialização de réplica) será aplicada. |
|ReplicaChangeRoleFailureWarningReportThreshold|int, o padrão é 2147483647|Dinâmico| Inteiro. Especifique o número de falhas de API durante a promoção primária após a qual o relatório de integridade de aviso será gerada.|
|ServiceApiHealthDuration | Tempo em segundos, o padrão é 30 minutos |Dinâmico| Especifique o intervalo de tempo em segundos. ServiceApiHealthDuration define por quanto tempo é preciso esperar para que uma API de serviço seja executada antes que ela seja comunicada como não íntegra. |
|ServiceReconfigurationApiHealthDuration | Tempo em segundos, o padrão é de 30 |Dinâmico| Especifique o intervalo de tempo em segundos. ServiceReconfigurationApiHealthDuration define por quanto tempo é preciso esperar para que uma API de serviço seja executada antes que ela seja comunicada como não íntegra. Isso se aplica a chamadas à API que afetam a disponibilidade.|

## <a name="replication"></a>Replicação
| **Parâmetro** | **Valores permitidos** | **Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, o padrão é Common::TimeSpan::FromMilliseconds(15)|estático|Especifique o intervalo de tempo em segundos. Determina a quantidade de tempo que o replicador aguarda depois de receber uma operação antes de enviar de volta uma confirmação. Outras operações recebidas durante esse período de tempo terão suas confirmações enviadas de volta em uma única mensagem-> reduzindo o tráfego de rede, mas reduzindo potencialmente a produtividade do replicador.|
|MaxCopyQueueSize|uint, o padrão é 1024|estático|Esse é o valor máximo que define o tamanho inicial da fila que mantém as operações de replicação. Observe que ele deve ser uma potência de 2. Se, durante a o tempo de execução, a fila aumentar para esse tamanho, as operações serão limitadas entre os replicadores primários e secundários.|
|MaxPrimaryReplicationQueueMemorySize|uint, o padrão é 0|estático|Esse é o valor máximo da fila de replicação primária em bytes.|
|MaxPrimaryReplicationQueueSize|uint, o padrão é 1024|estático|Esse é o número máximo de operações que poderiam existir na fila de replicação primária. Observe que ele deve ser uma potência de 2.|
|MaxReplicationMessageSize|uint, o padrão é 52428800|estático|Tamanho máximo da mensagem das operações de replicação. O padrão é 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, o padrão é 0|estático|Esse é o valor máximo da fila de replicação secundária em bytes.|
|MaxSecondaryReplicationQueueSize|uint, o padrão é 2048|estático|Esse é o número máximo de operações que poderiam existir na fila de replicação secundária. Observe que ele deve ser uma potência de 2.|
|QueueHealthMonitoringInterval|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(30)|estático|Especifique o intervalo de tempo em segundos. Esse valor determina o período de tempo usado pelo replicador para monitorar quaisquer eventos de integridade de aviso/erro nas filas de operação de replicação. Um valor de '0' desabilita o monitoramento de integridade |
|QueueHealthWarningAtUsagePercent|uint, o padrão é 80|estático|Esse valor determina o uso de fila de replicação (em percentual) após o qual relatamos um aviso sobre um uso alto de fila. Podemos fazer isso depois de um intervalo de cortesia de QueueHealthMonitoringInterval. Se o uso da fila ficar abaixo desse percentual no intervalo de cortesia|
|ReplicatorAddress|cadeia de caracteres, o padrão é "localhost:0"|estático|O ponto de extremidade na forma de uma cadeia de caracteres -'IP:Port', usada pelo replicador do Windows Fabric para estabelecer conexões com outras réplicas a fim de enviar/receber operações.|
|ReplicatorListenAddress|cadeia de caracteres, o padrão é "localhost:0"|estático|O ponto de extremidade na forma de uma cadeia de caracteres -'IP:Port', usada pelo replicador do Windows Fabric para receber operações de outras réplicas.|
|ReplicatorPublishAddress|cadeia de caracteres, o padrão é "localhost:0"|estático|O ponto de extremidade na forma de uma cadeia de caracteres -'IP:Port', usada pelo replicador do Windows Fabric para enviar operações para outras réplicas.|
|RetryInterval|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(5)|estático|Especifique o intervalo de tempo em segundos. Quando uma operação for perdida ou rejeitada, o temporizador determinará com que frequência o replicador tentará novamente enviar a operação.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|IsEnabled|bool, o padrão é FALSE |estático|Controla se o serviço está habilitado no cluster ou não. |

## <a name="runas"></a>RunAs
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia de caracteres, o padrão é "" |Dinâmico|Indica o nome da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio\usuário" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, o padrão é "" |Dinâmico|Indica o tipo de conta RunAs. Isso é necessário para qualquer seção RunAs Os valores válidos são "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|cadeia de caracteres, o padrão é "" |Dinâmico|Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runasdca"></a>RunAs_DCA
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia de caracteres, o padrão é "" |Dinâmico|Indica o nome da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio\usuário" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, o padrão é "" |Dinâmico|Indica o tipo de conta RunAs. Isso é necessário para qualquer seção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, o padrão é "" |Dinâmico|Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia de caracteres, o padrão é "" |Dinâmico|Indica o nome da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio\usuário" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, o padrão é "" |Dinâmico|Indica o tipo de conta RunAs. Isso é necessário para qualquer seção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, o padrão é "" |Dinâmico|Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|RunAsAccountName |cadeia de caracteres, o padrão é "" |Dinâmico|Indica o nome da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser" ou "ManagedServiceAccount". Os valores válidos são "domínio\usuário" ou "user@domain". |
|RunAsAccountType|cadeia de caracteres, o padrão é "" |Dinâmico|Indica o tipo de conta RunAs. Isso é necessário para qualquer seção RunAs Os valores válidos são "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|cadeia de caracteres, o padrão é "" |Dinâmico|Indica a senha da conta RunAs. Isso só é necessário para o tipo de conta "DomainUser". |

## <a name="security"></a>Segurança
| **Parâmetro** | **Valores permitidos** |**Política de Atualização**| **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|AADClientApplication|cadeia de caracteres, o padrão é ""|estático|ID ou nome do aplicativo cliente nativo que representa os clientes do Fabric |
|AADClusterApplication|cadeia de caracteres, o padrão é ""|estático|ID ou nome do aplicativo de API Web que representa o cluster |
|AADTenantId|cadeia de caracteres, o padrão é ""|estático|ID do locatário (GUID) |
|AdminClientCertThumbprints|cadeia de caracteres, o padrão é ""|Dinâmico|Impressões digitais de certificados usados pelos clientes na função de administrador. É uma lista de nomes separados por vírgula. |
|AdminClientClaims|cadeia de caracteres, o padrão é ""|Dinâmico|Todas as declarações possíveis esperadas de clientes do administrador, com o mesmo formato de ClientClaims. Essa lista é adicionada internamente a ClientClaims, portanto, não há necessidade de adicionar as mesmas entradas também a ClientClaims. |
|AdminClientIdentities|cadeia de caracteres, o padrão é ""|Dinâmico|Identidades do Windows de clientes do Fabric na função de administrador, usadas para autorizar operações do Fabric privilegiadas. É uma lista separada por vírgula em que cada entrada é um nome de conta de domínio ou nome de grupo. Por questão de conveniência, a função de administrador é atribuída automaticamente a ServiceFabricAdministrators e à conta que executa o fabric.exe. |
|CertificateExpirySafetyMargin|TimeSpan, o padrão é Common::TimeSpan::FromMinutes(43200)|estático|Especifique o intervalo de tempo em segundos. Margem de segurança para a expiração do certificado. O status do relatório de integridade do certificado muda de OK para Aviso quando a expiração está mais próxima do que isso. O padrão é 30 dias. |
|CertificateHealthReportingInterval|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(3600 * 8)|estático|Especifique o intervalo de tempo em segundos. Especifique o intervalo de relatórios de integridade de certificado. O valor padrão é de 8 horas. Configurar para 0 desabilita o relatório de integridade do certificado |
|ClientCertThumbprints|cadeia de caracteres, o padrão é ""|Dinâmico|Impressões digitais dos certificados usados pelos clientes para se comunicar com o cluster. O cluster as utiliza para autorizar a conexão de entrada. É uma lista de nomes separados por vírgula. |
|ClientClaimAuthEnabled|bool, o padrão é FALSE|estático|Indica se a autenticação baseada em declarações está habilitada em clientes. Configurar isso para true define ClientRoleEnabled implicitamente. |
|ClientClaims|cadeia de caracteres, o padrão é ""|Dinâmico|Todas as declarações possíveis esperadas de clientes para se conectar ao gateway. Essa é uma lista 'OR': ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry ... cada ClaimsEntry é uma lista "AND": ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|ClientIdentities|cadeia de caracteres, o padrão é ""|Dinâmico|Identidades do Windows de FabricClient. O gateway de nomenclatura utiliza isso para autorizar conexões de entrada. É uma lista separada por vírgula em que cada entrada é um nome de conta de domínio ou nome de grupo. Por questão de conveniência, ServiceFabricAllowedUsers, ServiceFabricAdministrators e a conta que executa o fabric.exe são permitidos automaticamente. |
|ClientRoleEnabled|bool, o padrão é FALSE|estático|Indica se a função do cliente está habilitada; quando definido como true, funções com base nas identidades dos clientes são atribuídas a eles. Para V2, habilitar isso significa que o cliente que não está em AdminClientCommonNames/AdminClientIdentities só pode executar operações somente leitura. |
|ClusterCertThumbprints|cadeia de caracteres, o padrão é ""|Dinâmico|Impressões digitais de certificados com permissão para ingressar no cluster, uma lista de nomes separados por vírgula. |
|ClusterCredentialType|cadeia de caracteres, o padrão é "None"|Não Permitido|Indica o tipo de credenciais de segurança a usar para proteger o cluster. Os valores válidos são "None/X509/Windows" |
|ClusterIdentities|cadeia de caracteres, o padrão é ""|Dinâmico|Identidades do Windows de nós de cluster, usadas para autorização de associação do cluster. É uma lista separada por vírgula em que cada entrada é um nome de conta de domínio ou nome de grupo |
|ClusterSpn|cadeia de caracteres, o padrão é ""|Não Permitido|Nome da entidade de serviço do cluster. Quando o Fabric é executado como um único usuário de domínio (conta de usuário de domínio/gMSA). É o SPN de ouvintes de concessão e ouvintes em fabric.exe: ouvintes de federação, ouvintes de replicação internos, ouvinte de serviço de tempo de execução e ouvinte de gateway de nomenclatura. Isso deve ser deixado vazio quando o Fabric é executado como contas de computador, caso em que o SPN do ouvinte de computação do lado que realiza a conexão do SPN do endereço de transporte do ouvinte. |
|CrlCheckingFlag|uint, o padrão é 0x40000000|Dinâmico|Sinalizador para validação de cadeia de certificados padrão, por exemplo, Federação/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Configurar para 0 desabilita a verificação de CRL A lista completa de valores com suporte é documentada por dwFlags de CertGetCertificateChain: http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|CrlDisablePeriod|TimeSpan, o padrão é Common::TimeSpan::FromMinutes(15)|Dinâmico|Especifique o intervalo de tempo em segundos. Por quanto tempo a verificação de CRL está desabilitada para um determinado certificado depois de encontrar o erro offline caso o erro de CRL offline possa ser ignorado. |
|CrlOfflineHealthReportTtl|TimeSpan, o padrão é Common::TimeSpan::FromMinutes(1440)|Dinâmico|Especifique o intervalo de tempo em segundos. |
|DisableFirewallRuleForDomainProfile| bool, o padrão é TRUE |estático| Indica se a regra de firewall não deve ser habilitada para o perfil de domínio |
|DisableFirewallRuleForPrivateProfile| bool, o padrão é TRUE |estático| Indica se a regra de firewall não deve ser habilitada para o perfil privado | 
|DisableFirewallRuleForPublicProfile| bool, o padrão é TRUE | estático|Indica se a regra de firewall não deve ser habilitada para o perfil público |
|FabricHostSpn| cadeia de caracteres, o padrão é "" |estático| O nome da entidade de serviço de FabricHost quando a malha é executada como um único usuário de domínio (conta de usuário de domínio/gMSA) e FabricHost é executado na conta de computador. É o SPN do ouvinte de IPC para o FabricHost, que por padrão deve ser deixado vazio já que o FabricHost é executado na conta do computador |
|IgnoreCrlOfflineError|bool, o padrão é FALSE|Dinâmico|Se deseja ou não ignorar o erro de CRL offline quando o lado do servidor verifica certificados de cliente de entrada |
|IgnoreSvrCrlOfflineError|bool, o padrão é TRUE|Dinâmico|Se deseja ou não ignorar o erro de CRL offline quando o lado do cliente verifica certificados de servidor de entrada; o padrão é true. Ataques com certificados de servidor revogados exigem um comprometimento de DNS mais intenso do que aqueles com certificados de cliente revogados. |
|ServerAuthCredentialType|cadeia de caracteres, o padrão é "None"|estático|Indica o tipo de credenciais de segurança a usar para proteger a comunicação entre o FabricClient e o Cluster. Os valores válidos são "None/X509/Windows" |
|ServerCertThumbprints|cadeia de caracteres, o padrão é ""|Dinâmico|Impressões digitais dos certificados de servidor usados pelo cluster para se comunicar com os clientes. Os clientes as utilizam para autenticar o cluster. É uma lista de nomes separados por vírgula. |
|SettingsX509StoreName| cadeia de caracteres, o padrão é "MY"| Dinâmico|Repositório de certificados X509 usado pela malha para proteção da configuração |
|UseClusterCertForIpcServerTlsSecurity|bool, o padrão é FALSE|estático|Se usar o certificado de cluster para proteger a unidade de transporte do IPC Server TLS |
|X509Folder|string, o padrão é /var/lib/waagent|estático|Pasta em que as chaves privadas e os certificados X509 estão localizados |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico|Esta é uma lista de par de "Nome" e "Valor". Cada "Name" é do nome comum da entidade ou DnsName do X509 certificados tem autorizados para operações de cliente do administrador. Para um determinado "nome", "Valor" é uma lista de separada por vírgulas das impressões digitais de certificado para o emissor fixação, se não estiver vazio, o emissor direto dos certificados de cliente do administrador deve estar na lista. |

## <a name="securityclientaccess"></a>Security/ClientAccess
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ActivateNode |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para ativar um nó. |
|CancelTestCommand |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Cancelará um TestCommand específico se ele estiver em trânsito. |
|CodePackageControl |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para reiniciar pacotes de códigos. |
|CreateApplication |cadeia de caracteres, o padrão é "Admin" | Dinâmico|Configuração de segurança para a criação de aplicativos. |
|CreateComposeDeployment|cadeia de caracteres, o padrão é "Admin"| Dinâmico|Cria uma implantação de redação descrita pelos arquivos de redação |
|CreateName |cadeia de caracteres, o padrão é "Admin" |Dinâmico|Configuração de segurança para a criação do URI de nomenclatura. |
|CreateService |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para a criação do serviço. |
|CreateServiceFromTemplate |cadeia de caracteres, o padrão é "Admin" |Dinâmico|Configuração de segurança para a criação do serviço com base no modelo. |
|CreateVolume|cadeia de caracteres, o padrão é "Admin"|Dinâmico|CreateVolume |
|DeactivateNode |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para desativar um nó. |
|DeactivateNodesBatch |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para desativar vários nós. |
|Excluir |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configurações de segurança para operação de exclusão do repositório de imagens. |
|DeleteApplication |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para a exclusão de aplicativos. |
|DeleteComposeDeployment|cadeia de caracteres, o padrão é "Admin"| Dinâmico|Exclui a implantação de redação |
|DeleteName |cadeia de caracteres, o padrão é "Admin" |Dinâmico|Configuração de segurança para a exclusão do URI de nomenclatura. |
|DeleteService |cadeia de caracteres, o padrão é "Admin" |Dinâmico|Configuração de segurança para a exclusão do serviço. |
|DeleteVolume|cadeia de caracteres, o padrão é "Admin"|Dinâmico|Exclui um volume.| 
|EnumerateProperties |cadeia de caracteres, o padrão é "Admin\|\|User" | Dinâmico|Configuração de segurança para a enumeração da propriedade de Nomenclatura. |
|EnumerateSubnames |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para a enumeração do URI de Nomenclatura. |
|FileContent |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para transferência do arquivo do cliente do repositório de imagens (externo ao cluster). |
|FileDownload |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para iniciação do download do arquivo do cliente do repositório de imagens (externo ao cluster). |
|FinishInfrastructureTask |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para encerrar tarefas de infraestrutura. |
|GetChaosReport | cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Busca o status de caos dentro de um determinado intervalo de tempo. |
|GetClusterConfiguration | cadeia de caracteres, o padrão é "Admin\|\|User" | Dinâmico|Induz GetClusterConfiguration em uma partição. |
|GetClusterConfigurationUpgradeStatus | cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Induz GetClusterConfigurationUpgradeStatus em uma partição. |
|GetFabricUpgradeStatus |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para sondar o status de atualização do cluster. |
|GetNodeDeactivationStatus |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para verificar o status de desativação. |
|GetNodeTransitionProgress | cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para obter o andamento em um comando de transição do nó. |
|GetPartitionDataLossProgress | cadeia de caracteres, o padrão é "Admin\|\|User" | Dinâmico|Busca o andamento de uma chamada à API de perda de dados de invocação. |
|GetPartitionQuorumLossProgress | cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Busca o andamento de uma chamada à API de perda de quorum de invocação. |
|GetPartitionRestartProgress | cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Busca o andamento de uma chamada à API de partição de redefinição. |
|GetServiceDescription |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para notificações de serviço de sondagem longa e descrições de serviço de leitura. |
|GetStagingLocation |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para recuperação do local de preparo do cliente do repositório de imagens. |
|GetStoreLocation |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para recuperação do local de repositório do cliente do repositório de imagens. |
|GetUpgradeOrchestrationServiceState|cadeia de caracteres, o padrão é "Admin"| Dinâmico|Induz GetUpgradeOrchestrationServiceState em uma partição |
|GetUpgradesPendingApproval |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Induz GetUpgradesPendingApproval em uma partição. |
|GetUpgradeStatus |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para sondar o status de atualização do aplicativo. |
|InternalList |cadeia de caracteres, o padrão é "Admin" | Dinâmico|Configuração de segurança para operação da lista de arquivos do cliente do repositório de imagens (interno). |
|InvokeContainerApi|Wstring, o padrão é "Admin"|Dinâmico|Chama a API do contêiner |
|InvokeInfrastructureCommand |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para comandos de gerenciamento de tarefa de infraestrutura. |
|InvokeInfrastructureQuery |cadeia de caracteres, o padrão é "Admin\|\|User" | Dinâmico|Configuração de segurança para consultar tarefas de infraestrutura. |
|Listar |cadeia de caracteres, o padrão é "Admin\|\|User" | Dinâmico|Configuração de segurança para operação da lista de arquivos do cliente do repositório de imagens. |
|MoveNextFabricUpgradeDomain |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para retomar as atualizações do cluster com um domínio de atualização explícito. |
|MoveNextUpgradeDomain |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para retomar as atualizações do aplicativo com um domínio de atualização explícito. |
|MoveReplicaControl |cadeia de caracteres, o padrão é "Admin" | Dinâmico|Mova a réplica. |
|NameExists |cadeia de caracteres, o padrão é "Admin\|\|User" | Dinâmico|Configuração de segurança para verificações de existência do URI de Nomenclatura. |
|NodeControl |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para iniciar, interromper e reiniciar nós. |
|NodeStateRemoved |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para relatar o estado do nó removido. |
|Ping |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configurações de segurança para pings de clientes. |
|PredeployPackageToNode |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Predeployment api. |
|PrefixResolveService |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para resolução de prefixo do serviço de reclamação. |
|PropertyReadBatch |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para operações de leitura da propriedade de Nomenclatura. |
|PropertyWriteBatch |cadeia de caracteres, o padrão é "Admin" |Dinâmico|Configurações de segurança para operações de gravação da propriedade de Nomenclatura. |
|ProvisionApplicationType |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para provisionamento de tipo de aplicativo. |
|ProvisionFabric |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para MSI e/ou provisionamento de manifesto do cluster. |
|Consultar |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para consultas. |
|RecoverPartition |cadeia de caracteres, o padrão é "Admin" | Dinâmico|Configuração de segurança para recuperar uma partição. |
|RecoverPartitions |cadeia de caracteres, o padrão é "Admin" | Dinâmico|Configuração de segurança para recuperar partições. |
|RecoverServicePartitions |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para recuperar partições de serviço. |
|RecoverSystemPartitions |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para recuperar partições de serviço de sistema. |
|RemoveNodeDeactivations |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para reverter a desativação de vários nós. |
|ReportFabricUpgradeHealth |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para retomar as atualizações do cluster com o andamento da atualização atual. |
|ReportFault |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para falha de relatório. |
|ReportHealth |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para relatar integridade. |
|ReportUpgradeHealth |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para retomar as atualizações do aplicativo com o andamento da atualização atual. |
|ResetPartitionLoad |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para carga de redefinição para um failoverUnit. |
|ResolveNameOwner |cadeia de caracteres, o padrão é "Admin\|\|User" | Dinâmico|Configuração de segurança para determinar o proprietário do URI de Nomenclatura. |
|ResolvePartition |cadeia de caracteres, o padrão é "Admin\|\|User" | Dinâmico|Configuração de segurança para determinar os serviços de sistema. |
|ResolveService |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para a resolução do serviço de reclamação. |
|ResolveSystemService|cadeia de caracteres, o padrão é "Admin\|\|User"|Dinâmico| Configuração de segurança para determinar os serviços de sistema |
|RollbackApplicationUpgrade |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para reverter atualizações do aplicativo. |
|RollbackFabricUpgrade |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para reverter atualizações do cluster. |
|ServiceNotifications |cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para notificações de serviço baseadas em evento. |
|SetUpgradeOrchestrationServiceState|cadeia de caracteres, o padrão é "Admin"| Dinâmico|Induz SetUpgradeOrchestrationServiceState em uma partição |
|StartApprovedUpgrades |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Induz StartApprovedUpgrades em uma partição. |
|StartChaos |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Iniciará o caos se ele não tiver sido iniciado ainda. |
|StartClusterConfigurationUpgrade |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Induz StartClusterConfigurationUpgrade em uma partição. |
|StartInfrastructureTask |cadeia de caracteres, o padrão é "Admin" | Dinâmico|Configuração de segurança para iniciar tarefas de infraestrutura. |
|StartNodeTransition |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para iniciar a transição de um nó. |
|StartPartitionDataLoss |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Induz a perda de dados em uma partição. |
|StartPartitionQuorumLoss |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Induz a perda de quorum em uma partição. |
|StartPartitionRestart |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Reinicia simultaneamente algumas ou todas as réplicas de uma partição. |
|StopChaos |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Interromperá o caos se ele já tiver sido iniciado. |
|ToggleVerboseServicePlacementHealthReporting | cadeia de caracteres, o padrão é "Admin\|\|User" |Dinâmico| Configuração de segurança para alternar o ServicePlacement HealthReporting detalhado. |
|UnprovisionApplicationType |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para desprovisionamento de tipo de aplicativo. |
|UnprovisionFabric |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para MSI e/ou desprovisionamento de manifesto do cluster. |
|UnreliableTransportControl |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Transporte não confiável para adicionar e remover comportamentos. |
|UpdateService |cadeia de caracteres, o padrão é "Admin" |Dinâmico|Configuração de segurança para atualizações de serviço. |
|UpgradeApplication |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para iniciar ou interromper atualizações do aplicativo. |
|UpgradeComposeDeployment|cadeia de caracteres, o padrão é "Admin"| Dinâmico|Atualiza a implantação de redação |
|UpgradeFabric |cadeia de caracteres, o padrão é "Admin" |Dinâmico| Configuração de segurança para iniciar atualizações do cluster. |
|Carregar |cadeia de caracteres, o padrão é "Admin" | Dinâmico|Configuração de segurança para operação de upload do repositório de imagens. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, o padrão é None |Dinâmico|Repositórios de certificados do emissor X509 para certificados do cliente; Nome = clientIssuerCN; Valor = lista separada por vírgula de repositórios |

## <a name="securityclientx509names"></a>Security/AdminClientX509Names
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico|Esta é uma lista de par de "Nome" e "Valor". Cada "Name" é do nome comum da entidade ou DnsName do X509 certificados autorizados a operações do cliente. Para um determinado "nome", "Valor" é uma lista de separada por vírgulas das impressões digitais de certificado para o emissor fixação, se não estiver vazio, o emissor direto de certificados de cliente deve estar na lista.|

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, o padrão é None |Dinâmico|Repositórios de certificados do emissor X509 para certificados do cluster; Nome = clusterIssuerCN; Valor = lista separada por vírgula de repositórios |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico|Esta é uma lista de par de "Nome" e "Valor". Cada "Nome" é de nome comum de assunto ou DnsName de certificados X509 autorizados para operações de cluster. Para um determinado "Nome", "Valor" é uma lista separada por vírgula de impressões digitais de certificado para fixação de emissor, se não estiver vazia, o emissor direto de certificados de cluster deve estar na lista.|

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, o padrão é None |Dinâmico|Repositórios de certificados do emissor X509 para certificados do servidor; Nome = serverIssuerCN; Valor = lista separada por vírgula de repositórios |

## <a name="securityserverx509names"></a>Security/ServerX509Names
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, o padrão é None|Dinâmico|Esta é uma lista de par de "Nome" e "Valor". Cada "Name" é do nome comum da entidade ou DnsName do X509 certificados tem autorizados para operações de servidor. Para um determinado "nome", "Valor" é uma lista de separada por vírgulas das impressões digitais de certificado para o emissor fixação, se não estiver vazio, o emissor direto dos certificados de servidor deve estar na lista.|

## <a name="setup"></a>Configuração
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ContainerNetworkName|cadeia de caracteres, o padrão é ""| estático |O nome de rede a ser usado ao configurar uma rede de contêineres.|
|ContainerNetworkSetup|bool, o padrão é FALSE| estático |Se configurar ou não uma rede de contêiner.|
|FabricDataRoot |Cadeia de caracteres | Não Permitido |Diretório raiz de dados do Service Fabric. O padrão para o Azure é d:\svcfab |
|FabricLogRoot |Cadeia de caracteres | Não Permitido |Diretório raiz de log do Service Fabric. É nele que logs e os rastreamentos do SF são colocados. |
|NodesToBeRemoved|cadeia de caracteres, o padrão é ""| Dinâmico |Os nós que devem ser removidos como parte da atualização de configuração. (Somente para implantações autônomas)|
|ServiceRunAsAccountName |Cadeia de caracteres | Não Permitido |O nome da conta na qual executar o serviço de host de malha. |
|SkipContainerNetworkResetOnReboot|bool, o padrão é FALSE|NotAllowed|Se deseja ignorar a redefinição da rede de contêiner na reinicialização.|
|SkipFirewallConfiguration |Bool, o padrão é false | Não Permitido |Especifica se as configurações do firewall precisam ou não ser definidas pelo sistema. Isso se aplicará apenas se você estiver usando o Firewall do Windows. Se você estiver usando firewalls de terceiros, deverá abrir as portas para o sistema e aplicativos a utilizar |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|Provedores |cadeia de caracteres, o padrão é "DSTS" |estático|Lista separada por vírgulas dos provedores de validação de tokens para habilitar (os provedores válidas são: DSTS; AAD). No momento, somente um provedor pode ser habilitado a qualquer momento. |

## <a name="traceetw"></a>Rastreamento/Etw
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|Nível |Int, o padrão é 4 | Dinâmico |O nível de Rastreamento de Eventos para Windows pode assumir os valores 1, 2, 3 e 4. Para que tenha suporte, você deve manter o nível de rastreamento em 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tempo em segundos, o padrão é 0,015 | estático | Especifique o intervalo de tempo em segundos. Determina a quantidade de tempo que o replicador aguarda depois de receber uma operação antes de enviar de volta uma confirmação. Outras operações recebidas durante esse período de tempo terão suas confirmações enviadas de volta em uma única mensagem-> reduzindo o tráfego de rede, mas reduzindo potencialmente a produtividade do replicador. |
|MaxCopyQueueSize |Uint, o padrão é 16384 | estático |Esse é o valor máximo que define o tamanho inicial da fila que mantém as operações de replicação. Observe que ele deve ser uma potência de 2. Se, durante a o tempo de execução, a fila aumentar para esse tamanho, as operações serão limitadas entre os replicadores primários e secundários. |
|MaxPrimaryReplicationQueueMemorySize |Uint, o padrão é 0 | estático |Esse é o valor máximo da fila de replicação primária em bytes. |
|MaxPrimaryReplicationQueueSize |Uint, o padrão é 8192 | estático |Esse é o número máximo de operações que poderiam existir na fila de replicação primária. Observe que ele deve ser uma potência de 2. |
|MaxReplicationMessageSize |Uint, o padrão é 52428800 | estático | Tamanho máximo da mensagem das operações de replicação. O padrão é 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, o padrão é 0 | estático |Esse é o valor máximo da fila de replicação secundária em bytes. |
|MaxSecondaryReplicationQueueSize |Uint, o padrão é 16384 | estático |Esse é o número máximo de operações que poderiam existir na fila de replicação secundária. Observe que ele deve ser uma potência de 2. |
|ReplicatorAddress |cadeia de caracteres, o padrão é "localhost:0" | estático | O ponto de extremidade na forma de uma cadeia de caracteres -'IP:Port', usada pelo replicador do Windows Fabric para estabelecer conexões com outras réplicas a fim de enviar/receber operações. |

## <a name="transport"></a>Transporte
| **Parâmetro** | **Valores permitidos** |**Política de atualização** |**Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(60)|estático|Especifique o intervalo de tempo em segundos. Tempo limite para a configuração de conexão no lado de entrada e aceitando (incluindo a negociação de segurança no modo de segurança) |
|FrameHeaderErrorCheckingEnabled|bool, o padrão é TRUE|estático|Configuração padrão para verificação de erros no cabeçalho do quadro no modo não seguro; configuração de componente sobrescreve isso. |
|MessageErrorCheckingEnabled|bool, o padrão é FALSO|estático|Configuração padrão para verificação de erros no cabeçalho e no corpo da mensagem no modo não seguro; configuração de componente sobrescreve isso. |
|ResolveOption|cadeia de caracteres, o padrão é "não especificado"|estático|Determina como o FQDN é resolvido.  Os valores válidos são "unspecified/ipv4/ipv6". |
|SendTimeout|TimeSpan, o padrão é Common::TimeSpan::FromSeconds(300)|Dinâmico|Especifique o intervalo de tempo em segundos. Tempo limite para a detecção de conexão paralisado de envio. Relatórios de falhas TCP não são confiáveis em alguns ambientes. Isso pode precisar ser ajustado de acordo com a largura de banda de rede disponível e o tamanho dos dados de saída (\*MaxMessageSize \/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, o padrão é true |estático| Sondagem automática e ação de atualização baseadas em um arquivo de estado de meta. |
|MinReplicaSetSize |Int, o padrão é 0 |estático |O MinReplicaSetSize para UpgradeOrchestrationService.
|PlacementConstraints | cadeia de caracteres, o padrão é "" |estático| O PlacementConstraints para UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tempo em segundos, o padrão é MaxValue |estático| Especifique o intervalo de tempo em segundos. O QuorumLossWaitDuration para UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tempo em segundos, o padrão é 60 minutos|estático| Especifique o intervalo de tempo em segundos. O ReplicaRestartWaitDuration para UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tempo em segundos, o padrão é 60*24*7 minutos |estático| Especifique o intervalo de tempo em segundos. O StandByReplicaKeepDuration para UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, o padrão é 0 |estático |O TargetReplicaSetSize para UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, o padrão é false | estático|Configuração para fazer a atualização de código exigir aprovação do administrador antes de continuar. |

## <a name="upgradeservice"></a>UpgradeService
| **Parâmetro** | **Valores permitidos** | **Política de Atualização** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
|BaseUrl | cadeia de caracteres, o padrão é "" |estático|BaseUrl para UpgradeService. |
|ClusterId | cadeia de caracteres, o padrão é "" |estático|ClusterId para UpgradeService. |
|CoordinatorType | cadeia de caracteres, o padrão é "WUTest"|Não Permitido|O CoordinatorType para UpgradeService. |
|MinReplicaSetSize | Int, o padrão é 2 |Não Permitido| O MinReplicaSetSize para UpgradeService. |
|OnlyBaseUpgrade | Bool, o padrão é false |Dinâmico|OnlyBaseUpgrade para UpgradeService. |
|PlacementConstraints |cadeia de caracteres, o padrão é "" |Não Permitido|O PlacementConstraints para o serviço de atualização. |
|TargetReplicaSetSize | Int, o padrão é 3 |Não Permitido| O TargetReplicaSetSize para UpgradeService. |
|TestCabFolder | cadeia de caracteres, o padrão é "" |estático| TestCabFolder para UpgradeService. |
|X509FindType | cadeia de caracteres, o padrão é ""|Dinâmico| X509FindType para UpgradeService. |
|X509FindValue | cadeia de caracteres, o padrão é "" |Dinâmico| X509FindValue para UpgradeService. |
|X509SecondaryFindValue | cadeia de caracteres, o padrão é "" |Dinâmico| X509SecondaryFindValue para UpgradeService. |
|X509StoreLocation | cadeia de caracteres, o padrão é "" |Dinâmico| X509StoreLocation para UpgradeService. |
|X509StoreName | cadeia de caracteres, o padrão é "My"|Dinâmico|X509StoreName para UpgradeService. |

## <a name="next-steps"></a>Próximas etapas
Leia estes artigos para obter mais informações sobre gerenciamento de cluster:

[Adicionar, transferir e remover certificados do cluster do Azure ](service-fabric-cluster-security-update-certs-azure.md) 

