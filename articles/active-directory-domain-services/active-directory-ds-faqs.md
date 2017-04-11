<properties
    pageTitle="Perguntas frequentes - serviços de domínio Active Directory do Azure | Microsoft Azure"
    description="Perguntas frequentes sobre o Azure Active Directory Domain Services"
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
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Serviços de domínio Active Directory do Azure: Perguntas frequentes (perguntas frequentes)

Esta página responde perguntas frequentes sobre os serviços de domínio Active Directory do Azure. Verificando atualizações do.

### <a name="troubleshooting-guide"></a>Guia de solução de problemas
Consulte nosso [guia de solução de problemas](active-directory-ds-troubleshooting.md) para soluções para problemas comuns encontrados ao configurar ou administração de serviços de domínio do Azure AD.


### <a name="configuration"></a>Configuração

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios para um único diretório do Azure AD?
Não. Você só pode criar um único domínio atendido pelos serviços de domínio do Azure AD para um único diretório do Azure AD.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Pode habilitar serviços de domínio do Azure AD em uma rede virtual do Gerenciador de recursos do Azure?
Não. Serviços de domínio do AD Azure só podem ser habilitados em uma rede de virtual Azure clássica. Você pode conectar a rede virtual clássica a uma rede virtual do Gerenciador de recursos usando rede virtual correspondência para usar seu domínio gerenciado em uma rede virtual do Gerenciador de recursos.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso tornar os serviços de domínio do Azure AD disponíveis em várias redes virtuais dentro da minha assinatura?
O próprio serviço não suporta diretamente este cenário. Serviços de domínio do AD Azure está disponível no somente uma rede virtual por vez. No entanto, você pode configurar a conectividade entre várias redes virtuais para expor serviços de domínio do Azure AD para outras redes virtuais. Este artigo descreve como você pode [conectar redes virtuais no Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Pode habilitar serviços de domínio do Azure AD usando o PowerShell?
Implantação/automatizado do PowerShell dos serviços de domínio do Azure AD não está disponível no momento.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Serviços de domínio do Azure AD está disponível no novo portal do Azure?
Não. Serviços de domínio do AD Azure podem ser configurados apenas no [Azure portal clássico](https://manage.windowsazure.com). Esperamos estender o suporte para o [portal do Azure](https://portal.azure.com) no futuro.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Pode adicionar controladores de domínio a um domínio de serviços de domínio do Azure AD gerenciado?
Não. O domínio fornecido pelos serviços de domínio do Azure AD é um domínio gerenciado. Você não precisa provisionar, configurar ou caso contrário, gerenciar controladores de domínio para este domínio - essas atividades de gerenciamento são fornecidas como um serviço pela Microsoft. Portanto, você não poderá adicionar controladores de domínio adicionais (leitura / gravação ou somente leitura) para o domínio gerenciado.

### <a name="administration-and-operations"></a>Administração e operações

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso conectar ao controlador de domínio para o meu domínio gerenciado usando a área de trabalho remota?
Não. Você não tem permissões para se conectar a controladores de domínio para o domínio gerenciado por meio de área de trabalho remota. Membros do grupo 'Administradores de DC AAD' podem administrar o domínio gerenciado usando ferramentas de administração do AD como o Centro de administração do Active Directory (ADAC) ou o PowerShell do AD. Essas ferramentas são instaladas usando o recurso 'Ferramentas de administração de servidor remoto' em um Windows server associado ao domínio gerenciado.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Posso habilitou os serviços de domínio do Azure AD. Qual conta de usuário usar máquinas de junção de domínio para este domínio?
Os usuários que você adicionou ao grupo administrativo (por exemplo, ' AAD DC administradores') podem máquinas de junção de domínio. Além disso, os usuários neste grupo recebem acesso de desktop remoto para máquinas que tenha sido adicionado ao domínio.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>Posso exercer privilégios de administrador de domínio para o domínio fornecidos pelos serviços de domínio do Azure AD?
Não. Você não tem privilégios administrativos no domínio gerenciado. Privilégios de ' Administrador de domínio ' e ' administrador da empresa ' não estão disponíveis para serem usados dentro do domínio. Administrador de domínio existente ou grupos de administradores de empresa no diretório Azure AD também não recebem privilégios de administrador de domínio/corporativo no domínio.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>Posso modificar associações de grupo usando o LDAP ou outras ferramentas administrativas do AD em domínios fornecidos pelos serviços de domínio do Azure AD?
Não. Membros do grupo não podem ser modificados em domínios atendidos pelos serviços de domínio do Azure AD. O mesmo se aplica para atributos de usuário. No entanto você pode alterar associações de grupo ou atributos de usuário no Azure AD ou em seu domínio local. Essas alterações são sincronizadas automaticamente aos serviços de domínio do Azure AD.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>Posso estender o esquema do domínio fornecido pelos serviços de domínio do Azure AD?
Não. O esquema é administrado pela Microsoft para o domínio gerenciado. Extensões de esquema não são suportadas pelos serviços de domínio do Azure AD.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>É possível modificar ou adicionar registros DNS no meu domínio gerenciado?
Sim. Usuários que pertencem ao grupo 'Administradores de DC AAD' são concedidos privilégios de ' Administrador de DNS', para modificar registros DNS do domínio gerenciado. Esses usuários podem usar o console do Gerenciador de DNS em um computador executando o Windows Server associado ao domínio gerenciado, gerenciar DNS. Para usar o console do Gerenciador de DNS, instale 'Ferramentas do servidor DNS', que é parte do recurso opcional 'Ferramentas de administração de servidor remoto' no servidor. Mais informações sobre [utilitários para administrar, monitoramento e solução de problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) estão disponíveis no TechNet.


### <a name="billing-and-availability"></a>Disponibilidade e cobrança

#### <a name="is-azure-ad-domain-services-a-paid-service"></a>É que um serviço pago de serviços de domínio do Azure AD?
Sim. Para obter mais informações, consulte a [página de preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Existe uma avaliação gratuita do serviço?
Esse serviço está incluído na avaliação gratuita do Azure. Você pode se inscrever para uma [avaliação gratuita de um mês do Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems"></a>É possível obter os serviços de domínio do Azure AD como parte do pacote de mobilidade Enterprise (EMS)?
#### <a name="do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Eu preciso Azure AD Premium uso dos serviços de domínio do Azure AD?
Não. Serviços de domínio do AD Azure é um serviço Azure pré-pago e não faz parte de EMS. Serviços de domínio do Azure AD estão disponíveis para todas as edições do Azure AD (gratuito, básica e, Premium) e é cobrado em uma base por hora, dependendo do uso.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Quais regiões Azure o serviço está disponível no?
Consulte a página de [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para ver uma lista das regiões Azure onde os serviços de domínio do Azure AD está disponível.
