
<properties
    pageTitle="Solucionar problemas de criar conjuntos de híbrido RemoteApp | Microsoft Azure"
    description="Saiba como solucionar problemas de falhas de criação de conjunto de híbrido RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Solucionar problemas de criar conjuntos de híbrido RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Uma coleção de híbrido é hospedada em e armazena dados na nuvem Azure, mas também permite que os usuários acessar dados e recursos armazenados em sua rede local. Os usuários podem acessar aplicativos efetuando com suas credenciais corporativas sincronizados ou federado com o Active Directory do Azure. Você pode implantar um conjunto de híbrido que usa uma rede Virtual do Azure existente, ou você pode criar uma nova rede virtual. Recomendamos que você crie ou usa uma sub-rede de rede virtual com um intervalo CIDR grande o suficiente para crescimento futuro esperado para RemoteApp do Azure.

Ainda não tiver criado seu conjunto ainda? Consulte [criar um conjunto de híbrido](remoteapp-create-hybrid-deployment.md) para ver as etapas.

Se você estiver tendo problemas para criar seu conjunto, ou se o conjunto não está funcionando da maneira você acha que deveria, confira as informações a seguir.

## <a name="your-image-is-invalid"></a>Sua imagem é inválida ##
Se você vir uma mensagem como, "GoldImageInvalid" quando você está esperando Azure provisionar seu conjunto, significa que sua imagem de modelo não atender a [necessidades de imagem definidas pelo](remoteapp-imagereqs.md). Portanto, vá ler esses [requisitos](remoteapp-imagereqs.md), corrigir sua imagem e tentar criar seu conjunto novamente.



## <a name="does-your-vnet-have-network-security-groups-defined"></a>O seu VNET possui grupos de segurança de rede definidos? ##
Se você tiver definidos na sub-rede que você está usando para seu conjunto de grupos de segurança de rede, verifique se estas [URLs e portas](remoteapp-ports.md) estão acessíveis a partir dentro sua sub-rede.

Você pode adicionar grupos de segurança de rede adicionais às VMs implantadas por você na sub-rede para controle maior.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>Você está usando seus próprios servidores DNS? E eles são acessíveis a partir de sua sub-rede VNET? ##
>[AZURE.NOTE] Você precisa Verifique se que os servidores DNS em seu VNET sempre estão acima e sempre capaz de resolver máquinas virtuais hospedadas na VNET. Não use o Google DNS para isso.


Para conjuntos de híbrido você usar seus próprios servidores DNS. Especifique-los no seu esquema de configuração de rede ou por meio do portal de gerenciamento quando você cria sua rede virtual. Servidores DNS são usados na ordem em que eles são especificados de uma maneira de failover (em vez de repetição alternada).  
Consulte [Resolução de nomes para VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para certificar-se de que seus servidores DNS são correcly configurado.

Verifique se os servidores DNS para seu conjunto são acessíveis e disponíveis da sub-rede VNET especificado para este conjunto.

Por exemplo:

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Definir seu DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>Você está usando um controlador de domínio do Active Directory no seu conjunto? ##
Atualmente somente um domínio do Active Directory pode ser associado a RemoteApp do Azure. A coleção de híbrido suporta somente as contas do Active Directory do Azure que foram sincronizadas usando a ferramenta de DirSync uma implantação do Active Directory do Windows Server; Especificamente, seja sincronizado com a opção de sincronização de senha ou sincronizado com a federação de serviços de Federação do Active Directory (AD FS) configurada. Você precisa criar um domínio personalizado que coincida com o sufixo UPN do domínio para seu domínio local e configurar a integração de diretório.

Consulte [Configurando o Active Directory do Azure RemoteApp](remoteapp-ad.md) para obter mais informações.

Verifique se os detalhes do domínio fornecidos são válidos e o controlador de domínio é acessível a partir a máquina virtual criada na sub-rede usada para o aplicativo remoto do Azure. Verifique também as credenciais de conta de serviço fornecidas tem permissões para adicionar computadores ao domínio fornecido e que o nome do AD fornecido pode ser resolvido a partir do DNS fornecido na VNET.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>Qual nome de domínio que você especificou quando você criou sua coleção? ##

O nome de domínio que você criou ou adicionado deve ser um nome de domínio interno (não seu nome de domínio Azure AD) e deve estar no formato DNS resolvido (contoso. local). Por exemplo, você tem um nome interno do Active Directory (contoso. local) e um UPN de diretório ativo (contoso.com), você precisa usar o nome interno quando você cria sua coleção.
