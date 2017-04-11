<properties
    pageTitle="Visão geral dos serviços de domínio Active Directory do Azure | Microsoft Azure"
    description="Visão geral dos serviços de domínio Active Directory do Azure"
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
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Serviços de domínio do Azure AD

## <a name="overview"></a>Visão geral
Serviços de infraestrutura Azure permitem que você implantar uma ampla variedade de soluções de computação de forma ágil. Com o máquinas virtuais do Azure, você pode implantar quase instantaneamente e você paga apenas com o minuto. Usando o suporte para Windows, Linux, SQL Server, Oracle, IBM, SAP e BizTalk, você pode implantar qualquer carga de trabalho, qualquer idioma, em praticamente qualquer sistema operacional. Esses benefícios permitem migrar aplicativos herdados implantados local para o Azure, para salvar as despesas operacionais.

Um aspecto importante de migração de aplicativos de locais no Azure está lidando com as necessidades de identidade desses aplicativos. Aplicativos de reconhecimento de diretório podem confiar em LDAP para leitura ou acesso de gravação ao diretório corporativo ou dependem de autenticação integrada do Windows (autenticação Kerberos ou NTLM) para autenticar os usuários finais. Aplicativos do linha de negócios (LOB) em execução no Windows Server são normalmente implantados em máquinas de domínio associado, para que eles podem ser gerenciados com segurança usando diretiva de grupo. Aplicativos de 'lift shift e' locais na nuvem, essas dependências na infraestrutura de identidade corporativa precisam ser resolvidos.

Os administradores geralmente ativar a uma das seguintes soluções para atender às necessidades de identidade dos seus aplicativos implantados no Azure:

- Implante uma conexão de VPN-to-site entre cargas de trabalho nos serviços de infraestrutura do Azure e o diretório corporativo local.
- Estenda a infraestrutura de floresta/domínio AD corporativa, configurando controladores de domínio de réplica usando Azure máquinas virtuais.
- Implante um domínio autônomo no Azure usando controladores de domínio implantados como Azure máquinas virtuais.

Todas essas abordagens sofrem de alto custo e sobrecarga administrativa. Os administradores são necessários para implantar controladores de domínio usando máquinas virtuais no Azure. Além disso, precisam gerenciar, proteger, patch, monitorar, fazer backup e solucionar essas máquinas virtuais. A dependência em conexões VPN para o diretório local faz com que as cargas de trabalho implantadas no Azure vulnerável a problemas de rede temporário ou interrupções. Essas interrupções de rede alternadamente resultarem em atividade inferior e redução de confiabilidade para esses aplicativos.

Podemos projetados serviços de domínio do Azure AD para fornecer uma alternativa mais fácil.


## <a name="introducing-azure-ad-domain-services"></a>Apresentando o serviços de domínio do Azure AD
Serviços de domínio do AD Azure fornece serviços de domínio gerenciado como associação de domínio, autenticação de Kerberos/NTLM do grupo política, LDAP, que são totalmente compatíveis com o Active Directory do Windows Server. Você pode consumir esses serviços de domínio sem a necessidade de implantar, gerenciar e patch controladores de domínio na nuvem. Serviços de domínio do AD Azure integra-se com seu locatário existente do Azure AD, portanto, tornando possível para os usuários façam logon usando suas credenciais corporativas. Além disso, você pode usar contas de usuário e grupos existentes para proteger o acesso aos recursos, garantindo um melhoram 'lift shift e' dos recursos locais para serviços de infraestrutura do Azure.

Funcionalidade de serviços de domínio do AD Azure funciona perfeitamente, independentemente se seu locatário do Azure AD é sincronizado com o Active Directory local ou somente na nuvem.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Serviços de domínio Azure AD para organizações somente na nuvem
Uma nuvem somente locatário Azure AD (conhecido como 'locatários gerenciados') não tem nenhum espaço de identidade de local. Em outras palavras, contas de usuário, senhas e membros do grupo são todos nativos à nuvem - ou seja, criadas e gerenciadas no Azure AD. Considere por um momento que Contoso é uma nuvem somente locatário Azure AD. Como mostrado na ilustração a seguir, o administrador da Contoso configurou uma rede virtual nos serviços de infraestrutura do Azure. Aplicativos e cargas de trabalho de servidor são implantadas nesta rede virtual no Azure máquinas virtuais. Como Contoso é um locatário somente na nuvem, todas as identidades de usuário, suas credenciais e membros do grupo são criados e gerenciados no Azure AD.

