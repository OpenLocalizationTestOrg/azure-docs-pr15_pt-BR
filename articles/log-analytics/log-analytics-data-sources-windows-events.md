<properties 
   pageTitle="Logs de eventos do Windows na análise de Log | Microsoft Azure"
   description="Os logs de eventos do Windows são uma das fontes de dados mais comuns usadas por análise de Log.  Este artigo descreve como configurar o conjunto de logs de eventos do Windows e detalhes dos registros criam no repositório OMS."
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

# <a name="windows-event-log-data-sources-in-log-analytics"></a>Fontes de dados de log de eventos do Windows em análise de Log

Os logs de eventos do Windows são uma das mais comuns de [fontes de dados](log-analytics-data-sources.md) usado para agentes Windows como este é o método usado pela maioria dos aplicativos para registrar informações e erros.  Você pode coletar eventos de logs padrão como o sistema e aplicativo além de especificar qualquer logs personalizados criados por aplicativos que você precisa monitorar.

![Eventos do Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Logs de eventos do Windows Configurando

Configure logs de eventos do Windows no [menu de dados nas configurações de análise de Log](log-analytics-data-sources.md#configuring-data-sources).

Análise de log somente coletará eventos dos logs de eventos do Windows que são especificados nas configurações.  Você pode adicionar um novo log digitando na caixa Nome do log e clicando em **+**.  Para cada log, somente eventos com as gravidades selecionados serão coletados.  Verifique as gravidades para o log específico que você deseja coletar.  Você não pode fornecer qualquer critério adicional para filtrar eventos.

![Configurar eventos do Windows](media/log-analytics-data-sources-windows-events/configure.png)


## <a name="data-collection"></a>Coleta de dados

Análise de log coletará cada evento que coincida com uma gravidade selecionada de um log de eventos monitorar conforme o evento for criado.  O agente gravará seu lugar em cada log de eventos que ela coleta de.  Se o agente de ficar offline por um período de tempo, em seguida, a análise de Log coletará eventos de onde parou, mesmo se os eventos foram criados enquanto o agente estava offline.


## <a name="windows-event-records-properties"></a>Propriedades de registros de eventos do Windows

Registros de eventos do Windows têm um tipo de **evento** e as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Computador            | Nome do computador que o evento foram coletado do. |
| EventCategory       | Categoria do evento. |
| EventData           | Todos os dados de evento em formato bruto. |
| Iddoevento             | Número do evento. |
| EventLevel          | Gravidade do evento no formulário numérico. |
| EventLevelName      | Gravidade do evento na forma de texto. |
| Log de eventos            | Nome do log de eventos que o evento foram coletado do. |
| ParameterXml        | Valores de parâmetro de evento no formato XML. |
| ManagementGroupName | Nome do grupo de gerenciamento de agentes SCOM.  Para outros agentes, isso é AOI-<workspace ID> |
| RenderedDescription | Descrição do evento com valores de parâmetro |
| Fonte              | Origem do evento. |
| SourceSystem  | Tipo de agente que o evento foram coletado do. <br> Conectar OpsManager – agente do Windows, direta ou SCOM <br> Linux – todos os agentes de Linux  <br> AzureStorage – diagnóstico do Azure |
| TimeGenerated       | Data e hora que o evento foi criado no Windows. |
| Nome de usuário            | Nome de usuário da conta que registrou o evento. |



## <a name="log-searches-with-windows-events"></a>Pesquisas de log com eventos do Windows

A tabela a seguir fornece exemplos diferentes de pesquisas de log que recuperar registros de eventos do Windows.

| Consulta | Descrição |
|:--|:--|
| Tipo = evento | Todos os eventos do Windows. |
| Tipo = evento EventLevelName = erro | Todos os eventos do Windows com gravidade do erro. |
| Tipo = evento & #124; Count () medida por fonte | Eventos de contagem do Windows pela fonte. |
| Tipo = evento EventLevelName = erro & #124; Count () medida por fonte | Eventos de erro de contagem do Windows pela fonte. |

## <a name="next-steps"></a>Próximas etapas

- Configure a análise de Log para coletar outras [fontes de dados](log-analytics-data-sources.md) para análise.
- Saiba mais sobre [as pesquisas de log](log-analytics-log-searches.md) analisar os dados coletados de fontes de dados e soluções.  
- Use [Campos personalizados](log-analytics-custom-fields.md) para analisar os registros de eventos em campos individuais.
- Configure o [conjunto de contadores de desempenho](log-analytics-data-sources-performance-counters.md) de seus agentes do Windows.