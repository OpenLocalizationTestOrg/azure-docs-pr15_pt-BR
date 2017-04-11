<properties
   pageTitle="Introdução ao implantar e atualizar aplicativos em seu cluster local | Microsoft Azure"
   description="Configurar um cluster de serviço tecidos local, implantar um aplicativo existente para ele e, em seguida, atualize o aplicativo."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Introdução ao implantar e atualizar aplicativos em seu cluster local
O SDK do Azure serviço tecidos inclui um ambiente de desenvolvimento local completo que você pode usar para começar rapidamente com implantação e gerenciamento de aplicativos em um cluster local. Neste artigo, você cria um cluster local, implanta um aplicativo existente para ele e, em seguida, atualiza o aplicativo para uma nova versão, tudo a partir do Windows PowerShell.

> [AZURE.NOTE] Este artigo pressupõe que você já [configurar seu ambiente de desenvolvimento](service-fabric-get-started.md).

## <a name="create-a-local-cluster"></a>Criar um cluster local
Um cluster de estrutura de serviço representa um conjunto de recursos de hardware que você pode implantar aplicativos. Normalmente, um cluster é composto por qualquer lugar de cinco para muitos milhares de máquinas. Entretanto, o SDK do serviço tecidos inclui uma configuração de cluster que pode ser executados em uma única máquina.

É importante entender que o cluster local tecidos do serviço não é um emulador ou simulator. Ele é executado o mesmo código de plataforma encontrada em clusters múltipla máquina. A única diferença é que ele seja executado os processos de plataforma que são normalmente distribuídos entre cinco máquinas em um computador.

O SDK fornece duas maneiras de configurar um cluster local: um script do Windows PowerShell e o aplicativo de bandeja de sistema do Gerenciador de Cluster de Local. Neste tutorial, usamos o script do PowerShell.

> [AZURE.NOTE] Se você já tiver criado um cluster local Implantando um aplicativo do Visual Studio, você pode ignorar esta seção.


1. Inicie uma nova janela do PowerShell como administrador.

2. Execute o script de configuração de cluster da pasta SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    Configuração de cluster leva alguns momentos. Após a instalação estiver concluída, você verá a saída semelhante a:

    ![Saída de configuração de cluster][cluster-setup-success]

    Agora você está pronto para experimentar implantar um aplicativo para o seu cluster.

