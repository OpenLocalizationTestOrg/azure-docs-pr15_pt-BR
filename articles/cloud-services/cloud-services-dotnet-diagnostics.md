<properties
    pageTitle="Como usar o diagnóstico do Azure (.NET) com os serviços de nuvem | Microsoft Azure"
    description="Usando o diagnóstico do Azure para coletar dados de serviços de nuvem Azure para depuração, medir o desempenho, monitoramento, análise de tráfego e muito mais."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="01/25/2016"
    ms.author="robb"/>



# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Habilitando diagnóstico do Azure em serviços de nuvem Azure

Consulte [Visão geral de diagnóstico do Azure](../azure-diagnostics.md) para um plano de fundo no diagnóstico do Azure.


## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Como habilitar o diagnóstico em uma função de trabalho

Este passo a passo descreve como implementar uma função de trabalho Azure que emite dados de telemetria usando a classe .NET EventSource. Diagnóstico do Azure é usado para coletar os dados de telemetria e armazená-la em uma conta de armazenamento do Azure. Ao criar uma função de trabalho o Visual Studio permite automaticamente 1.0 de diagnóstico como parte da solução no Azure SDKs para .NET 2,4 e versões anteriores. As instruções a seguir descrevem o processo para criar a função de trabalho, desabilitando o diagnóstico 1.0 da solução e implementando diagnóstico 1.2 ou 1.3 à sua função de trabalho.

### <a name="pre-requisites"></a>Pré-requisitos
Este artigo pressupõe que você tiver uma assinatura do Azure e estiver usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma assinatura do Azure, você pode inscrever-se para a [Avaliação gratuita][]. Certifique-se de [instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior][].

### <a name="step-1-create-a-worker-role"></a>Etapa 1: Criar uma função de trabalho
1.  Inicie o **Visual Studio 2013**.
2.  Crie um novo projeto de **Serviço de nuvem do Azure** do modelo de **nuvem** que destinos 4,5 do .NET Framework.  Nome do projeto "WadExample" e clique em Okey.
3.  Selecione a **Função de trabalho** e clique Okey. O projeto será criado.
4.  No **Solution Explorer**, clique duas vezes no arquivo de propriedades de **WorkerRole1** .
5.  Na **configuração** da guia desmarque **Habilitar diagnóstico** para desabilitar o diagnóstico 1.0 (Azure SDK 2,4 e eariler).
6.  Crie uma solução para verificar se você tem sem erros.

### <a name="step-2-instrument-your-code"></a>Etapa 2: Instrumenta seu código
Substitua o conteúdo de WorkerRole.cs com o seguinte código. A classe SampleEventSourceWriter, herdada da [Classe EventSource][], implementa quatro métodos de registro em log: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método **WriteEvent** define a identificação do evento respectivos. O método de execução implementa um loop infinito que chama cada um dos métodos log implementados na classe **SampleEventSourceWriter** cada 10 segundos.

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Diagnostics;
    using System.Diagnostics.Tracing;
    using System.Net;
    using System.Threading;

    namespace WorkerRole1
    {
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
    }


### <a name="step-3-deploy-your-worker-role"></a>Etapa 3: Implantar sua função de trabalho
1.  Implante sua função de trabalho para o Azure de dentro do Visual Studio selecionando o projeto de **WadExample** no Solution Explorer e **Publicar** no menu **Build** .
2.  Escolha sua assinatura.
3.  Na caixa de diálogo **Configurações de publicação do Microsoft Azure** selecione **Criar novo...**.
4.  Na caixa de diálogo **Criar serviço de nuvem e conta de armazenamento** , insira um **nome** (por exemplo, "WadExample") e selecione uma região ou grupo de afinidade.
5.  Configure o **ambiente** para **teste**.
6.  Modifique outras **configurações** conforme apropriado e clique em **Publicar**.
7.  Após a conclusão da implantação verificar no portal de clássico do Azure que seu serviço de nuvem está em um estado **em execução** .

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Etapa 4: Criar seu arquivo de configuração de diagnóstico e instale a extensão
1.  Baixe a definição de esquema de arquivo de configuração pública executando o seguinte comando do PowerShell:
2.
        (Get-AzureServiceAvailableExtension - Nome_da_extensão 'PaaSDiagnostics' - ProviderNamespace 'Microsoft.Azure.Diagnostics'). PublicConfigurationSchema | Out-File-codificação utf8 - caminho de arquivo 'WadConfig.xsd'

2.  Adicionar um arquivo XML ao seu projeto de **WorkerRole1** clicando no projeto **WorkerRole1** e selecione **Adicionar** -> **Novo Item...**  ->  **Visual c# itens** -> **dados** -> **Arquivo XML**. Nomeie o arquivo "WadExample.xml".

    ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.  Associe o WadConfig.xsd com o arquivo de configuração. Verifique se que a janela do editor de WadExample.xml é a janela ativa. Pressione **F4** para abrir a janela de **Propriedades** . Clique na propriedade **esquemas** na janela **Propriedades** . Clique em **…** na propriedade **esquemas** . Clique na **Adicionar...** botão e navegue até o local onde você salvou o arquivo XSD e selecione o arquivo WadConfig.xsd. Clique em **Okey**.
4.  Substitua o conteúdo do arquivo de configuração WadExample.xml com o seguinte XML e salvar o arquivo. Este arquivo de configuração define alguns contadores de desempenho para coletar: uma para utilização da CPU e outra para a utilização de memória. Em seguida, a configuração define os quatro eventos correspondente para os métodos na classe SampleEventSourceWriter.

```
        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Etapa 5: Instalar o diagnóstico em sua função de trabalho
Cmdlets do PowerShell para gerenciar o diagnóstico em uma função da web ou de trabalho são: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e remover AzureServiceDiagnosticsExtension.

1.  Abra o PowerShell Azure.
2.  Execute o script para instalar o diagnóstico em sua função de trabalho (substituir *StorageAccountKey* com a chave da conta de armazenamento para sua conta de armazenamento wadexample):

```
    $storage_name = "wadexample"
    $key = "<StorageAccountKey>"
    $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
    $service_name="wadexample"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Etapa 6: Examine seus dados de telemetria
No Visual Studio **Server Explorer** , navegue até a conta de armazenamento wadexample. Após a nuvem serviço está em execução cerca de 5 minutos você deve ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foram coletada.
    ![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)


## <a name="configuration-file-schema"></a>Esquema de arquivo de configuração

O arquivo de configuração de diagnóstico define os valores que são usados para inicializar configurações de diagnóstico quando o agente de diagnóstico inicia. Consulte a [referência de esquema mais recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para valores válidos e exemplos.

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas, consulte [Solução de problemas de diagnóstico do Azure](../azure-diagnostics-troubleshooting.md) para obter ajuda com problemas comuns.

## <a name="next-steps"></a>Próximas etapas
[Ver uma lista de máquina virtual relacionados artigos de diagnóstico do Azure](azure-diagnostics.md#cloud-services) para alterar os dados que você está coletando, solucionar problemas ou para saber mais sobre o diagnóstico em geral.


[EventSource classe]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
