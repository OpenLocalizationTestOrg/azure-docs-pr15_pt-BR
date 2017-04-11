
<properties
   pageTitle="Atualização do aplicativo: atualizar os parâmetros | Microsoft Azure"
   description="Descreve os parâmetros relacionados ao atualizar um aplicativo de serviço tecidos, incluindo verificações de integridade para executar e políticas para desfazer automaticamente a atualização."
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



# <a name="application-upgrade-parameters"></a>Parâmetros de atualização do aplicativo

Este artigo descreve os diversos parâmetros que se aplicam durante a atualização de um aplicativo do Azure serviço tecidos. Os parâmetros incluem o nome e a versão do aplicativo. Eles estão botões que controlam a tempos limite e verificações de integridade que são aplicadas durante a atualização, e eles especificam as políticas que devem ser aplicadas quando a falha de uma atualização.


<br>

| Parâmetro | Descrição |
| --- | --- |
| ApplicationName | Nome do aplicativo que está sendo atualizado. Exemplos: tecidos: / VisualObjects, tecidos: / ClusterMonitor  |
| TargetApplicationTypeVersion | Tipo de versão do aplicativo que os destinos de atualização. |
| FailureAction | A ação executada pelo serviço tecidos quando a atualização falhará. O aplicativo poderá ser revertido para a versão de pré-lançamento de atualização (reversão) ou a atualização pode ser interrompida no domínio de atualização atual. No último caso, o modo de atualização também será alterado para Manual. Valores permitidos são Manual e reversão. |
| HealthCheckWaitDurationSec | O tempo de espera (em segundos) após a atualização no domínio atualização antes de serviço tecidos avalia a integridade do aplicativo. Essa duração também pode ser considerada como o tempo de que um aplicativo deve estar em execução antes que possa ser considerado eficaz. Se a verificação de integridade passa, o processo de atualização passa para o próximo domínio de atualização.  Se a verificação de integridade falhar, o serviço tecidos aguarda um intervalo (o UpgradeHealthCheckInterval) antes de tentar a verificação de integridade novamente até que o HealthCheckRetryTimeout seja atingido. O padrão e o valor recomendado é 0 segundos. |
| HealthCheckRetryTimeoutSec | A duração (em segundos) tecidos desse serviço continua a executar avaliação de integridade antes de declarar a atualização como falha. O padrão é 600 segundos. Essa duração inicia após HealthCheckWaitDuration é alcançado. Dentro este HealthCheckRetryTimeout, serviço tecidos podem executar verificações de integridade de vários da integridade aplicativo. O valor padrão é de 10 minutos e deve ser personalizado adequadamente para o seu aplicativo. |
| HealthCheckStableDurationSec | A duração (em segundos) para verificar se o aplicativo é estável antes de mover para o próximo domínio de atualização ou concluir a atualização. Essa duração de espera é usada para impedir alterações não detectadas de integridade direita após a verificação de integridade é executada. O valor padrão é 120 segundos e deve ser personalizado adequadamente para o seu aplicativo. |
| UpgradeDomainTimeoutSec | Tempo máximo (em segundos) para atualizar um único domínio de atualização. Se o tempo limite for atingido, a atualização interrompe e prossegue com base na configuração de para UpgradeFailureAction. O valor padrão é nunca (infinito) e devem ser personalizadas adequadamente para o seu aplicativo. |
| UpgradeTimeout | Um tempo limite (em segundos) que aplica-se para a atualização inteira. Se o tempo limite for atingido, as paradas de atualização e UpgradeFailureAction é disparada. O valor padrão é nunca (infinito) e devem ser personalizadas adequadamente para o seu aplicativo. |
| UpgradeHealthCheckInterval | A frequência que o status de saúde está marcado. Este parâmetro é especificado na seção de ClusterManager do _cluster_ _manifesto_e não estiver especificado como parte do cmdlet atualização. O valor padrão é 60 segundos.  |






<br>
## <a name="service-health-evaluation-during-application-upgrade"></a>Avaliação de integridade de serviço durante a atualização do aplicativo

<br>
Os critérios de avaliação de integridade são opcionais. Se os critérios de avaliação de integridade não forem especificados quando uma atualização for iniciado, o serviço tecidos usa as diretivas de integridade de aplicativo especificadas na ApplicationManifest.xml da instância do aplicativo.


<br>

