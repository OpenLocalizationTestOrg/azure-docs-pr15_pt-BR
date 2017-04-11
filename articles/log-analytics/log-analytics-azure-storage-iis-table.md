<properties
    pageTitle="Usando o armazenamento de blob para armazenamento de tabela e IIS para eventos | Microsoft Azure"
    description="Análise de log pode ler os logs para serviços do Azure que escrever diagnóstico ao armazenamento de tabela ou logs do IIS gravados armazenamento de blob."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Usando o armazenamento de blob do IIS e armazenamento de tabela para eventos

Análise de log pode ler os logs para os seguintes serviços que escrever diagnóstico ao armazenamento de tabela ou logs do IIS gravados armazenamento de blob:

+ Serviço tecidos clusters (prévia)
+ Máquinas virtuais
+ Funções da Web/trabalhador

Antes de análise de Log pode coletar dados para esses recursos, diagnóstico do Azure deve ser habilitado.

Depois de diagnóstico está habilitado, você pode usar o portal do Azure ou PowerShell configure a análise de Log para coletar os logs.

Diagnóstico do Azure é uma extensão Azure que permite que você coletar dados de diagnóstico de uma função de trabalho, função web ou máquina virtual em execução no Azure. Os dados são armazenados em uma conta de armazenamento do Azure e, em seguida, podem ser coletados pela análise de Log.

Para a análise de Log coletar esses logs de diagnóstico do Azure, os logs devem ser nos seguintes locais:

| Tipo de log | Tipo de recurso | Local |
| -------- | ------------- | -------- |
| Logs do IIS | Máquinas virtuais <br> Funções da Web <br> Funções de trabalho | wad-iis-arquivos de log (armazenamento de Blob) |
| Syslog | Máquinas virtuais | LinuxsyslogVer2v0 (armazenamento de tabela) |
| Eventos operacionais do serviço tecidos | Nós de estrutura de serviço | WADServiceFabricSystemEventTable |
| Eventos do serviço tecidos ator confiável | Nós de estrutura de serviço | WADServiceFabricReliableActorEventTable |
| Eventos do serviço tecidos serviço confiável | Nós de estrutura de serviço | WADServiceFabricReliableServiceEventTable |
| Logs de eventos do Windows | Nós de estrutura de serviço <br> Máquinas virtuais <br> Funções da Web <br> Funções de trabalho | WADWindowsEventLogsTable (armazenamento de tabela) |
| Logs do Windows ETW | Nós de estrutura de serviço <br> Máquinas virtuais <br> Funções da Web <br> Funções de trabalho | WADETWEventTable (armazenamento de tabela) |

>[AZURE.NOTE] Atualmente, não há suporte para logs do IIS de sites do Azure.

Para máquinas virtuais, você tem a opção de instalação do [agente de Log de análise](log-analytics-azure-vm-extension.md) em sua máquina virtual para habilitar a obtenção de informações adicionais. Além de ser capaz de analisar logs do IIS e Logs de eventos, você pode executar análises adicionais, incluindo o controle de alterações de configuração, avaliação de SQL e avaliação de atualização.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Habilitar o diagnóstico do Azure em uma máquina virtual para o conjunto de log de log de eventos e IIS

Use o procedimento a seguir para habilitar o diagnóstico do Azure em uma máquina virtual do Log de eventos e IIS coleta de log usando o portal do Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Para habilitar o diagnóstico do Azure em uma máquina virtual com o portal do Azure

1. Instale o agente de máquina virtual quando você cria uma máquina virtual. Se a máquina virtual já existir, verifique se que o agente de máquina virtual já está instalado.
    - No portal do Azure, navegue até a máquina virtual, selecione **Configuração opcional**, **Diagnóstico** e defina **Status** **em**.

    Após a conclusão, a máquina virtual tem a extensão de diagnóstico do Azure instalado e em execução. Essa extensão é responsável por coletar os dados de diagnóstico.

2. Habilitar o monitoramento e configurar o log de eventos em uma máquina virtual existente. Você pode habilitar o diagnóstico do nível de máquina virtual. Para habilitar o diagnóstico e configurar o log de eventos, execute as seguintes etapas:
    1. Selecione a máquina virtual.
    2. Clique em **monitoramento**.
    3. Clique em **Diagnóstico**.
    4. Defina o **Status** para **ativado**.
    5. Selecione cada log de diagnóstico que deseja coletar.
    7. Clique em **Okey**.

Usando o PowerShell do Azure você pode especificar mais precisamente os eventos que são gravados ao armazenamento do Azure. Consulte [coletar dados usando o diagnóstico do Azure gravado armazenamento de tabela ou Logs do IIS gravados blob](log-analytics-azure-storage-json.md).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Habilitar o diagnóstico do Azure em uma função da Web para conjunto de registro e eventos do IIS

