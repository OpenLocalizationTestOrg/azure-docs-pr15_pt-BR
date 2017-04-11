<properties
   pageTitle="Como invocar perda de dados nos serviços do serviço tecidos | Microsoft Azure"
   description="Descreve como usar a perda de dados api"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# <a name="how-to-invoke-data-loss-on-services"></a>Como invocar perda de dados nos serviços

>[AZURE.WARNING] Este documento descrevem como causar perda de dados nos seus serviços e deve ser usado com cuidado.

## <a name="introduction"></a>Introdução
Você pode chamar perda de dados em uma partição do seu serviço de tecidos por chamada StartPartitionDataLossAsync().  Essa api usa a inclusão de falhas e o serviço de análise para executar o trabalho para causar condições de perda de dados.

## <a name="using-the-fault-injection-and-analysis-service"></a>Usando o serviço de análise e a inclusão de falhas

O serviço de análise e probabilidade atualmente suporta as seguintes APIs no gráfico abaixo.  O lado direito do gráfico mostra o cmdlet do PowerShell correspondente.  Consulte a documentação do msdn sobre cada API para obter mais informações sobre cada um deles.

|           API DO C#                    |         Cmdlet do PowerShell                      |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync] [dl]   |[Início-ServiceFabricPartitionDataLoss] [psdl]   |
|[StartPartitionQuorumLossAsync] [ql] |[Início-ServiceFabricPartitionQuorumLoss] [psql] |
|[StartPartitionRestartAsync] [rp]    |[Início-ServiceFabricPartitionRestart] [psrp]    |

## <a name="conceptual-overview-of-running-a-command"></a>Visão geral conceitual da execução de um comando

O serviço de análise e probabilidade usa um modelo assíncrono onde você inicia o comando com uma API, conhecido como API "Início" neste documento e verifica o andamento deste comando usando um "GetProgress" API até que ele atingiu um estado terminal ou você cancelá-la.
Para iniciar um comando, chame a API de "Início" para a API correspondente.  Essa API retorna quando o serviço de análise e probabilidade aceitou a solicitação.  No entanto, ele não indica quanto tempo um comando foi executado, ou mesmo se ele ainda foi iniciado.  Para verificar o progresso de um comando, ligue a API que corresponde à API "Início" anteriormente chamada de "GetProgress".  "GetProgress" API retornará um objeto que indica o status atual do comando dentro de sua propriedade de estado.  A execução do comando indefinidamente até:

1.  Concluído com êxito.  Se você chamar "GetProgress" nesse caso, o andamento estado do objeto será concluído.
2.  Ele encontra um erro fatal.  Se você chamar "GetProgress" nesse caso, o andamento estado do objeto será interrompido
3.  Cancelá-la por meio do [CancelTestCommandAsync]  [ cancel] API ou [Parar-ServiceFabricTestCommand]  [ cancelps] cmdlet do PowerShell.  Se você chamar "GetProgress" nesse caso, o andamento estado do objeto será cancelado ou ForceCancelled, dependendo de um argumento para essa API.  Consulte a documentação do [CancelTestCommandAsync]  [ cancel] para obter mais detalhes.


## <a name="details-of-running-a-command"></a>Detalhes da execução de um comando

Para iniciar um comando, chame a API comece com os argumentos esperados.  Todas as APIs iniciar têm um argumento de Guid denominado operationId.  Você deve controlar do argumento operationId, porque é usado para controlar o andamento deste comando.  Isso deve ser passado em "GetProgress" API para controlar o andamento do comando.  O operationId deve ser exclusivo.

Depois de chamar com êxito a API Iniciar, a API GetProgress deve ser chamada em um loop até que a andamento retornado propriedade do objeto estado seja concluída.  Todos os [do FabricTransientException]  [ fte] e OperationCanceledException deve ser repetida.
Quando o comando atingiu um estado terminal (concluído, com defeito ou cancelado), a andamento retornado propriedade do objeto resultado terá informações adicionais.  Se o estado for concluído, Result.SelectedPartition.PartitionId conterá o id de partição que foi selecionado.  Result.Exception será nulo.  Se o estado está com defeito, Result.Exception terá o motivo pelo qual a probabilidade de inserção e serviço de análise com defeito o comando.  Result.SelectedPartition.PartitionId terão a id de partição que foi selecionada.  Em algumas situações, o comando pode não Avançar suficiente para escolher uma partição.  Nesse caso, o PartitionId será 0.  Se o estado for cancelado, Result.Exception será nulo.  Como o caso com defeito, Result.SelectedPartition.PartitionId terá a id de partição que foi escolhida, mas se o comando não tiver prosseguido suficiente para fazê-lo, ele será 0.  Consulte também o exemplo abaixo.

O código de exemplo a seguir mostra como começar e verificar o progresso em um comando para causar perda de dados em uma partição específica.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

O exemplo abaixo mostra como usar o PartitionSelector para escolher uma partição aleatória de um serviço especificado:

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## <a name="history-and-truncation"></a>Histórico e truncamento

Após um comando atingiu um estado terminal, seus metadados permanecerá na probabilidade de inserção e serviço de análise para determinada hora, antes que ele será removido para economizar espaço.  Se "GetProgress" é chamado usando o operationId de um comando depois que ele foi removido, ela retornará uma FabricException com um código de erro de KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx
