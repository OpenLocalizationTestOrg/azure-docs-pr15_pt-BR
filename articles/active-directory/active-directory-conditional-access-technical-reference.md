
<properties
    pageTitle="Referência técnica do Azure Active Directory Access condicional | Microsoft Azure"
    description="Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas que você escolher quando a autenticação do usuário e antes de permitir acesso ao aplicativo. Depois que essas condições forem atendidas, o usuário autenticado e permissão para acessar o aplicativo."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referência técnica do Azure Active Directory Access condicional

## <a name="services-enabled-with-conditional-access"></a>Serviços habilitados com acesso condicional
Regras de acesso condicional são suportadas em vários tipos de aplicativos do Azure AD. Esta lista inclui:

- Aplicativos federados da Galeria de aplicativo Azure AD
- Aplicativos de SSO de senha da Galeria de aplicativo Azure AD
- Aplicativos registrados com o Proxy de aplicativo do Azure
- Desenvolvido linha de negócios e aplicativos de vários locatários registrados com o Azure AD
- Visual Studio on-line
- Aplicativo Remote Azure
-   Dynamics CRM
- Microsoft Office 365 Yammer
- Exchange Online do Microsoft Office 365
- Microsoft Office 365 SharePoint Online (inclui o SkyDrive PRO)


## <a name="enable-access-rules"></a>Habilitar as regras de acesso

Cada regra pode ser habilitada ou desabilitada em um por bases de aplicativo. Quando as regras são **Diante** serão habilitados e impostas para usuários acessando o aplicativo. Quando elas são **desativado** eles não serão usados e não afetará o sinal de usuários na experiência.

## <a name="applying-rules-to-specific-users"></a>Aplicando regras a usuários específicos
Regras podem ser aplicadas em conjuntos específicos de usuários com base no grupo de segurança definindo **Aplicar a**. **Aplicar a** pode ser definido para **Todos os usuários** ou **grupos**. Quando definido para **Todos os usuários** as regras serão aplicadas a qualquer usuário com acesso ao aplicativo. A opção **grupos** permite específicas de segurança e grupos de distribuição a ser selecionada, regras serão aplicadas somente para esses grupos.

Ao implantar uma regra, é comum primeiro aplicá-lo um conjunto limitado de usuários, que são membros de grupos um conduzindo. Depois de concluir a regra pode ser aplicada a **Todos os usuários**. Isso fará com que a regra ser imposto para todos os usuários na organização.

Selecionar grupos também podem ser isento de política usando a opção **exceto** . Todos os membros desses grupos serão isentos mesmo se eles aparecem em um grupo incluído.

## <a name="at-work-networks"></a>"No trabalho" redes


Regras de acesso condicional que usa uma rede "no trabalho", dependem de intervalos de endereços IP confiáveis que foram configurados no Azure AD, ou use da declaração "dentro da rede corporativa" do AD FS. Essas regras incluem:

- Exigir autenticação multifator quando não estiver no trabalho
- Bloquear o acesso quando não estiver no trabalho

Opções para especificar redes "no trabalho"

1. Configure intervalos de endereços IP confiáveis na [página de configuração de autenticação multifator](../multi-factor-authentication/multi-factor-authentication-whats-next.md). Política de acesso condicional usará os intervalos configurados em cada solicitação de autenticação e emissão de token para avaliar regras. 
2. Configurar o uso de dentro reivindicar de rede corporativa, essa opção pode ser usada com diretórios federados, usam AD FS. [Saber mais sobre dentro declarações de coronet](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Configure intervalos de endereços IP públicos. Na guia Configurar, para seu diretório, você pode definir endereços IP públicos. Acesso condicional use essas como 'at work' endereços IP, isso permite intervalos adicionais configurar, acima do limite de endereço IP 50 imposta pela página de configuração de MFA.



## <a name="rules-based-on-application-sensitivity"></a>Regras baseadas em sensibilidade do aplicativo

Regras são configuradas por aplicativo, permitindo que os serviços de alto valor para ser protegidos sem afetar o acesso a outros serviços. Regras de acesso condicional podem ser configuradas na guia **Configurar** do aplicativo. 

Regras atualmente oferecidas:

- **Exigir autenticação multifator**
 - Todos os usuários que esta política é aplicada a será necessários para autenticar via autenticação multifator pelo menos uma vez.
 
- **Exigir autenticação multifator quando não estiver no trabalho**
 - Se esta política é aplicada, todos os usuários precisarão ter realizado pelo menos uma vez a autenticação multifator se eles acessarem o serviço de um local remoto inativo. Se eles movimentem de um trabalho para um local remoto, eles deverão executar autenticação multifator ao acessar o serviço.
 
- **Bloquear o acesso quando não estiver no trabalho** 
 - Quando os usuários movem do trabalho para um local remoto, eles serão bloqueados, se a política de "Bloquear o acesso quando não estiver no trabalho" é aplicada a eles.  Eles novamente terão acesso quando está em um local de trabalho.


## <a name="related-topics"></a>Tópicos relacionados

- [Proteger o acesso ao Office 365 e outros aplicativos conectado ao Azure Active Directory](active-directory-conditional-access.md)
- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
