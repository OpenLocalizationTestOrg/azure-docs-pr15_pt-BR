<properties
    pageTitle="Armazenar e exibir dados de diagnóstico no armazenamento do Azure | Microsoft Azure"
    description="Obter dados de diagnóstico do Azure para o armazenamento do Azure e exibi-lo"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="cloud-services"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="robb" />

# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Armazenar e exibir dados de diagnóstico no armazenamento do Azure

Dados de diagnóstico não são armazenados permanentemente, a menos que ele é transferido para o emulador de armazenamento do Microsoft Azure ou para o armazenamento do Azure. Uma vez no armazenamento, ele pode ser exibido com uma das várias ferramentas disponíveis.

## <a name="specify-a-storage-account"></a>Especificar uma conta de armazenamento

Especificar a conta de armazenamento que você deseja usar no arquivo ServiceConfiguration. As informações da conta são definidas como uma cadeia de conexão em uma configuração. O exemplo a seguir mostra a cadeia de conexão padrão criada para um novo projeto de serviço de nuvem no Visual Studio:


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Você pode alterar essa cadeia de caracteres de conexão para fornecer informações de conta para uma conta de armazenamento do Azure.

Dependendo do tipo de dados de diagnóstico que está sendo coletados, o diagnóstico do Azure usa o serviço de Blob ou o serviço de tabela. A tabela a seguir mostra as fontes de dados que são persistentes e seu formato.

|Fonte de dados|Formato de armazenamento|
|---|---|
|Logs do Azure|Tabela|
|Logs do IIS 7.0|Blob|
|Azure logs de infraestrutura de diagnóstico|Tabela|
|Falha de logs de rastreamento de solicitação|Blob|
|Logs de eventos do Windows|Tabela|
|Contadores de desempenho|Tabela|
|Despejos|Blob|
|Logs de erro personalizada|Blob|

## <a name="transfer-diagnostic-data"></a>Transferir dados de diagnóstico

2.5 SDK e posterior, a solicitação para transferir dados de diagnóstico pode ocorrer por meio do arquivo de configuração. Você pode transferir dados de diagnóstico em intervalos agendados conforme especificado na configuração.

Para SDK 2,4 e anterior, você pode solicitar para transferir os dados de diagnósticos através do arquivo de configuração, bem como programaticamente. A abordagem de programação também permite que você faça transferências de sob demanda.


>[AZURE.IMPORTANT] Quando você transfere dados de diagnóstico para uma conta de armazenamento do Azure, você provoca custos para os recursos de armazenamento que usa os dados de diagnósticos.

## <a name="store-diagnostic-data"></a>Armazenar dados de diagnóstico

Dados de log são armazenados no armazenamento de Blob ou tabela com os seguintes nomes:

**Tabelas**

- **WadLogsTable** - Logs escritos em código usando o ouvinte de rastreamento.

- **WADDiagnosticInfrastructureLogsTable** - monitor de diagnóstico e configuração é alterado.

- **WADDirectoriesTable** – diretórios que o monitor de diagnóstico está monitorando.  Isso inclui logs do IIS, IIS falhou logs de solicitação e diretórios personalizados.  O local do arquivo de log do blob é especificado no campo contêiner e o nome do blob está no campo RelativePath.  O campo de CaminhoAbsoluto indica o local e o nome do arquivo como eram na máquina virtual Azure.

- **WADPerformanceCountersTable** – contadores de desempenho.

- **WADWindowsEventLogsTable** – logs de eventos do Windows.

**BLOBs**

- **contêiner de controle wad** – (somente para o SDK 2,4 e anteriores) contém os arquivos de configuração XML que controla o diagnóstico do Azure.

- **wad-iis-failedreqlogfiles** – contém informações do IIS falhou solicitar logs.

- **wad-iis-arquivos de log** – contém informações sobre o IIS logs.

- **"personalizado"** – um contêiner personalizada com base em Configurando diretórios monitorados pelo monitor de diagnóstico.  O nome deste contêiner de blob será especificado em WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para exibir dados de diagnóstico
Diversas ferramentas estão disponíveis para exibir os dados depois que ele é transferido para armazenamento. Por exemplo:

- Server Explorer no Visual Studio - se você instalou as ferramentas do Azure para Microsoft Visual Studio, você pode usar o nó de armazenamento do Azure no Server Explorer para exibir dados de tabela e blob somente leitura de suas contas de armazenamento do Azure. Você pode exibir dados de sua conta de emulador de armazenamento local e também de contas de armazenamento que você criou para Azure. Para obter mais informações, consulte [navegação e gerenciamento de recursos de armazenamento com Server Explorer](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).

- [Gerenciador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite que você facilmente trabalhar com dados de armazenamento do Azure no Windows, OSX e Linux.

- [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Gerenciador de diagnóstico do Azure que permite exibir, baixar e gerenciar os dados de diagnóstico coletados pelos aplicativos em execução no Azure.


## <a name="next-steps"></a>Próximas etapas

[Rastrear o fluxo em um aplicativo de serviços de nuvem com diagnóstico do Azure](cloud-services-dotnet-diagnostics-trace-flow.md)
