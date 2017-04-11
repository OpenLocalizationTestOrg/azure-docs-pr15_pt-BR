<properties
   pageTitle="Criar um cluster de autônomo com VMs do Azure executando o Windows | Microsoft Azure"
   description="Aprenda a criar e gerenciar um cluster de estrutura de serviço do Azure em Azure máquinas virtuais executando o Windows Server."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Criar um cluster de estrutura de serviço de autônomo de três nós com Azure máquinas virtuais executando o Windows Server

Este artigo descreve como criar um cluster baseado no Windows Azure VMs (máquinas virtuais), usando o instalador do serviço tecidos autônomo para o Windows Server. Este é um caso especial de [criar e gerenciar um cluster executando o Windows Server](service-fabric-cluster-creation-for-windows-server.md) VMs anotados [Azure VMs executando o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), mas você não estiver criando [um cluster de estrutura de serviço baseado em nuvem Azure](service-fabric-cluster-creation-via-portal.md). A diferença é que o cluster de tecidos serviço autônomo criado pelas etapas a seguir é totalmente gerenciado por você, enquanto o Azure clusters de estrutura de serviço baseado em nuvem são gerenciadas e atualizados pelo provedor de recursos de serviço tecidos.


## <a name="steps-to-create-the-standalone-cluster"></a>Etapas para criar o cluster autônomo

1. Entre no portal do Azure e criar uma nova Windows Server 2012 R2 data center máquina virtual em um grupo de recursos. Leia o artigo [criar uma máquina de virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter mais detalhes.
2. Adicione algumas mais Windows Server 2012 R2 data center VMs ao mesmo grupo de recursos. Certifique-se de que cada uma das VMs tem o mesmo nome de usuário de administrador e senha quando criado. Uma vez criado, você deverá ver todas as três VMs na mesma rede virtual.
3. Conectar-se a cada uma das VMs e desativar o Firewall do Windows usando o [Gerenciador de servidor, o painel de servidor Local](https://technet.microsoft.com/library/jj134147.aspx). Isso garante que o tráfego de rede pode comunicar-se entre as máquinas. Enquanto estiver conectado a cada máquina, obtenha o endereço IP abrindo um prompt de comando e digitando `ipconfig`. Como alternativa, você pode ver o endereço IP de cada máquina, selecionando o recurso de rede virtual para o grupo de recursos no portal do Azure.
4. Conectar a uma das VMs e teste que você pode executar ping duas VMs com êxito.
5. Conectar a uma das VMs e [Baixe o pacote de serviço tecidos autônomo para o Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) em uma nova pasta na máquina e extraia o pacote.
6. Abra o arquivo *ClusterConfig.Unsecure.MultiMachine.json* no bloco de notas e edite cada nó com os três endereços IP das máquinas. Alterar o nome de cluster na parte superior e salve o arquivo.  Um exemplo parcial do manifesto cluster é mostrado abaixo.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Abra uma [janela do PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Navegue até a pasta onde você extraídas o pacote de instalação autônoma baixado e salva o arquivo de manifesto cluster. Execute o seguinte comando do PowerShell.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Você deve ver o PowerShell executar, conectar-se a cada máquina e criar um cluster. Depois de cerca de um minuto, você pode verificar se o cluster estiver operacional conectando-se ao serviço tecidos Explorer um endereço IP do computador por exemplo, usando `http://10.7.0.5:19080/Explorer/index.html`. Como este é um cluster de autônomo usando VMs do Azure, para tornar mais segura, você precisa [implantar certificados para VMs Azure](service-fabric-windows-cluster-x509-security.md) ou configurar um das máquinas como um [controlador de Windows Server Active Directory (AD) para autenticação do Windows](service-fabric-windows-cluster-windows-security.md), exatamente como você faria no local.


## <a name="next-steps"></a>Próximas etapas
- [Criar clusters de estrutura de serviço autônomo no Windows Server ou no Linux](service-fabric-deploy-anywhere.md)
- [Adicionar ou remover nós para um cluster de estrutura de serviço autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Definições de configuração de cluster do Windows autônoma](service-fabric-cluster-manifest.md)
- [Proteger um cluster de autônomo no Windows usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteger um cluster de autônomo no Windows usando X509 certificados](service-fabric-windows-cluster-x509-security.md)
