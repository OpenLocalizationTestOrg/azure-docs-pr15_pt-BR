<properties
    pageTitle="Serviços de domínio do Azure AD: Comparar domínio do Azure AD serviços para controladores de domínio DIY | Microsoft Azure"
    description="Comparando Azure Active Directory Domain Services para controladores de domínio DIY"
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
    ms.date="10/01/2016"
    ms.author="maheshu"/>

# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Como decidir se serviços de domínio do Azure AD é adequado para o seu caso de uso
Serviços de domínio do AD Azure permite implantar suas cargas de trabalho nos serviços de infraestrutura do Azure, sem precisar se preocupar em manter sua infraestrutura de identidade. Este serviço gerenciado é diferente de uma implantação típica do Active Directory do Windows Server que você implantar e administra sua própria. O serviço destina-se a facilitar a implantação da, monitoramento de integridade automatizada e correção e uma infraestrutura de identidade simples para a nuvem. Estamos constantemente expandindo o serviço para adicionar suporte para cenários de implantação comuns.

Decidir usar os serviços de domínio do Azure AD ou girar e gerenciar seu próprios infraestrutura do AD (faça) no Azure:

- Consulte a lista de [recursos oferecidos pelos serviços de domínio do Azure AD](active-directory-ds-features.md).

- Revise comuns [cenários de implantação para serviços de domínio do Azure AD](active-directory-ds-scenarios.md).

