<properties
 pageTitle="Agendador alta disponibilidade e confiabilidade"
 description="Agendador alta disponibilidade e confiabilidade"
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
 ms.date="08/16/2016"
 ms.author="deli"/>


# <a name="scheduler-high-availability-and-reliability"></a>Agendador alta disponibilidade e confiabilidade

## <a name="azure-scheduler-high-availability"></a>Agendador Azure alta disponibilidade

Como um serviço de plataforma Windows Azure núcleo, o Agendador do Azure é altamente disponível e recursos de implantação do serviço de localização geográfica redundantes e replicação de trabalho de localização geográfica regionais.

### <a name="geo-redundant-service-deployment"></a>Implantação do serviço de localização geográfica redundantes

Agendador do Azure está disponível por meio da interface do usuário em quase todos os região geográfica que está no Azure hoje. A lista de regiões que o Agendador do Azure está disponível no está [listado aqui](https://azure.microsoft.com/regions/#services). Se um data center em uma região hospedado é renderizado indisponível, os recursos de failover do Agendador do Azure são que o serviço está disponível na Central de dados de outra.

### <a name="geo-regional-job-replication"></a>Replicação de trabalho de localização geográfica regionais

Não é apenas o Agendador do Azure front-end disponível para solicitações de gerenciamento, mas seu trabalho também é replicado localização geográfica. Quando houver uma interrupção em uma região, o Agendador do Azure Falha ao longo e garante que o trabalho é executado a partir de outro data center na região geográfica par.

Por exemplo, se você criou um trabalho no Centro Sul dos EUA, o Agendador do Azure replica automaticamente esse trabalho no Centro Norte dos EUA. Quando houver uma falha no Centro Sul dos EUA, o Agendador do Azure garante que o trabalho é executado a partir do Centro Norte dos EUA. 

![][1]

Como resultado, o Agendador do Azure garante que seus dados permaneçam dentro da região geográfica mais ampla mesma no caso de uma falha de Azure. Como resultado, você não precisa duplicar seu trabalho apenas para adicionar alta disponibilidade – o Agendador do Azure fornece recursos de alta disponibilidade para seus trabalhos automaticamente.

## <a name="azure-scheduler-reliability"></a>Agendador Azure confiabilidade

Agendador do Azure garante sua própria alta disponibilidade e leva uma abordagem diferente para trabalhos criados pelo usuário. Por exemplo, seu trabalho pode invocar um ponto de extremidade HTTP que não está disponível. Agendador do Azure ainda assim tenta executar seu trabalho com êxito, fornecendo opções alternativas de lidar com falha. Agendador do Azure faz isso de duas maneiras:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Política de repetir configurável via "retryPolicy"

Agendador do Azure permite que você configure uma política de repetição. Por padrão, se uma tarefa falhar, Agendador tenta o trabalho mais novamente quatro vezes, em intervalos de 30 segundos. Você pode configurar novamente esta política de Repetir para ser mais curtas (por exemplo, dez vezes, em intervalos de 30 segundos) ou menor (por exemplo, duas vezes, em intervalos diários.)

Como um exemplo de quando isso pode ajudar, você pode criar um trabalho que é executada uma vez por semana e invoca um ponto de extremidade HTTP. Se o ponto de extremidade HTTP for pressionada por algumas horas quando o seu trabalho é executado, não convém aguardar uma semana mais para o trabalho executar novamente, desde que exibem o mesmo a política padrão de repetição. Nesses casos, você pode reconfigurar a política padrão de repetição para repetir a cada três horas (por exemplo) em vez de cada 30 segundos.

Para saber como configurar uma diretiva de repetição, consulte [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Capacidade de configuração de ponto de extremidade alternativo via "errorAction"

Se o ponto de extremidade de destino para o trabalho do Agendador do Azure permanece inacessível, o Agendador do Azure volta para o ponto de extremidade de tratamento de erro alternativo após seguir sua política de repetição. Se um ponto de extremidade de tratamento de erro alternativo estiver configurado, o Agendador do Azure invoca-lo. Com um ponto de extremidade alternativo, seus próprios trabalhos são altamente disponíveis no caso de falha.

Como exemplo, no diagrama abaixo, o Agendador do Azure segue sua política de Repetir para acertar um serviço da web de Nova York. Depois que as tentativas falharem, ele verifica se há uma alternativa. Ele vai em frente e começa a fazer solicitações a alternativa com a mesma diretiva de repetição.

![][2]

Observe que a mesma diretiva de repetição se aplica a ação original e a ação de erro alternativo. Também é possível ter o tipo de ação da ação erro alternativo ser diferente do tipo de ação da ação principal. Por exemplo, embora a ação principal pode ser invocar um ponto de extremidade HTTP, a ação de erro em vez disso, pode ser uma fila do armazenamento, fila de barramento de serviço ou ação de tópico de barramento de serviço que faz log de erro.

Para saber como configurar um ponto de extremidade alternativo, consulte [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Consulte também

 [O que é o Agendador?](scheduler-intro.md)

 [Hierarquia de entidades, terminologia e conceitos de Agendador Azure](scheduler-concepts-terms.md)

 [Começar a usar o Agendador no portal do Azure](scheduler-get-started-portal.md)

 [Planos e cobranças no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência de API do Agendador REST Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador Azure](scheduler-powershell-reference.md)

 [Limites de Agendador Azure, padrões e códigos de erro](scheduler-limits-defaults-errors.md)

 [Autenticação de saída Agendador Azure](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
