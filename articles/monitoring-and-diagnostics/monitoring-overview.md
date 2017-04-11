<properties
    pageTitle="Visão geral de monitoramento no Microsoft Azure | Microsoft Azure"
    description="Superior visão geral de nível monitoramento e diagnóstico no Microsoft Azure incluindo alertas, webhooks, escala automática e mais."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"l
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="robb"/>

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Visão geral de monitoramento no Microsoft Azure

Este artigo fornece uma visão conceitual do monitoramento dos recursos do Azure. Ele fornece ponteiros para informações sobre tipos específicos de recursos.  Para obter informações de alto nível sobre monitoramento seu aplicativo do ponto de vista não Azure, consulte [diretrizes de monitoramento e diagnóstico](../best-practices-monitoring.md).

Aplicativos em nuvem são complexos com muitas partes móveis. Monitoramento fornece dados para garantir que seu aplicativo permaneça ativo e em execução em um estado de integridade. Ele também ajudará a afastar possíveis problemas ou solucionar passaram aquelas. Além disso, você pode usar dados de monitoramento para obter ideias profunda sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho do aplicativo ou manutenção ou automatizar ações que exijam caso contrário intervenção manual.

O diagrama a seguir mostra uma exibição conceitual de monitoramento Azure, incluindo o tipo de logs, você pode coletar e o que você pode fazer com esses dados.   

