<properties
   pageTitle="Atualizar um cluster de estrutura de serviço autônomo no Windows Server | Microsoft Azure"
   description="Atualizar o código de estrutura de serviço e/ou configuração que executa um cluster de estrutura de serviço autônomo, incluindo a configuração de modo de atualização de cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Atualizar seu cluster de estrutura de serviço autônomo no Windows Server

> [AZURE.SELECTOR]
- [Cluster Azure](service-fabric-cluster-upgrade.md)
- [Cluster autônomo](service-fabric-cluster-upgrade-windows-server.md)

Para qualquer sistema moderno, design para possibilidade de atualização é chave para atingir o sucesso a longo prazo do seu produto. Um cluster de estrutura de serviço é um recurso que você possui. Este artigo descreve como você pode garantir que o cluster sempre executa versões suportadas da estrutura serviço código e configurações.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão de tecidos que é executado em seu Cluster

Você pode definir seu cluster baixar as atualizações do serviço tecidos, quando a Microsoft lançar uma nova versão ou escolha uma versão de tecidos com suporte que deseja que o seu cluster no. 

Você pode fazer isso definindo a configuração de cluster "fabricClusterAutoupgradeEnabled" como true ou false.


>[AZURE.NOTE] Certifique-se de manter o seu cluster sempre executando uma versão de serviço tecidos suportada. Como e quando podemos anunciar o lançamento de uma nova versão da estrutura de serviço, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias da data. Os novos lançamentos estão anunciado [no blog da equipe de estrutura de serviço](https://blogs.msdn.microsoft.com/azureservicefabric/ ). A nova versão está disponível para escolher depois. 


Você pode atualizar seu cluster para a nova versão somente se você estiver usando uma configuração de nó de estilo de produção, onde cada nó tecidos do serviço é alocada em um computador virtual ou física separada. Se você tiver um cluster de desenvolvimento, onde há mais de um serviço tecidos nós em um única física ou máquina virtual, você deve subdividir seu cluster e recriá-lo com a nova versão.


Há dois fluxos de trabalho distintos para atualizar seu cluster para o mais recente ou uma versão de estrutura de serviço com suporte. Para clusters que possuem conectividade para baixar a versão mais recente automaticamente e a segunda para clusters que estão sem conectividade para baixar a versão mais recente do serviço tecidos.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters com conectividade para baixar o código e configuração mais recentes 

Use estas etapas para atualizar seu cluster para uma versão compatível, se os nós de cluster tem conectividade com a internet [http://download.microsoft.com](http://download.microsoft.com) 

Para clusters que possuem conectividade [http://download.microsoft.com](http://download.microsoft.com), podemos verificar periodicamente a disponibilidade de novas versões de tecidos do serviço.


Quando uma nova versão de tecidos do serviço estiver disponível, o pacote é baixado localmente ao cluster e provisionado para atualização. Além para informar ao cliente dessa nova versão, o sistema coloca um aviso de integridade explícita cluster similar ao seguinte:

"Cluster versão atual [versão #] suporte termina em [Data].", depois que o cluster está executando a versão mais recente, o aviso desaparece.


#### <a name="cluster-upgrade-workflow"></a>Atualização cluster de fluxo de trabalho.
 
Quando você ver o aviso de integridade do cluster, você precisa fazer o seguinte:

1. Conectar-se ao cluster de qualquer computador que tenha acesso de administrador para todas as máquinas que são listados como nós no cluster. Máquina que esse script é executado em não precisa ser parte do cluster

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obter a lista de estrutura de serviço versões que você pode atualizar para

    ```powershell

    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Você deve receber uma saída semelhante a esta:

    ![obter tecidos versões][getfabversions]

3. Disparar uma atualização de cluster para uma das versões que está disponível usando o [PowerShell Start-ServiceFabricClusterUpgrade cmd](https://msdn.microsoft.com/library/mt125872.aspx)

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Você pode monitorar o andamento da atualização no Explorador de tecidos do serviço ou executando o seguinte comando do shell de energia

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the Start-ServiceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Depois que você tiver corrigido os problemas que resultou na reversão, é necessário iniciar a atualização novamente, seguindo as mesmas etapas antes.


### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters com <U>nenhuma conectividade</u> para baixar o código e configuração mais recentes

Use estas etapas para atualizar seu cluster para uma versão compatível, se seu cluster nós **não tiver** conectividade com a internet [http://download.microsoft.com](http://download.microsoft.com) 


>[AZURE.NOTE]Se você estiver executando um cluster que não está conectado à internet, você precisará monitorar o blog da equipe do serviço tecidos receber notificações de uma nova versão. O sistema **não** coloque qualquer aviso de integridade de cluster para alertá-lo dele.  

1. Modificar a configuração do cluster para definir a propriedade a seguir para falso.

        "fabricClusterAutoupgradeEnabled": false,

e disparar uma atualização de configuração. consulte [Iniciar-ServiceFabricClusterUpgrade PS cmd](https://msdn.microsoft.com/library/mt125872.aspx) para obter detalhes de uso. A versão de manifesto cluster é a versão que você tem na clusterConfig.JSON. Certifique-se de atualizá-lo antes de iniciar desativar a atualização de configuração.

```powershell

    Start-ServiceFabricClusterUpgrade [-Config] [-ClusterConfigVersion] -FailureAction Rollback -Monitored 

```

#### <a name="cluster-upgrade-workflow"></a>Atualização cluster de fluxo de trabalho.
 


1. Baixar a versão mais recente do pacote do documento de [criar cluster de estrutura de serviço para o windows server](service-fabric-cluster-creation-for-windows-server.md) 


1. Conectar-se ao cluster de qualquer computador que tenha acesso de administrador para todas as máquinas que são listados como nós no cluster. Máquina que esse script é executado em não precisa ser parte do cluster 

    ```powershell

    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Copie o pacote baixado para o armazenamento de imagens de cluster.

    ```powershell

    ###### Get the list of available service fabric versions 
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"


    ```

2. Registrar o pacote copiado 

    ```powershell

    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 

    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```


3. Disparar uma atualização de cluster para uma das versões que está disponível. 

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Você pode monitorar o andamento da atualização no Explorador de tecidos do serviço ou executando o seguinte comando do shell de energia

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the start-serviceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Depois que você tiver corrigido os problemas que resultou na reversão, é necessário iniciar a atualização novamente, seguindo as mesmas etapas antes.



## <a name="next-steps"></a>Próximas etapas
- Saiba como personalizar algumas das [configurações do serviço tecidos cluster tecidos](service-fabric-cluster-fabric-settings.md)
- Saiba como [dimensionar seu cluster e reduzir](service-fabric-cluster-scale-up-down.md)
- Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
