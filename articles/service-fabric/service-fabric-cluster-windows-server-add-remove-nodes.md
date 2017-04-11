<properties
   pageTitle="Adicionar ou remover nós para um cluster de estrutura de serviço autônomo | Microsoft Azure"
   description="Saiba como adicionar ou remover nós para um cluster de estrutura de serviço do Azure em um física ou máquina virtual executando o Windows Server, que pode ser local ou em qualquer nuvem."
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
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós a um cluster de estrutura de serviço autônomo em execução no Windows Server

Após ter [criado o seu cluster de estrutura de serviço autônomo em máquinas Windows Server](service-fabric-cluster-creation-for-windows-server.md), suas necessidades de negócios podem mudar para que talvez seja necessário adicionar ou remover vários nós ao cluster. Este artigo fornece etapas detalhadas para atingir esse objetivo.


## <a name="add-nodes-to-your-cluster"></a>Adicionar nós ao cluster

1. Preparar a máquina virtual/máquina que você deseja adicionar ao seu cluster seguindo as etapas mencionadas na seção [preparar as máquinas para cumprir os pré-requisitos para implantação do cluster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Planeje quais domínio de falha e o domínio de atualização, você vai adicionar esta máquina virtual/máquina.
3. Área de trabalho remota (RDP) na máquina virtual/máquina que você deseja adicionar ao cluster.
4. Copiar ou [Baixe o pacote autônomo para tecidos do serviço para o Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) para esta máquina virtual/máquina e descompacte o pacote.
5. Executar o Powershell como administrador e navegue até o local do pacote descompactado.
6. Execute o Powershell *AddNode.ps1* com os parâmetros que descreve o nó novo para adicionar. O exemplo a seguir adiciona um novo nó chamado VM5, com tipo NodeType0, o endereço IP 182.17.34.52 em UD1 e FD1. O *ExistingClusterConnectionEndPoint* é um ponto de extremidade de conexão de um nó já no cluster existente. Para esse ponto de extremidade, você pode escolher o endereço IP de *qualquer* nó no cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Remover nós do cluster

1. Dependendo do nível de Reliablity escolhido para o cluster, você não pode remover os nós (3/5/7/9) n primeiro o tipo de nó principal
2. Não há suporte para a execução de comando RemoveNode em um cluster de desenvolvimento.
2. Área de trabalho remota (RDP) na máquina virtual/máquina que você deseja remover do cluster.
2. Copiar ou [baixar o pacote autônomo para tecidos do serviço para o Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompactar o pacote para esta máquina virtual/máquina.
3. Executar o Powershell como administrador e navegue até o local do pacote descompactado.
4. Execute *RemoveNode.ps1* no PowerShell. O exemplo a seguir remove o nó atual do cluster. O *ExistingClientConnectionEndpoint* é um ponto de extremidade de conexão de cliente para qualquer nó que permanecerá no cluster. Escolha o endereço IP e a porta de ponto de extremidade de *qualquer* **outro nó** no cluster. Este **outro nó** alternadamente atualizará a configuração de cluster para o nó removido. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

Observe que mesmo após a remoção de um nó, ele pode aparecer como sendo pressionada em consultas e SFX. Este é um defeito conhecido e será corrigido em uma versão futura. 


## <a name="next-steps"></a>Próximas etapas
- [Definições de configuração de cluster do Windows autônoma](service-fabric-cluster-manifest.md)
- [Proteger um cluster de autônomo no Windows usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteger um cluster de autônomo no Windows usando X509 certificados](service-fabric-windows-cluster-x509-security.md)
- [Criar um cluster de estrutura de serviço autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
