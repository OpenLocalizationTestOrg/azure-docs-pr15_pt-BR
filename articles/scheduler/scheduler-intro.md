<properties
 pageTitle="O que é o Agendador do Azure? | Microsoft Azure"
 description="Agendador do Azure permite maneira declarativa descrevem ações a serem executadas na nuvem. Depois que o agenda e executa essas ações automaticamente."
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
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="what-is-azure-scheduler"></a>O que é o Agendador do Azure?

Agendador do Azure permite maneira declarativa descrevem ações a serem executadas na nuvem. Depois que o agenda e executa essas ações automaticamente.  Agendador faz isso usando [o portal do Azure](scheduler-get-started-portal.md), código, [API REST](https://msdn.microsoft.com/library/mt629143.aspx)ou Azure PowerShell.

Agendador cria, mantém e invoca o trabalho agendado.  Agendador não hospedar qualquer cargas de trabalho ou executar qualquer código. IT somente o código _invoca_ hospedado em outro lugar — no Azure, local, ou com outro provedor. Ele chama via HTTP, HTTPS, uma fila do armazenamento, uma fila de barramento de serviço ou um tópico de barramento de serviço.

Agendador agenda [trabalhos](scheduler-concepts-terms.md), mantém um histórico de resultados da execução de trabalho que uma pode revisar e forma determinista e confiável agenda cargas de trabalho a ser executado. Azure WebJobs (parte do recurso Web Apps no serviço de aplicativo do Azure) e outro Azure agendamento de recursos usam o Agendador no plano de fundo. A [API do Agendador REST](https://msdn.microsoft.com/library/mt629143.aspx) ajuda a gerenciar a comunicação para essas ações. Assim, Agendador oferece suporte [agendas complexas e recorrência avançada](scheduler-advanced-complexity.md) facilmente.

Há várias situações que abrem-se para o uso do Agendador. Por exemplo:

+ _Ações do aplicativo recorrente:_ Periodicamente coleta de dados do Twitter em um feed.
+ _Manutenção diária:_ Remoção diária dos logs, fazendo backups e outras tarefas de manutenção. Por exemplo, um administrador pode optar por fazer backup do banco de dados às 1:00 todos os dias para os próximos meses nove.

Scheduler permite que você criar, atualizar, excluir, exibir e gerenciar trabalhos e [conjuntos de trabalho](scheduler-concepts-terms.md) programaticamente, usando scripts e no portal.

## <a name="see-also"></a>Consulte também

 [Hierarquia de entidades, terminologia e conceitos de Agendador Azure](scheduler-concepts-terms.md)

 [Começar a usar o Agendador no portal do Azure](scheduler-get-started-portal.md)

 [Planos e cobranças no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência de API do Agendador REST Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador Azure](scheduler-powershell-reference.md)

 [Azure Agendador alta disponibilidade e confiabilidade](scheduler-high-availability-reliability.md)

 [Limites de Agendador Azure, padrões e códigos de erro](scheduler-limits-defaults-errors.md)

 [Autenticação de saída Agendador Azure](scheduler-outbound-authentication.md)