| Parâmetro | Descrição |
| --- | --- |
| ConsiderWarningAsError | Valor padrão é falso. Trate os eventos de integridade de aviso para o aplicativo como erros ao avaliar a integridade do aplicativo durante a atualização. Por padrão, o serviço tecidos não avaliar eventos de integridade de aviso para ser falhas (erros), para que a atualização possa continuar mesmo se há eventos de aviso.   |
| MaxPercentUnhealthyDeployedApplications | Padrão e valor recomendado é 0. Especifique o número máximo de aplicativos implantados (consulte a [seção integridade](service-fabric-health-introduction.md)) que podem ser não íntegra antes que o aplicativo é considerado não íntegro e falha a atualização. Este parâmetro define a integridade do aplicativo no nó e ajuda a detectar problemas durante a atualização. Normalmente, as réplicas do aplicativo obtém balanceamento de carga para o outro nó, que permite que o aplicativo aparecem íntegra, permitindo a atualização prosseguir. Especificando uma integridade MaxPercentUnhealthyDeployedApplications estrita, serviço tecidos pode detectar um problema com o pacote de aplicativo rapidamente e ajudar a produzir reprovado rápida atualização. |
| MaxPercentUnhealthyServices | Padrão e valor recomendado é 0. Especifique o número máximo de serviços na instância do aplicativo que pode ser não íntegra antes que o aplicativo é considerado não íntegro e falha a atualização. |
| MaxPercentUnhealthyPartitionsPerService | Padrão e valor recomendado é 0. Especifique o número máximo de partições em um serviço que pode ser não íntegro antes do serviço é considerado não íntegro. |
| MaxPercentUnhealthyReplicasPerPartition | Padrão e valor recomendado é 0. Especifica o número máximo de réplicas em partição que pode ser não íntegra antes que a partição é considerada não íntegra. |
| UpgradeReplicaSetCheckTimeout | **Serviço de estado**– dentro de um único domínio de atualização, serviço tecidos tenta garantir que instâncias adicionais do serviço estejam disponíveis. Se a contagem de instância de destino for mais de um, o serviço tecidos aguarda mais de uma instância esteja disponível, até um valor de tempo limite máximo. O tempo limite é especificado usando a propriedade UpgradeReplicaSetCheckTimeout. Se o tempo limite expirar, o serviço tecidos prossegue com a atualização, independentemente do número de instâncias do serviço. Se a contagem de instância de destino, tecidos do serviço não espere e prossegue imediatamente com a atualização. **Serviço de estado**– em um único domínio de atualização, serviço tecidos tenta Certifique-se de que o conjunto de réplica tem um quorum. Serviço tecidos aguarda um quorum esteja disponível, até um valor de tempo limite máximo (especificado pela propriedade UpgradeReplicaSetCheckTimeout). Se o tempo limite expirar, o serviço tecidos prossegue com a atualização, independentemente de quorum. Esta configuração está definido como nunca (infinito) quando avance e 900 segundos quando revertendo. |
| /Forcerestart | Se você atualizar um pacote de dados ou configuração sem atualizar o código do serviço, o serviço é reiniciado somente se a propriedade /forcerestart é definida como true. Quando a atualização for concluída, o serviço tecidos notifica o serviço que um novo pacote de configuração ou pacote de dados está disponível. O serviço é responsável por aplicar as alterações. Se necessário, pode reiniciar o serviço em si. |



<br>
<br>
Os critérios de MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService e MaxPercentUnhealthyReplicasPerPartition podem ser especificados por tipo de serviço para uma instância do aplicativo. Definir esses parâmetros por serviço permite que um aplicativo contêm tipos diferentes serviços com políticas de avaliação diferentes. Por exemplo, um tipo de serviço de gateway sem estado pode ter um MaxPercentUnhealthyPartitionsPerService que é diferente de um tipo de serviço do mecanismo de estado para uma instância de aplicativo específico.

## <a name="next-steps"></a>Próximas etapas

[Atualizar o seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) conduz você por uma atualização do aplicativo usando o Visual Studio.

[Atualizando seu aplicativo usando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) conduz você por uma atualização do aplicativo usando o PowerShell.

Torne suas atualizações de aplicativo compatíveis aprender como usar a [Serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada durante a atualização de seu aplicativo fazendo referência a [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigi problemas comuns em atualizações de aplicativo consultando as etapas na [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).
 
