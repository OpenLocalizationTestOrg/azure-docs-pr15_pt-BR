<properties
 pageTitle="Gerenciar nós de computação de cluster HPC Pack | Microsoft Azure"
 description="Saiba mais sobre as ferramentas de script do PowerShell para adicionar, remover, iniciar e parar de nós de computação de cluster HPC Pack no Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Gerenciar o número e a disponibilidade de nós de computação em um cluster de HPC Pack no Azure

Se você criou um cluster de HPC Pack no Azure VMs, convém maneiras de adicionar, remover, inicie (Configurar) ou parar (deprovision) um número de computação nó VMs do cluster facilmente. Para executar essas tarefas, execute scripts do PowerShell do Azure que são instalados no nó principal máquina virtual. Esses scripts ajudarão-lo a controlar o número e a disponibilidade dos recursos cluster HPC Pack para que você pode controlar os custos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Pré-requisitos

* **Cluster de HPC Pack no Azure VMs** - criar um cluster de HPC Pack no modelo clássico de implantação usando pelo menos HPC Pack 2012 R2 Update 1. Por exemplo, você pode automatizar a implantação usando a imagem de máquina virtual do HPC Pack atual no mercado do Azure e um script do PowerShell do Azure. Para obter informações e os pré-requisitos, consulte [criar um Cluster de HPC com o script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

    Após a implantação, localizar o nó scripts de gerenciamento no % CCP\_pasta de compartimento % HOME no nó principal. Você deve executar cada um dos scripts como administrador.

* **Azure publicar certificado de arquivo ou gerenciamento de configurações** - você precisa fazer o seguinte no nó principal:

    * **Importar o Azure Publicar arquivo de configurações**. Para fazer isso, execute os seguintes cmdlets do PowerShell do Azure no nó principal:

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Configurar o certificado de gerenciamento Azure no nó principal**. Se você tiver o arquivo. cer, importe-o no repositório de certificados CurrentUser\My e execute o seguinte cmdlet do PowerShell do Azure para seu ambiente Azure (AzureCloud ou AzureChinaCloud):

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Adicionar nó de computação VMs

Adicione nós de computação com o script **Add-HpcIaaSNode.ps1** .

### <a name="syntax"></a>Sintaxe
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parâmetros

* **ServiceName** - nome da nuvem serviço novo nó computação VMs serão adicionadas ao.

* **ImageName** - nome de imagem de máquina virtual do Azure, que pode ser obtida por meio do Azure portal clássico ou o cmdlet do PowerShell do Azure **Get-AzureVMImage**. A imagem deve atender aos seguintes requisitos:

    1. Um sistema operacional Windows deve ser instalado.

    2. HPC Pack deve ser instalado na função de nó de computação.

    3. A imagem deve ser uma imagem particular na categoria de usuário, não uma imagem de máquina virtual do Azure pública.

* **Quantidade** - número de nó de computação VMs a ser adicionado.

* **InstanceSize** - tamanho do nó computação VMs.

* **DomainUserName** - nome de usuário de domínio, que será usado para associar as novas VMs ao domínio.

* **DomainUserPassword** - senha do usuário do domínio.

* **NodeNameSeries** (opcional) - padrão de nomenclatura para os nós de computação. O formato deve ser &lt; *raiz\_nome*&gt;&lt;*Iniciar\_número*&gt;%. Por exemplo, MyCN % 10% significa uma série de nomes de nó computação iniciando de MyCN11. Se não especificado, o script usa o nó configurado série no cluster HPC de nomenclatura.

### <a name="example"></a>Exemplo

O exemplo a seguir adiciona nó de computação grandes 20 size VMs na nuvem serviço *hpcservice1*, com base na imagem de máquina virtual *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Remover o nó de computação VMs

Remova nós de computação com o script **HpcIaaSNode.ps1 remover** .

### <a name="syntax"></a>Sintaxe

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parâmetros

* **Nome** - nomes de nós de cluster a ser removido. Caracteres curinga é suportada. O nome do conjunto de parâmetro é o nome. Você não pode especificar o **nome** e o **nó** parâmetros.

* **Nó** - HpcNode o objeto para os nós sejam removidas, que pode ser obtido pelo cmdlet do PowerShell do HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetro é nó. Você não pode especificar o **nome** e o **nó** parâmetros.

* **DeleteVHD** (opcional) - configuração para excluir os discos associados para as VMs que são removidas.

* **Forçar** (opcional) - configuração para forçar nós HPC offline antes de removê-las.

* **Confirmar** (opcional) - solicitar confirmação antes de executar o comando.

* **WhatIf** - configuração para descrever o que acontecerá se você executou o comando sem realmente executar o comando.

### <a name="example"></a>Exemplo

O exemplo a seguir força nós off-line com nomes começando *HPCNode-CN -* e elas remove os nós e seus discos associados.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Iniciar o nó de computação VMs

Inicie o nós de computação com o script **HpcIaaSNode.ps1 iniciar** .

### <a name="syntax"></a>Sintaxe

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parâmetros

* **Nome** - nomes de nós do cluster para ser iniciado. Caracteres curinga é suportada. O nome do conjunto de parâmetro é o nome. Você não pode especificar o **nome** e o **nó** parâmetros.

* **Nó**- HpcNode o objeto para os nós a ser iniciado, que pode ser obtido pelo cmdlet do PowerShell do HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetro é nó. Você não pode especificar o **nome** e o **nó** parâmetros.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia nós com nomes que começam *HPCNode-CN -*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Interromper o nó de computação VMs

Pare de nós de computação com o script **HpcIaaSNode.ps1 parar** .

### <a name="syntax"></a>Sintaxe

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parâmetros


* **Nome**- nomes de nós de cluster seja interrompido. Caracteres curinga é suportada. O nome do conjunto de parâmetro é o nome. Você não pode especificar o **nome** e o **nó** parâmetros.

* **Nó** - HpcNode o objeto para os nós a ser interrompido, que pode ser obtido pelo cmdlet do PowerShell do HPC [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). O nome do conjunto de parâmetro é nó. Você não pode especificar o **nome** e o **nó** parâmetros.

* **Forçar** (opcional) - configuração para forçar nós HPC offline antes de interrompê-los.

### <a name="example"></a>Exemplo

O exemplo a seguir força nós off-line com nomes começando *HPCNode-CN -* e, em seguida, interrompe os nós.

Parar-HPCIaaSNode.ps1 – nome HPCNodeCN-*-forçar

## <a name="next-steps"></a>Próximas etapas

* Se você quiser uma forma automaticamente aumente ou diminua nós do cluster de acordo com a carga de trabalho atual de trabalhos e tarefas no cluster, consulte [automaticamente ampliar ou reduzir os recursos de cluster HPC Pack no Azure de acordo com a carga de trabalho do cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).
