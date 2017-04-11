<properties
    pageTitle="Criar uma cópia da sua máquina virtual Linux do Azure | Microsoft Azure"
    description="Saiba como criar uma cópia da máquina virtual do Azure Linux no modelo de implantação do Gerenciador de recursos"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>Criar uma cópia de uma máquina virtual do Linux em execução no Azure


Este artigo mostra como criar uma cópia da sua máquina virtual Azure (máquina virtual) executando Linux usando o modelo de implantação do Gerenciador de recursos. Primeiro você copia o sistema operacional e discos de dados para um novo recipiente, e em seguida, configure os recursos de rede e cria a nova máquina virtual.

Você também pode [carregar e criar uma máquina virtual da imagem de disco personalizada](virtual-machines-linux-upload-vhd.md).


## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que você atenda aos seguintes pré-requisitos antes de iniciar as etapas:

- Você tem a [CLI do Azure] (... / xplat-cli-install.md) baixadas e instaladas no computador. 

- Você também precisa algumas informações sobre sua máquina virtual Linux de Azure existente:

| Informações de fonte de máquina virtual | Onde obtê-lo |
|------------|-----------------|
| Nome de máquina virtual | `azure vm list` |
| Nome do grupo de recursos | `azure vm list` |
| Local | `azure vm list` |
| Nome da conta de armazenamento | `azure storage account list -g <resourceGroup>` |
| Nome de contêiner | `azure storage container list -a <sourcestorageaccountname>` |
| Nome do arquivo de VM VHD de origem | `azure storage blob list --container <containerName>` |



- Você precisará fazer algumas opções sobre sua máquina virtual novo:   <br> -Nome contêiner   <br> Nome - máquina virtual   <br> Tamanho - máquina virtual   <br> nome - vNet   <br> -Nome sub-rede   <br> Nome - IP   <br> Nome - NIC
    

## <a name="login-and-set-your-subscription"></a>Logon e configurar sua assinatura

1. Faça login CLI.
        
        azure login

2. Verifique se que você está no modo do Gerenciador de recursos.
    
        azure config mode arm

3. Defina a assinatura correta. Você pode usar 'lista de conta do Microsoft azure' para ver todas as suas assinaturas.

        azure account set <SubscriptionId>



## <a name="stop-the-vm"></a>Parar a máquina virtual 

Parar e desalocar a fonte de máquina virtual. Você pode usar 'lista de máquina virtual azure' para obter uma lista de todas as VMs em sua assinatura e seus recursos nomes de grupo.
    
        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## <a name="copy-the-vhd"></a>Copie o VHD


Você pode copiar o VHD do armazenamento de origem para o destino usando o `azure storage blob copy start`. Neste exemplo, vamos copiar o VHD para a mesma conta de armazenamento, mas um contêiner diferente.

Para copiar o VHD para outro contêiner na mesma conta de armazenamento, digite:

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
        

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurar a rede virtual para sua nova VM

Configure uma rede virtual e NIC para sua máquina virtual novo. 

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## <a name="create-the-new-vm"></a>Criar a máquina virtual novo 

Agora você pode criar uma máquina virtual do seu disco virtual carregados [usando um modelo do Gerenciador de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou através da CLI, especificando o URI no disco copiado digitando:

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## <a name="next-steps"></a>Próximas etapas

Para saber como usar o Azure CLI para gerenciar sua nova máquina virtual, consulte [Azure comandos para o Gerenciador de recursos do Azure](azure-cli-arm-commands.md).
