<properties
   pageTitle="Atualizações de aplicativo de solução de problemas | Microsoft Azure"
   description="Este artigo aborda alguns problemas comuns em torno de atualização de um aplicativo de serviço tecidos e como resolvê-los."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="troubleshoot-application-upgrades"></a>Solucionar problemas de atualizações de aplicativo

Este artigo aborda alguns dos problemas comuns em torno de atualização de um aplicativo de estrutura de serviço do Azure e como resolvê-los.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Solucionar problemas de uma atualização do aplicativo que falhou

Quando uma atualização falha, o resultado do comando **Get-ServiceFabricApplicationUpgrade** contém informações adicionais para a falha de depuração.  A lista a seguir especifica como as informações adicionais podem ser usadas:

1. Identifique o tipo de falha.
2. Identifique o motivo de falha.
3. Isole um ou mais componentes de falha para investigação posterior.

Essas informações estarão disponíveis quando serviço tecidos detecta a falha independentemente do fato a **FailureAction** ser reverter ou suspender a atualização.

### <a name="identify-the-failure-type"></a>Identificar o tipo de falha

Na saída do **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica o carimbo de hora (no UTC) em que uma falha de atualização foi detectada pelo serviço tecidos e **FailureAction** foi acionado. **FailureReason** identifica uma das três causas possíveis de alto nível da falha:

1. UpgradeDomainTimeout - indica que um determinado domínio atualização demorou demais para ser concluída e **UpgradeDomainTimeout** expirou.
2. OverallUpgradeTimeout - indica que a atualização geral demorou demais para ser concluída e **UpgradeTimeout** expirou.
3. Verificação de integridade - indica que após a atualização de um domínio de atualização, o aplicativo permaneceu não íntegro de acordo com as diretivas de integridade especificada e **HealthCheckRetryTimeout** expirou.

Essas entradas somente aparecem na saída quando a atualização falhará e inicia revertendo. Informações adicionais são exibidas dependendo do tipo da falha.

### <a name="investigate-upgrade-timeouts"></a>Investigar tempos limite de atualização

Atualize tempo limite falhas são geralmente causadas por problemas de disponibilidade do serviço. A saída seguindo este parágrafo é típica de atualizações onde réplicas de serviço ou instâncias falharem ao iniciar a nova versão do código. O campo **UpgradeDomainProgressAtFailure** captura um instantâneo de qualquer trabalho de atualização pendente no momento da falha.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

Neste exemplo, a atualização falha no domínio de atualização *MYUD1* e duas partições (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) foram preso. As partições foram preso porque o runtime não conseguiu colocar réplicas principais (*WaitForPrimaryPlacement*) em nós de destino *Node1* e *Nó4*.

O comando **Get-ServiceFabricNode** pode ser usado para verificar se esses dois nós estão no domínio de atualização *MYUD1*. O *UpgradePhase* diz *PostUpgradeSafetyCheck*, que significa que essas verificações de segurança estão ocorrendo depois de todos os nós no domínio de atualização concluída a atualização. Todas essas informações aponta para um possível problema com a nova versão do código do aplicativo. Os maioria dos problemas comuns são erros de serviço em aberto ou promoção para caminhos de código principal.

Um *UpgradePhase* de *PreUpgradeSafetyCheck* significa havia problemas Preparando o domínio de atualização antes de ela foi executada. Nesse caso, os problemas mais comuns são erros de serviço no rebaixamento de caminhos de código primário ou fechar.

O atual **UpgradeState** é *RollingBackCompleted*, portanto, a atualização original deve ter sido realizada com uma reversão **FailureAction**, que automaticamente revertidas a atualização em caso de falha. Se a atualização original foi realizada com um manual **FailureAction**, em seguida, a atualização seria em vez disso, ser em um estado suspenso para permitir que o live depuração do aplicativo.

### <a name="investigate-health-check-failures"></a>Investigar falhas de verificação de integridade

Falhas de verificação de integridade podem ser acionadas por vários problemas que podem ocorrer após terminar de todos os nós em um domínio de atualização, atualizando e passando todas as verificações de segurança. A saída seguindo este parágrafo é típica de uma falha de atualização devido a verificações de integridade falhou. O campo **UnhealthyEvaluations** captura um instantâneo de verificações de integridade que falhou no momento da atualização de acordo com a [política de integridade](service-fabric-health-introduction.md)de especificada.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Primeiro investigar falhas de verificação de integridade exige uma compreensão do modelo de integridade do serviço tecidos. Mas mesmo sem tal uma compreensão profundamente, podemos ver que dois serviços são não íntegros: *tecidos: / DemoApp/Svc3* e *tecidos: / DemoApp/Svc2*, juntamente com os relatórios de integridade de erro ("InjectedFault" neste caso). Neste exemplo, os serviços de duas de quatro são não íntegros, que está abaixo de destino padrão de 0% não íntegra (*MaxPercentUnhealthyServices*).

A atualização foi suspenso após falha especificando uma **FailureAction** do manual ao iniciar a atualização. Esse modo permite investigar sistema live no estado de falha antes de realizar qualquer outra ação.

### <a name="recover-from-a-suspended-upgrade"></a>Recuperar uma atualização suspensa

Com uma reversão **FailureAction**, não há nenhuma recuperação necessária, já que a atualização reverte automaticamente após falha. Com um manual **FailureAction**, há várias opções de recuperação:

1. Acionar uma reversão manualmente
2. Prossiga o resto da atualização manualmente
3. Retomar a atualização monitorar

O comando **Start-ServiceFabricApplicationRollback** pode ser usado a qualquer momento para iniciar Revertendo o aplicativo. Depois que o comando retornar com êxito, a solicitação de reversão tiver sido registrada no sistema e inicia logo depois.

