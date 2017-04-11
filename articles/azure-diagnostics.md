<properties
    pageTitle="Visão geral do diagnóstico do Azure | Microsoft Azure"
    description="Use o diagnóstico do Azure para depuração, medir o desempenho, monitoramento, análise de tráfego em serviços de nuvem, máquinas virtuais e estrutura de serviço"
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
    ms.date="06/02/2016"
    ms.author="robb"/>


# <a name="what-is-microsoft-azure-diagnostics"></a>O que é o diagnóstico do Microsoft Azure


Diagnóstico do Azure é o recurso dentro do Azure que permite a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de um número de diferentes fontes. Suportado atualmente são Web de serviço de nuvem do Azure e funções de trabalho, máquinas virtuais do Azure executando o Microsoft Windows e o serviço tecidos. Outros serviços do Azure têm sua próprias diagnóstico separado.

## <a name="data-you-can-collect"></a>Você pode coletar de dados

Diagnóstico do Azure pode coletar os seguintes tipos de dados:

Fonte de dados|Descrição
---|---
Contadores de desempenho | Sistema operacional e contadores de desempenho personalizados
Logs de aplicativos     | Rastrear mensagens escritas pelo seu aplicativo
Logs de eventos do Windows   | Informações enviadas para o sistema de log de eventos do Windows
Fonte de evento do .NET    | Código de gravar eventos usando a classe .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)
Logs do IIS             | Informações sobre sites do IIS
Manifesto com base ETW   | Eventos de rastreamento do Windows gerados por qualquer processo
Despejos          | Informações sobre o estado do processo em caso de uma falha de aplicativo
Logs de erro personalizada    | Logs criados pelo aplicativo ou serviço
Logs de infraestrutura de diagnóstico Azure|Informações sobre diagnóstico do próprio

A extensão de diagnóstico do Azure pode transferir esses dados para uma conta de armazenamento do Azure ou enviá-lo para serviços como o [Aplicativo ideias](./application-insights/app-insights-cloudservices.md). Você pode usar os dados para depuração e solução de problemas, medir o desempenho, uso do recurso, a análise de tráfego e a capacidade de planejamento de monitoramento e auditoria.


## <a name="versioning"></a>Controle de versão
Ver o [histórico de controle de versão do diagnóstico do Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Próximas etapas
Escolha qual serviço que você está tentando coletar diagnósticos e use os artigos a seguir para começar. Use os links de diagnóstico do Azure geral para referência para tarefas específicas.

## <a name="web-apps"></a>Aplicativos Web
Observe que aplicativos Web não use diagnóstico do Azure. Encontrar as informações equivalentes em [Aplicativos Web](./app-service-web/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Serviços de nuvem usando o diagnóstico do Azure
- Se usando o Visual Studio, consulte [Usar Visual Studio para rastrear um aplicativo de serviços de nuvem](./vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar. Caso contrário, consulte
- [Como monitorar os serviços de nuvem usando o diagnóstico do Azure](./cloud-services/cloud-services-how-to-monitor.md)
- [Configurar o diagnóstico do Azure em um aplicativo de serviços de nuvem](./cloud-services/cloud-services-dotnet-diagnostics.md)

Para tópicos mais avançados, consulte

- [Usando o diagnóstico do Azure com ideias de aplicativo de serviços de nuvem](./application-insights/app-insights-cloudservices.md)
- [Rastrear o fluxo de um aplicativo de serviços de nuvem com diagnóstico do Azure](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Usar o PowerShell para configurar o diagnóstico em serviços de nuvem](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## <a name="virtual-machines-using-azure-diagnostics"></a>Máquinas virtuais usando o diagnóstico do Azure
- Se usando o Visual Studio, consulte [Usar Visual Studio para rastrear máquinas virtuais do Azure](./vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar. Caso contrário, consulte
- [Configurar o diagnóstico do Azure em uma máquina Virtual do Azure](./virtual-machines-dotnet-diagnostics.md)

Para tópicos mais avançados, consulte

- [Usar o PowerShell para configurar o diagnóstico em máquinas virtuais do Azure](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Criar uma máquina Virtual do Windows com o monitoramento e diagnóstico usando o modelo de Gerenciador de recursos do Azure](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="service-fabric-using-azure-diagnostics"></a>Estrutura de serviço usando o diagnóstico do Azure
Introdução ao [Monitor um aplicativo de serviço tecidos](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muitos outros artigos de diagnóstico de serviço tecidos estarão disponíveis na árvore de navegação à esquerda quando chegar a deste artigo.

## <a name="general-azure-diagnostics-articles"></a>Artigos de diagnóstico do Azure geral
- [Configuração de esquema de diagnóstico do azure](https://msdn.microsoft.com/library/azure/mt634524.aspx) - Aprenda a alterar o arquivo de esquema para coletar e rotear os dados de diagnóstico. Observe que você também pode usar Visual Studio para alterar o arquivo de esquema.
- [Como diagnóstico do Azure dados são armazenados no armazenamento do Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - saber os nomes das tabelas e blobs onde os dados diagnósticos são gravados.
- Aprenda a [Usar contadores de desempenho no diagnóstico do Azure](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Saiba como [informações de diagnóstico do Azure rota de obtenção de informações de aplicativo](./azure-diagnostics-configure-applicationinsights.md)
- Se você tiver problemas com diagnóstico iniciando ou localizar seus dados em tabelas de armazenamento do Azure, consulte [Solução de problemas de diagnóstico do Azure](./azure-diagnostics-troubleshooting.md)