![Modelo lógico para monitoramento e diagnóstico para recursos não computação](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figura 1: Modelo conceitual para monitoramento e diagnóstico para recursos não computação

<br/>

![Modelo lógico para monitoramento e diagnóstico para recursos de computação](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figura 2: Modelo conceitual para monitoramento e diagnóstico para recursos de computação


## <a name="monitoring-sources"></a>Fontes de monitoramento
### <a name="activity-logs"></a>Logs de atividades
Você pode pesquisar o Log de atividade (anteriormente chamado operacional ou Logs de auditoria) para obter informações sobre o recurso como visto por infraestrutura do Azure. O log contém informações como horários quando os recursos são criados ou destruídos.  

### <a name="host-vm"></a>Host de máquina virtual
**Calcular apenas**


Alguns recursos como serviços de nuvem, máquinas virtuais, de computação e serviço tecidos ter uma VM Host dedicado interagem com. A máquina virtual Host é o equivalente da máquina virtual raiz no modelo de hipervisor Hyper-V. Nesse caso, você pode coletar métricas apenas a máquina virtual Host além SO convidado.  

Para outros serviços do Azure, não é necessariamente um mapeamento 1:1 entre o recurso e de uma determinada VM de Host para que métricas de máquina virtual do host não estejam disponíveis.


### <a name="resource---metrics-and-diagnostics-logs"></a>Recurso - métricas e Logs de diagnóstico
Métricas de coletáveis variam com base no tipo de recurso. Por exemplo, máquinas virtuais fornece estatísticas no disco IO e por cento da CPU. Mas essas estatísticas não existem para uma fila de barramento de serviço, que em vez disso fornece métricas como produtividade de tamanho e a mensagem da fila.

Recursos de computação, você pode obter métricas nos módulos SO convidado e o diagnóstico de como o diagnóstico do Azure. Diagnóstico do Azure ajuda a coleta e rotear reunir dados de diagnóstico para outros locais, incluindo o armazenamento do Azure.

Uma lista de avaliações atualmente coletáveis está disponível em [métricas de suporte](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicativo - Logs de diagnóstico, Logs de aplicativos e métricas
**Calcular apenas**

Aplicativos podem ser executados na parte superior de SO convidado no modelo de computação. Eles emitem seu próprio conjunto de métricas e logs.

Tipos de métricas incluem

- Contadores de desempenho
- Logs de aplicativos
- Logs de eventos do Windows
- Fonte de evento do .NET
- Logs do IIS
- Manifesto com base ETW
- Despejos
- Logs de erro do cliente


## <a name="uses-for-monitoring-data"></a>Usos para monitoramento de dados

### <a name="visualize"></a>Visualizar
Visualizar os dados de monitoramento em gráficos e elementos gráficos ajuda você a localizar tendências muito mais rápido que examinar os dados em si.  

Alguns métodos de visualização incluem:

- Usar o portal do Azure
- Rotear dados para obtenção de informações de aplicativo do Azure
- Rotear dados para Microsoft PowerBI
- Rotear os dados para uma ferramenta de visualização de terceiros usando qualquer um dos streaming live ou fazendo com que a ferramenta de ler a partir de um arquivo no armazenamento do Azure

### <a name="archive"></a>Arquivo morto
Monitorar os dados normalmente é gravado armazenamento do Azure e mantido lá até você excluí-lo.

Algumas maneiras de usar esses dados:

- Uma vez escrito, você pode ter outras ferramentas dentro ou fora do Azure lê-la e processar.
- Baixar os dados localmente para arquivamento local ou alterar sua política de retenção na nuvem para manter os dados por longos períodos de tempo.  
- Você deixar os dados no armazenamento do Azure indefinidamente, embora você precise pagar para o armazenamento do Azure com base na quantidade de dados que você mantém.
-

### <a name="query"></a>Consulta
Você pode usar a API REST Monitor Azure, cruz comandos da Interface de linha de comando de plataforma, cmdlets do PowerShell ou o SDK do .NET para acessar os dados no sistema ou armazenamento do Azure

Alguns exemplos incluem:

-  Obter dados para um aplicativo de monitoramento personalizado que você escreveu
-  Criar consultas personalizadas e enviar dados para um aplicativo de terceiros.

### <a name="route"></a>Roteiro
Você pode transmitir dados de monitoramento para outros locais em tempo real.

Alguns exemplos incluem:

- Envie para obtenção de informações de aplicativo para que você pode usar as ferramentas de visualização lá.
- Envie para Hubs de evento para que você possa rotear para ferramentas de terceiros para executar análise em tempo real.

### <a name="automate"></a>Automatizar
Você pode usar dados de monitoramento a eventos de acionamento ou processos par inteiro exemplos incluem:

- Use dados em instâncias de computação de escala automática para cima ou para baixo com base na carga do aplicativo.
- Envie emails quando uma métrica exceder um limite predeterminado.
- Chamar uma URL da web (webhook) para executar uma ação em um sistema fora do Azure
- Iniciar um runbook no Azure automação para executar qualquer variedade de tarefas

## <a name="methods-of-use"></a>Métodos de uso
Em geral, você pode manipular o controle de dados, roteamento e recuperação usando um dos seguintes métodos. Nem todos os métodos estão disponíveis para todas as ações ou tipos de dados.

- [Portal do Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)  
- [Entre plataformas comando Interface de linha)](insights-cli-samples.md)
- [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Ofertas de monitoramento do Azure
Azure tem ofertas disponíveis para monitorar seus serviços de infraestrutura de depender de telemetria do aplicativo. O melhor monitoramento estratégia combina o uso de todas as três para percepção abrangente e detalhado a integridade dos seus serviços.

- [Monitor de azure](http://aka.ms/azmondocs) – ofertas de visualização, consulta, roteamento, alertas, escala automática e automação em dados tanto da infraestrutura do Azure (registro de atividade) e cada recurso individual Azure (Logs de diagnóstico). Este artigo faz parte da documentação do Monitor do Azure. O nome do Monitor do Azure foi lançado 25 de setembro em Ignite 2016.  O nome anterior era "Azure ideias."  
- [Obtenção de informações de aplicativo](https://azure.microsoft.com/documentation/services/application-insights/) – fornece detecção avançada e o diagnóstico de problemas na camada de aplicativo do seu serviço, bem integrada sobre dados do Azure monitoramento. É a plataforma de diagnóstico padrão de aplicativo de serviço Web Apps.  Você pode rotear dados de outros serviços para ela.  
- [Análise de log](https://azure.microsoft.com/documentation/services/log-analytics/) parte do [Pacote de gerenciamento de operações](https://www.microsoft.com/cloud-platform/operations-management-suite) – fornece uma solução de gerenciamento de TI abrangente para locais e terceiros baseado em nuvem infraestrutura de (como AWS) além dos recursos do Azure.  Dados do Azure Monitor podem ser roteados diretamente para a análise de Log para poder ver logs e métricas para todo o ambiente em um só lugar.     


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre

- [Monitor Azure em um vídeo de Ignite 2016](https://myignite.microsoft.com/videos/4977)
- [Introdução ao Azure Monitor](monitoring-get-started.md)
- [Diagnóstico do azure](../azure-diagnostics.md) se você estiver tentando diagnosticar problemas em seu aplicativo de serviço de nuvem, Máquina Virtual ou tecidos do serviço.
- [Obtenção de informações de aplicativo](https://azure.microsoft.com/documentation/services/application-insights/) se você estiver tentando problemas de diagnósticos em seu aplicativo Web do serviço de aplicativo.
- [Solução de problemas de armazenamento do Azure](../storage/storage-e2e-troubleshooting.md) ao usar o armazenamento de Blobs, tabelas ou filas
- [Análise de log](https://azure.microsoft.com/documentation/services/log-analytics/) e o [pacote de gerenciamento de operações](https://www.microsoft.com/cloud-platform/operations-management-suite)
