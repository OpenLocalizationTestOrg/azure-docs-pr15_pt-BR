<properties
    pageTitle="Implantar um aplicativo em conjuntos de escala de máquina virtual | Microsoft Azure"
    description="Implantar um aplicativo em conjuntos de escala de máquina virtual"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>


# <a name="upgrade-a-virtual-machine-scale-set"></a>Atualizar um conjunto de escala de máquina virtual

Este artigo descreve como você pode reverter uma atualização de sistema operacional para uma escala de Azure máquina virtual definir sem qualquer tempo de inatividade. Nesse contexto, uma atualização do sistema operacional envolve alterando a versão ou SKU do sistema operacional ou alterando o URI de uma imagem personalizada. Atualizando sem inatividade significa atualizando máquinas virtuais um por vez ou em grupos (como um domínio de falhas por vez) em vez de uma só vez. Por isso, qualquer máquinas virtuais que não estão sendo atualizadas pode manter em execução.

Para evitar ambiguidade, vamos distinguir três tipos de atualização de sistema operacional, que talvez você queira executar:

- Alterando a versão ou SKU de uma imagem de plataforma. Por exemplo, alterando Ubuntu versão 14.04.2-LTS de 14.04.201506100 14.04.201507060, ou alterar as Ubuntu 15.10/últimas SKU para 16.04.0-LTS/latest. Este cenário é descrito neste artigo.

- Alterar o URI que aponta para uma nova versão de uma imagem personalizada criada (**Propriedades** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **imagem** > **uri**). Este cenário é descrito neste artigo.

- O sistema operacional de dentro de uma máquina virtual de patch (isso exemplos instalando um patch de segurança e executando o Windows Update). Este cenário é aceito, mas não descrito neste artigo.

As duas primeiras opções são suportados requisitos cobertos por este artigo. Você precisará criar uma nova escala definir para executar a terceira opção.

Conjuntos de escala de máquina virtual que são implantados como parte de um cluster de [Estrutura de serviço do Azure](https://azure.microsoft.com/services/service-fabric/) não são abordados aqui.

A sequência de básica para alterar o sistema operacional versão/SKU de uma imagem de plataforma ou o URI de uma imagem personalizada fica assim:

1. Obtenha o modelo de conjunto de escala de máquina virtual.

2. Alterar a versão, SKU ou URI valor no modelo.

3. Atualize o modelo.

4. Fazer uma chamada de *manualUpgrade* nas máquinas virtuais do conjunto de escala. Esta etapa só é relevante se *upgradePolicy* está definido como **Manual** no seu conjunto de escala. Se ele estiver definido como **automático**, todas as máquinas virtuais são atualizadas ao mesmo tempo, fazendo com que o tempo de inatividade.


Com essas informações de plano de fundo em mente, vamos ver como você pode atualizar a versão de uma escala definidas no PowerShell e usando a API REST. Esses exemplos cobrir o caso de uma imagem de plataforma, mas este artigo fornece informações suficientes para adaptar esse processo para uma imagem personalizada.

## <a name="powershell"></a>PowerShell##

Este exemplo atualiza uma escala de máquina virtual do Windows definida para a nova versão 4.0.20160229. Depois de atualizar o modelo, ele faz uma atualização de uma instância de máquina virtual por vez.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Se você estiver atualizando o URI para uma imagem personalizada em vez de alterar uma versão de imagem de plataforma, substitua a linha "definir a nova versão" algo parecido com o seguinte:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>A API REST

Aqui estão alguns exemplos de Python que usam a API REST Azure para implementar uma atualização de versão do sistema operacional. Ambos usam a biblioteca de leve [azurerm](https://pypi.python.org/pypi/azurerm) das funções de embalagem da API do Azure REST para obter um registro no modelo do conjunto de escala, seguido por um colocar com um modelo atualizado. Eles também examinar modos de exibição de instâncias de máquina virtual para identificar as máquinas virtuais por domínio de atualização.

### <a name="vmssupgrade"></a>Vmssupgrade

 [Vmssupgrade](https://github.com/gbowerman/vmsstools) é um script de Python que é usado para distribuir uma atualização de SO para uma escala de máquina virtual em execução definir um domínio de atualização de cada vez.

![Script de Vmssupgrade para escolher máquinas virtuais ou um domínio de atualização](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Esse script permite que você escolha máquinas virtuais específicas para atualizar ou especificar um domínio de atualização. Ele dá suporte a alteração de uma versão de imagem de plataforma ou alterando o URI de uma imagem personalizada.

### <a name="vmsseditor"></a>Vmsseditor

[Vmsseditor](https://github.com/gbowerman/vmssdashboard) é um editor de uso geral para conjuntos de escala de máquina virtual que mostra o status da máquina virtual como um heatmap onde uma linha representa um domínio de atualização. Entre outras coisas, você pode atualizar o modelo para um conjunto de escala com uma nova versão, SKU ou imagem personalizada URI e escolha domínios de falha de atualização. Quando você fizer isso, todas as máquinas virtuais nesse domínio atualização são atualizadas para o novo modelo. Como alternativa, você pode fazer uma atualização sem interrupção com base no tamanho do lote de sua escolha.  

A captura de tela a seguir mostra um modelo de uma escala definido para Ubuntu 14.04-2LTS versão 14.04.201507060. Muito mais opções foram adicionadas a esta ferramenta desde esta captura de tela.

![Modelo de Vmsseditor de uma escala definido para Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Depois de clicar em **Atualizar** e **Obter detalhes**, máquinas virtuais em UD 0 comece a atualizar.

![Atualização de mostrando de Vmsseditor em andamento](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)
