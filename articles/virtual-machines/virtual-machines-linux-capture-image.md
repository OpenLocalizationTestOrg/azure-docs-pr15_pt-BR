<properties
    pageTitle="Capturar uma VM Linux para usar como um modelo | Microsoft Azure"
    description="Saiba como capturar e generalize uma imagem de uma baseados em Linux Azure máquina virtual (VM) criada com o modelo de implantação do Gerenciador de recursos do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="iainfou"/>


# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Capturar uma máquina virtual do Linux em execução no Azure

Siga as etapas neste artigo para generalizar e capture sua máquina virtual do Azure Linux (máquina virtual) no modelo de implantação do Gerenciador de recursos. Quando você generaliza a máquina virtual, você pode remove informações pessoais de conta e preparar a máquina virtual a ser usado como uma imagem. Em seguida, você captura uma imagem de generalizado do disco rígido virtual (VHD) para o sistema operacional, VHDs para discos de dados anexado e um [modelo do Gerenciador de recursos](../azure-resource-manager/resource-group-overview.md) para novas implantações de máquina virtual. 

Para criar VMs usando a imagem, configurar recursos de rede para cada nova VM e usar o modelo (um arquivo JavaScript Object Notation ou JSON,) para implantá-lo das imagens capturadas VHD. Dessa forma, você pode replicar uma máquina virtual com sua configuração atual de software, semelhante a forma que você usar imagens do Azure Marketplace.

>[AZURE.TIP]Se você quiser criar uma cópia da sua VM Linux existente com seu estado especializado para backup ou depuração, consulte [criar uma cópia de uma máquina virtual do Linux em execução no Azure](virtual-machines-linux-copy-vm.md). E se você deseja carregar um VHD Linux de uma máquina virtual local, consulte [carregar e criar uma VM Linux de imagem de disco personalizada](virtual-machines-linux-upload-vhd.md).  

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que você atenda aos seguintes pré-requisitos:

* **Máquina virtual do azure criado no modelo de implantação do Gerenciador de recursos** - se você ainda não tiver criado uma VM Linux, você pode usar o [portal](virtual-machines-linux-quick-create-portal.md), o [Azure CLI](virtual-machines-linux-quick-create-cli.md)ou [Gerenciador de recursos de modelos](virtual-machines-linux-cli-deploy-templates.md). 

    Configure a máquina virtual conforme necessário. Por exemplo, [Adicionar discos de dados](virtual-machines-linux-add-disk.md), aplicar atualizações e instalar aplicativos. 
* **Azure CLI** - instalar o [Azure CLI](../xplat-cli-install.md) em um computador local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Etapa 1: Remover o agente de Linux do Azure

Primeiro, execute o comando **waagent** com o parâmetro **deprovision** na VM Linux. Este comando exclui arquivos e dados para tornar a máquina virtual pronto para generalizar. Para obter detalhes, consulte o [guia de usuário do agente do Azure Linux](virtual-machines-linux-agent-user-guide.md).

1. Conecte a sua VM Linux usando um cliente SSH.

2. Na janela SSH, digite o seguinte comando:

    ```
    sudo waagent -deprovision+user
    ```
    >[AZURE.NOTE] Somente execute este comando em uma máquina virtual que você pretende capturar como uma imagem. Ele não garante que a imagem está desmarcada de todas as informações confidenciais ou é adequada para redistribuição.

3. Digite **y** para continuar. Você pode adicionar o **-Forçar** parâmetro para evitar essa etapa de confirmação.

4. Após o comando terminar, digite **Sair**. Esta etapa fecha o cliente SSH.

    
## <a name="step-2-capture-the-vm"></a>Etapa 2: Capturar a máquina virtual

Use a CLI do Azure para generalizar e capturar a máquina virtual. Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Exemplos de nomes de parâmetro incluem **myResourceGroup**, **myVnet**e **myVM**.

5. Em seu computador local, abra o Azure CLI e [login à sua assinatura do Azure](../xplat-cli-connect.md). 

6. Verifique se que você está no modo do Gerenciador de recursos.

    ```
    azure config mode arm
    ```