- Por fim, [compare serviços de domínio do Azure AD para uma opção de AD tipo faça você mesmo](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparar os serviços de domínio do Azure AD para o domínio AD DIY no Azure
A tabela a seguir ajuda você a decidir entre usando os serviços de domínio do Azure AD e gerenciando seu próprios infraestrutura do AD no Azure.

|**Recurso**|**Serviços de domínio do Azure AD**|**Anúncio 'Faça' em VMs Azure**|
|---|:---:|:---:|
|[**Serviço gerenciado**](active-directory-ds-comparison.md#managed-service)|**& #x 2713;**|**& #x 2715;**|
|[**Implantações seguras**](active-directory-ds-comparison.md#secure-deployments)|**& #x 2713;**|Administrador precisa proteger a implantação.|
|[**Servidor DNS**](active-directory-ds-comparison.md#dns-server)|**& #x 2713;** (serviço gerenciado)|**& #x 2713;**|
|[**Privilégios de administrador corporativo ou de domínio**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|**& #x 2715;**|**& #x 2713;**|
|[**Associação de domínio**](active-directory-ds-comparison.md#domain-join)|**& #x 2713;**|**& #x 2713;**|
|[**Autenticação de domínio usando NTLM e Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|**& #x 2713;**|**& #x 2713;**|
|[**Estrutura de unidade Organizacional personalizada**](active-directory-ds-comparison.md#custom-ou-structure)|**& #x 2713;**|**& #x 2713;**|
|[**Extensões de esquema**](active-directory-ds-comparison.md#schema-extensions)|**& #x 2715;**|**& #x 2713;**|
|[**Relações de confiança do domínio/floresta do AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|**& #x 2715;**|**& #x 2713;**|
|[**Leitura LDAP**](active-directory-ds-comparison.md#ldap-read)|**& #x 2713;**|**& #x 2713;**|
|[**LDAP seguro (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|**& #x 2713;**|**& #x 2713;**|
|[**Gravação LDAP**](active-directory-ds-comparison.md#ldap-write)|**& #x 2715;**|**& #x 2713;**|
|[**Política de grupo**](active-directory-ds-comparison.md#group-policy)|Simples|Completo|
|[**Implantações distribuídos geográfica**](active-directory-ds-comparison.md#geo-dispersed-deployments)|**& #x 2715;**|**& #x 2713;**|


#### <a name="managed-service"></a>Serviço gerenciado
Azure domínios de serviços de domínio do AD são gerenciados pela Microsoft. Você não precisa se preocupar sobre patches, atualizações, monitoramento, backups e garantir a disponibilidade de seu domínio. Estas tarefas de gerenciamento são oferecidas como um serviço pela Microsoft Azure para seus domínios gerenciados.

#### <a name="secure-deployments"></a>Implantações seguras
Domínio gerenciado com segurança é bloqueado acordo com as práticas recomendadas de segurança da Microsoft para implantações do AD. Estas práticas recomendadas originam de décadas AD a equipe de produto da experiência de engenharia e suporte implantações do AD. Para implantações tipo faça você mesmo, você precisa tomar medidas de implantação específicas para bloquear down/proteger sua implantação.

#### <a name="dns-server"></a>Servidor DNS
Um domínio gerenciado de serviços de domínio do Azure AD inclui gerenciados serviços DNS. Membros do grupo 'Administradores de DC AAD' podem gerenciar o DNS do domínio gerenciado. Membros deste grupo recebem privilégios totais de administração do DNS do domínio gerenciado. Gerenciamento de DNS pode ser realizado usando o 'console de administração do DNS' incluído no pacote de ferramentas de administração de servidor remoto (RSAT).

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilégios de administrador corporativo ou domínio
Esses privilégios elevados não são oferecidos em um domínio gerenciado AAD-DS. Aplicativos que exigem esses privilégios elevados para ser instalado/executar não podem ser executados em domínios gerenciados. Um subconjunto menor de privilégios administrativos está disponível para membros do grupo de administração delegada chamado 'AAD DC administradores'. Esses privilégios incluem privilégios para configurar o DNS, configurar política de grupo, obtenha privilégios de administrador no domínio máquinas etc.

#### <a name="domain-join"></a>Associação de domínio
Você pode ingressar máquinas virtuais ao domínio gerenciado semelhante a como você ingressar computadores em um domínio do AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticação de domínio usando NTLM e Kerberos
Com os serviços de domínio do Azure AD, você pode usar credenciais corporativas para autenticar com o domínio gerenciado. Credenciais são mantidas em sincronia com seu locatário do Azure AD. Para locatários sincronizados, o Azure AD Connect garante que as alterações feitas de credenciais locais são sincronizadas ao Azure AD. Com uma configuração de domínio DIY, talvez você precise configurar uma relação de confiança do domínio com uma floresta de conta local para os usuários autenticar com suas credenciais corporativas. Como alternativa, você talvez precise configurar replicação do AD para garantir que as senhas de usuário sincronizem para seu domínio Azure de máquinas virtuais de controlador.

#### <a name="custom-ou-structure"></a>Estrutura de unidade Organizacional personalizada
Membros do grupo 'Administradores de DC AAD' podem criar unidades organizacionais personalizados dentro do domínio gerenciado.

#### <a name="schema-extensions"></a>Extensões de esquema
Você não pode estender o esquema de base de um domínio de serviços de domínio do Azure AD gerenciado. Portanto, os aplicativos que dependem extensões de esquema do AD (por exemplo, novos atributos em objeto do usuário) não podem ser ativados e deslocados para domínios AAD-DS.

#### <a name="ad-domain-or-forest-trusts"></a>Domínio AD ou relações de confiança de floresta
Domínios gerenciados não podem ser configurados para configurar relações de confiança com outros domínios (entrada/saída). Portanto, cenários como implantações de floresta de recursos ou casos onde você preferir não para sincronizar senhas ao Azure AD não podem usar os serviços de domínio do Azure AD.

#### <a name="ldap-read"></a>Leitura LDAP
Domínio gerenciado suporta LDAP ler cargas de trabalho. Portanto, você pode implantar aplicativos que realizam operações de leitura LDAP em relação ao domínio gerenciado.

#### <a name="secure-ldap"></a>LDAP seguro
Você pode configurar serviços de domínio do Azure AD para fornecer acesso seguro do LDAP ao seu domínio gerenciado, inclusive pela internet.

#### <a name="ldap-write"></a>Gravação LDAP
O domínio gerenciado é somente leitura para objetos de usuário. Portanto, aplicativos que realizam operações de gravação LDAP contra atributos do objeto do usuário não funcionam em um domínio gerenciado. Além disso, senhas de usuário não podem ser alteradas dentro do domínio gerenciado. Outro exemplo seria modificação de associações de grupo ou atributos de grupo dentro do domínio gerenciado, que não é permitido. No entanto, quaisquer alterações efetuadas atributos de usuário ou senhas feitas no Azure AD (via portal do PowerShell/Azure) ou AD são sincronizados com o domínio gerenciado AAD-DS locais.

#### <a name="group-policy"></a>Política de grupo
Construções de política de grupo sofisticados não têm suporte no domínio gerenciado AAD-DS. Por exemplo, você não pode criar e implantar GPOs separados para cada OU personalizada no domínio ou use a filtragem de direcionamento de GP WMI. Não há um interno GPO cada para os contêineres 'AADDC computadores' e 'AADDC usuários', que pode ser personalizado para configurar a política de grupo.

#### <a name="geo-dispersed-deployments"></a>Implantações disperso geográfica
Azure domínios gerenciados de serviços de domínio do AD estão disponíveis em uma única rede virtual no Azure. Cenários que exigem controladores de domínio esteja disponível em vários regiões Azure em todo o mundo, configurando controladores de domínio no Azure IaaS VMs pode ser a melhor alternativa.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opções de implantação 'Faça' (DIY) AD
Você pode ter casos de uso de implantação onde você precisa de alguns dos recursos oferecidos por uma instalação do Windows Server AD. Nesses casos, considere uma das seguintes opções (DIY) faça:

- **Domínio de nuvem autônomo:** Você pode configurar autônoma 'nuvem domínio' usando Azure máquinas virtuais que foram configuradas como controladores de domínio. Este infraestrutura não integrar com o seu local ambiente do AD. Esta opção requer um conjunto diferente de 'credenciais de nuvem' logon/administrar VMs na nuvem.

- **Implantação de floresta do recurso:** Você pode configurar um domínio na topologia de floresta do recurso, usando o Azure máquinas virtuais configuradas como controladores de domínio. Em seguida, você pode configurar uma relação de confiança do AD com seu local ambiente do AD. Você pode computadores de junção de domínio (Azure VMs) para esta floresta de recurso na nuvem. Autenticação do usuário acontece sobre qualquer uma uma conexão de VPN/rota expressa para seu diretório local.

- **Estenda o seu domínio local para o Azure:** Você pode se conectar uma rede virtual Azure à sua rede local usando uma conexão de VPN/rota expressa, para que o Azure VMs podem ser unidas para seu local AD. Outra alternativa é promover controladores de domínio de réplica do seu domínio local no Azure como uma máquina virtual. Em seguida, você pode configurá-lo para replicar a uma conexão de VPN/rota expressa para seu diretório local. Este modo de implantação efetivamente estende seu domínio local no Azure.

> [AZURE.NOTE] Você pode determinar que uma opção DIY é mais adequada para seus casos de uso de implantação. Considere a possibilidade de [compartilhamento de comentários](active-directory-ds-contact-us.md) para ajudar a entender o que ajuda a recursos que você escolheu os serviços de domínio do Azure AD no futuro. Esse comentário nos ajudam a evoluir o serviço para melhor atender às suas necessidades de implantação e casos de uso.

Podemos ter publicado [as diretrizes de implantação do Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) para ajudar a facilitar DIY instalações.


## <a name="related-content"></a>Conteúdo relacionado
- [Recursos - serviços de domínio do Azure AD](active-directory-ds-features.md)

- [Cenários de implantação - serviços de domínio do Azure AD](active-directory-ds-scenarios.md)

- [Diretrizes para implantar o servidor Active Directory do Windows Azure máquinas virtuais](https://msdn.microsoft.com/library/azure/jj156090.aspx)
