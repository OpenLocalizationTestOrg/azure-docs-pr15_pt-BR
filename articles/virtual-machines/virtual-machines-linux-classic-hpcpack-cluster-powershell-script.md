<properties
   pageTitle="Script do PowerShell para implantar Linux HPC cluster | Microsoft Azure"
   description="Executar um script PowerShell para implantar um cluster de Linux HPC Pack em Azure máquinas virtuais"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Criar um Linux computação de alto desempenho cluster (HPC) com o script de implantação de HPC Pack IaaS

Execute a implantação HPC Pack IaaS script do PowerShell para implantar um cluster HPC concluído para cargas de trabalho de Linux em Azure máquinas virtuais. O cluster consiste em um nó de cabeça ingressaram do Active Directory, executando o Windows Server e Microsoft HPC Pack e nós de computação que execute uma das distribuições Linux suportadas pelo HPC Pack. Se você quiser implantar um cluster de HPC Pack em cargas de trabalho do Azure para Windows, consulte [criar um cluster de HPC do Windows com o script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). Você também pode usar um modelo do Gerenciador de recursos do Azure para implantar um cluster de HPC Pack. Por exemplo, consulte [criar um cluster de HPC com Linux nós de computadores](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Arquivo de configuração de exemplo

O arquivo de configuração a seguir cria um novo controlador de domínio e floresta de domínio e implanta um cluster de HPC Pack que tem 1 nó principal com bancos de dados locais e 10 nós de computação Linux. Todos os serviços de nuvem são criados diretamente no local da Ásia Oriental. Os nós de computação Linux são criados no 2 serviços de nuvem e 2 contas de armazenamento (ou seja, _MyLnxCN-0001_ para _MyLnxCN-0005_ no _MyLnxCNService01_ e _mylnxstorage01_) e _MyLnxCN-0006_ para _MyLnxCN-0010_ no _MyLnxCNService02_ e _mylnxstorage02_. Os nós de computação são criados a partir de uma imagem de Linux OpenLogic CentOS versão 7.0. 

Substituir por seus próprios valores para o seu nome de assinatura e os nomes de conta e serviço.

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Solução de problemas

* **Erro "VNet não existe"** - se você executar o script de implantação de HPC Pack IaaS para implantar vários clusters no Azure simultaneamente em uma assinatura, implantações de um ou mais podem falhar com o erro "VNet *VNet\_nome* não existe".
Se esse erro ocorrer, execute novamente o script para a implantação falha.

* **Problemas de acesso à Internet da rede virtual Azure** - se você cria um cluster de HPC Pack com um novo controlador de domínio usando o script de implantação, ou você manualmente elevar um nó cabeça máquina virtual controlador de domínio, você pode enfrentar problemas de conexão VMs da rede virtual Azure com a Internet. Isso pode ocorrer se um servidor do encaminhadores DNS é automaticamente configurado no controlador de domínio e servidor encaminhadores DNS não for resolvido corretamente.

    Para contornar esse problema, faça logon no controlador de domínio e remova a configuração de encaminhadores ou configurar um servidor DNS encaminhadores válida. Para fazer isso, no Gerenciador de servidores clique em **Ferramentas** >
    **DNS** para abrir o Gerenciador de DNS e, em seguida, clique duas vezes em **encaminhadores**.
    
## <a name="next-steps"></a>Próximas etapas

* Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md) para obter informações sobre distribuições Linux suportadas, movendo dados e envio de trabalhos a um cluster de HPC Pack com Linux nós de computadores.
* Para tutoriais que usam o script para criar um cluster e executar uma carga de trabalho Linux HPC, consulte:
    * [Executar NAMD com Microsoft HPC Pack em nós de computação Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Executar OpenFOAM com Microsoft HPC Pack em nós de computação Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [Executar ESTRELA-CCM + com Microsoft HPC Pack no Linux calcular nós no Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)
