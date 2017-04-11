<properties
    pageTitle="Automatizar o gerenciamento de aplicativo de serviço tecidos usando o PowerShell | Microsoft Azure"
    description="Implantar, atualizar, testar e remover aplicativos de serviço tecidos usando o PowerShell."
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="ryanwi"/>

# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatizar o ciclo de vida do aplicativo usando o PowerShell

Muitos aspectos do ciclo de [vida de aplicativo de serviço tecidos](service-fabric-application-lifecycle.md) podem ser automatizados.  Este artigo mostra como usar o PowerShell para automatizar tarefas comuns de implantação, atualização, removendo e testar aplicativos do Azure serviço tecidos.  Gerenciado e APIs de HTTP para gerenciamento de aplicativo também estão disponíveis. Consulte [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) para obter mais informações.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de passar às tarefas no artigo, certifique-se a:

+ Familiarize-se com os conceitos de serviço tecidos descritos na [Visão geral técnica do serviço tecidos](service-fabric-technical-overview.md).
+ [Instalar o tempo de execução, SDK e ferramentas](service-fabric-get-started.md), que também instala o módulo do PowerShell **ServiceFabric** .
+ [Execução de scripts do PowerShell habilitar](service-fabric-get-started.md#enable-powershell-script-execution).
+ Inicie um cluster local.  Inicie uma nova janela do PowerShell como administrador e, em seguida, execute o script de instalação do cluster da pasta SDK:`& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Antes de executar qualquer comando do PowerShell neste artigo, primeiro se conecte ao cluster tecidos serviço local usando [**ServiceFabricCluster conectar**](https://msdn.microsoft.com/library/azure/mt125938.aspx):`Connect-ServiceFabricCluster localhost:19000`
+ As tarefas a seguir exigem um pacote de aplicativo v1 para implantar e um pacote de aplicativo v2 para atualização. Baixe o [aplicativo de exemplo **WordCount** ](http://aka.ms/servicefabricsamples) (localizada nos exemplos Introdução). Criar e empacotar o aplicativo no Visual Studio (com o botão direito em **WordCount** em Solution Explorer e selecione **pacote**). Copie o pacote de v1 no `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` para `C:\Temp\WordCount`. Cópia `C:\Temp\WordCount` para `C:\Temp\WordCountV2`, criação do pacote de aplicativo v2 para atualização. Abrir `C:\Temp\WordCountV2\ApplicationManifest.xml` em um editor de texto. No elemento **ApplicationManifest** , altere o atributo de **ApplicationTypeVersion** de "1.0.0" para "2.0.0" para atualizar o número de versão do aplicativo. Salve o arquivo de ApplicationManifest.xml foi alterado.

## <a name="task-deploy-a-service-fabric-application"></a>Tarefa: Implantar um aplicativo de serviço tecidos

Depois de criado e empacotado o aplicativo (ou baixado o pacote de aplicativo), você pode implantar o aplicativo em um cluster de serviço tecidos local. Implantação envolve carregando o pacote de aplicativos, registrar o tipo de aplicativo e criar a instância do aplicativo. Use as instruções nesta seção para implantar um novo aplicativo a um cluster.

### <a name="step-1-upload-the-application-package"></a>Etapa 1: Carregar o pacote de aplicativo
Carregar o pacote de aplicativos para o repositório de imagem coloca em um local acessível para os componentes de serviço tecidos internos.  O pacote de aplicativo contém a configuração necessárias de manifesto do aplicativo, manifestos de serviço e código e o pacote de dados para criar o aplicativo e instâncias de serviço.  O comando [**Copiar ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) carrega o pacote. Por exemplo:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Etapa 2: Registrar o tipo de aplicativo
Registrar o pacote de aplicativo torna o tipo de aplicativo e versão declarados no manifesto do aplicativo disponível para uso. O sistema lê o pacote carregado na etapa 1, verifique se o pacote (equivalente a execução [**ServiceFabricApplicationPackage de teste**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localmente), o conteúdo do pacote de processo e copie o pacote processado para um local de sistema interno.  Execute o cmdlet [**ServiceFabricApplicationType de registro**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCount
```
Para ver todos os tipos de aplicativo registrados no cluster, execute o cmdlet [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) :

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Etapa 3: Criar a instância do aplicativo
Um aplicativo pode ser instanciado usando qualquer versão do tipo de aplicativo que foi registrada com êxito usando o comando [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) . O nome de cada aplicativo é declarado no tempo de implantação e deve começar com o **tecidos:** esquema e ser exclusivo para cada instância do aplicativo. O nome do tipo de aplicativo e a versão do tipo de aplicativo são declarados no arquivo **ApplicationManifest.xml** do pacote de aplicativo. Se algum serviço padrão foram definido no manifesto do aplicativo do tipo de aplicativo de destino, aqueles são criadas neste momento.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

O comando [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) lista todas as instâncias de aplicativo que foram criadas com êxito, juntamente com seus status geral. O comando [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) lista todas as instâncias de serviço que foram criadas com êxito dentro de uma instância de determinado aplicativo. Serviços de padrão (se houver) são listados.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Tarefa: Atualizar um aplicativo de serviço tecidos
Você pode atualizar um aplicativo de serviço tecidos implantado anteriormente com um pacote de aplicativo atualizado. Esta tarefa atualiza o aplicativo WordCount que foi implantado na "tarefa: implantar um aplicativo de serviço tecidos." Para mais informações, leia por meio de [atualização do aplicativo de serviço tecidos](service-fabric-application-upgrade.md) .

Para manter as coisas simples para este exemplo, apenas o número de versão do aplicativo foi atualizado no pacote do aplicativo WordCountV2 criado nos pré-requisitos. Um cenário mais realista envolva atualizando seus arquivos de código, configuração ou dados de serviço e, em seguida, recriar e empacotamento do aplicativo com números de versão atualizada.  

### <a name="step-1-upload-the-updated-application-package"></a>Etapa 1: Carregar o pacote de aplicativo atualizado
O aplicativo de v1 WordCount está pronto para ser atualizado. Se você abrir uma janela do PowerShell como administrador e tipo [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), você verá que essa versão 1.0.0 do tipo de aplicativo WordCount é implantado.  

Agora, copie o pacote de aplicativo atualizado para o armazenamento de imagens de serviço tecidos (onde os pacotes do aplicativo são armazenados pelo serviço tecidos). O parâmetro **ApplicationPackagePathInImageStore** informa tecidos de serviço onde ele pode localizar o pacote de aplicativos. O comando a seguir copia o pacote de aplicativo para **WordCountV2** na loja de imagem:  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Etapa 2: Registrar o tipo de aplicativo atualizado
A próxima etapa é registrar a nova versão do aplicativo com estrutura de serviço, que podem ser executadas usando o cmdlet [**ServiceFabricApplicationType de registro**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Etapa 3: Inicie a atualização
Vários critérios de integridade, tempos limite e atualização parâmetros podem ser aplicados às atualizações de aplicativo. Leia os documentos de [parâmetros de atualização do aplicativo](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para saber mais. Todos os serviços e instâncias devem ser _eficaz,_ após a atualização.  Defina o **HealthCheckStableDuration** para 60 segundos (de forma que os serviços são eficaz, pelo menos 20 segundos antes que a atualização prossiga para o próximo domínio de atualização).  Também defina o **UpgradeDomainTimeout** 1200 segundos e o **UpgradeTimeout** para 3000 segundos. Finalmente, defina a **UpgradeFailureAction** a **reversão**, que solicita que serviço tecidos reverte o aplicativo para a versão anterior se falhas forem encontradas durante a atualização.

Agora você pode começar a atualização do aplicativo usando o cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Observe que o nome do aplicativo é a mesma que o nome do aplicativo v1.0.0 implantado anteriormente (tecidos: / WordCount). Estrutura de serviço usa esse nome para identificar qual aplicativo está obtendo atualizado. Se você definir os tempos limite seja muito pequeno, talvez você encontre uma mensagem de falha de tempo limite afirmando o problema. Consulte [Solucionar problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md)ou aumentar os tempos limite.

### <a name="step-4-check-upgrade-progress"></a>Etapa 4: O progresso de atualização de seleção
Você pode monitorar o andamento de atualização de aplicativo usando o [Serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md)ou usando o cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) :

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Em poucos minutos, o cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) mostra que todos os domínios de atualização foram atualizados (concluído).

## <a name="task-test-a-service-fabric-application"></a>Tarefa: Testar um aplicativo de serviço tecidos

Para escrever serviços de alta qualidade, os desenvolvedores precisam ser capaz de induzi falhas de infraestrutura confiável para testar a estabilidade dos seus serviços. Estrutura de serviço permite que os desenvolvedores induzem ações de falhas e testar serviços na presença de falhas usando os cenários de teste caos e failover.  Leia a [Visão geral de capacidade de teste](service-fabric-testability-overview.md) para obter informações adicionais.

### <a name="step-1-run-the-chaos-test-scenario"></a>Etapa 1: Executar o cenário de teste caos
O cenário de teste caos gera falhas em todo o cluster tecidos de serviço. O cenário compacta falhas geralmente vistas nos meses ou anos para algumas horas. A combinação de falhas intercaladas com uma taxa de falhas alta localiza ocorrências de canto que caso contrário poderiam ser perdidas. O exemplo a seguir executa o cenário de teste caos para 60 minutos.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Etapa 2: Executar o cenário de teste de failover
O failover uma partição de serviço específico em vez de todo o cluster de destinos de cenário de teste e deixa outros serviços afetados. O cenário itera em uma sequência de simulada falhas na validação de serviço enquanto executa sua lógica comercial. Uma falha na validação de serviço indica um problema que precisa de mais investigação. O teste de failover induz falhas apenas uma vez, em vez de cenário do teste caos, que pode induzir várias falhas. O exemplo a seguir executa o teste de failover para 60 minutos a estrutura: / WordCount/WordCountService serviço.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Tarefa: Remover um aplicativo de serviço tecidos
Você pode excluir uma instância de um aplicativo implantado, remova o tipo de aplicativo provisionado cluster e remover o pacote de aplicativo da ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Etapa 1: Remover uma instância do aplicativo
Quando uma instância do aplicativo não for mais necessário, você pode removê-lo permanentemente usando o cmdlet [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) . Isso também automaticamente remove todos os serviços pertencentes ao aplicativo, removendo permanentemente todo o estado de serviço. Esta operação não pode ser revertida e estado do aplicativo não poderão ser recuperado.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Etapa 2: Cancelar o registro do tipo de aplicativo
Quando você não precisa mais uma versão específica de um tipo de aplicativo, cancelar o registro-lo usando o cmdlet [**ServiceFabricApplicationType cancela o registro**](https://msdn.microsoft.com/library/azure/mt125885.aspx) . Cancelando o registro de tipos não utilizados libera espaço de armazenamento usado pelo pacote de aplicativo na loja de imagem. Um tipo de aplicativo pode ser cancelado, desde que há nenhum aplicativo criado em relação a ele ou pendente atualizações de aplicativo fazendo referência a ela.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Etapa 3: Remover o pacote de aplicativo
Após o tipo de aplicativo for cancelado, o pacote de aplicativo pode ser removido do armazenamento de imagem usando o cmdlet [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) .

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Próximas etapas
[Ciclo de vida de aplicativo de serviço tecidos](service-fabric-application-lifecycle.md)

[Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[Atualização do aplicativo](service-fabric-application-upgrade.md)

[Cmdlets de serviço tecidos Azure](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlets de capacidade de teste do Azure tecidos de serviço](https://msdn.microsoft.com/library/azure/mt125844.aspx)
