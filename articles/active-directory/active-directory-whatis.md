<properties
    pageTitle="O que é Azure Active Directory?"
    description="Use o Active Directory do Azure para estender sua identidades locais existentes na nuvem ou desenvolver aplicativos do Azure AD integrada."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="markusvi"/>


# <a name="what-is-azure-active-directory"></a>O que é Azure Active Directory?





Azure Active Directory (AD Azure) é baseado em nuvem do locatário vários diretório Microsoft e serviço de gerenciamento de identidade.

Para administradores de TI, o Azure AD fornece uma solução acessível e fácil de usar para dar único acesso sign-on (SSO) de funcionários e parceiros de negócios para [milhares de aplicativos SaaS de nuvem](http://blogs.technet.com/b/ad/archive/2014/09/03/50-saas-apps-now-support-federation-with-azure-ad.aspx) como o Office 365, Salesforce.com, DropBox e Concur.

Para desenvolvedores de aplicativos, que Azure AD você focalize a criação de seu aplicativo, tornando mais rápida e simples a integração com uma solução de gerenciamento de identidade do mundo classe usada por milhões de organizações em todo o mundo.

Azure AD também inclui um conjunto completo de recursos de gerenciamento de identidade, incluindo a autenticação multifator, registro de dispositivo, gerenciamento de senha de autoatendimento, gerenciamento de grupo de autoatendimento, gerenciamento de conta com privilégios, baseado em função controle de acesso, monitoramento de uso do aplicativo, avançada de auditoria e monitoramento de segurança e alertas. Esses recursos podem ajudar aplicativos baseado em nuvem de seguro, simplifique os processos de TI, cortar custos e ajudar a garantir que as metas de conformidade corporativa são atendidas.

Além disso, com apenas [quatro cliques](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx), Azure AD pode ser integrado com um Windows Server Active Directory existente, dando organizações a capacidade de aproveitar seus existentes investimentos de identidade para gerenciar o acesso à nuvem com base em aplicativos SaaS ao local.

Se você for um cliente do Office 365, Azure ou Dynamics CRM Online, você não pode perceber que você já estiver usando AD Azure. Cada locatário do Office 365, Azure e Dynamics CRM já é um locatário do Azure AD. Sempre que desejar, que você pode começar a usar o locatário para gerenciar o acesso a milhares de outros aplicativos de nuvem Azure AD integra-se!





![Azure AD Connect pilha](./media/active-directory-whatis/Azure_Active_Directory.png)


## <a name="how-reliable-is-azure-ad"></a>Como confiável é Azure AD?

O design de vários locatários, localização geográfica, distribuídos alta disponibilidade Azure AD significa que você pode confiar nele para as necessidades de negócios mais importantes. Ficando sem centros de 28 dados em todo o mundo com failover automatizado, você terá o conforto de saber que o Azure AD é altamente confiável e que mesmo se um data center desce, cópias de seu diretório de dados são ao vivo pelo menos dois mais regional disperso centros de dados e disponíveis para acesso instantâneo.

Para obter mais detalhes, consulte [Os contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).



## <a name="what-are-the-benefits-of-azure-ad"></a>Quais são os benefícios do Azure AD?

Sua organização pode usar o Azure AD para aumentar a produtividade do funcionário, simplifique os processos de TI, melhorar a segurança e cortar custos de várias maneiras:

-   Adotar rapidamente os serviços de nuvem, fornecendo aos funcionários e parceiros com um logon único fácil na experiência da plataforma Azure AD totalmente automatizado SaaS aplicativo gerenciamento e provisionamento serviços capacidades de acesso.
-   Habilitar os funcionários com acesso aos aplicativos de nuvem de classe mundial e capacidades de autoatendimento de onde eles precisam trabalhar nos dispositivos que eles adoram usar.
-   Facilidade e segurança gerencie o acesso de funcionário e fornecedor para suas contas de mídia social corporativa.
-   Melhore a segurança de aplicativo com autenticação multifator do Azure AD e acesso condicional.
-   Implementar gerenciamento de acesso de aplicativo consistente, pessoal, permitindo que os proprietários de negócios para mover-se rapidamente enquanto recortando os custos de TI e custos gerais indiretos.
-   Monitorar o uso do aplicativo e proteger sua empresa contra ameaças avançadas com segurança monitoramento e geração de relatórios.
-   Móvel (acesso remoto seguro) para aplicativos de local.






## <a name="how-can-i-get-started"></a>Como posso podem começar a?
-   Se você for um administrador de TI:
 - [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) -Você pode inscrever para uma avaliação gratuita do 30 hoje e implantar sua solução de nuvem primeira em menos de 5 minutos usando esse link
 - Ler o "Guia de Introdução Azure AD" para obter dicas e truques sobre colocar um locatário do Azure AD em funcionamento rapidamente
-   Se você for um desenvolvedor:
 - Confira o nosso [Guia dos desenvolvedores](active-directory-developers-guide.md) para o Active Directory do Azure
 - [Iniciar uma avaliação](https://azure.microsoft.com/trial/get-started-active-directory/) – entre para uma gratuita de 30 dias avaliação hoje e comece a integração de seus aplicativos com o Azure AD



## <a name="where-can-i-learn-more"></a>Onde posso saber mais?

Temos uma tonelada de ótimos recursos online para ajudá-lo a saber tudo sobre o Azure AD. Aqui está uma lista de artigos excelentes para você começar:


- [Habilitando o diretório para o gerenciamento de híbrido com o Azure AD Connect](active-directory-aadconnect.md)

- [Segurança adicional para um mundo nunca conectado](../multi-factor-authentication/multi-factor-authentication.md)

- [Automatizar usuário provisionamento e desprovisionamento para aplicativos SaaS com o Active Directory do Azure](active-directory-saas-app-provisioning.md)

- [Introdução aos relatórios do Azure AD](active-directory-reporting-getting-started.md)

- [Gerenciar suas senhas de qualquer lugar](active-directory-passwords.md)

- [O que é o acesso de aplicativo e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

- [Automatizar usuário provisionamento e desprovisionamento para aplicativos SaaS com o Active Directory do Azure](active-directory-saas-app-provisioning.md)

- [Como fornecer acesso remoto seguro para aplicativos de local](active-directory-application-proxy-get-started.md)

- [Gerenciando o acesso aos recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

- [O que é o licenciamento de Microsoft Azure Active Directory?](active-directory-licensing-what-is.md)

- [Como pode descobrir aplicativos de nuvem unsanctioned que são usados em minha organização](active-directory-cloudappdiscovery-whatis.md)
