<properties 
   pageTitle="Fontes de dados em análise de Log | Microsoft Azure"
   description="Fontes de dados definem os dados que coleta de análise de Log de agentes e outros conectadas fontes.  Este artigo descreve o conceito de como a análise de Log usa fontes de dados, explica os detalhes de como configurá-los e fornece um resumo das fontes de dados diferentes disponíveis."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="data-sources-in-log-analytics"></a>Fontes de dados em análise de Log

Análise de log coleta dados de fontes de conectado no seu espaço de trabalho do OMS e armazena no repositório OMS.  Os dados coletados de cada são definidos por fontes de dados que você configurar.  Dados no repositório OMS são armazenados como um conjunto de registros.  Cada fonte de dados cria registros de um tipo específico com cada tipo com seu próprio conjunto de propriedades.

![Log de coleta de dados de análise](./media/log-analytics-data-sources/overview.png)

Fontes de dados são diferentes soluções OMS também coletar dados de fontes conectadas e criar registros no repositório OMS.  As soluções podem ser adicionadas ao seu espaço de trabalho da Galeria de soluções e normalmente fornecerá ferramentas adicionais de análise no portal do OMS.  

## <a name="summary-of-data-sources"></a>Resumo das fontes de dados

Fontes de dados que estão disponíveis atualmente no Log Analytics estão listadas na tabela a seguir.  Cada tem um link para um artigo separado fornecer detalhes de fonte de dados.

| Fonte de dados | Tipo de evento | Descrição |
|:--|:--|:--|
| [Logs personalizados](log-analytics-data-sources-custom-logs.md) | \<LogName\>_CL | Arquivos de texto que contém informações de log de agentes Windows ou Linux. |
| [Logs de eventos do Windows](log-analytics-data-sources-windows-events.md) | Evento | Eventos coletados do log de eventos em computadores com Windows. |
| [Contadores de desempenho do Windows](log-analytics-data-sources-performance-counters.md) | Desempenho | Contadores de desempenho coletados de computadores com Windows. |
| [Contadores de desempenho de Linux](log-analytics-data-sources-performance-counters.md) | Desempenho | Contadores de desempenho coletados de computadores Linux. |
| [Logs do IIS](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Logs de serviços de informações da Internet no formato W3C. |
| [Syslog](log-analytics-data-sources-syslog.md) | Syslog | Eventos de syslog em computadores Windows ou Linux. |

## <a name="configuring-data-sources"></a>Configurar fontes de dados

Você configura fontes de dados no menu de **dados** no Log Analytics **configurações**.  Qualquer configuração foi entregue para todas as fontes conectadas em seu espaço de trabalho do OMS.  Atualmente, você não pode excluir quaisquer agentes dessa configuração.

![Configurar eventos do Windows](./media/log-analytics-data-sources/configure-events.png)

2. No console do OMS selecione o bloco **configurações** .
3. Selecione **dados**.
4. Clique na fonte de dados para configurar.
5. Siga o link a documentação de cada fonte de dados na tabela acima para ver detalhes sobre sua configuração.

## <a name="data-collection"></a>Coleta de dados

Configurações de fonte de dados são entregues a agentes que estão conectados diretamente ao OMS em poucos minutos.  Os dados especificados estão coletados do agente e entregue diretamente para a análise de Log em intervalos específicos para cada fonte de dados.  Consulte a documentação para cada fonte de dados para essas especificações.

Para agentes do System Center Operations Manager (SCOM) em um grupo de gerenciamento conectada, configurações de fonte de dados são traduzidas em pacotes de gerenciamento e entregue ao grupo de gerenciamento a cada 5 minutos por padrão.  O agente baixa o pacote de gerenciamento como qualquer outro e coleta de dados especificado. Dependendo da fonte de dados os dados serão enviadas para um servidor de gerenciamento que encaminha os dados para a análise de Log ou o agente enviará os dados para a análise de Log sem passar pelo servidor de gerenciamento. Consulte [detalhes de conjunto de dados para recursos OMS e soluções](log-analytics-add-solutions.md#data-collection-details-for-oms-features-and-solutions) para obter detalhes.  Você pode ler sobre detalhes de conectando SCOM e OMS e modificando a frequência que a configuração seja entregue ao [Configurar integração com o System Center Operations Manager](log-analytics-om-agents.md).

## <a name="log-analytics-records"></a>Registros de log de análise

Todos os dados coletados pela análise de Log está armazenado no repositório OMS como registros.  Registros coletados pelo diferentes fontes de dados terão seu próprio conjunto de propriedades e ser identificados por sua propriedade **tipo** .  Consulte a documentação para cada fonte de dados e solução para obter detalhes sobre cada tipo de registro.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as soluções](log-analytics-add-solutions.md) que adicionar funcionalidade a análise de Log e também coletar dados para o repositório OMS.
- Saiba mais sobre [as pesquisas de log](log-analytics-log-searches.md) analisar os dados coletados de fontes de dados e soluções.  
- Configure [alertas](log-analytics-alerts.md) para notificá-lo de dados críticos coletados de fontes de dados e soluções proativamente.
