<properties
    pageTitle="Como usar o diagnóstico do Azure em máquinas virtuais | Microsoft Azure"
    description="Usando o diagnóstico do Azure para coletar dados de máquinas virtuais do Azure para depuração, medir o desempenho, monitoramento, análise de tráfego e muito mais."
    services="virtual-machines"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Habilitar o diagnóstico do Azure máquinas virtuais

Consulte [Visão geral de diagnóstico do Azure](azure-diagnostics.md) para um plano de fundo no diagnóstico do Azure.

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>Como habilitar o diagnóstico em uma máquina Virtual

Este passo a passo descreve como instalar remotamente o diagnóstico a uma máquina virtual Azure de um computador de desenvolvimento. Você também Saiba como implementar um aplicativo que é executado na máquina virtual Azure e emite dados de telemetria usando a.NET [EventSource classe][]. Diagnóstico do Azure é usado para coletar a telemetria e armazená-la em uma conta de armazenamento do Azure.

### <a name="pre-requisites"></a>Pré-requisitos
Este passo a passo supõe que você possui uma assinatura do Azure e estiver usando o Visual Studio 2013 com o SDK do Azure. Se você não tiver uma assinatura do Azure, você pode inscrever-se para a [Avaliação gratuita][]. Certifique-se de [instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior][].

### <a name="step-1-create-a-virtual-machine"></a>Etapa 1: Criar uma máquina Virtual
1.  Em seu computador de desenvolvimento, inicie o Visual Studio 2013.
2.  No Visual Studio **Server Explorer** expandir **Azure**, **máquinas virtuais** de atalho e selecione **Create Virtual Machine**.
3.  Selecione sua assinatura do Azure na caixa de diálogo **Escolher uma assinatura** e clique em **Avançar**.
4.  Selecione **Windows Server 2012 R2 Datacenter, novembro de 2014** na caixa de diálogo **Selecionar uma imagem de máquina Virtual** e clique em **Avançar**.
5.  Nas **Configurações básicas de máquina Virtual**, defina o nome de máquina virtual para "wadexample". Defina seu nome de usuário de administrador e a senha e clique em **Avançar**.
6.  Na caixa de diálogo **Configurações de serviço de nuvem** crie um novo serviço de nuvem denominado "wadexampleVM". Criar uma nova conta de armazenamento denominada "wadexample" e clique em **Avançar**.
7.  Clique em **criar**.

### <a name="step-2-create-your-application"></a>Etapa 2: Criar seu aplicativo
1.  Em seu computador de desenvolvimento, inicie o Visual Studio 2013.
2.  Crie um novo Visual c# aplicativo Console destinos 4,5 do .NET Framework. Nome do projeto "WadExampleVM".
    ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3.  Substitua o conteúdo de Program.cs com o seguinte código. A classe **SampleEventSourceWriter** implementa quatro métodos de registro em log: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o método WriteEvent define a identificação do evento respectivos. O método de execução implementa um loop infinito que chama cada um dos métodos log implementados na classe **SampleEventSourceWriter** cada 10 segundos.

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
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

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

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
        }
        }


4.  Salve o arquivo e selecione **Build Solution** no menu **Build** para construir o código.


### <a name="step-3-deploy-your-application"></a>Etapa 3: Implantar seu aplicativo
1.  Clique com botão direito no projeto **WadExampleVM** no **Solution Explorer** e escolha **Abrir pasta no Explorador de arquivos**.
2.  Navegue até a pasta *bin\Debug* e copie todos os arquivos (WadExampleVM.*)
3.  No **Explorador de servidor** com o botão direito na máquina virtual e escolha **Conectar usando a área de trabalho remota**.
4.  Depois de conectar-se para a máquina virtual crie uma pasta chamada WadExampleVM e colar arquivos de seu aplicativo para a pasta.
5.  Inicie o aplicativo WadExampleVM.exe. Você verá uma janela de console em branco.

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>Etapa 4: Criar sua configuração de diagnóstico e instale a extensão
1.  Baixe a definição de esquema de arquivo de configuração público no seu computador de desenvolvimento executando o seguinte comando do PowerShell:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'

2.  Abra um novo arquivo XML no Visual Studio, seja em um projeto que você já abriu ou em um Visual Studio instância com nenhum projetos abertos. No Visual Studio, selecione **Adicionar** -> **Novo Item...**  ->  **Visual c# itens** -> **dados** -> **Arquivo XML**. Nomeie o arquivo "WadExample.xml"
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

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>Etapa 5: Instalar remotamente diagnóstico em sua máquina Virtual do Azure
Cmdlets do PowerShell para gerenciar o diagnóstico em uma máquina virtual são: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e remover AzureVMDiagnosticsExtension.

1.  Em seu computador de desenvolvedor, abra o PowerShell do Azure.
2.  Execute o script para instalar remotamente o diagnóstico em sua máquina virtual (substituir *StorageAccountKey* com a chave da conta de armazenamento para sua conta de armazenamento wadexamplevm):

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


### <a name="step-6-look-at-your-telemetry-data"></a>Etapa 6: Examine seus dados de telemetria
No Visual Studio **Server Explorer** , navegue até a conta de armazenamento wadexample. Após a máquina virtual execução cerca de 5 minutos, você deverá ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Clique duas vezes em uma das tabelas para exibir a telemetria que foram coletada.

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>Esquema de arquivo de configuração

O arquivo de configuração de diagnóstico define os valores que são usados para inicializar configurações de diagnóstico quando o agente de diagnóstico inicia. Consulte a [referência de esquema mais recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) para valores válidos e exemplos.

## <a name="troubleshooting"></a>Solução de problemas

Consulte [Solução de problemas de diagnóstico do Azure](azure-diagnostics-troubleshooting.md) para obter mais informações.


## <a name="next-steps"></a>Próximas etapas
[Ver uma lista de máquina virtual relacionados artigos de diagnóstico do Azure](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) para alterar os dados que você está coletando, solucionar problemas ou para saber mais sobre o diagnóstico em geral.


[EventSource classe]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
