<properties 
   pageTitle="O alerta de gerenciamento no Microsoft monitoramento produtos | Microsoft Azure"
   description="Um alerta indica algum problema que requer atenção do administrador.  Este artigo descreve as diferenças em como os alertas sejam criadas e gerenciadas no System Center Operations Manager (SCOM) e a análise de Log e fornece práticas recomendadas ao aproveitar os dois produtos para uma estratégia de gerenciamento de alerta híbrida." 
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="managing-alerts-with-microsoft-monitoring"></a>Gerenciar alertas com monitoramento da Microsoft 

Um alerta indica algum problema que requer atenção do administrador.  Existem diferenças distintas entre System Center Operations Manager (SCOM) e a análise de Log no pacote de gerenciamento de operações (OMS) em termos de como os alertas são criadas, como eles são gerenciados e analisados e como você será notificado de que foi detectado um problema crítico.

## <a name="alerts-in-operations-manager"></a>Alertas no Operations Manager
Alertas no SCOM são geradas por regras individuais ou monitores para indicar um problema específico.  Um monitor pode gerar um alerta quando ele entra em um estado de erro enquanto uma regra pode gerar um alerta para indicar algum problema crítico que não está relacionado diretamente para a integridade de um objeto gerenciado.  Pacotes de gerenciamento incluem uma variedade de fluxos de trabalho que criar alertas para o aplicativo ou serviço que gerenciam.  Parte do processo de configuração de um novo pacote de gerenciamento está ajustando-o para garantir que você não receberá alertas excessivas para problemas que você não considere crítico.

![Modo de exibição de alerta do SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM fornece completos de gerenciamento de alerta com alertas tendo um status que pode ser alterado por administradores, como elas funcionam para resolver o problema.  Quando o problema foi resolvido, o administrador define o alerta fechada no momento em que ela não aparecerá mais em modos de exibição exibindo alertas ativos.  Alertas que são gerados de monitores podem ser resolvidas automaticamente quando o monitor de volta para um estado de integridade.

![Status de alerta](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertas no Log de análise
Um alerta na análise de Log é criado a partir de uma consulta de log que é executada automaticamente em intervalos regulares.  Você pode criar uma regra de alerta de qualquer consulta de log.  Se a consulta retorna resultados que correspondem aos critérios que você especificar, um alerta é criado.  Isso pode ser uma consulta específica que cria um alerta se um determinado evento for detectado, ou você pode usar uma consulta mais geral que procura por qualquer evento de erro relacionado a um aplicativo específico.

Alertas de análise de log são gravadas no repositório de OMS como um evento e podem ser recuperadas com uma consulta de log.  Eles não tem um status como SCOM eventos para que você pode indicar quando o problema foi resolvido.

![Alerta OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Quando SCOM é usado como fonte de dados para análise de Log, alertas SCOM são gravadas ao repositório OMS conforme eles são criados e modificados.  

![Alerta SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

A [solução de gerenciamento de alerta](http://technet.microsoft.com/library/mt484092.aspx) fornece um resumo dos alertas ativos e várias consultas comuns para recuperar diferentes conjuntos de alertas.  Isso oferece a análise mais eficaz dos seus alertas de um relatório no SCOM.  Você pode fazer buscas detalhadas em dos resumos para dados detalhados e criar consultas ad hoc para recuperar diferentes conjuntos de alertas.

![Solução de gerenciamento de alerta](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notificações
Notificações no SCOM enviarem um email ou texto em resposta a alertas que correspondem a critérios específicos.  Você pode criar assinaturas de notificação diferente que têm diferentes pessoas notificadas dependendo de critérios, como o objeto monitorado, a gravidade do alerta, o tipo de problema detectado, ou a hora do dia.

Algumas assinaturas podem ser usadas para implementar uma estratégia de notificação de conclusão para um grande número de pacotes de gerenciamento.

![Alertas SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Análise de log pode notificá-lo por meio de email que foi criado um alerta definindo uma ação de notificação de email em cada [regra de alerta](http://technet.microsoft.com/library/mt614775.aspx).  Ele não tem a capacidade de SCOM a inscrever-se vários alertas com uma única regra.  Você também precisa criar suas próprias regras de alerta como OMS não fornece qualquer pré-configurado.

![Alertas de análise de log](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Completamente é possível gerenciar alertas SCOM no Log Analytics apesar desde que você só pode modificá-los no Console de operações.  Análise de log é útil como parte de um gerenciamento de alerta apesar processo para fornecer ferramentas de análise que SCOM sozinho não tem.

## <a name="alert-remediation"></a>Correção de alerta
[Correção](http://technet.microsoft.com/library/mt614775.aspx) se refere a uma tentativa de corrigir automaticamente o problema identificado por um alerta.
  
SCOM permite que você executar o diagnóstico e recuperação em resposta a um monitor entrando em um estado não íntegro.  Isso acontece simultâneo para o monitor criando o alerta.  Diagnóstico e recuperação normalmente é implementadas como um script que é executado no agente.  Um diagnóstico tenta coletar mais informações sobre o problema detectado enquanto uma recuperação tenta corrigir o problema.

Análise de log permite que você iniciar um [runbook de automação do Azure](https://azure.microsoft.com/documentation/services/automation/) ou chamar uma webhook em resposta a um alerta de análise de Log.  Runbooks pode conter lógica complexa implementada no PowerShell.  O script é executado no Azure e pode acessar qualquer recursos Azure ou recursos externos disponíveis da nuvem.  Automação Azure têm a capacidade de executar runbooks em um servidor no seu centro de dados local, mas esse recurso não está disponível atualmente ao iniciar runbook em resposta a alertas de análise de Log.

Recuperação no SCOM tanto runbooks no OMS pode conter scripts do PowerShell, mas recuperação é mais difíceis de criar e gerenciar porque eles devem estar contidos em um pacote de gerenciamento.  Runbooks são armazenados na automação do Azure que fornece recursos para criação, teste e gerenciando runbooks.

Se você usar SCOM como uma fonte de dados para análise de Log, você pode criar um alerta de análise de Log usando uma consulta de log para recuperar alertas SCOM armazenadas no repositório do OMS.  Isso permitirá que você execute uma runbook de automação do Azure em resposta a um alerta de SCOM.  Claro, como runbook será executado no Azure, isso não seria uma estratégia viável para recuperação de problemas de local.

## <a name="next-steps"></a>Próximas etapas

- Conheça os detalhes de [alertas no System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).