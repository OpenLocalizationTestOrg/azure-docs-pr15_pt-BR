<properties
   pageTitle="Visão geral de monitoramento e gerenciamento de segurança do Azure | Microsoft Azure"
   description=" Azure fornece mecanismos de segurança para ajudar no gerenciamento e monitoramento de serviços de nuvem Azure e máquinas virtuais.  Este artigo fornece uma visão geral desses recursos de segurança básicos e serviços. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-security-management-and-monitoring-overview"></a>Visão geral de monitoramento e gerenciamento de segurança do Azure

Azure fornece mecanismos de segurança para ajudar no gerenciamento e monitoramento de serviços de nuvem Azure e máquinas virtuais. Este artigo fornece uma visão geral desses recursos de segurança básicos e serviços. São fornecidos links para artigos que lhe dará detalhes de cada para saber mais.

A segurança dos seus serviços de nuvem da Microsoft é uma parceria e responsabilidade compartilhada entre você e a Microsoft. Responsabilidade compartilhada significa que a Microsoft é responsável pela Microsoft Azure e centros de segurança física dos seus dados (por meio do uso proteção de segurança como portas de entrada do selo bloqueadas, limites e protege). Além disso, o Azure fornece fortes níveis de segurança de nuvem na camada de software que atende às necessidades de segurança, privacidade e conformidade de seus clientes exigentes.

Você possui seus dados e identidades, a responsabilidade por protegendo-los, a segurança dos recursos locais e a segurança dos componentes de nuvem sobre os quais você tem controle. Microsoft oferece controles de segurança e recursos para ajudar a proteger seus dados e aplicativos. A responsabilidade de segurança baseia-se no tipo de serviço de nuvem.

O gráfico a seguir resume o saldo de responsabilidade pela Microsoft e o cliente.

![Responsabilidade compartilhada][1]

Para se aprofundar gerenciamento de segurança, consulte [gerenciamento de segurança no Azure](azure-security-management.md).

Aqui estão os principais recursos a serem abordados neste artigo:

- Controle de acesso baseado em função
- Antimalware
- Autenticação multifator
- Rota expressa
- Gateways de rede virtual
- Gerenciamento de identidades privilegiado
- Proteção de identidade
- Centro de segurança

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Controle de acesso baseado em função (RBAC) fornece gerenciamento de acesso refinados para recursos Azure. Usando RBAC, você pode conceder pessoas apenas a quantidade de acesso que eles precisam para realizar seus trabalhos.  RBAC também pode ajudar a garantir que, quando as pessoas saem da organização eles perderão acesso aos recursos na nuvem.

Saiba Mais:

