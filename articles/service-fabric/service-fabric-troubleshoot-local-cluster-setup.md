<properties
   pageTitle="Solucionar problemas de sua configuração de cluster tecidos serviço local | Microsoft Azure"
   description="Este artigo discute um conjunto de sugestões para seu cluster de desenvolvimento local de solução de problemas"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="troubleshoot-your-local-development-cluster-setup"></a>Solucionar problemas de configuração de cluster seu local de desenvolvimento

Se você tiver um problema durante a interação com seu cluster local de desenvolvimento do Azure serviço tecidos, examine as seguintes sugestões para obter soluções possíveis.

## <a name="cluster-setup-failures"></a>Falhas de configuração de cluster

### <a name="cannot-clean-up-service-fabric-logs"></a>Não é possível limpar logs de estrutura de serviço

#### <a name="problem"></a>Problema

Enquanto estiver executando o script DevClusterSetup, você verá um erro como esta:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solução

Fechar a janela atual do PowerShell e abra uma nova janela do PowerShell como administrador. Você agora deve ser capaz de executar o script com êxito.

## <a name="cluster-connection-failures"></a>Falhas de conexão de cluster

### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Cmdlets do PowerShell do serviço tecidos não são reconhecidos no PowerShell do Azure

#### <a name="problem"></a>Problema

Se você tentar executar qualquer um dos cmdlets do PowerShell de tecidos do serviço, tais como `Connect-ServiceFabricCluster` em uma janela do PowerShell do Azure, falhar, dizendo que o cmdlet não é reconhecido. O motivo para isso é que o Azure PowerShell usa a versão de 32 bits do Windows PowerShell (mesmo em versões de sistema operacional de 64 bits), enquanto os cmdlets de serviço tecidos só funcionam em ambientes de 64 bits.

#### <a name="solution"></a>Solução

Sempre executar cmdlets do serviço tecidos diretamente do Windows PowerShell.

>[AZURE.NOTE] A versão mais recente do PowerShell do Azure não criar um atalho de especial, para que isso não deve ocorrer mais.

### <a name="type-initialization-exception"></a>Digite exceção de inicialização

#### <a name="problem"></a>Problema

Quando você estiver se conectando ao cluster no PowerShell, você verá o erro TypeInitializationException para System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solução

A variável path não foi definida corretamente durante a instalação. Verifique se desconectar do Windows e entrar novamente. Isso atualizará totalmente seu caminho.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Conexão de cluster falha com "Objeto estiver fechado"

#### <a name="problem"></a>Problema

Uma chamada para conectar-ServiceFabricCluster falha com um erro como esta:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solução

Fechar a janela atual do PowerShell e abra uma nova janela do PowerShell como administrador. Você agora deve conseguir se conectar.

### <a name="fabric-connection-denied-exception"></a>Exceção de Conexão negado tecidos

#### <a name="problem"></a>Problema

Quando a depuração do Visual Studio, você recebe um erro de FabricConnectionDeniedException.

#### <a name="solution"></a>Solução

Esse erro geralmente ocorre quando você tentar ao tentar iniciar um host de serviço processar manualmente, em vez de permitir que o tempo de execução de tecidos do serviço para iniciá-lo para você.

Certifique-se de que você não tem quaisquer projetos de serviço definido como projetos de inicialização na sua solução. Somente projetos de aplicativo de serviço tecidos devem ser definidos como projetos de inicialização.

>[AZURE.TIP] Se, após a instalação, seu cluster local começa a se comportar de forma anormal, você poderá redefini-la usando o aplicativo de bandeja de sistema do cluster local manager. Isso removerá cluster existente e configurar um novo. Observe que todos os aplicativos implantados e dados associados serão removidos.

## <a name="next-steps"></a>Próximas etapas

- [Compreender e solucionar problemas de seu cluster com relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualizar seu cluster com o serviço tecidos Explorer](service-fabric-visualizing-your-cluster.md)