O comando de **Currículo-ServiceFabricApplicationUpgrade** pode ser usado para percorrer o resto da atualização manualmente, um domínio de atualização por vez. Nesse modo, verificações de segurança somente são executadas pelo sistema. Não há mais verificações de integridade são executadas. Este comando só pode ser usado quando o *UpgradeState* mostra *RollingForwardPending*, que significa que o domínio de atualização atual terminou a atualização, mas próximo não iniciado (pendente).

O comando de **Atualização-ServiceFabricApplicationUpgrade** pode ser usado para retomar a atualização monitorar com os dois segurança e verificações de integridade está sendo executada.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

A atualização continua a partir o domínio de atualização onde ele foi suspenso última e use a mesma atualizar parâmetros e diretivas de integridade como antes. Se necessário, qualquer um dos parâmetros de atualização e diretivas de integridade mostradas na saída anterior pode ser alterado no mesmo comando, quando a atualização currículos. Neste exemplo, a atualização foi reiniciada no modo de monitorados, com os parâmetros e as diretivas de integridade inalteradas.

## <a name="further-troubleshooting"></a>Solução de problemas

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Serviço tecidos não está acompanhando as diretivas de integridade especificada

Possível causa 1:

Serviço tecidos converte todas as porcentagens em números reais de entidades (por exemplo, réplicas, partições e serviços) para avaliação de integridade e sempre Arredonda para cima para entidades inteiras. Por exemplo, se o máximo _MaxPercentUnhealthyReplicasPerPartition_ é 21% e há cinco réplicas, serviço tecidos permite até duas réplicas não íntegras (que is,'Math.Ceiling (5\*0.21)). Portanto, diretivas de integridade devem ser definidas adequadamente.

Possível causa 2:

Diretivas de integridade são especificadas em termos de porcentagens de total de serviços e instâncias de serviço não específico. Por exemplo, antes de uma atualização, se um aplicativo tem quatro instâncias A, B, C e D, de serviço onde serviço D é não íntegro, mas com pouco impacto para o aplicativo. Queremos ignorar o serviço conhecido não íntegro D durante a atualização e definir o parâmetro *MaxPercentUnhealthyServices* seja 25%, considerando que somente A, B e C precisa estar íntegra.

No entanto, durante a atualização, D pode ficar eficaz, enquanto C fica não íntegra. A atualização ainda conseguirão porque apenas 25% dos serviços são não íntegra. No entanto, ele pode resultar em erros inesperados devido a C sendo inesperadamente não íntegra em vez de D. Nessa situação, D deve ser modelado como um tipo de serviço diferente da, B e C. Como as diretivas de integridade são especificadas por tipo de serviço, limites de porcentagem não íntegra diferentes podem ser aplicados a diferentes serviços. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Eu não especificou uma diretiva de integridade para atualização do aplicativo, mas a atualização ainda falha para alguns interagindo com dados que eu nunca especificado

Quando as diretivas de integridade não são fornecidas para a solicitação de atualização, elas são tiradas da *ApplicationManifest.xml* da versão atual do aplicativo. Por exemplo, se você estiver atualizando o aplicativo X da versão 1.0 para a versão 2.0, diretivas de integridade de aplicativo especificado para na versão 1.0 são usadas. Se uma diretiva de integridade diferente deve ser usada para a atualização, a política precisa ser especificado como parte da chamada de API de atualização do aplicativo. As políticas especificadas como parte da chamada API se aplicam somente durante a atualização. Após a atualização estiver concluída, as políticas especificadas na *ApplicationManifest.xml* são usadas.

### <a name="incorrect-time-outs-are-specified"></a>Interagindo com dados incorretos é especificados

Você pode ter se perguntou sobre o que acontece quando tempos limite é definida inconsistente. Por exemplo, você pode ter um *UpgradeTimeout* que é menor que o *UpgradeDomainTimeout*. A resposta é que um erro é retornado. Erros são retornados se a *UpgradeDomainTimeout* for menor do que a soma de *HealthCheckWaitDuration* e *HealthCheckRetryTimeout*, ou se *UpgradeDomainTimeout* for menor do que a soma de *HealthCheckWaitDuration* e *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Minhas atualizações estão demorando muito

O tempo para uma atualização ser concluída depende as verificações de integridade e tempos limite especificado. Verificações de integridade e tempos limite depende quanto tempo leva para copiar, implantar e coloque o aplicativo. Sendo muito rigorosas com tempos limite pode significam mais falhas atualizações, portanto, é recomendável conservadoramente começando com tempos limite mais longa.

Aqui está um atualizador rápido sobre como os tempos limite interagem com os tempos de atualização:

Atualizações para um domínio de atualização não pode ser concluída mais rápido que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Falha de atualização não pode ocorrer mais rápido que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

O horário de atualização para um domínio de atualização é limitado pelo *UpgradeDomainTimeout*.  Se *HealthCheckRetryTimeout* e *HealthCheckStableDuration* estão diferente de zero e a integridade do aplicativo continua migrando trocadas, em seguida, a atualização eventualmente expira em *UpgradeDomainTimeout*. *UpgradeDomainTimeout* inicia a contagem regressiva após a atualização para o domínio de atualização atual começa.

## <a name="next-steps"></a>Próximas etapas

[Atualizar o seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) conduz você por uma atualização do aplicativo usando o Visual Studio.

[Atualizando seu aplicativo usando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) conduz você por uma atualização do aplicativo usando o PowerShell.

Controle como o seu aplicativo atualizações usando [Parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprender como usar a [Serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada durante a atualização de seu aplicativo fazendo referência a [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigi problemas comuns em atualizações de aplicativo consultando as etapas na [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).
 