- [Blog da equipe Active Directory na RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Controle de acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalware

Com o Azure você pode usar o software de antimalware de fornecedores de segurança principais como Microsoft, Symantec, antivírus, McAfee e Kaspersky para ajudar a proteger suas máquinas virtuais do arquivos mal-intencionados, adware e outras ameaças.

Antimalware da Microsoft oferece a capacidade de instalar um agente antimalware para funções de PaaS e máquinas virtuais. Com base em proteção de ponto de extremidade do sistema central, esse recurso traz local comprovado tecnologia de segurança para a nuvem.

Também oferecemos profunda integração da tendência [Segurança profunda](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ e [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ produtos da plataforma Windows Azure. DeepSecurity é uma solução antivírus e SecureCloud é uma solução de criptografia. DeepSecurity será implantado dentro de VMs usando um modelo de extensão. Usando o portal de interface do usuário e o PowerShell, você pode optar por usar DeepSecurity dentro de novos VMs que estão sendo giradas para cima ou VMs existentes que já foram implantadas.

Proteção de ponto de extremidade da Symantec (Set) também é suportado no Azure. Por meio da integração de portal, os clientes têm a capacidade de especificar que eles planeja usar Set dentro de uma máquina virtual. SET pode ser instalado em uma máquina virtual nova através do Portal do Azure ou pode ser instalado em uma VM existente usando o PowerShell.

Saiba Mais:

- [Implantar soluções de Antimalware no Azure máquinas virtuais](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Antimalware da Microsoft para serviços de nuvem Azure e máquinas virtuais](../security/azure-security-antimalware.md)
- [Como instalar e configurar tendência Micro profunda Security como um serviço em uma máquina virtual Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Como instalar e configurar a proteção de ponto de extremidade do Symantec em uma máquina virtual Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Novas opções de Antimalware para proteção de máquinas virtuais Azure – proteção de ponto de extremidade do McAfee](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Autenticação multifator

A autenticação multifator Azure (MFA) é um método de autenticação que requer o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança ao usuário entrada e transações. MFA ajuda a proteger informações acesso aos dados e aplicativos atendendo demanda do usuário para um processo de entrada simples. Ele oferece autenticação sólida via uma variedade de opções de verificação — chamada telefônica, uma mensagem de texto ou aplicativo móvel notificação ou verificação de código e 3º festa JURAMENTO tokens.

Saiba Mais:

- [Autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [O que é a autenticação multifator Azure?](../multi-factor-authentication/multi-factor-authentication.md)
- [Como funciona a autenticação multifator do Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>Rota expressa

Rota expressa do Microsoft Azure lhe permite estender suas redes locais em nuvem da Microsoft através de uma conexão privada dedicada facilitada por um provedor de conectividade. Com a rota expressa, você pode estabelecer conexões com serviços de nuvem da Microsoft, como Microsoft Azure, Office 365 e CRM Online. Conectividade pode ser uma rede de (IP VPN) para qualquer, uma rede de Ethernet de ponto a ponto ou uma conexão entre virtual através de um provedor de conectividade em uma instalação de localização conjunta. Rota expressa conexões não vá na Internet pública. Isso permite conexões de rota expressa oferecer mais confiabilidade, rapidez, latências menores e maior segurança que conexões típicas pela Internet.

Saiba Mais:

- [Visão geral técnica de rota expressa](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual

Gateways VPN, também chamado de Gateways de rede Virtual do Azure, são usados para enviar tráfego de rede entre redes virtuais e locais de local. Elas também são usadas para enviar tráfego entre várias redes virtuais dentro do Azure (VNet-para-VNet).  Gateways VPN fornecem conectividade de seguro entre locais entre Azure e sua infraestrutura.

Saiba Mais:

- [Sobre gateways VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Visão geral de segurança de rede Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Gerenciamento de identidades privilegiado

Às vezes, os usuários precisam realizar operações privilegiadas no Azure recursos ou outros aplicativos SaaS. Isso geralmente significa que as organizações precisam dar a elas acesso privilegiado permanente do Azure Active Directory (AD Azure). Este é um risco de segurança crescente para recursos hospedados em nuvem porque organizações suficientemente não podem monitorar o que os usuários estão fazendo com seu acesso privilegiado.
Além disso, se uma conta de usuário com acesso privilegiado é comprometida, que uma violação poderão afetar sua segurança geral na nuvem. Gerenciamento de identidades do Azure AD privilegiado ajuda a resolver esse risco, reduzindo o tempo de exposição de privilégios e aumentar a visibilidade de uso.  

Gerenciamento de identidades privilegiados apresenta o conceito de um administrador temporário para uma função ou "no momento" acesso de administrador, que é um usuário que precisa concluir um processo de ativação para essa função atribuída. O processo de ativação altera a atribuição do usuário para uma função no Azure AD do período inativo para ativo, por um tempo especificado como 8 horas.

Saiba Mais:

- [Gerenciamento de identidades do Azure AD privilegiado](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Introdução ao gerenciamento de identidades do Azure AD privilegiado](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Proteção de identidade

Proteção de identidade do Azure Active Directory (AD) fornece uma visão consolidada de atividades de entrada suspeitas e possíveis vulnerabilidades para ajudar a proteger sua empresa. Proteção de identidade detecta atividades suspeitas para usuários e identidades privilegiado (admin), com base em sinais como ataques de força bruta, perdido credenciais e suplementos de entrada de locais desconhecidos e infectados dispositivos.

Fornecendo notificações e correção recomendada, proteção de identidade ajuda a reduzir riscos em tempo real. Calcula a gravidade do risco de usuário, e você pode configurar políticas baseada em riscos para automaticamente ajudar a proteger aplicativo acessar de ameaças futuras.

Saiba Mais:

- [Proteção de identidade do Active Directory do Azure](../active-directory/active-directory-identityprotection.md)
- [Canal 9: Azure AD e identidade de slides: visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de segurança

O Centro de segurança do Azure ajuda você a evitar, detectar e responder a ameaças e fornece que maior visibilidade e controle sobre, a segurança dos seus recursos Azure. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas Azure, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

Central de segurança ajuda você a otimizar e monitorar a segurança dos seus recursos Azure por:

- Permitindo que você defina políticas para os recursos da sua assinatura Azure acordo com as necessidades de segurança da sua empresa e do tipo de aplicativos ou confidencialidade dos dados em cada assinatura.
- Monitorando o estado de suas máquinas virtuais Azure, rede e aplicativos.
- Fornecer uma lista prioridades de alertas de segurança, incluindo alertas de soluções de parceiros integradas, juntamente com as informações que necessárias para investigar rapidamente e recomendações sobre como corrigir um ataque.

Saiba Mais:

- [Introdução à Central de segurança do Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
