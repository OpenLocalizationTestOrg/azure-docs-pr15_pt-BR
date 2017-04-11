<properties
   pageTitle="Visão geral de segurança de gerenciamento de identidade Azure | Microsoft Azure"
   description=" Identidade e Microsoft access gerenciamento soluções ajudam IT proteger o acesso aos recursos e aplicativos em todo o data center corporativo e na nuvem, permitindo níveis adicionais de validação como autenticação multifator e políticas de acesso condicional. Este artigo fornece uma visão geral do núcleo recursos de segurança do Azure que ajudam com o gerenciamento de identidades. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-identity-management-security-overview"></a>Visão geral de segurança de gerenciamento de identidade Azure

Identidade e Microsoft access gerenciamento soluções ajudam IT proteger o acesso aos recursos e aplicativos em todo o data center corporativo e na nuvem, permitindo níveis adicionais de validação como autenticação multifator e políticas de acesso condicional. Atividades suspeitas monitoramento por meio de segurança avançada, relatórios, auditoria e alertas ajuda a reduzir possíveis problemas de segurança. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) fornece logon único para milhares de nuvem (SaaS) aplicativos e acesso a aplicativos web executar no local.

Benefícios de segurança do Azure Active Directory (AD) incluem a capacidade de:

- Criar e gerenciar uma identidade única para cada usuário em sua empresa híbridos, mantendo os usuários, grupos e dispositivos em sincronia
- Fornecer acesso de logon único para seus aplicativos incluindo milhares de aplicativos de SaaS previamente integrados
- Ativar a segurança de acesso do aplicativo aplicando regras a autenticação baseada em multifator para ambos os locais e na nuvem aplicativos
- Provisionar o acesso remoto seguro para aplicativos de web de local por meio de Proxy de aplicativo do Azure AD

O objetivo deste artigo é fornecer uma visão geral do núcleo do recursos de segurança do Azure que ajudam com o gerenciamento de identidades. Também fornecemos links para artigos que lhe dará detalhes de cada recurso para saber mais.  

O artigo aborda os seguintes recursos de gerenciamento de identidade do Azure principais:

- Logon único
- Proxy reverso
- Autenticação multifator
- Monitoramento de segurança, alertas e relatórios de baseados em aprendizado de máquina
- Gerenciamento de identidades e acesso do consumidor
- Registro de dispositivo
- Gerenciamento de identidades privilegiado
- Proteção de identidade
- Gerenciamento de identidades híbrido

## <a name="single-sign-on"></a>Logon único

Único logon (SSO) significa poder acessar todos os aplicativos e os recursos que você precisa fazer negócios, entrando apenas uma vez, usando uma conta de usuário único. Uma vez conectado, você pode acessar todos os aplicativos que você precisa sem exigência de autenticação (por exemplo, digite uma senha) uma segunda vez.

Muitas organizações dependem de software como um aplicativos de serviço (SaaS) como o Office 365, caixa e Salesforce para produtividade do usuário final. Historicamente, a equipe de TI necessária criar e atualizar contas de usuário em cada aplicativo SaaS individualmente e os usuários tinham lembrar uma senha para cada aplicativo SaaS.

Azure AD estende do Active Directory local para a nuvem, permitindo que os usuários usar sua conta organizacional primária não apenas entrar para seus dispositivos de domínio e recursos da empresa, mas também todos os aplicativos da web e SaaS necessárias para o seu trabalho.

Não somente os usuários não precisam gerenciar vários conjuntos de nomes de usuário e senhas, acesso de aplicativo pode ser automaticamente provisionados ou eliminação provisionados grupos organizacionais com base em e seus status como funcionário. Azure AD introduz segurança e controles de gestão de acesso que permitem gerenciar centralmente o acesso dos usuários em aplicativos SaaS.

Saiba Mais:

