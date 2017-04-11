<properties
    pageTitle="Fluxo de dados de diagnóstico do Azure no caminho de alta utilizando Hubs de evento | Microsoft Azure"
    description="Ilustra como configurar o diagnóstico do Azure com Hubs de eventos de ponta a ponta, inclusive orientação para cenários comuns."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/14/2016"
    ms.author="sethm" />

# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Fluxo de dados de diagnóstico do Azure no caminho quente usando Hubs de evento

Diagnóstico do Azure oferece maneiras flexíveis para coletar logs e métricas de nuvem serviços VMs (máquinas virtuais) e transferir os resultados para o armazenamento do Azure. Iniciando no período março de 2016 (2,9 SDK), você pode PIA diagnóstico a fontes de dados completamente personalizados e transferir dados de caminho quente em segundos usando [Hubs de evento do Azure](https://azure.microsoft.com/services/event-hubs/).

Tipos de dados com suporte incluem:

- Eventos de rastreamento para Windows (ETW)
- Contadores de desempenho
- Logs de eventos do Windows
- Logs de aplicativos
- Azure logs de infraestrutura de diagnóstico

Este artigo mostra como configurar o diagnóstico do Azure com Hubs de eventos de ponta a ponta. Orientação também é fornecida para os seguintes cenários comuns:

- Como personalizar os logs e métricas que obter sinked para Hubs de evento
- Como alterar as configurações em cada ambiente
- Como exibir dados de fluxo de Hubs de evento
- Como solucionar problemas com a conexão  

## <a name="prerequisites"></a>Pré-requisitos

Hubs de evento abaixar no diagnóstico do Azure é compatível com os serviços de nuvem, VMs, conjuntos de escala de máquina Virtual e estrutura de serviço iniciando no 2,9 de SDK do Azure e as ferramentas de Azure correspondente para Visual Studio.

- Extensão de diagnóstico Azure 1,6 ([SDK do Azure para .NET 2,9 ou posterior](https://azure.microsoft.com/downloads/) destina-se isso por padrão)
- [Visual Studio 2013 ou superior](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Configurações do diagnóstico do Azure existentes em um aplicativo usando um arquivo de *.wadcfgx* e um dos seguintes métodos:
    - O Visual Studio: [Configurando o diagnóstico de serviços de nuvem Azure e máquinas virtuais](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
    - Windows PowerShell: [Habilitar o diagnóstico nos serviços de nuvem do Azure usando o PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Evento Hubs namespace provisionado pelo artigo [Introdução com Hubs de evento](./event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Conectar o diagnóstico do Azure ao receptor de Hubs de evento

Diagnóstico do Azure sempre recpetores logs e métricas, por padrão, a uma conta de armazenamento do Azure. Além disso, um aplicativo pode PIA aos Hubs de evento adicionando uma nova seção **receptores** ao elemento **WadCfg** na seção **PublicConfig** do arquivo *.wadcfgx* . No Visual Studio, o arquivo de *.wadcfgx* está armazenado no seguinte caminho: **Projeto de serviço de nuvem** > **funções** >  **(nome de função)** > **diagnostics.wadcfgx** arquivo.

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

Neste exemplo, a URL do Hub de evento está definida para o namespace totalmente qualificado do Hub evento: namespace Hubs de evento + "/" + nome do Hub de evento.  

A URL do Hub de evento é exibida no [portal do Azure](http://go.microsoft.com/fwlink/?LinkID=213885) no painel Hubs de evento.  

O nome de **PIA** pode ser definido para qualquer cadeia de caracteres válida, desde que o mesmo valor é usado consistentemente em todo o arquivo de configuração.

> [AZURE.NOTE]  Pode haver receptores adicionais, como *applicationInsights* configurados nesta seção. Diagnóstico do Azure permite receptores de um ou mais ser definido se cada receptor também é declarado na seção **PrivateConfig** .  

O receptor de evento Hubs também deve ser declarado e definido na seção **PrivateConfig** do arquivo de configuração *.wadcfgx* .

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

O `SharedAccessKeyName` valor deve corresponder a uma chave de assinatura de acesso compartilhado (SAS) e a política definida no namespace **Hubs de evento** . Navegue até o painel de Hubs de evento no [portal do Azure](https://manage.windowsazure.com), clique na guia **Configurar** e configurar uma regra nomeada (por exemplo, "SendRule") que tem permissões *Enviar* . O **StorageAccount** também é declarado em **PrivateConfig**. Não é necessário alterar valores aqui se eles estão trabalhando. Neste exemplo, podemos deixe os valores vazio, que é um sinal de que um ativo downstream definirá os valores. Por exemplo, o arquivo de configuração do ambiente de *ServiceConfiguration.Cloud.cscfg* define as teclas e nomes de ambiente apropriados.  

> [AZURE.WARNING] A chave de evento Hubs SAS é armazenada em texto sem formatação no arquivo *.wadcfgx* . Normalmente, esta tecla check-in para controle de código fonte ou está disponível como um ativo no seu servidor de compilação, portanto você deve protegê-lo conforme apropriado. Recomendamos que você use uma chave SAS aqui com permissões *Enviar apenas* para que um usuário mal-intencionado possa gravar Hub do evento, mas não ouvi-la ou gerenciá-lo.

## <a name="configure-azure-diagnostics-logs-and-metrics-to-sink-with-event-hubs"></a>Configurar logs de diagnóstico do Azure e métricas coletar com Hubs de evento

Conforme discutido anteriormente, todos os dados de diagnóstico personalizadas e padrão, ou seja, métricas e logs, é automaticamente sinked ao armazenamento do Azure em intervalos configurados. Com Hubs de evento e qualquer receptor adicional, você pode especificar qualquer nó raiz ou folha na hierarquia para ser sinked com o Hub de evento. Isso inclui eventos ETW, contadores de desempenho, logs de eventos do Windows e logs de aplicativos.   

É importante levar em consideração quantos pontos de dados, na verdade, devem ser transferidos para Hubs de evento. Normalmente, os desenvolvedores transferem dados de caminho quente de baixa latência que devem ser consumidos e interpretados rapidamente. Sistemas que monitoram alertas ou regras de escala automática são exemplos. Um desenvolvedor também pode configurar um armazenamento de análise alternativo ou pesquisar store – por exemplo, a análise de fluxo Azure, Elasticsearch, um sistema de monitoramento personalizado ou um sistema de monitoramento favorito de outras pessoas.

A seguir estão alguns exemplos de configurações.

```
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
</PerformanceCounters>
```

No exemplo a seguir, o receptor é aplicado ao nó **PerformanceCounters** pai na hierarquia, significando que todos os filhos **PerformanceCounters** serão enviada para Hubs de evento.  

```
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```

No exemplo anterior, o receptor é aplicado a apenas três contadores: **Solicitações em fila** **Solicitações rejeitadas**e **% tempo de processador**.  

O exemplo a seguir mostra como um desenvolvedor pode limitar a quantidade de dados enviados sejam as métricas críticas que são usadas para integridade desse serviço.  

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

Neste exemplo, o receptor é aplicado aos logs e é filtrado somente para o nível de erro de rastreamento.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Implantar e atualizar uma configuração de aplicativo e o diagnóstico de serviços de nuvem

Visual Studio fornece o caminho mais fácil para implantar o aplicativo e a configuração de receptor de evento Hubs. Para exibir e editar o arquivo, abra o arquivo *.wadcfgx* no Visual Studio, editá-lo e salvá-lo. O caminho é o **Projeto de serviço de nuvem** > **funções** > **(nome de função)** > **diagnostics.wadcfgx**.  

Neste ponto, todos os implantação e implantação atualizar ações no Visual Studio, Visual Studio Team System e todos os comandos ou scripts que são baseados no MSBuild e use o **/t: publicar** destino incluir o *.wadcfgx* no processo de embalagem. Além disso, atualizações e implantações implante o arquivo no Azure usando a extensão do agente de diagnóstico do Azure apropriada em suas VMs.

Depois de implantar o aplicativo e a configuração de diagnóstico do Azure, você verá imediatamente atividade no painel do Hub evento. Isso indica que você está pronto para prosseguir para exibir os dados de caminho quente na ferramenta de cliente ou análise de ouvinte de sua escolha.  

Na figura a seguir, o painel Hubs de evento mostra eficaz, de envio de dados de diagnóstico para o Hub de evento iniciando um dia após 11 PM. É quando o aplicativo foi implantado com um arquivo atualizado *.wadcfgx* e o receptor foi configurado corretamente.

![][0]  

> [AZURE.NOTE] Quando você faz atualizações para o arquivo de configuração de diagnóstico do Azure (.wadcfgx), é recomendável que você enviar as atualizações para o aplicativo inteiro, bem como a configuração usando a publicação do Visual Studio, ou um script do Windows PowerShell.  

## <a name="view-hot-path-data"></a>Exibir dados de caminho quente

Conforme discutido anteriormente, há muitos casos de uso para ouvir e processar dados Hubs de evento.

Uma abordagem simples é criar um aplicativo de console pequena de teste para ouvir o Hub de evento e imprimir o fluxo de saída. Você pode colocar o código a seguir, que é explicado mais detalhadamente [começar com Hubs de evento](./event-hubs-csharp-ephcs-getstarted.md)), em um aplicativo de console.  

Observe que o aplicativo de console deve incluir o [pacote do evento processador Host Nuget](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Lembre-se de substituir os valores entre colchetes na função **principal** com valores para seus recursos.   

```
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sink"></a>Solucionar problemas de receptor de Hubs de evento

- O Hub de evento não mostram a atividade de eventos de entrada ou de saída conforme esperado.

    Verifique seu Hub de evento é provisionado com êxito. Todas as informações de conexão na seção **PrivateConfig** da *.wadcfgx* devem coincidir os valores do recurso como visto no portal. Certifique-se de que você tem uma política SAS definida ("SendRule" no exemplo) no portal e que *Enviar* tem permissão.  

- Após uma atualização, o Hub de evento não mostra atividade de evento de entrada ou de saída.

    Primeiro, certifique-se de que as informações de configuração e Hub de evento estão correta conforme explicado anteriormente. Às vezes, o **PrivateConfig** é redefinido em uma atualização de implantação. A solução recomendada é fazer todas as alterações em *.wadcfgx* do projeto e, em seguida, enviar uma atualização do aplicativo completo. Se isso não for possível, certifique-se de que a atualização de diagnóstico envia uma completa **PrivateConfig** que inclua a tecla SAS.  

- Tentei as sugestões e o Hub de evento ainda não estiver funcionando.

    Tente procurar na tabela de armazenamento do Azure que contém erros e logs de diagnóstico do Azure em si: **WADDiagnosticInfrastructureLogsTable**. Uma opção é usar uma ferramenta como o [Gerenciador de armazenamento do Azure](http://www.storageexplorer.com) para conectar a esta conta de armazenamento, exibir esta tabela e adicionar uma consulta para o carimbo de hora nas últimas 24 horas. Você pode usar a ferramenta para exportar um arquivo. csv e abri-lo em um aplicativo como o Microsoft Excel. Excel torna mais fácil pesquisar cadeias de caracteres de cartão, como **EventHubs**, para ver qual erro é relatado.  

## <a name="next-steps"></a>Próximas etapas

• [Saiba mais sobre Hubs de evento](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Apêndice: Concluir exemplo de arquivo (.wadcfgx) de configuração de diagnóstico do Azure

```
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount />
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

O complementar *ServiceConfiguration.Cloud.cscfg* para este exemplo é semelhante ao seguinte.

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png
