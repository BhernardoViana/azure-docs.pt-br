---
title: Habilitar o Application Insights Profiler para aplicativos que são hospedados nos recursos de Serviços de Nuvem do Azure | Microsoft Docs
description: Saiba como configurar o Application Insights Profiler em um aplicativo em execução nos Serviços de Nuvem do Azure.
services: application-insights
documentationcenter: ''
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: ef6d8cb47971b1cf261edceb13485b940eff3d13
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Habilitar o Application Insights Profiler para VMs do Azure, o Service Fabric e os Serviços de Nuvem do Azure

Este artigo demonstra como habilitar o Azure Application Insights Profiler em um aplicativo ASP.NET hospedado por um recurso de Serviços de Nuvem do Azure.

Os exemplos neste artigo incluem suporte para Máquinas Virtuais do Azure, conjuntos de dimensionamento de máquinas virtuais, Azure Service Fabric e Serviços de Nuvem do Azure. Todos os exemplos dependem de modelos que dão suporte ao modelo de implantação do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Visão geral

A imagem a seguir mostra como o Application Insights Profiler funciona com aplicativos que são hospedados nos recursos de Serviços de Nuvem do Azure. Os recursos de Serviços de Nuvem do Azure incluem Máquinas Virtuais, conjunto de dimensionamento, serviços de nuvem, e clusters do Service Fabric. A imagem usa uma máquina virtual do Azure como um exemplo.  

  ![Diagrama mostrando como o Application Insights Profiler funciona com os recursos dos Serviços de Nuvem do Azure](./media/enable-profiler-compute/overview.png)

Para habilitar completamente o criador de perfil, você deve alterar a configuração em três locais:

* O painel da instância do Application Insights no portal do Azure.
* O código-fonte do aplicativo (por exemplo, um aplicativo Web ASP.NET).
* O código-fonte da definição de implantação do ambiente (por exemplo, um template do Azure Resource Manager em um arquivo .json).


## <a name="set-up-the-application-insights-instance"></a>Configurar a instância do Application Insights

1. [Crie um novo recurso do Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-create-new-resource) ou selecione um existente. 

2. Navegue até o seu recurso do Application Insights e copie a chave de instrumentação.

   ![Localização da chave de instrumentação](./media/enable-profiler-compute/CopyAIKey.png)

3. Para concluir a configuração da instância do Application Insights para o Criador de Perfil, conclua o procedimento descrito em [Habilitar Criador de Perfil]. Você não precisa vincular os aplicativos web, porque as etapas são específicas para o recurso de serviços do aplicativo. Certifique-se de que o criador de perfil esteja habilitado no painel **Configurar Criador de Perfil**.


## <a name="set-up-the-application-source-code"></a>Configurar o código-fonte do aplicativo

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>Aplicativos Web ASP.NET, funções da Web de Serviços de Nuvem do Azure ou front-end de Web do ASP.NET do Service Fabric
Configure seu aplicativo para enviar dados de telemetria a uma instância do Application Insights em cada operação `Request`.  