- [Visão geral de logon único](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
- [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
- [Integrar o logon único Azure Active Directory com aplicativos de SaaS](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Proxy reverso

Proxy de aplicativo do Azure AD permite publicar aplicativos de local, como sites [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)e [IIS](http://www.iis.net/)-com base em aplicativos dentro de sua rede privada e fornece acesso seguro aos usuários fora da sua rede. Proxy do aplicativo fornece acesso remoto e logon único (SSO) para muitos tipos de aplicativos do local da web com milhares de aplicativos SaaS Azure AD compatível com. Funcionários podem fazer login em seus aplicativos de home em seus próprios dispositivos e autenticar este proxy de baseado na nuvem.

Saiba Mais:

- [Habilitando o Proxy de aplicativo do Azure AD](../active-directory/active-directory-application-proxy-enable.md)
- [Publicar aplicativos usando o Proxy de aplicativo do Azure AD](../active-directory/active-directory-application-proxy-publish.md)
- [Single sign-on com Proxy de aplicativo](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
- [Trabalhando com acesso condicional](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Autenticação multifator
A autenticação multifator Azure (MFA) é um método de autenticação que requer o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança ao usuário entrada e transações. MFA ajuda a proteger informações acesso aos dados e aplicativos atendendo demanda do usuário para um processo de entrada simples. Ele oferece autenticação sólida via uma variedade de opções de verificação — chamada telefônica, uma mensagem de texto ou aplicativo móvel notificação ou verificação de código e 3º festa OAuth tokens.

Saiba Mais:

- [Autenticação multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [O que é a autenticação multifator Azure?](../multi-factor-authentication/multi-factor-authentication.md)
- [Como funciona a autenticação multifator do Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitoramento de segurança, alertas e relatórios de baseados em aprendizado de máquina

Monitoramento de segurança e alertas e relatórios baseados em aprendizado de máquina que identificam padrões de acesso inconsistente podem ajudá-lo a proteger seus negócios. Você pode usar o acesso do Azure Active Directory e relatórios de uso para obter visibilidade a integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de diretório melhor pode determinar onde os possíveis riscos de segurança podem estar para que eles possam planejar adequadamente a reduzir esses riscos.

No portal de clássico do Azure, os relatórios são categorizados das seguintes maneiras:

- Relatórios de anomalia – contêm eventos que descobrimos ser anormais de entrada. Nosso objetivo é Explique você tal atividade e permitem que você seja capaz de tomar uma decisão sobre se um evento é suspeito.
- Relatórios de aplicativos integrados – fornecem visões como aplicativos em nuvem estão sendo usados em sua organização. Active Directory do Azure oferece integração com milhares de aplicativos em nuvem.
- Relatórios de erros – indicam erros que podem ocorrer ao provisionar contas para aplicativos externos.
- Relatórios de específicas do usuário – exibir dispositivo/entrada nos dados de atividade de um usuário específico.
- Logs de atividades – contêm um registro de todos os eventos auditados dentro as últimas 24 horas, últimos 7 dias, ou últimos 30 dias, bem como alterações de atividade do grupo e atividade de registro e redefinição de senha.

Saiba Mais:

- [Exibir seus relatórios do access e o uso](../active-directory/active-directory-view-access-usage-reports.md)
- [Introdução aos relatórios do Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
- [Guia de relatórios do Active Directory do Azure](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Gerenciamento de identidades e acesso do consumidor

Azure B2C diretório ativo é um serviço de gerenciamento de identidades global, altamente disponível para aplicativos voltados para o consumidor que se expande para centenas de milhões de identidades. Ele pode ser integrado em mobile e plataformas da web. Seus consumidores podem fazer logon para todos os seus aplicativos por meio de experiências personalizáveis usando suas contas sociais existentes ou criar novas credenciais.

No passado, os desenvolvedores de aplicativo que desejam inscrever-se e entre os consumidores em seus aplicativos teria gravado seu próprio código. E eles têm usados sistemas ou bancos de dados local para armazenar os nomes de usuário e senhas. Azure B2C diretório ativo oferece a sua organização uma melhor maneira para integrar o gerenciamento de identidades do consumidor em aplicativos com a Ajuda de uma plataforma segura e baseada em padrões e um conjunto de políticas de linguagem grande.

Quando você usa o Azure Active Directory B2C, seus consumidores Inscreva seus aplicativos utilizando suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de email e senha, ou nome de usuário e senha).

Saiba Mais:

- [O que é Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
- [Visualização do Azure Active Directory B2C: entrar para cima e entre os consumidores em seus aplicativos](../active-directory-b2c/active-directory-b2c-overview.md)
- [Visualização de B2C do Active Directory do Azure: Tipos de aplicativos](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registro de dispositivo

O registro de dispositivos do Azure AD é a base para cenários de baseado em dispositivo [acesso condicional](../active-directory/active-directory-conditional-access-on-premises-setup.md) . Quando um dispositivo é registrado, o registro de dispositivos do Azure Active Directory fornece o dispositivo com uma identidade que é usada para autenticar o dispositivo quando o usuário entra. O dispositivo autenticado e os atributos do dispositivo, em seguida, podem ser usados de aplicar políticas de acesso condicional para aplicativos que são hospedados na nuvem e local.

Quando combinado com uma solução de gerenciamento (MDM) de dispositivo móvel como o Intune, os atributos de dispositivo no Active Directory do Azure são atualizados com informações adicionais sobre o dispositivo. Isso permite que você crie regras de acesso condicional que impõem acesso de dispositivos para atender aos seus padrões de segurança e conformidade.

Saiba Mais:

- [Começar a usar o registro de dispositivos do Azure Active Directory](../active-directory/active-directory-conditional-access-device-registration-overview.md)
- [Configuração de acesso condicional local usando o registro de dispositivos do Azure Active Directory](../active-directory/active-directory-conditional-access-on-premises-setup.md)
- [Registro de dispositivo automática com dispositivos de domínio do Azure Active Directory para Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)

## <a name="privileged-identity-management"></a>Gerenciamento de identidades privilegiado
Gerenciamento de identidades Azure Active Directory (AD) privilegiados permite gerenciar, controlar e monitorar suas identidades privilegiadas e acesso aos recursos no Azure AD, bem como outros Microsoft online services como o Office 365 ou Microsoft Intune.

Às vezes, os usuários precisam realizar operações privilegiadas em recursos do Azure ou o Office 365 ou outros aplicativos SaaS. Isso geralmente significa que as organizações precisam dar a elas acesso privilegiado permanente no Azure AD. Este é um risco de segurança crescente para recursos hospedados em nuvem porque organizações suficientemente não podem monitorar o que os usuários estão fazendo com seus privilégios de administrador. Além disso, se uma conta de usuário com acesso privilegiado é comprometida, que uma violação poderão afetar seu segurança de nuvem geral. Gerenciamento de identidades do Azure AD privilegiado ajuda a resolver esse risco.

Gerenciamento de identidades do Azure AD privilegiado permite que você:

- Ver quais usuários são administradores do Azure AD
- Habilitar sob demanda, "no momento" acesso administrativo ao Microsoft Online Services como o Intune e o Office 365
- Obtenha relatórios sobre alterações e histórico de acesso de administrador em atribuições de administrador
- Receber alertas sobre o acesso a uma função privilegiado

Saiba Mais:

- [Gerenciamento de identidades do Azure AD privilegiado](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Funções no gerenciamento de identidades do Azure AD privilegiado](../active-directory/active-directory-privileged-identity-management-roles.md)
- [Azure gerenciamento privilegiados de identidade de AD: Como adicionar ou remover uma função de usuário](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Proteção de identidade
Proteção de identidade AD Azure é um serviço de segurança que fornece uma visão consolidada em eventos de risco e possíveis vulnerabilidades afetando identidades da sua organização. Proteção de identidade utiliza os recursos de detecção de anomalias existente Azure do Active Directory (disponíveis através relatórios do Azure AD de atividades anormais) e apresenta novos tipos de evento de risco que podem detectar problemas em tempo real.

Saiba Mais:

- [Proteção de identidade do Active Directory do Azure](../active-directory/active-directory-identityprotection.md)
- [Canal 9: Azure AD e identidade de slides: visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Gerenciamento de identidades híbrido

Abordagem da Microsoft ao identidade abrange locais e na nuvem, criando uma identidade de usuário único para autenticação e autorização para todos os recursos, independentemente de local.

Saiba Mais:

- [Whitepaper de identidade híbrido](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
- [Active Directory do Azure](https://azure.microsoft.com/documentation/services/active-directory/)
- [Blog da equipe do Active Directory](https://blogs.technet.microsoft.com/ad/)