7. Desligue a máquina virtual que você já desprovisionada usando o seguinte comando:

    ```
    azure vm deallocate -g MyResourceGroup -n myVM
    ```

8. Generalize a máquina virtual com o seguinte comando:

    ```
    azure vm generalize -g MyResourceGroup -n myVM
    ```

9. Agora, execute o comando de **máquina virtual azure capturar** , que captura a máquina virtual. No exemplo a seguir, os VHDs de imagem são capturados com nomes que começam com **MyVHDNamePrefix**e a opção **-t** Especifica um caminho para o modelo **MyTemplate.json**. 

    ```
    azure vm capture MyResourceGroup MyResourceGroup MyVHDNamePrefix -t MyTemplate.json
    ```

    >[AZURE.IMPORTANT]Os arquivos de imagem VHD obtém criados por padrão na mesma conta de armazenamento que a máquina virtual original usada. Use a *mesma conta de armazenamento* para armazenar os VHDs para quaisquer novas VMs que você cria a partir da imagem. 

6. Para encontrar o local de uma imagem capturada, abra o modelo JSON em um editor de texto. No **storageProfile**, encontre o **uri** da **imagem** localizada no contêiner de **sistema** . Por exemplo, o URI da imagem de disco do sistema operacional é semelhante a`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Etapa 3: Criar uma máquina virtual da imagem capturada
Agora use a imagem com um modelo para criar uma VM Linux. Estas etapas mostram como usar a CLI do Azure e o modelo de arquivo JSON capturado para criar a máquina virtual em uma nova rede virtual.

### <a name="create-network-resources"></a>Criar recursos de rede

Para usar o modelo, primeiro é necessário configurar uma rede virtual e NIC para sua máquina virtual novo. Recomendamos que você crie um grupo de recursos para esses recursos no local onde a imagem de máquina virtual está armazenada. Execute comandos semelhante para os seguintes, substituir os nomes dos seus recursos e um local apropriado Azure ("centralus" nesses comandos):

    azure group create MyResourceGroup1 -l "centralus"

    azure network vnet create MyResourceGroup1 myVnet -l "centralus"

    azure network vnet subnet create MyResourceGroup1 myVnet mySubnet

    azure network public-ip create MyResourceGroup1 myIP -l "centralus"

    azure network nic create MyResourceGroup1 myNIC -k mySubnet -m myVnet -p myIP -l "centralus"

### <a name="get-the-id-of-the-nic"></a>Obter a Id da NIC

Para implantar uma máquina virtual da imagem usando o JSON salvos durante a captura, você precisa da identificação do NIC. Obtê-lo, execute o seguinte comando:

    azure network nic show MyResourceGroup1 myNIC

A **Id** na saída é semelhante a`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`



### <a name="create-a-vm"></a>Criar uma máquina virtual
Agora, execute o seguinte comando para criar sua máquina virtual da imagem capturada de máquina virtual. Use o parâmetro **-f** para especificar o caminho para o arquivo JSON modelo salvo.

    azure group deployment create MyResourceGroup1 MyDeployment -f MyTemplate.json

Na saída do comando, você será solicitado a fornecer um novo nome de máquina virtual, o nome de usuário de administrador e senha e a Id da NIC você criou anteriormente.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: myNewVM
    adminUserName: myAdminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic

O exemplo a seguir mostra o que você vê para uma implantação bem-sucedida:

    + Inicializar configurações de modelo e parâmetros
    + Criando uma informações de implantação: criado xxxxxxx de implantação de modelo
    + Aguardando implantação para concluir dados: DeploymentName: dados MyDeployment: ResourceGroupName: dados MyResourceGroup1: ProvisioningState: bem-sucedida dados: carimbo de hora: xxxxxxx dados: modo: dados incrementais: valor de tipo de nome


    data:    ------------------  ------------  -------------------------------------

    dados: vmName myNewVM de cadeia de caracteres


    dados: vmSize Standard_D1 de cadeia de caracteres


    dados: adminUserName myAdminuser de cadeia de caracteres


    dados: adminPassword SecureString indefinido


    dados: networkInterfaceId informações de /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic de cadeia de caracteres: implantação do grupo criar comando Okey

### <a name="verify-the-deployment"></a>Verifique se a implantação

Agora SSH na máquina virtual criados para verificar a implantação e começar a usar a máquina virtual novo. Para se conectar via SSH, localize o endereço IP da máquina virtual criado executando o seguinte comando:

    azure network public-ip show MyResourceGroup1 myIP

O endereço IP público está listado na saída do comando. Por padrão você se conectar à VM Linux por SSH na porta 22.

## <a name="create-additional-vms"></a>Criar VMs adicionais
Use a imagem capturada e o modelo para implantar VMs adicionais usando as etapas na seção anterior. Outras opções para criar VMs da imagem incluem usando um modelo de início rápido ou executando o comando **criar de máquina virtual azure** .

### <a name="use-the-captured-template"></a>Usar o modelo capturado

Para usar a imagem capturada e o modelo, siga estas etapas (detalhadas na seção anterior):

* Certifique-se de que sua imagem de máquina virtual está na mesma conta de armazenamento que hospeda VHD do sua máquina virtual.
* Copie o arquivo de modelo JSON e especifique um nome exclusivo para o disco de sistema operacional de VHD da máquina virtual novo (ou VHDs). Por exemplo, na **storageProfile**, sob **vhd**no **uri**, especifique um nome exclusivo para o **osDisk** VHD, semelhante a`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Crie uma NIC na mesma ou uma rede virtual diferente.
* Usando o arquivo do modelo modificado JSON, crie uma implantação no grupo de recursos no qual você configurou a rede virtual.