![Visão geral de serviços de domínio do Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

Do Contoso administrador de TI pode habilitar serviços de domínio do Azure AD para seu locatário do Azure AD e optar por disponibilizar nesta rede virtual de serviços de domínio. Depois disso, os serviços de domínio do Azure AD provisiona um domínio gerenciado e disponibiliza na rede virtual. Todas as contas de usuário, as associações de grupo e credenciais de usuário disponíveis no locatário do Azure AD da Contoso também estão disponíveis neste domínio recém-criado. Esse recurso permite que os usuários na organização entrar no domínio usando suas credenciais corporativas - por exemplo, ao se conectar remotamente ao domínio máquinas via área de trabalho remota. Os administradores podem provisionar acesso aos recursos no domínio usando associações de grupo existente. Aplicativos implantados em máquinas virtuais em uma rede virtual podem usar recursos como associação de domínio, leitura LDAP, vincular LDAP, autenticação NTLM e Kerberos e política de grupo.

Alguns aspectos destaque do domínio gerenciado que está provisionado pelos serviços de domínio do Azure AD são da seguinte maneira:

- Do Contoso administrador de TI não precisa gerenciar, patch ou monitorar esse domínio ou controladores de domínio para este domínio gerenciado.
- Não é necessário para gerenciar a replicação do AD para este domínio. Contas de usuário, as associações de grupo e credenciais de locatário do Azure AD da Contoso ficam disponíveis automaticamente nesse domínio gerenciado.
- Desde que o domínio é gerenciado pelo Azure AD serviços de domínio, Contoso administrador de TI não tem privilégios de administrador corporativo ou administrador de domínio nesse domínio.


### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Serviços de domínio Azure AD para as organizações híbrido
Organizações com um híbrido infraestrutura consumam uma mistura de recursos de nuvem e locais. Tais organizações sincronizam informações de identidade de seu diretório local para seu locatário Azure AD. Como as organizações híbridas buscam para migrar mais de seus aplicativos de locais na nuvem, especialmente aplicativos herdados de reconhecimento de diretório, os serviços de domínio do Azure AD podem ser útil para eles.

Litware Corporation implantou o [Azure AD Connect](../active-directory/active-directory-aadconnect.md), para sincronizar informações de identidade de seu diretório local para seu locatário Azure AD. As informações de identidade que são sincronizadas incluem contas de usuário, hash suas credenciais para autenticação (sincronização de senha) e membros do grupo.

> [AZURE.NOTE] **Sincronização de senha é obrigatória para as organizações híbrido usar os serviços de domínio do Azure AD**. Esse requisito é porque as credenciais do usuário são necessárias no domínio gerenciado fornecido pelos serviços de domínio do Azure AD para autenticar esses usuários por meio de métodos de autenticação NTLM ou Kerberos.

![Serviços de domínio do Azure AD para Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

A ilustração anterior mostra como organizações com um híbrido infraestrutura, como Litware Corporation, podem usar os serviços de domínio do Azure AD. Aplicativos e cargas de trabalho de servidor que exigem os serviços de domínio do Litware são implantadas em uma rede virtual nos serviços de infraestrutura do Azure. Do Litware administrador de TI pode habilitar serviços de domínio do Azure AD para seu locatário do Azure AD e optar por disponibilizar um domínio gerenciado nesta rede virtual. Como Litware é uma organização com uma infraestrutura híbrida, credenciais, grupos e contas de usuário são sincronizadas para seu locatário do Azure AD do seu diretório local. Esse recurso permite que os usuários entrar no domínio usando suas credenciais corporativas - por exemplo, quando estiverem conectados remotamente máquinas associado ao domínio por meio de área de trabalho remota. Os administradores podem provisionar acesso aos recursos no domínio usando associações de grupo existente. Aplicativos implantados em máquinas virtuais em uma rede virtual podem usar recursos como associação de domínio, leitura LDAP, vincular LDAP, autenticação NTLM e Kerberos e política de grupo.

Alguns aspectos destaque do domínio gerenciado que está provisionado pelos serviços de domínio do Azure AD são da seguinte maneira:

- O domínio gerenciado é um domínio autônomo. Não é uma extensão de domínio do local do Litware.
- Do Litware administrador de TI não precisa gerenciar, patch, ou monitorar controladores de domínio para este domínio gerenciado.
- Não é necessário para gerenciar a replicação do AD para este domínio. Contas de usuário, as associações de grupo e credenciais do diretório de local do Litware são sincronizadas ao Azure AD via Azure AD Connect. Essas contas de usuário, as associações de grupo e credenciais ficam disponíveis automaticamente dentro do domínio gerenciado.
- Desde que o domínio é gerenciado pelo Azure AD serviços de domínio, Litware administrador de TI não tem privilégios de administrador corporativo ou administrador de domínio nesse domínio.


## <a name="benefits"></a>Benefícios
Com os serviços de domínio do Azure AD, você pode aproveitar os seguintes benefícios:

-   **Simples** – você pode satisfazer as necessidades de identidade de máquinas virtuais implantadas aos serviços de infraestrutura do Azure com apenas alguns cliques simples. Você não precisa implantar e gerenciar infraestrutura de identidade em conectividade Azure ou configuração voltar para sua infraestrutura de identidade de local.

-   **Integrado** – os serviços de domínio do Azure AD profundamente é integrado ao seu locatário do Azure AD. Agora você pode usar o Azure AD como um diretório de corporativos integrados baseado em nuvem que atende às necessidades de seus aplicativos modernos e de aplicativos de reconhecimento de diretório tradicionais.

-   **Compatível** – os serviços de domínio do Azure AD se baseia a infraestrutura de nível empresarial comprovada do Active Directory do Windows Server. Portanto, seus aplicativos podem confiar em um grau maior de compatibilidade com recursos do Active Directory do Windows Server. Nem todos os recursos disponíveis no Windows Server AD estão disponíveis atualmente nos serviços de domínio do Azure AD. No entanto, os recursos disponíveis são compatíveis com os recursos do Windows Server AD correspondentes que dependem em sua infraestrutura de local. Os recursos de associação LDAP, Kerberos, NTLM, política de grupo e domínio constituem uma oferta desenvolvida que foi testada e refinada sobre várias versões do Windows Server.

-   **Econômica** – com os serviços de domínio AD Azure, você pode evitar a carga de gerenciamento e infraestrutura que está associada ao gerenciamento de infraestrutura de identidade para oferecer suporte a aplicativos de reconhecimento de diretório tradicionais. Você pode mover esses aplicativos para serviços de infraestrutura do Azure e beneficiar maior economia em despesas operacionais.