Adicione o [SDK do Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) ao seu projeto de aplicativo. Verifique se as versões do pacote NuGet são as seguintes:  
  - Para aplicativos ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 ou mais recente.
  - Para aplicativos ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 ou posterior.
  - Para outros aplicativos .NET e .NET Core (por exemplo, um serviço sem estado do Service Fabric ou uma função de trabalho de Serviços de Nuvem): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ou [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 ou mais recente.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Funções de trabalho de Serviços de Nuvem do Azure ou back-end Sem Estado do Servic Fabric
Além de concluir a etapa anterior, se o seu aplicativo *não* for um aplicativo ASP.NET ou ASP.NET Core (por exemplo, se for uma função de trabalho de Serviços de Nuvem do Azure ou APIs sem estado da Malha de Serviço), faça o seguinte:  

  1. No início do tempo de vida do aplicativo, adicione o código a seguir:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Para obter mais informações essa configuração de chave de Instrumentação global, consulte [Use Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usando o Service Fabric com o Application Insights).  

  2. Para qualquer parte do código que você deseje instrumentar, adicione-a entre uma instrução `StartOperation<RequestTelemetry>` **USING**, como no exemplo a seguir:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Não há suporte para chamar `StartOperation<RequestTelemetry>` dentro de outro escopo `StartOperation<RequestTelemetry>`. Você pode usar `StartOperation<DependencyTelemetry>` no escopo aninhado, em vez disso. Por exemplo:   
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```

## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implantação do ambiente

O ambiente no qual o criador de perfil e o aplicativo são executados pode ser uma máquina virtual, um conjunto de dimensionamento de máquinas virtuais, um cluster do Service Fabric ou uma instância de Serviços de Nuvem.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Máquinas virtuais, conjuntos de dimensionamento ou Service Fabric

Exemplos completos:  
  * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Conjunto de dimensionamento de máquinas virtuais](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster do Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Para configurar seu ambiente, faça o seguinte:
1. Para garantir que você esteja usando o [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior, basta confirmar se o sistema operacional implantado é o `Windows Server 2012 R2` ou posterior.

2. Pesquise a extensão [Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) no arquivo do modelo de implantação e, em seguida, adicione a seção `SinksConfig` a seguir como um elemento filho de `WadCfg`. Substitua o valor da propriedade `ApplicationInsightsProfiler` pela sua própria chave de instrumentação do Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Para obter informações sobre como adicionar a extensão de Diagnóstico em seu modelo de implantação, consulte [Usar monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Para Máquinas Virtuais, uma alternativa às etapas baseadas em JSON acima é navegar no portal do Azure para **Máquinas Virtuais** > **Configurações de Diagnóstico** > **Coletores** > Definir a opção Enviar dados de diagnóstico para o Application Insights como **Habilitado** e selecionar uma conta do Application Insights ou uma ikey específica.

### <a name="azure-cloud-services"></a>Serviços de nuvem do Azure

1. Para garantir que você esteja usando o [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior, basta confirmar se os arquivos *ServiceConfiguration.\*.cscfg* têm um `osFamily` valor de "5" ou posterior.

2. Localize o arquivo [diagnostics.wadcfgx](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) do *Diagnóstico do Azure* para sua função de aplicativo, como mostrado aqui:  

   ![Local do arquivo de configuração de diagnóstico](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Se você não encontrar o arquivo, para saber como habilitar a extensão de Diagnóstico em seu projeto de Serviços de Nuvem do Azure, consulte [Configurando o diagnóstico para os Serviços de Nuvem do Azure e máquinas virtuais](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Adicione a seguinte seção `SinksConfig` como um elemento filho do `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

> [!NOTE]  
> Se o arquivo *diagnostics.wadcfgx* também tiver outro coletor do tipo `ApplicationInsights`, todas as três chaves de instrumentação deverão corresponder:  
>  * A chave que é usada pelo seu aplicativo.  
>  * A chave que é usada pelo coletor `ApplicationInsights`.  
>  * A chave que é usada pelo coletor `ApplicationInsightsProfiler`.  
>
> Você pode encontrar o valor real da chave de instrumentação que é usada pelo coletor `ApplicationInsights` nos arquivos *ServiceConfiguration.\*.cscfg*.  
> Após o lançamento do SDK do Azure do Visual Studio 15.5, apenas as chaves de instrumentação que são usadas pelo aplicativo e pelo coletor `ApplicationInsightsProfiler` precisam corresponder.


## <a name="configure-environment-deployment-and-runtime"></a>Configure o tempo de execução e a implantação do ambiente

1. Implante a definição de implantação de ambiente modificada.  

   Normalmente, a aplicação das modificações envolve uma implantação de modelo completo ou uma publicação baseada em serviços de nuvem por meio de cmdlets do PowerShell ou do Visual Studio.  

   Uma abordagem alternativa para máquinas virtuais existentes que envolve apenas a extensão do Diagnóstico do Azure é a seguinte:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Se o aplicativo desejado estiver em execução por meio do [IIS](https://www.microsoft.com/web/platform/server.aspx), habilite o recurso do Windows `IIS Http Tracing` fazendo o seguinte:  

   a. Estabeleça o acesso remoto ao ambiente e, em seguida, use a janela [Adicionar Recursos do Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ou execute o seguinte comando no PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se estabelecer o acesso remoto for um problema, você poderá usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para executar o comando a seguir:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Habilitar o Profiler em servidores locais

A ação de habilitar o criador de perfil em um servidor local também é conhecida como executar o Application Insights Profiler no modo autônomo. Ele não está ligado a modificações de extensão de Diagnóstico do Azure.

Não temos nenhum plano de oferecer suporte oficialmente aos servidores locais. Se você estiver interessado em experimentar este cenário, [baixe o código de suporte](https://github.com/ramach-msft/AIProfiler-Standalone). *Não* somos responsáveis pela manutenção desse código nem por responder a problemas e a solicitações de recursos relacionados ao código.

## <a name="next-steps"></a>Próximas etapas

- Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
- Consulte [Rastreamentos do criador de perfil](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) no portal do Azure.
- Obtenha ajuda para a solução de problemas do criador de perfil em [Solução de problemas do criador de perfil](app-insights-profiler.md#troubleshooting).
- Leia mais sobre o criador de perfil em [Application Insights Profiler](app-insights-profiler.md).
