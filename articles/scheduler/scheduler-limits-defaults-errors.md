<properties
 pageTitle="Limites do Agendador e padrões"
 description="Limites do Agendador e padrões"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-limits-and-defaults"></a>Limites do Agendador e padrões

## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Limitações, limites, padrões e cotas de Agendador

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>O cabeçalho x-ms-solicitação-id

Todas as solicitações feitas contra o serviço Agendador retorna um cabeçalho de resposta chamado**x-ms-solicitação-id**. Esse cabeçalho contém um valor opaco que identifica exclusivamente a solicitação.

Se uma solicitação de forma consistente está falhando e você verificou que a solicitação é formulada corretamente, você pode usar esse valor para relatar o erro à Microsoft. No seu relatório, inclua o valor de x-ms-solicitação-id, o tempo aproximado que a solicitação foi feita, o identificador da assinatura, conjunto de trabalho, e/ou o trabalho e o tipo de operação que tentou a solicitação.

## <a name="see-also"></a>Consulte também


 [O que é o Agendador?](scheduler-intro.md)

 [Hierarquia de entidades, terminologia e conceitos de Agendador Azure](scheduler-concepts-terms.md)

 [Começar a usar o Agendador no portal do Azure](scheduler-get-started-portal.md)

 [Planos e cobranças no Agendador do Azure](scheduler-plans-billing.md)

 [Referência de API do Agendador REST Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador Azure](scheduler-powershell-reference.md)

 [Azure Agendador alta disponibilidade e confiabilidade](scheduler-high-availability-reliability.md)

 [Autenticação de saída Agendador Azure](scheduler-outbound-authentication.md)