Consulte [Como para habilitar o diagnóstico em um serviço de nuvem](../cloud-services/cloud-services-dotnet-diagnostics.md) para etapas gerais sobre como habilitar o diagnóstico do Azure. As instruções abaixo usam essas informações e personalizá-lo para uso com a análise de Log.

Com o diagnóstico do Azure ativado:

- Logs do IIS são armazenados por padrão, com dados de log transferidos no intervalo de transferência de scheduledTransferPeriod.
- Logs de eventos do Windows não são transferidos por padrão.

### <a name="to-enable-diagnostics"></a>Para habilitar o diagnóstico

Para ativar Logs de eventos do Windows, ou para alterar o scheduledTransferPeriod, configure diagnóstico do Azure usando o arquivo de configuração de XML (diagnostics.wadcfg), conforme mostrado no [etapa 4: criar seu arquivo de configuração de diagnóstico e instale a extensão](../cloud-services/cloud-services-dotnet-diagnostics.md)

O arquivo de configuração de exemplo a seguir coleta Logs do IIS e todos os eventos dos logs do aplicativo e do sistema:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Certifique-se de que seu ConfigurationSettings Especifica uma conta de armazenamento, como no exemplo seguinte:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Os valores **AccountName** e **AccountKey** são encontrados no portal do Azure no painel de conta de armazenamento, em Gerenciar teclas de acesso. O protocolo para a cadeia de conexão deve ser **https**.

Depois que a configuração de diagnóstico atualizada é aplicada ao seu serviço de nuvem e está gravando diagnóstico ao armazenamento do Azure, em seguida, você está pronto para configurar a análise de Log.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Usar o portal do Azure para coletar logs do armazenamento do Azure

Você pode usar o portal do Azure para configurar a análise de Log para coletar os logs para os seguintes serviços Azure:

+ Serviço tecidos clusters
+ Máquinas virtuais
+ Funções da Web/trabalhador

No portal do Azure, navegue até seu espaço de trabalho de análise de Log e executar as seguintes tarefas:

1. Clique em *logs de contas de armazenamento*
2. Clique na tarefa *Adicionar*
3. Selecione a conta de armazenamento que contém os logs de diagnóstico
  - Essa conta pode ser uma conta de armazenamento clássico ou uma conta de armazenamento do Gerenciador de recursos do Azure
4. Selecione o tipo de dados que você deseja coletar logs para
  - As opções são Logs do IIS; Eventos; Syslog (Linux); Logs ETW; Eventos de estrutura de serviço
5. O valor de origem é preenchido automaticamente com base no tipo de dados e não pode ser alterado
6. Clique em Okey para salvar a configuração

Repita as etapas 2 a 6 para tipos de dados que deseja que a análise de Log para coletar e contas de armazenamento adicional.

Em aproximadamente 30 minutos, você será capaz de ver dados da conta de armazenamento em análise de Log. Você só verá dados gravados ao armazenamento após a configuração é aplicada. Análise de log não lê os dados previamente existentes da conta de armazenamento.

>[AZURE.NOTE] O portal não validar a origem existe na conta de armazenamento ou se os novos dados estão sendo gravados.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Habilitar o diagnóstico do Azure em uma máquina virtual para o log de eventos e IIS log conjunto usando o PowerShell

Use as etapas da [Análise de Log Configurando indexar diagnóstico do Azure](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) para usar o PowerShell para ler de diagnóstico do Azure escritas ao armazenamento de tabela.

Usando o PowerShell do Azure você pode especificar mais precisamente os eventos que são gravados ao armazenamento do Azure.
Para obter mais informações, consulte [Habilitando diagnóstico em máquinas virtuais do Azure](../virtual-machines-dotnet-diagnostics.md).

Você pode ativar e atualizar o diagnóstico do Azure usando o seguinte script do PowerShell.
Você também pode usar este script com uma configuração de log personalizado.
Modificar o script para configurar a conta de armazenamento, nome do serviço e nome da máquina virtual.
O script usa cmdlets para clássicas máquinas virtuais.

Revise o seguinte exemplo de script, copiá-la, modificá-lo conforme necessário, salve a amostra como um arquivo de script do PowerShell e execute o script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Próximas etapas

- [Arquivos de uso JSON no armazenamento de blob](log-analytics-azure-storage-json.md) ler os logs do Azure serviços que diagnóstico de gravação para armazenamento de blob no formato JSON.
- [Habilitar soluções](log-analytics-add-solutions.md) agregar os dados.
- [Consultas de pesquisa de uso](log-analytics-log-searches.md) para analisar os dados.
