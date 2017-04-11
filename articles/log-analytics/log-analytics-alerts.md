<properties 
   pageTitle="Alertas no Log Analytics | Microsoft Azure"
   description="Alertas no Log Analytics identificar informações importantes no seu repositório OMS e proativamente podem notificá-lo de problemas ou chamar ações para tentar corrigi-los.  Este artigo descreve como criar uma regra de alerta e detalhes as diferentes ações que elas podem tomar."
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
   ms.date="08/22/2016"
   ms.author="bwren" />

# <a name="alerts-in-log-analytics"></a>Alertas no Log de análise

Alertas no Log Analytics identificam informações importantes no seu repositório OMS.  Regras de alerta automaticamente executar pesquisas de log de acordo com uma agenda e criem um registro de alerta, se os resultados corresponderem critérios específicos.  A regra pode executar automaticamente uma ou mais ações para notificá-lo do alerta proativamente ou invocar outro processo.   

![Alertas de análise de log](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Criar uma regra de alerta
Para criar uma regra de alerta, você começar pela criação de uma pesquisa de log para os registros que deve chamar o alerta.  O botão de **alerta** , em seguida, estará disponível para que você pode criar e configurar a regra de alerta.

1.  Na página Visão geral do OMS, clique em **Pesquisa de Log**.
2.  Crie uma nova consulta de pesquisa de log ou selecionar uma pesquisa de log salvo. 
3.  Clique em **alerta** na parte superior da página para abrir a tela de **Adicionar regra de alerta** .
4. Consulte as tabelas abaixo para obter detalhes sobre as opções para configurar o alerta.
5. Quando você fornecer a janela de tempo para a regra de alerta, o número de registros existentes que correspondem ao critério de pesquisa para essa janela de tempo será exibido.  Isso pode ajudá-lo a determinar a frequência com que você terá o número de resultados que você espera.
4.  Clique em **Salvar** para concluir a regra de alerta.  Ele será iniciado executando imediatamente.


![Adicionar regra de alerta](media/log-analytics-alerts/add-alert-rule.png)

| Propriedade | Descrição |
|:--|:--|
| **Informações de alerta** | |
| Nome |  Nome exclusivo para identificar a regra de alerta. |
| Gravidade | Gravidade do alerta que é criado por esta regra. |
| Consulta de pesquisa | Selecione **usar a consulta de pesquisa atual** para usar a consulta atual ou selecione uma pesquisa salva existente na lista.  A sintaxe de consulta é fornecida na caixa de texto onde você pode modificá-lo se necessário.  |
| Janela de tempo | Especifica o intervalo de tempo para a consulta.  A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual.  Isso pode ser qualquer valor entre 5 minutos e 24 horas.  Ele deve ser maior ou igual à frequência de alerta.  <br><br> Por exemplo, se a janela de tempo está definida como 60 minutos e a consulta é executada no 1:15 PM, somente os registros criados entre 12:15 PM e 1:15 PM serão retornados. |
| **Cronograma** |     
| Limite | Critérios para quando criar um alerta.  Um alerta é criado se o número de registros retornados pela consulta corresponde este critério. |
| Frequência de alerta | Especifica com que frequência a consulta deve ser executada.  Pode ser qualquer valor entre 5 minutos e 24 horas.  Deve ser igual ou menor do que a janela de tempo. |
| Suprimir alertas | Quando você ativa supressão para a regra de alerta, ações da regra estão desabilitadas para um período definido de tempo depois de criar um novo alerta.  A regra ainda está sendo executado e criará registros de alerta, se o critério é atendido.  Isso é para permitir que o tempo para corrigir o problema sem executar ações duplicadas. |
| **Ações** | |
| Notificação por email | Especifique **Sim** se quiser que um email a ser enviada quando o alerta for acionado. |
| Assunto    | Assunto no email.  Você não pode modificar o corpo do email. |
| Destinatários | Endereços de todos os destinatários de email.  Se você especificar mais de um endereço, em seguida, separe os endereços com um ponto e vírgula (;). |
| Webhook | Especifique **Sim** se quiser chamar um webhook quando o alerta for acionado. |
| URL de Webhook | A URL da webhook. |
| Incluir carga JSON personalizada | Selecione esta opção se você quiser substituir a carga padrão com uma carga personalizada. |
| Insira sua carga JSON personalizada | A carga personalizada para o webhook.  Consulte a seção anterior para obter detalhes. |
| Runbook | Especifique **Yes** se você quiser iniciar um runbook de automação do Azure quando o alerta for acionado. |
| Selecione um runbook | Selecione runbook para iniciar a partir do runbooks na conta de automação configurada em sua solução de automação. |
| Executar em | Selecione **Azure** para executar runbook na nuvem Azure.  Selecione **Híbrido trabalhador** executem runbook em um [Operador de Runbook híbrido](..\automation\automation-hybrid-runbook-worker.md) no seu ambiente local. |


## <a name="manage-alert-rules"></a>Gerenciar regras de alerta
Você pode obter uma lista de todas as regras de alerta no menu **alertas** na análise de Log **configurações**.  

![Gerenciar alertas](./media/log-analytics-alerts/configure.png)

1. No console do OMS selecione o bloco **configurações** .
2. Selecione **alertas**.

Você pode executar várias ações nesse modo de exibição.

- Desabilite uma regra selecionando **Desativar** ao lado dela.
- Edite uma regra de alerta clicando no ícone de lápis ao lado dela.
- Remova uma regra de alerta clicando no ícone **X** ao lado dela. 


## <a name="setting-time-windows"></a>Janelas de tempo de configuração 

### <a name="event-alerts"></a>Alertas de eventos

Eventos incluem fontes de dados como logs de eventos do Windows, Syslog, e logs de personalizado.  Talvez você queira criar um alerta quando um evento de erro específico é criado ou quando vários eventos de erros são criados em uma janela de tempo específico.

Para alertar em um único evento, defina o número de resultados para maior que 0 e tanto a frequência e a janela de tempo para 5 minutos.  Que irá executar a consulta a cada 5 minutos e verifique a ocorrência de um único evento que foi criado desde a última vez em que a consulta foi executada.  Uma frequência mais longa pode atrasar a hora entre o evento coletado e o alerta está sendo criada.

Alguns aplicativos podem registrar um erro eventual que não deve necessariamente disparar um alerta.  Por exemplo, o aplicativo pode repetir o processo que criou o evento de erro e então êxito na próxima vez.  Nesse caso, talvez não deseja criar o alerta, a menos que vários eventos são criados em uma janela de tempo específico.  

Em alguns casos, talvez você queira criar um alerta na ausência de um evento.  Por exemplo, um processo pode registrar eventos normais para indicar que ele está funcionando corretamente.  Se ele não registrar um desses eventos dentro de uma janela de tempo específico, um alerta deve ser criado.  Nesse caso, você poderia definir o limite para *menor que 1*.

### <a name="performance-alerts"></a>Alertas de desempenho

[Dados de desempenho](log-analytics-data-sources-performance-counters.md) é armazenado como registros no repositório OMS semelhante aos eventos.  O valor em cada registro é a média medida sobre anterior 30 minutos.  Se você quiser alerta quando um contador de desempenho excede um determinado limite, esse limite deve ser incluído na consulta.

Por exemplo, se você quisesse Alertar quando o processador executa mais de 90% por 30 minutos, você usaria uma consulta como *tipo = Perf ObjectName = processador CounterName = "% tempo de processador" CounterValue > 90* e o limite para a regra de alerta para *maior do que 0*.  

 Como [os registros de desempenho](log-analytics-data-sources-performance-counters.md) são agregados a cada 30 minutos, independentemente da frequência que você colete cada contador, uma janela de tempo menor do que 30 minutos não pode retornar nenhum registro.  Definindo a janela de tempo para 30 minutos garantirá que você obtém um único registro para cada fonte conectada que representa a média ao longo do tempo.

## <a name="alert-actions"></a>Ações de alerta

Além de criar um registro de alerta, você pode configurar a regra de alerta para executar automaticamente uma ou mais ações.  Ações proativamente podem notificá-lo do alerta ou invocar algum processo que tenta corrigir o problema que foi detectado.  As seções a seguir descrevem as ações que estão disponíveis atualmente.

### <a name="email-actions"></a>Ações de email
Ações de email enviar um email com os detalhes do alerta para um ou mais destinatários.  Você pode especificar o assunto do email, mas seu conteúdo é um formato padrão criado pela análise de Log.  Ele inclui informações de resumo como o nome do alerta além dos detalhes de até dez registros retornados pela pesquisa log.  Ele também inclui um link para uma pesquisa de log de análise de Log que retornará todo o conjunto de registros da consulta.   O remetente do email é *equipe de pacote de gerenciamento de operações Microsoft &lt; noreply@oms.microsoft.com *. 


### <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook permitem que você invocar um processo externo por meio de uma única solicitação HTTP POST.  O serviço está sendo chamado deve webhooks de suporte e determinar como ele usará qualquer carga recebe.  Você também poderia chamar uma API REST que não especificamente dá suporte webhooks desde que a solicitação seja em um formato que entende de API.  Exemplos do uso de um webhook em resposta a um alerta estão usando um serviço como o [atraso](http://slack.com) para enviar uma mensagem com os detalhes do alerta ou criando um incidente em um serviço como [PagerDuty](http://pagerduty.com/).  

Instruções completas para a criação de uma regra de alerta com uma webhook para chamar um serviço de amostra está disponível em [Webhooks em alertas de análise de Log](log-analytics-alerts-webhooks.md).

Webhooks incluir uma URL e uma carga formatada em JSON que os dados enviados para o serviço externo.  Por padrão, a carga incluirá os valores da tabela a seguir.  Você pode optar por substituir essa carga por um personalizado de sua preferência.  Nesse caso você pode usar as variáveis na tabela para cada um dos parâmetros para incluir seu valor carga personalizada.


| Parâmetro | Variável | Descrição |
|:--|:--|:--|
| AlertRuleName | #alertrulename | Nome da regra de alerta. |
| AlertThresholdOperator | #thresholdoperator | Operador de limite para a regra de alerta.  *Maior* ou *menor que*. |
| AlertThresholdValue | #thresholdvalue | Valor de limite para a regra de alerta. |
| LinkToSearchResults | #linktosearchresults | Link para pesquisa de log de análise de Log que retorna os registros da consulta que criou o alerta. |
| ResultCount  | #searchresultcount | Número de registros nos resultados da pesquisa. |
| SearchIntervalEndtimeUtc  | #searchintervalendtimeutc | Hora de término para a consulta no formato UTC. |
| SearchIntervalInSeconds | #searchinterval | Janela de tempo para a regra de alerta. |
| SearchIntervalStartTimeUtc  | #searchintervalstarttimeutc | Inicie o tempo para a consulta no formato UTC. |
| SearchQuery | #SearchQuery | Consulta de pesquisa de log usada pela regra de alerta. |
| SearchResults | Veja abaixo | Registros retornados pela consulta no formato JSON.  Limitado a primeiros 5.000 registros. |
| WorkspaceID | #workspaceid | ID do seu espaço de trabalho do OMS. |


Por exemplo, você pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado de *texto*.  O serviço que este webhook chama seria esperando esse parâmetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Essa carga de exemplo seria resolver algo parecido com o seguinte quando enviado para a webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir os resultados de pesquisa em uma carga personalizada, adicione a seguinte linha como uma propriedade de nível superior na carga json.  

    "IncludeSearchResults":true

Por exemplo, para criar uma carga personalizada que inclui apenas o nome de alerta e os resultados da pesquisa, você pode usar o seguinte. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Você pode percorrer um exemplo completo da criação de uma regra de alerta com uma webhook para iniciar um serviço externo em [webhook exemplo de alerta de análise de Log](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Ações de runbook

Ações de runbook iniciar um runbook no Azure automação.  Para poder usar esse tipo de ação, você deve ter a [solução de automação](log-analytics-add-solutions.md) instalado e configurado em seu espaço de trabalho do OMS.  Se você não tiver instalado quando você cria uma nova regra de alerta, um link para sua instalação é exibido.  Você pode selecionar o runbooks na conta de automação que você configurou na solução automação.

Ações de runbook iniciar runbook usando um [webhook](../automation/automation-webhooks.md).  Quando você cria a regra de alerta, ele criará automaticamente um novo webhook para runbook com o nome **Correção de alerta de OMS** seguido por um GUID.  

Você não pode preencher diretamente quaisquer parâmetros do runbook, mas o [parâmetro $WebhookData](../automation/automation-webhooks.md) incluirá os detalhes do alerta, incluindo os resultados de pesquisa de log de que o criou.  Runbook precisará definir **$WebhookData** como um parâmetro para as propriedades do alerta de acesso.  Os dados de alerta estão disponíveis no formato json em uma única propriedade chamado **SearchResults** na propriedade **RequestBody** do **$WebhookData**.  Isso terá com as propriedades da tabela a seguir.


| Nó | Descrição |
|:--|:--|
| ID         | Caminho e GUID da pesquisa. |
| __metadata | Informações sobre o alerta, inclusive o número de registros e o status dos resultados da pesquisa. |
|  valor     |  Entrada separada para cada registro nos resultados da pesquisa.  Os detalhes da entrada corresponderão as propriedades e os valores do registro.   |

Por exemplo, seguir runbook seria extrair os registros retornados pela pesquisa log e atribua propriedades diferentes com base no tipo de cada registro.  Observe que inicia o runbook convertendo **RequestBody** de json para que ele pode ser trabalhou com como um objeto no PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value
    
    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer
        
        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }
        
        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>Registros de alerta

Registros de alerta criados por regras de alerta em análise de Log tem um **tipo** de **alerta** e um **SourceSystem** do **OMS**.  Eles têm as propriedades da tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Tipo          | *Alerta* |
| SourceSystem  | *OMS* |
| AlertSeverity | Nível de gravidade do alerta. |
| AlertName     | Nome do alerta. |
| Consulta         | Texto da consulta que foi executado.  |
| QueryExecutionEndTime   | Fim do intervalo de tempo para a consulta. |
| QueryExecutionStartTime | Início do intervalo de tempo para a consulta.  |
| TimeGenerated | Data e hora em que o alerta foi criado. |

Há outros tipos de registros de alerta criados pela [solução de gerenciamento de alerta](log-analytics-solution-alert-management.md) e por [exporta Power BI](log-analytics-powerbi.md).  Estes todos têm um **tipo** de **alerta** , mas são diferenciados por seus **SourceSystem**.




## <a name="next-steps"></a>Próximas etapas

- Instale a [solução de gerenciamento de alerta](log-analytics-solution-alert-management.md) para analisar os alertas criadas no Log Analytics juntamente com alertas coletadas do System Center Operations Manager (SCOM).
- Leia mais sobre [as pesquisas de log](log-analytics-log-searches.md) que pode gerar alertas.
- Execute uma passo a passo para [Configurar um webook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
- Aprenda a gravar [runbooks na automação do Azure](https://azure.microsoft.com/documentation/services/automation) para corrigir problemas identificados por alertas.