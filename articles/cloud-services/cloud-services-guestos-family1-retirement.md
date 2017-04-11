<properties
   pageTitle="Família de SO convidado 1 aposentadoria Observe | Microsoft Azure"
   description="Fornece informações sobre quando a desativação do Azure convidado SO família 1 aconteceu e como determinar se você é afetados"
   services="cloud-services"
   documentationCenter="na"
   authors="raiye"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/24/2016"
   ms.author="raiye"/>



# <a name="guest-os-family-1-retirement-notice"></a>Aviso de desativação do convidado SO família 1

A desativação do sistema operacional família 1 primeiro foi lançada em 1º de junho de 2013.

**2 de setembro de 2014** O sistema operacional do Azure convidado (SO de convidado) família 1. x, que é baseado no sistema operacional Windows Server 2008, oficialmente foi retirada. Todas as tentativas de implantar novos serviços ou atualizar serviços existentes usando família 1 falhará com uma mensagem de erro informando que o sistema operacional de convidado família 1 foi descontinuado.

**3 de novembro de 2014** Suporte estendido para convidado SO família 1 encerrada e ela é totalmente descartada. Todos os serviços ainda na família 1 serão afetados. Nós pode parar desses serviços a qualquer momento. Não há nenhuma garantia de que seus serviços continuará a ser executado, a menos que você manualmente atualizá-los por conta própria.

Se você tiver perguntas adicionais, visite os [Fóruns de serviços de nuvem](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [contate o suporte Azure](https://azure.microsoft.com/support/options/).




## <a name="are-you-affected"></a>São afetados?

Seus serviços de nuvem são afetados se aplica-se qualquer um destes procedimentos:

1. Você tem um valor de "osFamily ="1"seja explicitamente especificada no arquivo ServiceConfiguration de seu serviço de nuvem.
2. Você não tem um valor para osFamily explicitamente especificada no arquivo de ServiceConfiguration de seu serviço de nuvem. Atualmente, o sistema usa o valor padrão de "1" neste caso.
3. O portal de clássico Azure lista o valor de família do sistema operacional convidado como "Windows Server 2008".

Para localizar qual dos seus serviços de nuvem estiver executando o qual família de sistema operacional, você pode executar o script abaixo no Azure PowerShell, embora primeiro você deve [Configurar o Azure PowerShell](../powershell-install-configure.md) . Para obter detalhes adicionais sobre o script, consulte [Azure convidado SO família 1 fim da vida útil: junho de 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Seus serviços de nuvem serão afetados por aposentadoria SO família 1 se a coluna osFamily na saída do script estiver vazia ou contiver um "1".

## <a name="recommendations-if-you-are-affected"></a>Se você for afetado de recomendações

Recomendamos que você migrar suas funções de serviço de nuvem para uma das famílias de sistema operacional convidado com suporte:

**SO convidado família 4. x** -Windows Server 2012 R2 *(recomendado)*

1. Certifique-se de que seu aplicativo está usando o SDK 2.1 ou posterior com o .NET framework 4.0, 4,5 ou 4.5.1.
2. Defina o atributo osFamily como "4" no arquivo ServiceConfiguration e reimplantar seu serviço de nuvem.


**SO convidado família 3. x** -Windows Server 2012

1. Certifique-se de que seu aplicativo está usando o SDK 1,8 ou posterior com o .NET framework 4.0 ou 4,5.
2. Defina o atributo osFamily como "3" no arquivo ServiceConfiguration e reimplantar seu serviço de nuvem.


**SO convidado família 2. x** -Windows Server 2008 R2

1. Certifique-se de que seu aplicativo está usando o SDK 1.3 e acima com .NET framework 3.5 ou 4.0.
2. Defina o atributo osFamily para "2" no arquivo ServiceConfiguration e reimplantar seu serviço de nuvem.


## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Suporte estendido para convidado SO família 1 encerrada 3 de novembro de 2014
Não há suporte para serviços de nuvem em família SO convidado 1. Faça a migração desativar família 1 assim que possível para evitar interrupção do serviço.  

## <a name="next-steps"></a>Próximas etapas
Examine as últimas [lançamentos de SO convidado](cloud-services-guestos-update-matrix.md).
