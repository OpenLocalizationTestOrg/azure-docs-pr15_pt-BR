<properties
   pageTitle="Gerenciar as zonas DNS usando o PowerShell | Microsoft Azure"
   description="Você pode gerenciar zonas DNS usando o Powershell do Azure. Como atualizar, excluir e criar zonas DNS no DNS do Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="how-to-manage-dns-zones-using-powershell"></a>Como gerenciar zonas DNS usando o PowerShell

> [AZURE.SELECTOR]
- [CLI Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



Este artigo mostrará como gerenciar sua zona DNS usando o PowerShell. Para poder usar essas etapas, você precisará instalar a versão mais recente do Azure Gerenciador de recursos de cmdlets do PowerShell (1.0 ou posteriores). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.


## <a name="create-a-new-dns-zone"></a>Criar uma nova zona DNS

Para criar uma zona DNS, consulte [criar uma zona DNS usando o PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Obtenha uma zona DNS

Para recuperar uma zona DNS, use o `Get-AzureRmDnsZone` cmdlet. Esta operação retorna um objeto de zona DNS correspondente a uma zona existente no Azure DNS. O objeto contém dados sobre a zona (como o número de conjuntos de registro), mas não contém os conjuntos de registro próprios.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## <a name="list-dns-zones"></a>Zonas DNS de lista

Omitindo o nome da zona de `Get-AzureRmDnsZone`, você pode enumerar todas as regiões em um grupo de recursos. Esta operação retorna uma matriz de objetos de zona.

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## <a name="update-a-dns-zone"></a>Atualizar uma zona DNS

Podem ser feitas alterações a um recurso de zona DNS usando `Set-AzureRmDnsZone`. Isso não atualizar qualquer um dos conjuntos de registro de DNS dentro da zona (consulte [como gerenciar DNS registros](dns-operations-recordsets.md)). Ele só é usado para atualizar propriedades do recurso zona em si. Isso é atualmente limitado ao Gerenciador de recursos de Azure 'tags' para o recurso de zona. Para obter mais informações, consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) .

Use uma destas duas maneiras de zona DNS de atualização:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Especificar a zona usando o grupo de recursos e de nome de zona

    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### <a name="specify-the-zone-using-a-zone-object"></a>Especificar a zona usando um objeto $zone

Especificar a zona usando um objeto de $zone de `Get-AzureRmDnsZone`. Ao usar `Set-AzureRmDnsZone` com um objeto $zone, verificações de Etag serão usadas para garantir que alterações simultâneas não são substituídas. Você pode usar o opcional *-Substituir* alternar suprima essas verificações. Para obter mais informações, consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) .


    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## <a name="delete-a-dns-zone"></a>Excluir uma zona de DNS

Zonas DNS podem ser excluídas usando o cmdlet Remove-AzureRmDnsZone.

Antes de excluir uma zona DNS no DNS do Azure, você precisará excluir todos os conjuntos de registros, exceto para os registros NS e SOA na raiz da zona que foram criados automaticamente quando a zona foi criada.

Use uma das duas maneiras a seguir para remover uma zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Especificar a zona usando o nome de zona e o nome do grupo de recursos

Esta operação tem um recurso opcional *-força* switch que elimina o prompt para confirmar que você deseja remover a zona DNS.

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

### <a name="specify-the-zone-using-a-zone-object"></a>Especificar a zona usando um objeto $zone

Especificar a zona usando um objeto de $zone de `Get-AzureRmDnsZone`. Esta operação tem um recurso opcional *-força* switch que elimina o prompt para confirmar que você deseja remover a zona DNS. Como com `Set-AzureRmDnsZone`, especificando a zona usando um objeto de $zone habilita verificações de Etag garantir que alterações simultâneas não são excluídas. <BR>
Opcional *-Substituir* sinalizador elimina essas verificações. Para obter mais informações, consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) .

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



O objeto de zona também pode ser usado em vez de estão sendo passados como um parâmetro:

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## <a name="next-steps"></a>Próximas etapas

Depois de criar uma zona DNS, crie [conjuntos de registro e registros](dns-getstarted-create-recordset.md) para iniciar a resolução de nomes do seu domínio da Internet.