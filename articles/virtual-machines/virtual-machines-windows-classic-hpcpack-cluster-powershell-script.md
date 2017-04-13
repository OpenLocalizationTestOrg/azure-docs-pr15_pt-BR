<properties
   pageTitle="Script do PowerShell para implantar Windows HPC cluster | Microsoft Azure"
   description="Executar um script PowerShell para implantar um cluster de HPC Pack do Windows Azure máquinas virtuais"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Criar um cluster (HPC) de computação de alto desempenho do Windows com o script de implantação de HPC Pack IaaS

Execute a implantação HPC Pack IaaS script do PowerShell para implantar um cluster HPC concluído para cargas de trabalho do Windows no Azure máquinas virtuais. O cluster consiste de um nó de cabeça ingressaram do Active Directory, executando o Windows Server e Microsoft HPC Pack e recursos que você especificar de computação de janelas adicionais. Se você quiser implantar um cluster de HPC Pack no Azure para cargas de trabalho do Linux, consulte [criar um cluster de HPC Linux com o script de implantação de HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Você também pode usar um modelo do Gerenciador de recursos do Azure para implantar um cluster de HPC Pack. Para obter exemplos, consulte [criar um cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) e [criar um cluster de HPC com um personalizado imagem nó de computação](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Arquivos de configuração de exemplo

Nos exemplos a seguir, substitua seus próprios valores para sua assinatura Id ou nome e os nomes de conta e serviço.

### <a name="example-1"></a>Exemplo 1

O arquivo de configuração a seguir implanta um cluster de HPC Pack que tem um nó principal com bancos de dados locais e cinco nós executando o sistema operacional Windows Server 2012 R2 de computadores. Todos os serviços de nuvem são criados diretamente no local Oeste EUA. O nó principal atua como controlador de domínio da floresta do domínio.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Exemplo 2

O arquivo de configuração a seguir implanta um cluster de HPC Pack em uma floresta de domínio existente. O cluster tem 1 nó principal com bancos de dados locais e 12 nós de computadores com a extensão de BGInfo VM aplicada.
Instalação automática de atualizações do Windows está desabilitada para todas as VMs da floresta de domínio. Todos os serviços de nuvem são criados diretamente no local da Ásia Oriental. Os nós de computação são criados em três serviços de nuvem e três contas de armazenamento: _MyHPCCN-0001_ a _MyHPCCN-0005_ no _MyHPCCNService01_ e _mycnstorage01_; _MyHPCCN-0006_ para _MyHPCCN0010_ em _MyHPCCNService02_ e _mycnstorage02_; e _MyHPCCN-0011_ para _MyHPCCN-0012_ no _MyHPCCNService03_ e _mycnstorage03_). Os nós de computação são criados a partir de uma imagem existente de particular capturada de um nó de computação. Auto ampliar ou reduzir o serviço está habilitado com padrão ampliadas e reduzidas intervalos.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Exemplo 3

O arquivo de configuração a seguir implanta um cluster de HPC Pack em uma floresta de domínio existente. O cluster contém um nó principal, servidor de um banco de dados com um disco de dados de 500 GB, 2 nós agente executando o sistema operacional Windows Server 2012 R2 e cinco nós de computação executando o sistema operacional Windows Server 2012 R2. O serviço de nuvem MyHPCCNService é criado no grupo afinidade *MyIBAffinityGroup*e os outros serviços de nuvem são criados no grupo de afinidade *MyAffinityGroup*. A API REST do HPC trabalho Agendador e o portal da web HPC estão habilitados no nó principal.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Exemplo 4

O arquivo de configuração a seguir implanta um cluster de HPC Pack em uma floresta de domínio existente. O cluster tem dois nó principal com bancos de dados locais, dois modelos de nó Azure são criados e três nós do tamanho médio Azure são criadas para o modelo de nó Azure _AzureTemplate1_. Um arquivo de script é executado no nó principal após o nó principal estiver configurado.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Solução de problemas


* **Erro "VNet não existe"** - se você executar o script para implantar vários clusters no Azure simultaneamente em uma assinatura, implantações de um ou mais podem falhar com o erro "VNet *VNet\_nome* não existe".
Se esse erro ocorrer, execute o script novamente para a implantação falha.

* **Problemas de acesso à Internet da rede virtual Azure** - se você cria um cluster com um novo controlador de domínio usando o script de implantação, ou você manualmente elevar um nó cabeça máquina virtual controlador de domínio, você pode enfrentar problemas de conexão VMs com a Internet. Esse problema pode ocorrer se um servidor DNS encaminhadores é configurado automaticamente no controlador de domínio e servidor encaminhadores DNS não for resolvido corretamente.

    Para contornar esse problema, faça logon no controlador de domínio e remova a configuração de encaminhadores ou configurar um servidor DNS encaminhadores válida. Para configurar essa configuração, no Gerenciador de servidores clique **Ferramentas** >
    **DNS** para abrir o Gerenciador de DNS e, em seguida, clique duas vezes em **encaminhadores**.

* **Problemas de acessar a rede RDMA de VMs pesados** - se você adicionar computação do Windows Server ou atuar como intermediário de nó VMs usando um tamanho habilitados para RDMA como A8 ou A9, você pode enfrentar problemas para se conectar a essas VMs à rede RDMA aplicativo. Um motivo que esse problema ocorre é se a extensão de HpcVmDrivers não está instalada corretamente quando as VMs são adicionadas ao cluster. Por exemplo, a extensão pode estar presas no estado de instalação.

    Para contornar esse problema, verifique primeiro o estado da extensão em VMs. Se a extensão não estiver instalada corretamente, tente remover os nós do cluster HPC e adicione os nós novamente. Por exemplo, você pode adicionar o nó de computação VMs executando o script HpcIaaSNode.ps1 adicionar no nó principal.
    
## <a name="next-steps"></a>Próximas etapas

* Tente executar uma carga de trabalho de teste no cluster. Por exemplo, consulte o [guia de Introdução](https://technet.microsoft.com/library/jj884144)de de HPC Pack.

* Para um tutorial para a implantação de cluster de script e executar uma carga de trabalho HPC, consulte [começar com um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](virtual-machines-windows-excel-cluster-hpcpack.md).

* Tente ferramentas do HPC Pack para iniciar, parar, adicionar e remover nós de computação de um cluster que você criar. Consulte [Gerenciar calcular nós em um cluster de HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Para configurar para enviar trabalhos ao cluster de um computador local, consulte [Enviar HPC trabalhos de um computador local para um cluster de HPC Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).
