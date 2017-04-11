<properties 
    pageTitle="Configurar o aplicativo ideias SDK com ApplicationInsights.config ou. XML" 
    description="Ativar ou desativar módulos de conjunto de dados e adicione contadores de desempenho e outros parâmetros" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/12/2016" 
    ms.author="awills"/>

# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar a obtenção de informações do aplicativo SDK com ApplicationInsights.config ou. XML

O aplicativo ideias .NET SDK consiste em um número de pacotes do NuGet. O [pacote de núcleo](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para envio de telemetria para a obtenção de informações do aplicativo. [Pacotes adicionais](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecem de telemetria _módulos_ e _inicializadores_ para controlar automaticamente telemetria de seu aplicativo e seu contexto. Ajustando o arquivo de configuração, você pode habilitar ou desabilitar inicializadores e módulos de telemetria e definir parâmetros para algumas delas.

O arquivo de configuração é chamado `ApplicationInsights.config` ou `ApplicationInsights.xml`, dependendo do tipo de seu aplicativo. Ele é automaticamente adicionado ao seu projeto quando você [instalar a maioria das versões do SDK][start]. Ele também é adicionado a um aplicativo web pelo [Monitor de Status em um servidor IIS][redfield], ou quando você seleciona a obtenção de informações de Appplication [extensão para um site Azure ou máquina virtual](app-insights-azure-web-apps.md).

Não existe um arquivo equivalente para controlar o [SDK em uma página da web][client].

Este documento descreve as seções que você vê na configuração de arquivos, como eles controlam os componentes do SDK e quais pacotes NuGet carregar esses componentes.

## <a name="telemetry-modules-aspnet"></a>Módulos de telemetria (ASP.NET)

Cada módulo de telemetria coleta um tipo específico de dados e usa a API principal para enviar os dados. Os módulos são instalados pelo diferentes pacotes NuGet, que também adicionar as linhas necessárias para o arquivo. config.

Não há um nó no arquivo de configuração para cada módulo. Para desativar um módulo, exclua o nó ou comentar-out.



### <a name="dependency-tracking"></a>Dependência de rastreamento

[Dependência de rastreamento](app-insights-dependencies.md) coleta telemetria sobre seu aplicativo faz para bancos de dados e serviços externos e de chamadas. Para permitir que este módulo para trabalhar em um servidor IIS, é necessário [instalar o Monitor de Status][redfield]. Para usá-lo em aplicativos web Azure ou VMs, [Selecione a extensão de obtenção de informações do aplicativo](app-insights-azure-web-apps.md).

Você também pode escrever seu próprio código usando a [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency)de rastreamento de dependências.


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Pacote do NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

### <a name="performance-collector"></a>Coletor de desempenho

A [coleta de contadores de desempenho do sistema](app-insights-performance-counters.md) como carga de CPU, memória e rede de instalações do IIS. Você pode especificar quais contadores para coletar, incluindo contadores de desempenho que você configurou por conta própria.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Pacote do NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .


### <a name="application-insights-diagnostics-telemetry"></a>Aplicativo ideias diagnóstico Telemetria

O `DiagnosticsTelemetryModule` relata erros no código de instrumentação de obtenção de informações do aplicativo em si. Por exemplo, se o código não é possível acessar contadores de desempenho ou se um `ITelemetryInitializer` gera uma exceção. Telemetria rastreamento acompanhada por este módulo aparece na [Pesquisa diagnóstico][diagnostic]. Envia dados de diagnóstico para dc.services.vsallin.net.
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* Pacote do NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se você instalar apenas este pacote, o arquivo ApplicationInsights.config não é criado automaticamente. 

### <a name="developer-mode"></a>Modo de desenvolvedor

`DeveloperModeWithDebuggerAttachedTelemetryModule`força a obtenção de informações de aplicativo `TelemetryChannel` para enviar dados imediatamente, item de um telemetria por vez, quando um depurador é anexado ao processo do aplicativo. Isso reduz a quantidade de tempo entre o momento quando seu aplicativo rastreia telemetria e quando ele aparece no portal de obtenção de informações do aplicativo. Faz com que uma sobrecarga significativa na largura de banda de rede e a CPU.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Aplicativo ideias Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote do NuGet

### <a name="web-request-tracking"></a>Acompanhamento de solicitações de Web

O [código de tempo e o resultado de resposta](app-insights-asp-net.md) de solicitações HTTP de relatórios. 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Pacote do NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Acompanhamento de exceção

`ExceptionTrackingTelemetryModule`trilhas exceções não tratadas em seu aplicativo web. Consulte [falhas e exceções][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Pacote do NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-rastreia [exceções unobserved tarefa](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-rastreia exceções sem tratamento para funções de trabalho, os serviços do windows e aplicativos de console.
* [Aplicativo ideias Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote do NuGet.

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights

O pacote de Microsoft.ApplicationInsights fornece o [núcleo da API](https://msdn.microsoft.com/library/mt420197.aspx) do SDK. Usam este comando os outros módulos de telemetria e você também pode [usá-lo para definir sua própria telemetria](app-insights-api-custom-events-metrics.md).

* Nenhuma entrada em ApplicationInsights.config.
* Pacote do NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se você instalar apenas esta NuGet, nenhum arquivo. config é gerado.

## <a name="telemetry-channel"></a>Canal de telemetria

O canal de telemetria gerencia buffer e a transmissão de telemetria no serviço de obtenção de informações do aplicativo. 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`é o canal padrão para serviços. Ele buffers dados na memória.
* `Microsoft.ApplicationInsights.PersistenceChannel`é uma alternativa para aplicativos de console. Ele pode salvar todos os dados unflushed armazenamento persistente quando seu aplicativo encerra e envia-lo quando o aplicativo for iniciado novamente.


## <a name="telemetry-initializers-aspnet"></a>Inicializadores de telemetria (ASP.NET)

Os inicializadores de telemetria definir propriedades de contexto que são enviadas juntamente com todos os itens de telemetria. 

Você pode [escrever seus próprio inicializadores](app-insights-api-filtering-sampling.md#add-properties) para definir propriedades de contexto.

Os inicializadores padrão são todos definidos pelos pacotes de Web ou WindowsServer NuGet:


* `AccountIdTelemetryInitializer`Define a propriedade AccountId.
* `AuthenticatedUserIdTelemetryInitializer`Define a propriedade AuthenticatedUserId como definido pelo SDK do JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer`atualizações do `RoleName` e `RoleInstance` propriedades do `Device` contexto para todos os itens de telemetria com informações extraídos do ambiente de tempo de execução Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`atualizações a `Version` propriedade do `Component` contexto para todos os itens de telemetria com o valor extraídas do `BuildInfo.config` arquivo produzidos por MS Build.
* `ClientIpHeaderTelemetryInitializer`atualizações `Ip` propriedade do `Location` contexto de todos os itens de telemetria de acordo com a `X-Forwarded-For` cabeçalho HTTP da solicitação.
* `DeviceTelemetryInitializer`atualiza as seguintes propriedades do `Device` contexto para todos os itens de telemetria.
 - `Type`está definida como "PC"
 - `Id`está definido para o nome de domínio do computador onde o aplicativo da web está sendo executado.
 - `OemName`está definido como o valor extraído do `Win32_ComputerSystem.Manufacturer` campo usando WMI.
 - `Model`está definido como o valor extraído do `Win32_ComputerSystem.Model` campo usando WMI.
 - `NetworkType`está definido como o valor extraído do `NetworkInterface`.
 - `Language`está definido para o nome do `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`atualizações do `RoleInstance` propriedade do `Device` contexto para todos os itens de telemetria com o nome de domínio do computador onde o aplicativo da web está sendo executado.
* `OperationNameTelemetryInitializer`atualizações a `Name` propriedade do `RequestTelemetry` e o `Name` propriedade do `Operation` contexto de todos os itens de telemetria de acordo com o método HTTP, bem como nomes de controlador de ASP.NET MVC e ação invocado para processar a solicitação.
* `OperationIdTelemetryInitializer`ou `OperationCorrelationTelemetryInitializer` atualizações a `Operation.Id` propriedade de contexto de todos os itens de telemetria controlada ao manipular uma solicitação com gerado automaticamente `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`atualizações a `Id` propriedade do `Session` contexto para todos os itens de telemetria com valor extraídas do `ai_session` cookie gerado pelo código JavaScript de ApplicationInsights instrumentação executado no navegador do usuário. 
* `SyntheticTelemetryInitializer`ou `SyntheticUserAgentTelemetryInitializer` atualizações a `User`, `Session` e `Operation` propriedades de contextos de todos os itens de telemetria controladas quando lidar com uma solicitação de uma fonte sintética, como uma disponibilidade testar ou bot de mecanismo de pesquisa. Por padrão, [Métricas Explorer](app-insights-metrics-explorer.md) não exibe telemetria sintética. 

    O `<Filters>` definir propriedades das solicitações de identificação.
* `UserAgentTelemetryInitializer`atualizações a `UserAgent` propriedade do `User` contexto de todos os itens de telemetria com base na `User-Agent` cabeçalho HTTP da solicitação.
* `UserTelemetryInitializer`atualizações do `Id` e `AcquisitionDate` propriedades de `User` contexto para todos os itens de telemetria com valores extraídas do `ai_user` cookie gerado pelo código JavaScript de ideias de aplicativo instrumentação executado no navegador do usuário.
* `WebTestTelemetryInitializer`Define o id de usuário, id da sessão e propriedades de fonte sintético para solicitações HTTP que vêm de [testes de disponibilidade](app-insights-monitor-web-app-availability.md).
O `<Filters>` definir propriedades das solicitações de identificação.

## <a name="telemetry-processors-aspnet"></a>Processadores de telemetria (ASP.NET)

Processadores de telemetria podem filtrar e modificar cada item de telemetria logo antes de ser enviado no SDK do portal.

Você pode [escrever seus próprios processadores de telemetria](app-insights-api-filtering-sampling.md#filtering).


#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processador de telemetria amostragem adaptativa (da 2.0.0-beta3)

Isso é ativado por padrão. Se seu aplicativo envia muitas telemetria, este processador remove algumas delas.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

O parâmetro fornece o destino que o algoritmo tenta obter. Cada instância do SDK funciona independentemente, portanto se o servidor for um cluster de vários computadores, o volume real de telemetria será multiplicado adequadamente.

[Saiba mais sobre amostragem](app-insights-sampling.md).



#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processador de telemetria amostragem de taxa fixa (da 2.0.0-beta1)

Também há um padrão [processador de telemetria amostragem](app-insights-api-filtering-sampling.md#sampling) (a partir do 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parâmetros do canal (Java)

Esses parâmetros afetam como o SDK Java deve armazenar e liberar os dados de telemetria que ela coleta.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity

O número de itens de telemetria que podem ser armazenados em armazenamento de memória do SDK. Quando esse número é alcançado, o buffer de telemetria é limpo - ou seja, os itens de telemetria são enviados para o servidor de obtenção de informações do aplicativo.

-   Mínimo: 1
-   Max: 1000
-   Padrão: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds 

Determina com que frequência os dados que estão armazenados no armazenamento de memória devem ser limpo (enviados de obtenção de informações de aplicativo).

-   Mínimo: 1
-   Máximo: 300
-   Padrão: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB

Determina o tamanho máximo em MB alocado para o armazenamento persistente no disco local. Este armazenamento é usado para itens de telemetria persistentes que falhou ao ser transmitido para o ponto de extremidade de obtenção de informações do aplicativo. Quando o tamanho do armazenamento foi atendido, novos itens de telemetria serão descartados.

-   Mínimo: 1
-   Máximo: 100
-   Padrão: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey

Isso determina o recurso de obtenção de informações do aplicativo na qual seus dados é exibida. Normalmente você cria um recurso separado, com uma chave separada, para cada um dos seus aplicativos.

Se você deseja definir a chave dinamicamente - por exemplo, se você quiser enviar os resultados do seu aplicativo para recursos diferentes - você pode omitir a chave do arquivo de configuração e defini-lo no código.

Para definir a chave para todas as instâncias de TelemetryClient, incluindo módulos de telemetria padrão, defina a chave no TelemetryConfiguration.Active. Fazer isso em um método de inicialização, como global.aspx.cs em um serviço ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Se você quiser apenas enviar um conjunto específico de eventos para um recurso diferente, você pode definir a chave para uma TelemetryClient específica:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Saiba mais sobre a API][api].

Para obter uma nova [criar um novo recurso no portal de obtenção de informações de aplicativo]do chave,[new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

