<properties
 pageTitle="Planos e cobranças no Agendador do Azure"
 description="Planos e cobranças no Agendador do Azure"
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

# <a name="plans-and-billing-in-azure-scheduler"></a>Planos e cobranças no Agendador do Azure

## <a name="job-collection-plans"></a>Planos de conjunto de trabalho

Coleções de trabalho são a entidade faturável no Agendador do Azure. Conjuntos de trabalho contiverem um número de trabalhos e vêm em três planos – livre, Standard e Premium – descritos abaixo.

|**Plano de conjunto de trabalho**|**Número máximo de trabalhos por conjunto de trabalho**|**Max recorrência**|**Conjuntos de trabalho máximo por assinatura**|**Limites**|
|:---|:---|:---|:---|:---|
|**Livre**|5 trabalhos por conjunto de trabalho|Uma vez por hora. Não é possível executar trabalhos mais frequência do que uma vez por hora|Uma assinatura é permitida até 1 coleção de trabalho gratuitos|Não é possível usar o [objeto de autorização de saída HTTP](scheduler-outbound-authentication.md)
|**Padrão**|50 trabalhos por conjunto de trabalho|Uma vez por minuto. Não é possível executar trabalhos mais frequência do que uma vez um minuto|Uma assinatura é permitida até 100 conjuntos de trabalho padrão|Acesso ao conjunto completo de recursos do Agendador|
|**P10 Premium**|50 trabalhos por conjunto de trabalho|Uma vez por minuto. Não é possível executar trabalhos mais frequência do que uma vez um minuto|Uma assinatura é permitida até 10.000 conjuntos de trabalho P10 Premium. <a href="mailto:wapteams@microsoft.com">Entre em contato conosco</a> para obter mais informações.|Acesso ao conjunto completo de recursos do Agendador|
|**P20 Premium**|trabalhos de 1000 por conjunto de trabalho|Uma vez por minuto. Não é possível executar trabalhos mais frequência do que uma vez um minuto|Uma assinatura é permitida até 10.000 conjuntos de trabalho P20 Premium. <a href="mailto:wapteams@microsoft.com">Entre em contato conosco</a> para obter mais informações.|Acesso ao conjunto completo de recursos do Agendador|

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Atualizações e Downgrades dos planos de conjunto de trabalho

Você pode atualizar ou fazer downgrade um plano de conjunto de trabalho a qualquer momento entre os planos livre, Standard e Premium. No entanto, ao fazer downgrade para um conjunto de trabalho gratuito, o downgrade pode falhar por um dos seguintes motivos:

- Uma coleção de trabalho gratuito já existe na assinatura
- Um trabalho no conjunto de trabalho possui uma recorrência maior que o permitido para trabalhos em conjuntos de trabalho gratuito. A recorrência máxima permitida em um conjunto de trabalho gratuito é uma vez por hora
- Há mais de 5 trabalhos no conjunto de trabalho
- Um trabalho no conjunto de trabalho possui uma ação de HTTP ou HTTPS que usa um [objeto de autorização de saída HTTP](scheduler-outbound-authentication.md)

## <a name="billing-and-azure-plans"></a>Planos do Azure e cobrança

Assinaturas não são cobradas gratuitamente conjuntos de trabalho. Se você tiver mais de 100 coleções de trabalho padrão (10 unidades cobranças padrão), ele é um negócio melhor ter todos os conjuntos de trabalho no plano de premium.

Se você tiver uma coleção de trabalho padrão e uma coleção de trabalho premium, você é cobrados uma cobrança unidade _e_ um premium cobrança unidade padrão. As listas de serviço do Agendador com base no número de conjuntos de trabalho ativo que são definidas como padrão ou premium; Isso é explicado mais próximas duas seções.

## <a name="standard-billable-units"></a>Unidades de faturáveis padrão

Uma unidade faturável padrão pode incluir até 10 conjuntos de trabalho padrão. Como um conjunto padrão de trabalho pode ter até 50 trabalhos por conjunto de trabalho, uma unidade de cobrança padrão permite uma assinatura tenha até 500 trabalhos – até quase milhões de 22 execuções de trabalho por mês.

Se você tiver entre 1 e 10 coleções de trabalho padrão, você será cobrado por 1 unidade padrão de cobrança. Se você tiver entre 11 e 20 conjuntos de trabalho padrão, você será cobrado por 2 unidades de cobranças padrão. Se você tiver entre 21 e conjuntos de 30 trabalho padrão, você será cobrado por 3 unidades padrão de cobranças e assim por diante.

## <a name="p10-premium-billable-units"></a>P10 Premium faturáveis unidades

Uma unidade de faturáveis premium P10 pode incluir até 10.000 conjuntos de trabalho de premium P10. Como um conjunto de trabalho P10 premium pode ter até 50 trabalhos por conjunto de trabalho, uma unidade de cobrança premium permite uma assinatura tenha até 500.000 trabalhos – até bilhões de 22 quase execuções de trabalho por mês.

Se você tiver entre 1 e 10.000 conjuntos de trabalho premium, você será cobrado por 1 unidade de cobrança do premium P10. Se você tiver entre 10,001 e conjuntos de trabalho de 20.000 premium, você será cobrado por 2 P10 unidades de cobrança de premium, etc.

Portanto, conjuntos de trabalho P10 premium têm a mesma funcionalidade que as coleções de arquivos de trabalho padrão, mas oferecem uma quebra de preço caso seu aplicativo requer muita conjuntos de trabalho.

## <a name="p20-premium-billable-units"></a>P20 Premium faturáveis unidades

Uma unidade de faturáveis premium P20 pode incluir até 5.000 conjuntos de trabalho de premium P20. Como um conjunto de trabalho P20 premium pode ter até 1.000 trabalhos por conjunto de trabalho, uma unidade de cobrança premium permite uma assinatura tenha até 5.000.000 trabalhos – até quase 220 bilhões de execuções de trabalho por mês.

Conjuntos de trabalho P20 premium fornece os mesmos recursos como coleções de trabalho do P10 premium, mas também suporta maior número trabalhos por conjunto de trabalho e um número de total maior de trabalhos gerais que premium P10 permitindo que você tenha mais escalabilidade.

## <a name="billing-and-active-status"></a>Status de cobrança e ativo

Conjuntos de trabalho estão sempre ativos, a menos que sua assinatura inteira teve em alguns temporário estado desabilitado devido a problemas de cobrança. A única maneira para garantir que um conjunto de trabalho não é cobrado é seja defini-la para o plano de _livre_ ou excluir o conjunto de trabalho.

Embora você pode desabilitar todos os trabalhos em um conjunto de trabalho em uma única operação, ela não altera o status de cobrança da coleção de trabalho – o conjunto de trabalho serão _ainda_ serão cobrados. Da mesma forma, conjuntos de trabalho vazia são considerados ativos e serão cobrados.

## <a name="pricing"></a>Preços

Para obter detalhes sobre preços, consulte [Agendador de preços](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Consulte também


 [O que é o Agendador?](scheduler-intro.md)

 [Hierarquia de entidades, terminologia e conceitos de Agendador Azure](scheduler-concepts-terms.md)

 [Começar a usar o Agendador no portal do Azure](scheduler-get-started-portal.md)

 [Referência de API do Agendador REST Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador Azure](scheduler-powershell-reference.md)

 [Azure Agendador alta disponibilidade e confiabilidade](scheduler-high-availability-reliability.md)

 [Limites de Agendador Azure, padrões e códigos de erro](scheduler-limits-defaults-errors.md)

 [Autenticação de saída Agendador Azure](scheduler-outbound-authentication.md)
