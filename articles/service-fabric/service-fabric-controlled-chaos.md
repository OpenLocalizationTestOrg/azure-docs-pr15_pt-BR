<properties
   pageTitle="Induzem caos em clusters de serviço tecidos | Microsoft Azure"
   description="Usando APIs de serviço de análise de Cluster e inclusão de falhas para gerenciar caos no cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induzem controlado caos em clusters tecidos de serviço
Distribuído sistemas de grande porte como infraestruturas de nuvem estão naturalmente não confiáveis. Azure tecidos de serviço permite que os desenvolvedores criem serviços confiáveis na parte superior de uma infraestrutura de não confiável. Para escrever serviços robustos, os desenvolvedores precisam ser capaz de induzi falhas contra tal infraestrutura confiável para testar a estabilidade dos seus serviços.

A inclusão de falhas e o serviço de análise de Cluster (também conhecido como o serviço de análise de falhas) permite que os desenvolvedores de induzi ações de falhas para testar serviços. No entanto, falhas simuladas alvo iniciá-lo somente até o momento. Para assumir o teste ainda mais, você pode usar caos.

Caos simula falhas contínuas, intercaladas (normais e amigável) em todo o cluster por longos períodos de tempo. Depois de configurar caos com a taxa e o tipo de falhas, você pode iniciar ou pará-la por meio de APIs c# ou PowerShell para gerar falhas no cluster e seu serviço.

Enquanto caos é executado, ela produz diferentes eventos que capturar o estado da execução no momento. Por exemplo, um ExecutingFaultsEvent contém todas as falhas que estão sendo executadas na iteração. Um ValidationFailedEvent contém os detalhes de uma falha que foi encontrado durante a validação de cluster. Você pode chamar a API GetChaosReportAsync para obter o relatório de execuções caos.

## <a name="faults-induced-in-chaos"></a>Falhas induzidas no caos
Caos gera falhas em todo o cluster tecidos de serviço e compacta falhas que são vistas em meses ou anos em poucas horas. A combinação de falhas intercaladas com a taxa de falhas alta localiza ocorrências de canto que caso contrário são perdidas. Neste exercício de caos leva a uma melhora significativa na qualidade código do serviço.

Caos induz falhas de categorias a seguir:

 - Reiniciar um nó
 - Reinicie um pacote de código implantado
 - Remover uma réplica
 - Reiniciar uma réplica
 - Mover uma réplica principal (configurável)
 - Mover uma réplica secundária (configurável)

Caos é executado em várias iterações. Cada iteração consiste em falhas e validação de cluster para o período especificado. Você pode configurar o tempo gasto para o cluster estabilizar e de validação tenha êxito. Se for encontrada uma falha na validação de cluster, caos gera e persistir um ValidationFailedEvent com o carimbo de hora do UTC e os detalhes de falha.

Por exemplo, considere uma instância de caos que é definido para ser executado por uma hora com um máximo de três falhas simultâneas. Caos induz três falhas e, em seguida, valida a integridade de cluster. Ele itera anterior passa etapa até que seja explicitamente parado por meio da API StopChaosAsync ou uma hora. Se o cluster ficar sem integridade em qualquer iteração (ou seja, ele não estabilizar dentro de um tempo configurado), caos gera uma ValidationFailedEvent. Este evento indica que algo deu errado e talvez seja necessário investigações.

Em sua forma atual, caos induz apenas falhas de seguras. Isso significa que, na ausência de falhas externas, uma perda de quorum ou dados nunca ocorra.

## <a name="important-configuration-options"></a>Opções de configuração importantes
 - **TimeToRun**: tempo Total em que executa o caos antes que ela seja concluída com êxito. Você pode parar caos antes que tenha executado para o período de TimeToRun por meio da API StopChaos.
 - **MaxClusterStabilizationTimeout**: A quantidade máxima de tempo de espera para o cluster se tornar eficaz, antes de verificar nele novamente. Essa espera é reduzir a carga no cluster enquanto ele está recuperando. As verificações executadas são:
    - Se a integridade de cluster for Okey
    - Se a integridade do serviço é Okey
    - Se a réplica de destino Definir tamanho é alcançada para a partição de serviço
    - Existam de réplicas sem InBuild
 - **MaxConcurrentFaults**: O número máximo de falhas simultâneas que são induziu em cada iteração. É o maior caos o número, mais rigorosas. Isso resulta em combinações de transição e failovers mais complexos. Caos garante que, na ausência de falhas externas, há sem perda de quorum ou dados, independentemente de um valor como alto tem essa configuração.
 - **EnableMoveReplicaFaults**: ativa ou desativa as falhas que causam réplicas primárias ou secundárias mover. Essas falhas estão desabilitadas por padrão.
 - **WaitTimeBetweenIterations**: A quantidade de tempo de espera entre iterações, ou seja, após um round de falhas e validação correspondente.
 - **WaitTimeBetweenFaults**: A quantidade de tempo de espera entre duas falhas consecutivas em uma iteração.

## <a name="how-to-run-chaos"></a>Como executar caos
**C#:**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
