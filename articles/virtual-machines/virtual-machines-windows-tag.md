<properties
   pageTitle="Como marcar uma máquina virtual | Microsoft Azure"
   description="Saiba mais sobre uma máquina virtual do Windows criada no Azure usando o modelo de implantação do Gerenciador de recursos de marcação"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como marcar uma máquina virtual Windows no Azure


Este artigo descreve maneiras diferentes para marcar uma máquina virtual Windows no Azure por meio do modelo de implantação do Gerenciador de recursos. Marcas são pares de chave/valor definido pelo usuário que podem ser colocados diretamente em um recurso ou um grupo de recursos. Azure atualmente suporta até 15 marcas por recurso e de grupo de recursos. Marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que marcas têm suporte para recursos criados por meio do modelo de implantação do Gerenciador de recursos somente. Se você deseja marcar uma máquina virtual Linux, consulte [como marcar uma máquina virtual de Linux no Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com o PowerShell

Para criar, adicionar e excluir marcas através do PowerShell, será necessário configurar seu [ambiente do PowerShell com o Gerenciador de recursos do Azure][]. Quando tiver concluído a instalação, você pode colocar marcas recursos de computação, rede e armazenamento na criação ou depois que o recurso é criado por meio do PowerShell. Este artigo se concentrará em marcas de exibição/edição colocadas em máquinas virtuais.

Primeiro, navegar para uma máquina Virtual por meio do `Get-AzureRmVM` cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se sua máquina Virtual já contiver marcas, você verá todas as marcas em seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se você gostaria de adicionar marcas através do PowerShell, você pode usar o `Set-AzureRmResource` comando. Observação ao atualizar marcas através do PowerShell, marcas são atualizadas como um todo. Portanto se você estiver adicionando uma marca a um recurso que já tenha marcas, você precisará incluir todas as marcas que você deseja ser colocado no recurso. Abaixo está um exemplo de como adicionar marcas adicionais para um recurso por meio de Cmdlets do PowerShell.

Esse cmdlet primeiro define todas as marcas colocadas na *MyTestVM* à variável *$tags* , usando o `Get-AzureRmResource` e `Tags` propriedade.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

O segundo comando exibe as marcas para a variável determinada.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

O terceiro comando adiciona uma marca adicional para a variável *$tags* . Observe o uso da **+=** acrescentar o novo par de chave/valor para a lista de *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

O quarto comando define todas as marcas definidas na variável de *$tags* ao recurso determinado. Nesse caso, é MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

O comando quinto exibe todas as marcas no recurso. Como você pode ver, *localização* é agora definido como uma marca com *MyLocation* como o valor.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

Para saber mais sobre a marcação através do PowerShell, confira os [Cmdlets de recurso do Azure][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a marcação de seus recursos Azure, consulte [Visão geral do Gerenciador de recursos do Azure][] e [Usando marcas para organizar seus recursos do Azure][].
* Para ver como marcas podem ajudá-lo a gerenciar o uso dos recursos do Azure, consulte [Noções básicas sobre sua fatura do Azure][] e [obtenha ideias para seu consumo de recursos do Microsoft Azure][].

[Ambiente do PowerShell com o Gerenciador de recursos do Azure]: ../powershell-azure-resource-manager.md
[Cmdlets de recurso Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Visão geral do Gerenciador de recursos do Azure]: ../azure-resource-manager/resource-group-overview.md
[Usando marcas para organizar seus recursos do Azure]: ../resource-group-using-tags.md
[Noções básicas sobre sua fatura Azure]: ../billing/billing-understand-your-bill.md
[Obter ideias para seu consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md
