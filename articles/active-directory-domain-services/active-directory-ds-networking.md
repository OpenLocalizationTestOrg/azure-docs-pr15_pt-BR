<properties
    pageTitle="Serviços de domínio do Azure AD: Diretrizes de rede | Microsoft Azure"
    description="Considerações de rede para Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="maheshu"/>

# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considerações de rede para serviços de domínio do Azure AD

## <a name="how-to-select-an-azure-virtual-network"></a>Como selecionar uma rede virtual Azure
As diretrizes a seguir o ajudarão a selecionar uma rede virtual para usar com serviços de domínio do Azure AD.

### <a name="type-of-azure-virtual-network"></a>Tipo de rede virtual Azure

- Você pode habilitar serviços de domínio do Azure AD em uma rede de virtual Azure clássico.

- Azure AD serviços de domínio **não pode ser ativada em redes virtuais criadas usando o Gerenciador de recursos do Azure**.

- Você pode se conectar a uma rede virtual baseada em Gerenciador de recursos a uma rede virtual clássica nas quais os serviços de domínio do Azure AD está ativado. Depois disso, você pode usar os serviços de domínio do Azure AD da rede virtual baseada em Gerenciador de recursos. Para obter mais informações, consulte a seção de [conectividade de rede](active-directory-ds-networking.md#network-connectivity) .

- **Redes virtuais regionais**: se você planeja usar uma rede virtual existente, certifique-se de que se trata de uma rede virtual regional.

    - Redes virtuais que usam o mecanismo de grupos de afinidade herdados não podem ser usadas com os serviços de domínio do Azure AD.

    - Para usar os serviços de domínio do Azure AD, [migrar legadas redes virtuais para redes virtuais regionais](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### <a name="azure-region-for-the-virtual-network"></a>Região Azure para a rede virtual

- Os serviços de domínio do Azure AD domínio gerenciado é implantado na mesma região Azure como a rede virtual que você optar por habilitar o serviço no.

- Selecione uma rede virtual em uma região Azure suportada pelos serviços de domínio do Azure AD.

- Consulte a página de [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) conheça as regiões Azure em que os serviços de domínio do Azure AD está disponível.


### <a name="requirements-for-the-virtual-network"></a>Requisitos para a rede virtual

- **Proximidade suas cargas de trabalho Azure**: selecione a rede virtual que atualmente hospeda/hospedará máquinas virtuais que precisam de acesso aos serviços de domínio do Azure AD.

- **Servidores DNS personalizada/trazer seu-própria**: Certifique-se de que não existem servidores DNS personalizados configurados para a rede virtual.

- **Domínios existente com o mesmo nome de domínio**: Certifique-se de que você não tem um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, suponha que você tenha um domínio chamado 'contoso.com' já disponíveis na rede virtual selecionada. Posteriormente, você tentar ativar um domínio de serviços de domínio do Azure AD gerenciado com o mesmo nome de domínio (ou seja 'contoso.com') na rede virtual. Você encontrar uma falha ao tentar ativar os serviços de domínio do Azure AD. Essa falha será devido a conflitos de nome para o nome de domínio da rede virtual. Nessa situação, você deve usar um nome diferente para configurar seu domínio gerenciado de serviços de domínio do Azure AD. Como alternativa, você pode eliminação provisionar o domínio existente e, em seguida, prossiga para ativar os serviços de domínio do Azure AD.

> [AZURE.WARNING] Você não pode mover os serviços de domínio a uma rede virtual diferente depois que você tiver habilitado o serviço.


## <a name="network-security-groups-and-subnet-design"></a>Grupos de segurança de rede e design de sub-rede
Um [Grupo de segurança de rede (NSG)](../virtual-network/virtual-networks-nsg.md) contém uma lista de regras de lista de controle de acesso (ACL) que permitir ou negar o tráfego de rede para suas instâncias de máquina virtual em uma rede Virtual. NSGs podem ser associadas a sub-redes ou instâncias individuais de máquina virtual dentro daquela sub-rede. Quando um NSG está associado uma sub-rede, as regras ACL se aplicam a todas as instâncias de máquina virtual nessa sub-rede. Além disso, o tráfego para um máquina virtual individual pode ser restrito mais associando um NSG diretamente para essa máquina virtual.

![Design de sub-rede recomendados](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)


### <a name="best-practices-for-choosing-a-subnet"></a>Práticas recomendadas para escolher uma sub-rede
- Implante os serviços de domínio do Azure AD para uma **sub-rede dedicada separada** dentro da sua rede virtual Azure.

- Não se aplicam NSGs à sub-rede dedicada para seu domínio gerenciado. Se você precisa aplicar NSGs à sub-rede dedicada, certifique-se que você **não bloqueie as portas necessárias para o serviço e gerenciar seu domínio**.

- Não restringir muito o número de endereços IP disponíveis dentro da sub-rede dedicada para seu domínio gerenciado. Essa restrição impede que o serviço disponibilizando dois controladores de domínio para seu domínio gerenciado.

- **Não habilitar serviços de domínio do Azure AD na sub-rede gateway** da sua rede virtual.


> [AZURE.WARNING] Quando você associa um NSG com uma sub-rede nas quais serviços de domínio do Azure AD está ativada, você pode interromper a capacidade da Microsoft de serviço e gerenciar o domínio. Além disso, a sincronização entre seu locatário do Azure AD e seu domínio gerenciado é interrompida. **O SLA não se aplica a implantações onde um NSG foi aplicada que bloqueia os serviços de domínio do Azure AD da atualização e gerenciamento de seu domínio.**


### <a name="ports-required-for-azure-ad-domain-services"></a>Portas necessárias para os serviços de domínio do Azure AD
As seguintes portas necessários para os serviços de domínio do Azure AD serviço e manter seu domínio gerenciado. Certifique-se de que essas portas não estão bloqueadas para a sub-rede em que você ativou o domínio gerenciado.

| Número da porta | Finalidade |
|---|---|
| 443 | Sincronização com o seu locatário do Azure AD |
| 3389 | Gerenciamento de seu domínio |
| 5986 | Gerenciamento de seu domínio |
| 636 | LDAP (LDAPS) acesso seguro a seu domínio gerenciado |



## <a name="network-connectivity"></a>Conectividade de rede
Um domínio gerenciado de serviços de domínio do Azure AD pode ser habilitado apenas dentro de uma única rede virtual clássica no Azure. Redes virtuais criadas usando o Gerenciador de recursos do Azure não são suportadas.


### <a name="scenarios-for-connecting-azure-networks"></a>Cenários para conectar redes Azure
Conecte redes virtuais Azure para usar o domínio gerenciado em qualquer um dos seguintes cenários de implantação:

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>Usar o domínio gerenciado em mais de uma rede de virtual clássico Azure
Você pode se conectar a outras redes de virtuais clássicos Azure com a Azure rede virtual clássica no qual você habilitou os serviços de domínio do Azure AD. Esta conexão VPN permite que você usar o domínio gerenciado com suas cargas de trabalho implantadas em outras redes virtuais.

![Conectividade de rede virtual clássico](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Usar o domínio gerenciado em uma rede virtual baseada em Gerenciador de recursos
Você pode se conectar a uma rede virtual baseada em Gerenciador de recursos de rede do virtual Azure clássico no qual você habilitou os serviços de domínio do Azure AD. Esta conexão permite que você usar o domínio gerenciado com suas cargas de trabalho implantadas na rede virtual baseada em Gerenciador de recursos.

![Gerenciador de recursos para conectividade de rede virtual clássico](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### <a name="network-connection-options"></a>Opções de conexão de rede

- **Conexões de VNet para VNet usando as conexões VPN - to-site**: a conexão de uma rede virtual para outra rede virtual (VNet-para-VNet) é semelhante ao se conectar a uma rede virtual a um site local na. Os dois tipos de conectividade usam um gateway VPN para fornecer um túnel seguro usando IPsec/IKE.

    ![Conectividade de rede virtual usando Gateway VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Mais informações - conectar redes virtuais usando o gateway VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Conexões de VNet para VNet usando a correspondência de rede virtual**: correspondência de rede Virtual é um mecanismo que conecta duas redes virtuais na mesma região através da rede de backbone Azure. Depois de peered, as duas redes virtuais aparecem como um para todos os fins de conectividade. Eles ainda são gerenciados como recursos separados, mas máquinas virtuais nestas redes virtuais possam se comunicar com os outros diretamente usando endereços IP particulares.

    ![Conectividade de rede virtual usando correspondência](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Mais informações - virtuais correspondência de rede](../virtual-network/virtual-network-peering-overview.md)



<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Correspondência de rede virtual Azure](../virtual-network/virtual-network-peering-overview.md)

- [Configurar uma conexão de VNet para VNet para o modelo clássico de implantação](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Grupos de segurança de rede Azure](../virtual-network/virtual-networks-nsg.md)
