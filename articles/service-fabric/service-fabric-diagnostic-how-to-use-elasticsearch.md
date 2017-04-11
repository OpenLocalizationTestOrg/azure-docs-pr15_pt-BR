<properties
   pageTitle="Usando Elasticsearch como um repositório de rastreamento de aplicativo de serviço tecidos | Microsoft Azure"
   description="Descreve como os aplicativos de serviço tecidos podem usar Elasticsearch e Kibana para armazenar, índice e pesquisar rastreamentos de aplicativo (registros)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>Usar Elasticsearch como um rastreamento de aplicativo de serviço tecidos armazenar
## <a name="introduction"></a>Introdução
Este artigo descreve como os aplicativos do [Azure serviço tecidos](https://azure.microsoft.com/documentation/services/service-fabric/) podem usar **Elasticsearch** e **Kibana** para armazenamento de rastreamento de aplicativos, indexação e pesquisa. [Elasticsearch](https://www.elastic.co/guide/index.html) é um código-fonte aberto, distribuído e escalável em tempo real pesquisa e a análise de mecanismo que é adequado para essa tarefa. Ele pode ser instalado no Windows e Linux máquinas virtuais em execução no Microsoft Azure. Elasticsearch com eficiência pode processar *estruturadas* rastreamentos produzidos usando tecnologias como o **Evento de rastreamento para Windows (ETW)**.

ETW é usado pelo tempo de execução de serviço tecidos para informações de diagnóstico de origem (rastreamentos). É o método recomendado para aplicativos de serviço tecidos para suas informações de diagnóstico, de fonte muito. Usando o mesmo mecanismo permite correlação entre rastreamentos fornecido pelo runtime e fornecido pelo aplicativo e torna mais fácil de solução de problemas. Modelos de projeto de serviço tecidos no Visual Studio incluem uma API de log (com base na classe .NET **EventSource** ) que emite rastreamentos ETW por padrão. Para obter uma visão geral de rastreamento de aplicativo de serviço tecidos usando ETW, consulte [monitoramento e diagnosticar serviços em uma configuração de desenvolvimento de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Para os rastreamentos apareça no Elasticsearch, eles precisam ser capturado em nós de cluster tecidos de serviço em tempo real (enquanto o aplicativo estiver em execução) e enviadas para um ponto de extremidade de Elasticsearch. Há duas opções principais para capturar de rastreamento:

+ **Captura de rastreamento em processo**  
O aplicativo, ou mais precisamente, processo de serviço, é responsável por enviar os dados de diagnóstico para o repositório de rastreamento (Elasticsearch).

+ **Captura de rastreamento de fora de processo**  
Um agente separado é capturando rastreamentos do processo de serviço do ou processos e enviá-los para o repositório de rastreamento.

Abaixo, podemos descrevem como configurar Elasticsearch no Azure, discutir os prós e contras ambos capturar opções e explicam como configurar um serviço de estrutura de serviço para enviar dados para Elasticsearch.


## <a name="set-up-elasticsearch-on-azure"></a>Configurar Elasticsearch no Azure
A maneira mais simples de configurar o serviço de Elasticsearch no Azure é por meio de [**modelos do Gerenciador de recursos do Azure**](../azure-resource-manager/resource-group-overview.md). Um abrangente [modelo do Gerenciador de recursos do início rápido do Azure para Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) está disponível no repositório de modelos de início rápido do Azure. Este modelo usa contas de armazenamento separado para unidades de escala (grupos de nós). Ele também pode provisionar cliente separada e nós de servidor com configurações diferentes e vários números de discos de dados anexados.

Aqui, podemos usar outro modelo, chamado **ES-vários nós** do [repositório de ferramentas de diagnóstico Azure](https://github.com/Azure/azure-diagnostics-tools). Este modelo é fácil de usar e, em seguida, cria um cluster de Elasticsearch protegido por autenticação básica HTTP. Antes de continuar, baixe o repositório do GitHub à sua máquina (por clonagem repositório ou baixar um arquivo zip). O modelo de ES-vários nós está localizado na pasta com o mesmo nome.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Preparar um computador para executar scripts de instalação Elasticsearch
A maneira mais fácil de usar o modelo de ES-vários nós é por meio de um script PowerShell do Azure fornecido chamado `CreateElasticSearchCluster`. Para usar esse script, você precisa instalar módulos do PowerShell e uma ferramenta chamada **openssl**. O último é necessário para a criação de uma chave SSH que pode ser usada para administrar seu cluster Elasticsearch remotamente.

`CreateElasticSearchCluster`script foi desenvolvido para facilidade de uso com o modelo de ES-vários nós de um computador Windows. É possível usar o modelo em uma máquina não-Windows, mas esse cenário está além do escopo deste artigo.

1. Se você ainda não instalou-los já, instale [**módulos do PowerShell do Azure**](http://aka.ms/webpi-azps). Quando solicitado, clique em **Executar**, **instalar**. Azure PowerShell 1.3 ou posterior é necessário.

2. A ferramenta de **openssl** está incluída na distribuição [**gito para**](http://www.git-scm.com/downloads)Windows. Se você não tiver feito isso, instale [Gito para Windows](http://www.git-scm.com/downloads) agora. (As opções de instalação padrão são Okey.)

3. Presumindo que gito foi instalado, mas não incluído no caminho do sistema, abra uma janela do Microsoft Azure PowerShell e execute os seguintes comandos:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Substituir o `<Git installation folder>` com a localização de gito em sua máquina; o padrão é **"C:\Program Files\Git"**. Observe o caractere de ponto e vírgula no início da primeira trajetória.

4. Certifique-se de que você está conectado ao Azure (via [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet) e que você selecionou a assinatura que deve ser usada para criar seu cluster elástica pesquisa. Você pode confirmar que a assinatura correta está selecionada usando `Get-AzureRmContext` e `Get-AzureRmSubscription` cmdlets.

5. Se você ainda não fez isso, altere a pasta atual para a pasta ES-vários nós.

### <a name="run-the-createelasticsearchcluster-script"></a>Execute o script CreateElasticSearchCluster
Antes de executar o script, abra o `azuredeploy-parameters.json` de arquivos e verificar ou fornecer valores para os parâmetros de script. Os seguintes parâmetros são fornecidos:

|Nome do parâmetro           |Descrição|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |O nome que é usado para criar o nome DNS publicamente visível para a pesquisa elástica cluster (anexando o domínio de região Azure para o nome fornecido). Por exemplo, se esse valor de parâmetro é "myBigCluster" e a região Azure escolhida é Oeste EUA, o nome DNS resultante para o cluster é myBigCluster.westus.cloudapp.azure.com. <br /><br />Esse nome também serve como um nome de raiz para muitos artefatos associados ao cluster de pesquisa elástica, como nomes de nó de dados.|
|adminUsername           |O nome da conta de administrador para gerenciamento do cluster de pesquisa elástica (chaves SSH correspondentes são geradas automaticamente).|
|dataNodeCount           |O número de nós no cluster elástica pesquisa. A versão atual do script não faz distinção entre nós de dados e consulta; todos os nós reproduzir ambas as funções. O padrão é 3 nós.|
|dataDiskSize            |O tamanho de discos de dados (em GB) que está alocado para cada nó de dados. Cada nó recebe 4 discos de dados, dedicados exclusivamente ao serviço de pesquisa elástica.|
|região                  |O nome do Azure região onde o cluster elástica pesquisa deve ser localizado.|
|esUserName              |O nome de usuário do usuário que está configurado para ter acesso ao cluster ES (sujeitos a autenticação básica HTTP). A senha não é parte do arquivo de parâmetros e deve ser fornecida quando `CreateElasticSearchCluster` script é chamado.|
|vmSizeDataNodes         |O tamanho do Azure máquina virtual pesquisa elástica para nós de cluster. O padrão é Standard_D2.|

Agora você está pronto para executar o script. Execute o seguinte comando:

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

onde 

|Nome do parâmetro de script    |Descrição|
|-----------------------  |--------------------------|
|`<es-group-name>`        |O nome do grupo de recursos Azure que conterá todos os recursos de cluster de pesquisa elástica.|
|`<azure-region>`         |O nome da região Azure onde cluster elástica pesquisa deve ser criado.|         
|`<es-password>`          |A senha do usuário de pesquisa elástica.|

>[AZURE.NOTE] Se você obtiver uma NullReferenceException do cmdlet AzureResourceGroup de teste, você tenha esquecido de fazer logon no Azure (`Add-AzureRmAccount`).

Se você receber um erro da execução do script e você determinar que o erro foi causado por um valor de parâmetro de modelo errado, corrija o arquivo de parâmetro e executar o script novamente com um nome de grupo de recurso diferente. Você também pode reutilizar o mesmo nome de grupo de recursos e ter o script limpar aquele antigo adicionando o `-RemoveExistingResourceGroup` parâmetro para a invocação de script.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>Resultado da execução do script de CreateElasticSearchCluster
Depois de executar o `CreateElasticSearchCluster` script, os seguintes artefatos principais serão criados. Neste exemplo, vamos supor que você usou "myBigCluster" como o valor da `dnsNameForLoadBalancerIP` parâmetro e que a região onde você criou o cluster é Oeste EUA.

|Artefato|Nome, local e comentários|
|----------------------------------|----------------------------------|
|Chave SSH para administração remota |arquivo de myBigCluster.key (no diretório de onde a CreateElasticSearchCluster foi executada). <br /><br />Este arquivo chave pode ser usado para conectar o nó de administração e (através do nó Administração) para nós de dados no cluster.|
|Nó de administração                        |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Uma máquina virtual dedicada para administração de cluster Elasticsearch remota – a única pessoa que permite conexões SSH externas. Ele é executado na mesma rede virtual como todos os nós de cluster Elasticsearch, mas ele não é executado quaisquer serviços Elasticsearch.|
|Nós de dados                        |myBigCluster1... myBigCluster*N* <br /><br />Nós de dados que estão executando serviços Elasticsearch e Kibana. Você pode conectar via SSH para cada nó, mas somente via o nó de administração.|
|Elasticsearch cluster             |http://myBigCluster.westus.cloudapp.Azure.com/es/ <br /><br />O principal ponto de extremidade para o cluster Elasticsearch (Observe o sufixo /es). Ele é protegido por autenticação básica de HTTP (as credenciais foram os parâmetros especificados de esUserName/esPassword do modelo ES-vários nós). O cluster também possui a cabeça plug-in instalada (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) para administração de cluster básico.|
|Serviço de Kibana                    |http://myBigCluster.westus.cloudapp.Azure.com <br /><br />O serviço de Kibana está configurado para mostrar dados de cluster Elasticsearch criado. Ele é protegido pelas mesmas credenciais de autenticação do próprio cluster.|

## <a name="in-process-versus-out-of-process-trace-capturing"></a>Em processo versus capturando de rastreamento de fora de processo
Na introdução, podemos mencionados duas maneiras fundamentais de coleta de dados de diagnóstico: em processo e fora de processo. Cada tem vantagens e desvantagens.

Vantagens do **rastreamento capturando em processo** :

1. *Implantação e configuração fácil*

    * A configuração do conjunto de dados de diagnóstico é apenas parte da configuração do aplicativo. É fácil sempre manter "em sincronia" com o resto do aplicativo.

    * Configuração por aplicativo ou por serviço é possível facilmente.

    * Captura de rastreamento de fora do processo geralmente requer uma implantação separada e configuração do agente de diagnóstico, que é uma tarefa administrativa extra e uma fonte potencial de erros. A tecnologia de agente específico com frequência permite apenas uma instância do agente por máquina virtual (nó). Isso significa que a configuração para a coleção da configuração do diagnóstico é compartilhada entre todos os aplicativos e serviços executados nesse nó.

2. *Flexibilidade*

    * O aplicativo pode enviar os dados onde quer que ele precisa ir, desde que há uma biblioteca de cliente que ofereça suporte para o sistema de armazenamento de dados de destino. Novo receptores podem ser adicionados como desejado.

    * Regras de captura, filtragem e agregação de dados complexas podem ser implementadas.

    * Uma captura de rastreamento de fora do processo muitas vezes é limitado pelo receptores de dados que o agente de suporte. Alguns agentes são extensíveis.

3. *Acesso a dados de aplicativo interno e contexto*

    * O diagnóstico subsistema executando dentro do processo de serviço do aplicativo pode aumentar facilmente os rastreamentos com informações contextuais.

    * Na abordagem de fora de processo, os dados devem ser enviados para um agente via algum mecanismo de comunicação entre processos, como o rastreamento de eventos do Windows. Esse mecanismo pode impor limitações adicionais.

Vantagens do **rastreamento de fora do processo capturando** incluem:

1. *A capacidade de monitorar o aplicativo e coletar falha descarta*

    * Captura de rastreamento em processo pode ser malsucedido se o aplicativo falha ao iniciar ou trava. Um agente independente tem uma melhor chance de capturar informações de solução de problemas essenciais.<br /><br />

2. *Vencimento, eficiência e desempenho comprovado*

    * Um agente desenvolvido por um fornecedor de plataforma (como um agente de diagnóstico do Microsoft Azure) foi sujeitos a rigorosos testes e batalha otimização.

    * Com o rastreamento de em processo capturando, deve-se ter cuidado para garantir que a atividade de enviar dados de diagnósticos de um processo de aplicativo não interferir com tarefas principais do aplicativo ou introduzir problemas de desempenho ou intervalo. Um agente de maneira independente em execução é menos sujeito a esses problemas e especificamente para limitar seu impacto no sistema.

É possível combinar e beneficiar as duas abordagens. Na verdade, talvez seja a melhor solução para muitos aplicativos.

Aqui, usaremos a **biblioteca de Microsoft.Diagnostic.Listeners** e o rastreamento de em processo capturando para enviar dados de um aplicativo de serviço tecidos para um cluster de Elasticsearch.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>Use a biblioteca de ouvintes para enviar dados de diagnóstico para Elasticsearch
A biblioteca de Microsoft.Diagnostic.Listeners é parte do aplicativo de serviço tecidos de exemplo PartyCluster. Para usá-lo:

1. Baixe [a amostra de PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) do GitHub.

2. Copie os projetos Microsoft.Diagnostics.Listeners e Microsoft.Diagnostics.Listeners.Fabric (pastas inteiras) da pasta de amostra PartyCluster para a pasta de solução do aplicativo que deveria para enviar os dados para Elasticsearch.

3. Abra a solução de destino, clique com botão direito no nó solução em Solution Explorer e escolha **Adicionar projeto existente**. Adicione o projeto Microsoft.Diagnostics.Listeners à solução. Repita o mesmo para o projeto Microsoft.Diagnostics.Listeners.Fabric.

4. Adicione uma referência de projeto de seus projetos de serviço para os dois projetos adicionados. (Cada serviço que deveria para enviar dados para Elasticsearch deve fazer referência Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric).

    ![Referências de projeto às bibliotecas de Microsoft.Diagnostics.EventListeners e Microsoft.Diagnostics.EventListeners.Fabric][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>Pacote de Microsoft.Diagnostics.Tracing Nuget e lançamento de disponibilidade do serviço tecidos geral
Aplicativos criados com o lançamento de disponibilidade do serviço tecidos geral (2.0.135, lançado em 31 de março de 2016) direcionar **.NET Framework 4.5.2**. Esta versão é a versão mais recente do .NET Framework suportados pelo Azure no momento do lançamento GA. Infelizmente, esta versão do framework não possui determinadas APIs EventListener que precisa de biblioteca Microsoft.Diagnostics.Listeners. Como EventSource (o componente que formam a base de log APIs em aplicativos de tecidos) e EventListener estejam intimamente ligadas, cada projeto que usa a biblioteca de Microsoft.Diagnostics.Listeners deve usar uma implementação alternativa da EventSource. Essa implementação é fornecida pelo **pacote Microsoft.Diagnostics.Tracing Nuget**, criados pela Microsoft. O pacote é totalmente compatível com EventSource incluído no framework, para que nenhuma alteração de código deve ser necessária diferente de alterações de namespace referenciado.

Para começar a usar a implementação de Microsoft.Diagnostics.Tracing da classe EventSource, siga estas etapas para cada projeto de serviço que precisa para enviar dados para Elasticsearch:

1. Clique com botão direito no projeto serviço e escolha **Gerenciar pacotes do Nuget**.

2. Alternar para a fonte de pacote nuget.org (se não já estiver selecionada) e pesquise por "**Microsoft.Diagnostics.Tracing**".

3. Instalar o `Microsoft.Diagnostics.Tracing.EventSource` pacote (e suas dependências).

4. Abra o arquivo **ServiceEventSource.cs** ou **ActorEventSource.cs** em seu projeto de serviço e substitua o `using System.Diagnostics.Tracing` diretiva sobre o arquivo com o `using Microsoft.Diagnostics.Tracing` diretiva.

Essas etapas não será necessárias quando o **.NET Framework 4.6** é suportado pelo Microsoft Azure.

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Configuração e instanciação de ouvinte Elasticsearch
A etapa final para enviar dados de diagnóstico para Elasticsearch é criar uma instância de `ElasticSearchListener` e configurá-lo com dados de conexão Elasticsearch. O ouvinte captura automaticamente todos os eventos gerados por meio de classes EventSource definidas no projeto de serviço. Ele precisa estar ativa durante o ciclo de vida do serviço, portanto é o melhor lugar para criá-lo no código de inicialização de serviço. Veja aqui como o código de inicialização para um serviço sem estado pode parecer após as alterações necessárias (adições indicado no comentários começando com `****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Dados de conexão Elasticsearch devem ser colocados em uma seção separada no arquivo de configuração do serviço (**PackageRoot\Config\Settings.xml**). O nome da seção deve corresponder ao valor passado para o `FabricConfigurationProvider` construtor, por exemplo:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Os valores de `serviceUri`, `userName` e `password` parâmetros correspondem o endereço do ponto de extremidade de cluster Elasticsearch, Elasticsearch nome de usuário e senha, respectivamente. `indexNamePrefix`é o prefixo para índices de Elasticsearch; a biblioteca de Microsoft.Diagnostics.Listeners cria um novo índice para seus dados diariamente.

### <a name="verification"></a>Verificação
Pronto! Agora, sempre que o serviço é executado, ele começa enviando rastreamentos para o serviço de Elasticsearch especificado na configuração. Você pode verificar isso abrindo a Kibana UI associadas à instância de Elasticsearch de destino. Em nosso exemplo, o endereço da página é http://myBigCluster.westus.cloudapp.azure.com/. Verifique se índices com o prefixo de nome escolhido para o `ElasticSearchListener` instância realmente foram criados e preenchida com dados.

![Eventos de aplicativo de PartyCluster Kibana mostrando][2]

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o diagnóstico e monitoramento de um serviço de estrutura de serviço](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png