## <a name="deploy-an-application"></a>Implantar um aplicativo
O SDK de tecidos serviço inclui um conjunto sofisticado de estruturas e ferramentas para a criação de aplicativos do desenvolvedor. Se você estiver interessado em aprender como criar aplicativos no Visual Studio, consulte [criar seu primeiro aplicativo de serviço tecidos no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Neste tutorial, usaremos um aplicativo de amostra existente (chamado WordCount) para que podemos concentrar os aspectos de gerenciamento da plataforma – incluindo implantação, monitoramento e atualização.


1. Inicie uma nova janela do PowerShell como administrador.

2. Importe o módulo do PowerShell do serviço tecidos SDK.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Crie um diretório para armazenar o aplicativo que você baixe e implanta, como C:\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Baixe o aplicativo de WordCount](http://aka.ms/servicefabric-wordcountapp) até o local você criou.  Observação: o navegador Microsoft Edge salva o arquivo com uma extensão *. zip* .  Altere a extensão de arquivo para *.sfpkg*.

5. Conectar-se para o cluster local:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Crie um novo aplicativo usando o comando de implantação do SDK com um nome e um caminho para o pacote de aplicativos.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Se tudo correr bem, você deve ver o resultado semelhante ao seguinte:

    ![Implantar um aplicativo para o cluster local][deploy-app-to-local-cluster]

7. Para ver o aplicativo em ação, inicie o navegador e navegue até [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Você deverá ver:

    ![Interface do usuário do aplicativo implantado][deployed-app-ui]

    O aplicativo WordCount é muito simple. Ele inclui código de JavaScript do lado do cliente para gerar aleatórias cinco caracteres "palavras", o que, em seguida, são enviadas ao aplicativo por meio de API Web do ASP.NET. Um serviço de estado rastreia o número de palavras contados. Eles são particionados com base no primeiro caractere da palavra. Você pode encontrar o código-fonte para o aplicativo de WordCount na [Introdução amostras](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).

    O aplicativo que estamos implantados contém quatro partições. Então palavras que comecem com À G são armazenadas na primeira partição, palavras que comecem com H até N são armazenadas na segunda partição e assim por diante.

## <a name="view-application-details-and-status"></a>Exibir detalhes do aplicativo e status
Agora que estamos implantou o aplicativo, vamos examinar alguns dos detalhes aplicativo no PowerShell.

1. Consulta implantados todos os aplicativos no cluster:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Presumindo que somente você implantou o aplicativo WordCount, você verá algo semelhante a:

    ![Consultar aplicativos implantados tudo no PowerShell][ps-getsfapp]

2. Vá para o próximo nível consultando o conjunto de serviços incluídos no aplicativo WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Serviços de lista para o aplicativo no PowerShell][ps-getsfsvc]

    O aplicativo consiste em dois serviços – web front-end e o serviço de estado que gerencia as palavras.

3. Por fim, dê uma olhada na lista de partições para WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Exibir as partições de serviço no PowerShell][ps-getsfpartitions]

    O conjunto de comandos que você usou, como todos os comandos do PowerShell do serviço tecidos, estão disponíveis para qualquer cluster que você pode se conectar, local ou remoto.

    Para obter uma forma mais visual interagir com o cluster, você pode usar a ferramenta de serviço tecidos Explorer baseada na web, navegando até [http://localhost:19080/Explorer](http://localhost:19080/Explorer) no navegador.

    ![Exibir detalhes do aplicativo no Explorador de estrutura de serviço][sfx-service-overview]

    > [AZURE.NOTE] Para saber mais sobre o serviço tecidos Explorer, consulte [visualizando seu cluster com o serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md).

## <a name="upgrade-an-application"></a>Atualizar um aplicativo
Serviço tecidos fornece atualizações de tempo de inatividade não monitorando a integridade do aplicativo, como ele implementa no cluster. Vamos executar uma atualização simple do aplicativo WordCount.

A nova versão do aplicativo agora conta somente palavras que começam com uma vogal. Como a atualização implementa, vemos duas alterações no comportamento do aplicativo. Primeiro, a taxa na qual a contagem de cresce deve lenta, pois menos palavras estão sendo contadas. Em segundo lugar, desde que a primeira partição tem dois vogais (A e E) e todas as outras partições contenham apenas um cada, sua contagem eventualmente deve começar ultrapassando a outras pessoas.

1. [Baixe o pacote de v2 WordCount](http://aka.ms/servicefabric-wordcountappv2) no mesmo local em que você baixou o pacote v1.

2. Retornar para a janela do PowerShell e use o comando de atualização do SDK para registrar a nova versão do cluster. Comece a atualizar a estrutura: / WordCount aplicativo.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Você verá a saída do PowerShell que começa aparência semelhante à seguinte como a atualização.

    ![Atualizar o progresso no PowerShell][ps-appupgradeprogress]

3. Enquanto a atualização está prosseguir, talvez seja mais fácil monitorar seu status do serviço tecidos Explorer. Inicie uma janela de navegador e navegue até [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expanda **aplicativos** na árvore à esquerda, em seguida, escolha **WordCount**e finalmente **tecidos: / WordCount**. Na guia essentials, você verá o status da atualização como ela passa domínios de atualização do cluster.

    ![Atualizar o progresso no Explorador de estrutura de serviço][sfx-upgradeprogress]

    Como a atualização passa para cada domínio, são executadas verificações de integridade para garantir que o aplicativo está funcionando adequadamente.

4. Se você executar novamente a consulta anterior para o conjunto de serviços na estrutura: / WordCount aplicativo, observe que a versão do WordCountService alterada mas a versão do WordCountWebService não:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Serviços de aplicativo de consulta após a atualização][ps-getsfsvc-postupgrade]

    Isso realça como o serviço tecidos gerencia atualizações de aplicativo. Ele toca apenas o conjunto de serviços (ou pacotes de código/configuração dentro desses serviços) que foram alterados, que torna o processo de atualização com mais rapidez e mais confiável.

5. Por fim, volte para o navegador para observar o comportamento da nova versão do aplicativo. Conforme esperado, a contagem progride mais lentamente e a primeira partição termina com um pouco mais do volume.

    ![Exibir a nova versão do aplicativo no navegador][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Limpando

Antes da conclusão, é importante lembrar que o cluster local é real. Aplicativos continuam a ser executado em segundo plano até você removê-los.  Dependendo da natureza de seus aplicativos, um aplicativo em execução pode levar recursos significativos em sua máquina. Você tem várias opções para gerenciar aplicativos e cluster:

1. Para remover um aplicativo individual e todos os seus dados, execute o seguinte:

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    Ou exclua o aplicativo no menu **ações** do serviço tecidos Explorer ou o menu de contexto na exibição de lista de aplicativos do painel esquerdo.

    ![Excluir um aplicativo é serviço tecidos Explorer][sfe-delete-application]

2. Após excluir o aplicativo do cluster, você pode cancelar o registro de versões 1.0.0 e 2.0.0 o WordCount do tipo de aplicativo. Exclusão remove os pacotes de aplicativos, incluindo o código e configuração do repositório de imagem do cluster.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    Ou, no Explorador de tecidos do serviço, escolha o **Tipo de desconfiguração** para o aplicativo.

3. Para desligar o cluster, mas manter os dados de aplicativo e rastreamentos, clique em **Parar Cluster Local** no aplicativo de bandeja do sistema.

4. Para excluir o cluster completamente, clique em **Remover Cluster Local** no aplicativo de bandeja do sistema. Esta opção resultará em outra implantação lenta na próxima vez que você pressionar F5 no Visual Studio. Remova o cluster local somente se você não pretende usá-lo por algum tempo ou se você precisar recuperar recursos.

## <a name="1-node-and-5-node-cluster-mode"></a>1 nó e 5 modo de cluster de nó

Ao trabalhar com o cluster local para desenvolver aplicativos, você geralmente estiver fazendo iterações rápidas de escrever código, depuração, alterando o código, depuração etc. Para ajudar a otimizar esse processo, o cluster local pode ser executados em dois modos: 1 nó ou 5. Ambos os modos de cluster têm seus benefícios.
Modo de cluster nó 5 permite que você trabalhe com um cluster real. Você pode testar cenários de failover, trabalhar com mais instâncias e réplicas dos seus serviços.
Modo de cluster nó 1 é otimizado para fazer a implantação rápida e registro de serviços, para ajudá-lo a validar rapidamente código usando o tempo de execução do serviço tecidos.

Modo de cluster 1 nó tanto 5 modo de cluster de nó não é um emulador ou simulator. Ele é executado o mesmo código de plataforma encontrada em clusters múltipla máquina.

> [AZURE.NOTE] Este recurso está disponível no SDK versão 5.2 e acima.

Para alterar o modo de cluster para um cluster de nó 1, use o Gerenciador de Cluster do serviço tecidos Local ou usar o PowerShell da seguinte maneira:

1. Inicie uma nova janela do PowerShell como administrador.

2. Execute o script de configuração de cluster da pasta SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```

    Configuração de cluster leva alguns momentos. Após a instalação estiver concluída, você verá a saída semelhante a:
    
    ![Saída de configuração de cluster][cluster-setup-success-1-node]

Se você estiver usando o Gerenciador de Cluster do serviço tecidos Local:

![Modo de cluster de mudança][switch-cluster-mode]

> [AZURE.WARNING] Ao alterar o modo de cluster, o cluster atual está sendo removido do seu sistema e um novo cluster está sendo criado. Os dados que você deve armazenou no cluster, serão excluídas quando você altera o modo de cluster.

## <a name="next-steps"></a>Próximas etapas
- Agora que você tiver implantado e atualizado alguns aplicativos previamente criados, você pode [tentar criar seu próprio no Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Todas as ações executadas em cluster local neste artigo podem ser executadas em um [cluster Azure](service-fabric-cluster-creation-via-portal.md) também.
- A atualização que estamos executados neste artigo foi básica. Consulte a [documentação de atualização](service-fabric-application-upgrade.md) para saber mais sobre a eficiência e flexibilidade das atualizações do serviço tecidos.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
