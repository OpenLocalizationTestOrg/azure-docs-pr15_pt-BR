<properties
   pageTitle="Coletar logs usando o diagnóstico do Azure Linux | Microsoft Azure"
   description="Este artigo descreve como configurar o diagnóstico do Azure para coletar logs de um cluster de serviço tecidos Linux em execução no Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Coletar logs usando o diagnóstico do Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando você está executando um cluster de estrutura de serviço do Azure, é uma boa ideia coletar logs de todos os nós em um local central. Ter os logs em um local central torna mais fácil analisar e solucionar problemas, independentemente de estarem no próprio cluster, seu aplicativo ou seus serviços. Uma maneira de carregar e coletar logs é usar a extensão de diagnóstico do Azure, que carrega os logs para armazenamento do Azure. Você pode ler os eventos de armazenamento e colocá-los em um produto como [Pesquisa elástica](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou outra solução de análise de log.

## <a name="log-sources-that-you-might-want-to-collect"></a>Fontes de registro que você talvez queira coletar
- **Logs de serviço tecidos**: emitida da plataforma via [LTTng](http://lttng.org) e carregados para sua conta de armazenamento. Logs podem ser eventos operacionais ou runtime que emite a plataforma. Estes registros são armazenados no local que especifica o manifesto cluster. (Para obter os detalhes da conta de armazenamento, pesquise a marca **AzureTableWinFabETWQueryable** e procure por **StoreConnectionString**.)
- **Eventos do aplicativo**: emitida de código do seu serviço. Você pode usar qualquer solução de log que grava arquivos de log baseado em texto, por exemplo, LTTng. Para obter mais informações, consulte a documentação de LTTng no rastreamento do aplicativo.  


## <a name="deploy-the-diagnostics-extension"></a>Implantar a extensão de diagnóstico
A primeira etapa coletar logs é implantar a extensão de diagnóstico em cada uma das VMs no cluster tecidos de serviço. A extensão de diagnóstico coleta logs em cada máquina virtual e carrega-las para a conta de armazenamento que você especificar. As etapas variam com base em se você usar o portal do Azure ou o Gerenciador de recursos do Azure.

Para implantar a extensão de diagnóstico VMs no cluster como parte da criação do cluster, configure o **Diagnóstico** para **em**. Depois de criar o cluster, você não pode alterar essa configuração usando o portal.

Em seguida, configure o diagnóstico do Azure Linux (LAD) para coletar os arquivos e colocá-los em sua conta de armazenamento. Esse processo é explicado como cenário 3 ("carregar seus próprios arquivos de log") o artigo [Usando LAD para monitorar e diagnosticar VMs Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Seguindo este processo obtém acesso aos rastreamentos. Você pode carregar os rastreamentos para um visualizador de sua escolha.

Você também pode implantar a extensão de diagnóstico usando o Gerenciador de recursos do Azure. O processo é semelhante para Windows e Linux e é documentado para clusters do Windows no [como coletar logs de diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Você também pode usar o pacote de gerenciamento de operações, conforme descrito na [Análise de Log de pacote de gerenciamento de operações com Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Depois de concluir essa configuração, o agente LAD monitora os arquivos de log especificado. Sempre que uma nova linha é acrescentada ao arquivo, ela cria uma entrada de syslog que é enviada para o armazenamento que você especificou.


## <a name="next-steps"></a>Próximas etapas
Para compreender mais detalhadamente os eventos que você deve examinar durante a solução de problemas, consulte [LTTng documentação](http://lttng.org/docs) e [Usando LAD](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).
