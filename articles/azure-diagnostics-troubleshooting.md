<properties
    pageTitle="Solução de problemas de diagnóstico do Azure"
    description="Solucionar problemas ao usar o diagnóstico do Azure em serviços de nuvem do Azure, máquinas virtuais e "
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="robb"/>


# <a name="azure-diagnostics-troubleshooting"></a>Diagnóstico do Azure para solução de problemas
Solução de problemas informações relevantes usando o diagnóstico do Azure. Para obter mais informações sobre diagnóstico do Azure, consulte [Visão geral de diagnóstico do Azure](azure-diagnostics.md#cloud-services).

## <a name="azure-diagnostics-is-not-starting"></a>Diagnóstico do Azure não está iniciando

Diagnóstico é composto por dois componentes: um agente de convidado plug-in e o agente de monitoramento. Você pode verificar os arquivos de log **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** para obter informações sobre por que o diagnóstico falha ao iniciar.  
  
Em uma função de serviço de nuvem, arquivos de log do plug-in do convidado agente estão localizados em: 

``` 
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\ 
``` 

Em uma máquina de Virtual do Azure, arquivos de log do plug-in do convidado agente estão localizados em: 

``` 
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\ 
``` 
 
A última linha dos arquivos de log conterá o código de saída.  

``` 
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0 
``` 

O plug-in retorna os seguintes códigos de saída:

Código de saída|Descrição
---|---
0|Sucesso.
-1|Erro genérico.
-2|Não é possível carregar o arquivo rcf.<p>Este é um erro interno que só deve acontecer se o inicializador de plug-in do agente de convidado é manualmente chamado, incorretamente, na máquina virtual.
-3|Não é possível carregar o arquivo de configuração de diagnóstico.<p><p>Solução: Este é o resultado de um arquivo de configuração não passando validação de esquema. A solução é fornecer um arquivo de configuração que esteja em conformidade com o esquema.
-4|Outra instância do agente de monitoramento diagnóstico já está usando o diretório do recurso local.<p><p>Solução: Especifique um valor diferente para **LocalResourceDirectory**.
-6|O inicializador de plug-in do agente do convidado tentado iniciar diagnóstico com uma linha de comando inválida.<p><p>Este é um erro interno que só deve acontecer se o inicializador de plug-in do agente de convidado é manualmente chamado, incorretamente, na máquina virtual.
-10|O plug-in diagnóstico encerrado com uma exceção não tratada.
-11|O agente de convidado não conseguiu criar o processo responsável por iniciando e monitorar o agente de monitoramento.<p><p>Solução: Verificar se os recursos de sistema suficientes estão disponíveis para iniciar novos processos.<p>
-101|Argumentos inválidos ao chamar o plug-in de diagnóstico.<p><p>Este é um erro interno que só deve acontecer se o inicializador de plug-in do agente de convidado é manualmente chamado, incorretamente, na máquina virtual.
-102|O processo de plug-in é impossível inicializar em si.<p><p>Solução: Verificar se os recursos de sistema suficientes estão disponíveis para iniciar novos processos.
-103|O processo de plug-in é impossível inicializar em si. Especificamente, não é possível criar o objeto do agente de log.<p><p>Solução: Verificar se os recursos de sistema suficientes estão disponíveis para iniciar novos processos.
-104|Não é possível carregar o arquivo de rcf fornecido pelo agente de convidado.<p><p>Este é um erro interno que só deve acontecer se o inicializador de plug-in do agente de convidado é manualmente chamado, incorretamente, na máquina virtual.
-105|O plug-in diagnósticos não pode abrir o arquivo de configuração de diagnóstico.<p><p>Este é um erro interno que deve ocorrer somente se o plug-in diagnóstico for manualmente chamado, incorretamente, sobre a máquina virtual.
-106|Não pode ler o arquivo de configuração de diagnóstico.<p><p>Solução: Este é o resultado de um arquivo de configuração não passando validação de esquema. Portanto, a solução é fornecer um arquivo de configuração que esteja em conformidade com o esquema. Você pode encontrar o XML que foi entregue para a extensão de diagnóstico na pasta *%SystemDrive%\WindowsAzure\Config* na VM. Abra o arquivo XML e pesquisa para **Microsoft.Azure.Diagnostics**, em seguida, para o campo **xmlCfg** apropriados. Os dados são codificação base64 portanto você precisará [decodificá-lo](http://www.bing.com/search?q=base64+decoder) ver o XML que foi carregado por diagnóstico.<p>
-107|A fase de diretório do recurso para o agente de monitoramento é inválida.<p><p>Este é um erro interno que deve ocorrer somente se o agente de monitoramento é manualmente chamado, incorretamente, na máquina virtual.</p>
-108    |Não é possível converter o arquivo de configuração de diagnóstico para o arquivo de configuração do agente monitoramento.<p><p>Este é um erro interno que deve ocorrer somente se o plug-in diagnóstico manualmente é invocado com um arquivo de configuração inválido.
-110|Erro de configuração de diagnóstico geral.<p><p>Este é um erro interno que deve ocorrer somente se o plug-in diagnóstico manualmente é invocado com um arquivo de configuração inválido.
-111|Não é possível iniciar o agente de monitoramento.<p><p>Solução: Verifique se os recursos de sistema suficientes disponíveis.
-112|Erro geral


## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dados de diagnóstico não são conectado ao armazenamento do Azure
Diagnóstico do Azure armazena todos os dados em um armazenamento do Azure.

A causa mais comum de evento dados ausentes é informações da conta de armazenamento definida incorretamente.

Solução: Corrija o seu arquivo de configuração de diagnóstico e instalar novamente o diagnóstico.
Se o problema persistir depois de instalar novamente a extensão de diagnóstico e em seguida, talvez você precise depurar ainda mais olhando os monitoramento agente erros. Antes de dados do evento são carregados à sua conta de armazenamento, ela é armazenada na LocalResourceDirectory.

Função de serviço de nuvem é o LocalResourceDirectory:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Para máquinas virtuais do LocalResourceDirectory é:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Se não houver nenhum arquivo na pasta LocalResourceDirectory, o agente de monitoramento é possível iniciar. Isso geralmente é causado por um arquivo de configuração inválido, um evento que deve ser relatado na CommandExecution.log.

Se o agente de monitoramento com êxito está coletando dados de eventos, que você verá os arquivos de .tsf para cada evento definido no seu arquivo de configuração. O agente de monitoramento registra os erros no arquivo MaEventTable.tsf. Para inspecionar o conteúdo desse arquivo, você pode usar o aplicativo de tabel2csv para converter o arquivo de .tsf para um arquivo de values(.csv) separados por vírgula:

Em uma função de serviço de nuvem:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*% SystemDrive %* em uma função de serviço de nuvem normalmente é d:

Em uma máquina Virtual:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Os comandos acima gera a de arquivo de log *maeventtable.csv*, que você pode abrir e inspecionar mensagens de falha.    


## <a name="diagnostics-data-tables-not-found"></a>Dados de diagnóstico tabelas não encontrados
As tabelas no armazenamento do Azure mantendo dados de diagnóstico do Azure são nomeadas usando o código abaixo:

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Aqui está um exemplo:

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Que gerará 4 tabelas:

Evento|Nome da tabela
---|---
provedor = "prov1" &lt;identificação de evento = "1" /&gt;|WADEvent + MD5("prov1") + "1"
provedor = "prov1" &lt;identificação de evento = "2" eventDestination = "dest1" /&gt;|WADdest1
provedor = "prov1" &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
provedor = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt;|WADdest2
