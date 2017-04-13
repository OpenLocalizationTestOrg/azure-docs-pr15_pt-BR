<properties
    pageTitle="Criar uma cópia de uma máquina virtual especializada no Azure | Microsoft Azure"
    description="Saiba como criar uma cópia de uma VM especializados do Windows em execução no Azure, no modelo de implantação do Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Criar uma cópia de uma VM especializados do Windows em execução no Azure 

Este artigo mostra como usar a ferramenta de AZCopy para criar uma cópia do VHD de uma máquina virtual Windows especializados que está em execução no Azure. Em seguida, você pode usar a cópia do VHD para criar uma nova VM. 

- Se quiser copiar uma máquina virtual generalizada, veja [como criar uma imagem de máquina virtual de uma VM Azure generalizado existente](virtual-machines-windows-capture-image.md).

- Se você deseja carregar um VHD de uma máquina virtual local, como um criado usando o Hyper-V, consulte [carregar um VHD do Windows de uma máquina virtual de locais no Azure](virtual-machines-windows-upload-image.md).


## <a name="before-you-begin"></a>Antes de começar

Certifique-se que você:

- Ter informações sobre as **contas de armazenamento de origem e destino**. Para a fonte de máquina virtual, você precisa nomes de conta e o contêiner de armazenamento. Geralmente, o nome do contêiner será **vhds**. Você também precisa ter uma conta de armazenamento de destino. Se você ainda não tiver um, você pode criar um usando o portal do (**Mais serviços** > contas de armazenamento > Adicionar) ou usando o cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) . 

- Tenham Azure [PowerShell 1.0](../powershell-install-configure.md) (ou posterior) instalado.

- Baixar e instalar a [ferramenta de AzCopy](../storage/storage-use-azcopy.md). 


## <a name="deallocate-the-vm"></a>Desalocar a máquina virtual

Desalocar VM, o que libera o VHD sejam copiados. 

- **Portal**: clique em **máquinas virtuais** > **myVM** > parar
- **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` desaloca a máquina virtual chamada **myVM** no grupo de recursos **myResourceGroup**.

O **Status** para a máquina virtual no portal do Azure muda de **Parar** para **parado (desalocado)**.


## <a name="get-the-storage-account-urls"></a>Obtenha as URLs de conta de armazenamento

Você precisa as URLs das contas de armazenamento de origem e destino. A aparência de URLs: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se você já souber o nome de conta e o contêiner de armazenamento, você pode substituir apenas as informações entre os colchetes para criar sua URL. 

Você pode usar o portal do Azure ou Azure Powershell para obter a URL:

- **Portal**: clique em **mais serviços** > **contas de armazenamento**  >  <storage account> **Blobs** e seu arquivo de VHD de origem é provavelmente no contêiner de **vhds** . Clique em **Propriedades** para o contêiner e copie o texto rotulado **URL**. Você precisará as URLs dos contêineres de origem e de destino. 

- **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` obtém as informações para máquina virtual nomeado **myVM** no grupo do recurso **myResourceGroup**. Nos resultados, examine a seção de **perfil de armazenamento** para o **Uri de Vhd**. A primeira parte do Uri é a URL para o contêiner e a última parte é o nome do sistema operacional VHD para a máquina virtual.

## <a name="get-the-storage-access-keys"></a>Obtenha as teclas de acesso de armazenamento

Obter as teclas de acesso de origem e destino contas de armazenamento. Para obter mais informações sobre as teclas de acesso, consulte [contas de armazenamento do Azure sobre](../storage/storage-create-storage-account.md).

- **Portal**: clique em **mais serviços** > **contas de armazenamento**  >  <storage account> **Todas as configurações** > **teclas de acesso**. Copie a chave rotulada como **CHAVE1**.
- **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` obtém a chave de armazenamento para a conta de armazenamento **mystorageaccount** no grupo do recurso **myResourceGroup**. Copie a chave rotulada como **CHAVE1**.


## <a name="copy-the-vhd"></a>Copie o VHD 

Você pode copiar arquivos entre contas de armazenamento usando AzCopy. Para o recipiente de destino, se o contêiner especificado não existir, ele será criado para você. 

Para usar AzCopy, abra um prompt de comando no computador local e navegue até a pasta onde o AzCopy está instalado. Ele será semelhante ao *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os arquivos em um contêiner, você usar a opção **/S** . Isso pode ser usado para copiar o VHD do sistema operacional e todos os discos de dados se estiverem no mesmo contêiner. Este exemplo mostra como copiar todos os arquivos no contêiner **mysourcecontainer** na conta de armazenamento **mysourcestorageaccount** para o contêiner **mydestinationcontainer** na conta de armazenamento **mydestinationstorageaccount** . Substitua os nomes das contas de armazenamento e contêineres seu próprio. Substituir `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` com suas próprias chaves.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se desejar copiar um VHD específico em um contêiner com vários arquivos, você também pode especificar o nome de arquivo usando a opção /Pattern. Neste exemplo, somente o arquivo chamado **myFileName.vhd** será copiado.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
        /Pattern:myFileName.vhd
```


Quando ele for concluído, você receberá uma mensagem parecida com:

```
  Finished 2 of total 2 file(s).
  [2016/10/07 17:37:41] Transfer summary:
  -----------------
  Total files transferred: 2
  Transfer successfully:   2
  Transfer skipped:        0
  Transfer failed:         0
  Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Solução de problemas

- Quando você usa AZCopy, se você vir o erro "Falha ao autenticar a solicitação de servidor. Verifique se que o valor de cabeçalho de autorização é formado corretamente, incluindo a assinatura." e você estiver usando chave 2 ou a chave de armazenamento secundário, tente usar a chave primária ou 1º de armazenamento.


## <a name="next-steps"></a>Próximas etapas

- Você pode criar uma nova VM anexando [a cópia do VHD para uma máquina virtual como um disco do sistema operacional](virtual-machines-windows-create-vm-specialized.md).












