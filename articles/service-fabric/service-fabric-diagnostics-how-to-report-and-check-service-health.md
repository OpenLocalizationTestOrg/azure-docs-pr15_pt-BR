<properties
   pageTitle="Relatar e verificar integridade com estrutura de serviço do Azure | Microsoft Azure"
   description="Saiba como enviar relatórios de integridade de seu código de serviço e como verificar a integridade do serviço, usando as ferramentas de monitoramento de integridade que fornece tecidos de serviço do Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>

# <a name="report-and-check-service-health"></a>Relatar e verificar a integridade do serviço
Quando seus serviços encontrar problemas, a capacidade de responder e corrigir ocorrências e interrupções depende de sua capacidade de detectar os problemas rapidamente. Se você relatar problemas e falhas no Gerenciador de integridade do Azure serviço tecidos do seu código de serviço, você pode usar ferramentas fornecidas pelo serviço tecidos para verificar o status de integridade de monitoramento de integridade padrão.

Há duas maneiras que você pode relatar integridade do serviço:

- Use objetos de [partição](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) ou [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) .  
Você pode usar o `Partition` e `CodePackageActivationContext` objetos para relatar a integridade dos elementos que fazem parte do contexto atual. Por exemplo, código que executa como parte da réplica pode relatar integridade somente na réplica, partição que ele pertence e o que é uma parte do aplicativo.

- Use `FabricClient`.   
Você pode usar `FabricClient` a integridade de relatório do código do serviço, se o cluster não estiver [seguro](service-fabric-cluster-security.md) ou se o serviço é executado com privilégios de administrador. Isso não será verdadeiro na maioria dos cenários do mundo real. Com `FabricClient`, você poderá relatar integridade de qualquer entidade que faz parte do cluster. Ideal, no entanto, código do serviço deve somente enviar relatórios relacionados ao seu próprio integridade.

Este artigo conduz você por meio de um exemplo que relatórios de integridade do código do serviço. O exemplo também mostra como as ferramentas que fornece tecidos de serviço podem ser usadas para verificar o status de integridade. Este artigo destina-se a ser uma rápida introdução às recursos da estrutura de serviço de monitoramento de integridade. Para obter informações mais detalhadas, você pode ler a série de artigos detalhadas sobre integridade que começam com o link no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte instalado:

   * Visual Studio de 2015
   * Serviço tecidos SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Para criar um cluster de desenvolvimento seguro local
- Abra o PowerShell com privilégios de administrador e execute os seguintes comandos.

![Comandos que mostram como criar um cluster de desenvolvimento seguro](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Implantar um aplicativo e verificar sua integridade

1. Abra o Visual Studio como administrador.

2. Crie um projeto usando o modelo de **Serviço de estado** .

    ![Criar um aplicativo de serviço tecidos com o serviço de estado](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Pressione **F5** para executar o aplicativo no modo de depuração. O aplicativo será implantado para o cluster local.

4. Após a execução do aplicativo, clique com botão direito no ícone do Gerenciador de Cluster Local na área de notificação e selecione **Gerenciar Cluster Local** no menu de atalho para abrir o serviço tecidos Explorer.

    ![Abra o Explorador de tecidos do serviço de área de notificação](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. A integridade do aplicativo deverá ser exibida como esta imagem. No momento, o aplicativo deve ser eficaz, sem erros.

    ![Aplicativo Íntegro no Explorador de estrutura de serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Você também pode verificar a integridade usando o PowerShell. Você pode usar ```Get-ServiceFabricApplicationHealth``` verificar a integridade de um aplicativo e você pode usar ```Get-ServiceFabricServiceHealth``` para verificar integridade do serviço. O relatório de integridade para o mesmo aplicativo no PowerShell é nesta imagem.

    ![Aplicativo Íntegro no PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Para adicionar eventos de integridade personalizado ao seu código de serviço
Os modelos de projeto de serviço tecidos no Visual Studio contêm código de amostra. As etapas a seguir mostram como você pode relatar eventos de integridade personalizados do seu código de serviço. Esses relatórios automaticamente aparecerá nas ferramentas padrão para monitoramento de integridade que serviço tecidos fornece, como o serviço tecidos Explorer, modo de exibição da integridade de portal Azure e PowerShell.

1. Reabra o aplicativo que você criou anteriormente no Visual Studio ou criar um novo aplicativo usando o modelo do Visual Studio de **Serviço de estado** .

2. Abra o arquivo Stateful1.cs e encontre o `myDictionary.TryGetValueAsync` ligar o `RunAsync` método. Você pode ver que esse método retorna um `result` que contém o valor atual do contador porque a lógica chave neste aplicativo é manter uma contagem em execução. Se isso fosse um aplicativo real, e se a falta de resultado representado uma falha, você desejaria sinalize desse evento.

3. Para relatar um evento de integridade quando a falta de resultado representa uma falha, adicione as seguintes etapas.

    a. Adicionar o `System.Fabric.Health` namespace para o arquivo de Stateful1.cs.

    ```csharp
    using System.Fabric.Health;
    ```

    b. Adicione o seguinte código após o `myDictionary.TryGetValueAsync` chamadas

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Podemos relatório integridade de réplica porque ele está sendo informado de um serviço de estado. O `HealthInformation` parâmetro armazena informações sobre o problema de integridade que está sendo relatado.

    Se você criou um serviço independente, use o seguinte código

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. Se o seu serviço é executado com privilégios de administrador ou se o cluster não estiver [protegido](service-fabric-cluster-security.md), você também pode usar `FabricClient` até integridade do relatório, conforme mostrado nas etapas a seguir.  

    a. Criar a `FabricClient` instância após a `var myDictionary` declaração.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b. Adicione o seguinte código após o `myDictionary.TryGetValueAsync` chamar.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Vamos simular essa falha e vê-la aparecem nas ferramentas de monitoramento de integridade. Para simular a falha, comente a primeira linha a saúde do código de relatório que você adicionou anteriormente. Depois que você comente a primeira linha, o código de aparência o exemplo a seguir.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 Este código agora será acionada este relatório de integridade sempre `RunAsync` executa. Após fazer a alteração, pressione **F5** para executar o aplicativo.

6. Após a execução do aplicativo, abra o Explorador de estrutura de serviço para verificar a integridade do aplicativo. Desta vez, serviço tecidos Explorer mostrará a que o aplicativo está com problemas. Isso é devido o erro que foi relatado do código que adicionamos anteriormente.

    ![Aplicativo não íntegro no Explorador de estrutura de serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Se você selecionar a réplica principal na exibição em árvore do serviço tecidos Explorer, você verá que o **Estado de integridade** indica um erro, também. Serviço tecidos Explorer também exibe os detalhes do relatório de integridade que foram adicionados ao `HealthInformation` parâmetro no código. Você pode ver os mesmos relatórios de integridade do PowerShell e o portal do Azure.

    ![Integridade de réplica no Explorador de estrutura de serviço](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Este relatório permanecerão no Gerenciador de integridade até que ele seja substituído por outro relatório ou até esta réplica é excluída. Porque nós não definiu `TimeToLive` para este relatório de integridade no `HealthInformation` objeto, o relatório nunca expirará.

Recomendamos que integridade deve ser relatada no nível mais granular, que é nesse caso a réplica. Você também pode relatar integridade em `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

A integridade do relatório no `Application`, `DeployedApplication`, e `DeployedServicePackage`, use `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Próximas etapas
[Detalhamento da integridade de serviço tecidos](service-fabric-health-introduction.md)
