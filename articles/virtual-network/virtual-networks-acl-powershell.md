<properties
   pageTitle="Como gerenciar listas de controle de acesso (ACLs) para pontos de extremidade usando o PowerShell"
   description="Saiba como gerenciar ACLs com o PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-manage-access-control-lists-acls-for-endpoints-by-using-powershell"></a>Como gerenciar listas de controle de acesso (ACLs) para pontos de extremidade usando o PowerShell

Você pode criar e gerenciar rede controle listas de acesso (ACLs) para pontos de extremidade usando o PowerShell do Azure ou no Portal de gerenciamento. Neste tópico, você encontrará procedimentos para tarefas comuns de ACL que podem ser concluídas usando o PowerShell. Para a lista de cmdlets do PowerShell do Azure consulte [Cmdlets de gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Para obter mais informações sobre as ACLs, consulte [o que é uma lista de controle de acesso (ACL) da rede?](virtual-networks-acl.md). Se você quiser gerenciar seus ACLs usando o Portal de gerenciamento, veja [como configurar o pontos de extremidade para uma máquina Virtual](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gerenciar ACLs de rede usando o PowerShell do Azure

Você pode usar cmdlets do PowerShell do Azure para criar, remover e configurar (definido) rede controle listas de acesso (ACLs). Há alguns exemplos de algumas das maneiras como você pode configurar uma ACL usando o PowerShell.

Para recuperar uma lista completa de cmdlets do PowerShell de ACL, você pode usar um destes procedimentos:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Criar uma ACL de rede com regras que permitem o acesso de uma sub-rede remota

O exemplo a seguir ilustra uma maneira de criar uma nova ACL que contém regras. Essa ACL é aplicado a um ponto de extremidade de máquina virtual. As regras ACL no exemplo a seguir permite o acesso de uma sub-rede remota. Para criar uma nova ACL de rede com regras de permissão para uma sub-rede remota, abra um Azure PowerShell ISE. Copie e cole o script abaixo, configurando o script com seus próprios valores e, em seguida, execute o script.

1. Crie um novo objeto ACL de rede.

        $acl1 = New-AzureAclConfig

1. Defina uma regra que permite o acesso de uma sub-rede remota. No exemplo abaixo, você pode definir regra *100* (que tem prioridade sobre regra 200 e superior) para permitir o acesso de *10.0.0.0/8* sub-rede remota para o ponto de extremidade de máquina virtual. Substitua os valores com seus próprios requisitos de configuração. O nome "SharePoint ACL config" deve ser substituído com o nome amigável que você deseja chamar esta regra.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"

1. Para regras adicionais, repita o cmdlet, substituindo os valores com seus próprios requisitos de configuração. Certifique-se de alterar a regra número pedido para refletir a ordem na qual deseja que as regras a serem aplicadas. O menor número de regra tem precedência sobre o número mais alto.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"

1. Em seguida, você pode criar uma nova empresa (Adicionar) ou definir a ACL para um ponto de extremidade existente (conjunto). Neste exemplo, podemos adicionará um novo ponto de extremidade de máquina virtual chamado "web" e atualizar o ponto de extremidade de máquina virtual com as configurações de ACL.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM

1. Em seguida, combinar os cmdlets e execute o script. Neste exemplo, os cmdlets combinados teria esta aparência:

        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Remover uma regra de ACL de rede que permite o acesso de uma sub-rede remota

O exemplo a seguir ilustra uma maneira de remover uma regra de ACL de rede.  Para remover uma regra de ACL de rede com regras de permissão para uma sub-rede remota, abra um Azure PowerShell ISE. Copie e cole o script abaixo, configurando o script com seus próprios valores e, em seguida, execute o script.

1. Primeira etapa é obter o objeto de ACL de rede para o ponto de extremidade de máquina virtual. Você vai remova a regra ACL. Nesse caso, estamos removendo-la por ID da regra. Isso removerá somente a identificação da regra 0 da ACL. Ele não remove o objeto ACL do ponto de extremidade de máquina virtual.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. Em seguida, você deve aplicar o objeto de ACL de rede para o ponto de extremidade de máquina virtual e atualizar a máquina virtual.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Remover uma ACL de rede de um ponto de extremidade de máquina virtual

Em determinados cenários, talvez você queira remover um objeto de ACL de rede de um ponto de extremidade de máquina virtual. Para fazer isso, abra um Azure PowerShell ISE. Copie e cole o script abaixo, configurando o script com seus próprios valores e, em seguida, execute o script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Próximas etapas

[O que é uma lista de controle de acesso (ACL) da rede?](virtual-networks-acl.md)