### <a name="use-a-quickstart-template"></a>Usar um modelo de início rápido

Se desejar que a rede configurar automaticamente quando você cria uma máquina virtual da imagem, você pode especificar esses recursos em um modelo. Por exemplo, consulte o [modelo 101 máquina virtual do usuário imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) do GitHub. Este modelo cria uma máquina virtual da sua imagem personalizada e necessária rede virtual, endereço IP público e recursos NIC. Para instruções passo a passo de como usar o modelo no portal do Azure, consulte [como criar uma máquina virtual de uma imagem personalizada usando um modelo do Gerenciador de recursos](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Use a máquina virtual azure comando create

Geralmente é mais fácil usar um modelo do Gerenciador de recursos para criar uma máquina virtual da imagem. No entanto, você pode criar na máquina virtual _imperativamente_ usando o comando **criar de máquina virtual azure** com o **-P** (**– imagem urn**) parâmetro. Se você usar esse método, você também passar o parâmetro **-d** (**vhd-disk--SO**) para especificar o local do arquivo. vhd SO para a máquina virtual novo. Esse arquivo deve estar no contêiner de vhds da conta de armazenamento onde o arquivo de VHD de imagem está armazenado. O comando copia o VHD para a máquina virtual novo automaticamente ao contêiner de **vhds** .

Antes de executar o comando **criar de máquina virtual azure** com a imagem, conclua as seguintes etapas:

1.  Criar um grupo de recursos ou identificar um grupo de recursos existentes para a implantação.

2.  Crie um recurso de endereço IP público e um recurso NIC para a máquina virtual novo. Para obter etapas criar uma rede virtual, endereço IP público e NIC usando a CLI, consulte anteriormente neste artigo. (**máquina virtual azure criar** também pode criar uma NIC, mas você precisa passar parâmetros adicionais para uma rede virtual e sub-rede.)


Em seguida, execute um comando que passa URIs para o novo arquivo de VHD do sistema operacional e a imagem existente. Neste exemplo, um tamanho de máquina virtual Standard_A1 é criada na região Leste EUA.

    azure vm create MyResourceGroup1 myNewVM eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u myAdminname -p myPassword -f myNIC

Opções de comando adicionais, executar `azure help vm create`.

## <a name="next-steps"></a>Próximas etapas

Para gerenciar suas VMs com a CLI, consulte as tarefas [implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de recursos do Azure e o Azure CLI](virtual-machines-linux-cli-deploy-templates.md).
