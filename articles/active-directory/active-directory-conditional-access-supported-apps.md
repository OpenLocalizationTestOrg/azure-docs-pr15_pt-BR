<properties
    pageTitle="Aplicativos que usam regras de acesso condicional do Active Directory do Azure | Microsoft Azure"
    description="Com controle de acesso condicional, o Azure Active Directory verifica condições específicas quando ele autentica o usuário e para permitir o acesso do aplicativo."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/26/2016"
    ms.author="markvi"/>

# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Aplicativos que usam regras de acesso condicional do Active Directory do Azure

Regras de acesso condicional são compatíveis com o Azure Active Directory (AD Azure)-aplicativos conectados, previamente integrado federado software como um serviço (SaaS) aplicativos, que usam senha logon único (SSO), aplicativos de linha de negócios e aplicativos que usam o Proxy de aplicativo do Azure AD. Para obter uma lista detalhada de aplicativos para o qual você pode usar o acesso condicional, consulte [serviços habilitados com acesso condicional](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). Acesso condicional funciona com aplicativos de desktop e móveis que usam autenticação moderna. Neste artigo, cobriremos como condicional funciona de acesso nos aplicativos de desktop e móveis.

Você pode usar o Azure AD entrar páginas de aplicativos que usam autenticação moderna. Com uma página de entrada, um usuário será solicitado a fornecer autenticação multifator. Uma mensagem é mostrada se o acesso do usuário será bloqueado. Autenticação moderna é necessária para o dispositivo autenticar com Azure AD, para que as diretivas de acesso condicional baseado no dispositivo são avaliadas.

É importante saber quais aplicativos podem usar as regras de acesso condicional e as etapas que talvez você precise tomar para proteger outros pontos de entrada do aplicativo.

## <a name="applications-that-use-modern-authentication"></a>Aplicativos que usam autenticação moderna

> [AZURE.NOTE] Se você tiver uma política de acesso condicional no Azure AD que tenha um equivalente no Office 365, configure ambas as políticas de acesso condicional. Isso faria se aplicam, por exemplo, às políticas de acesso condicional para o Exchange Online ou no SharePoint Online.

Os seguintes aplicativos permitem o acesso condicional para Office 365 e outros aplicativos de serviço Azure AD conectados:

| Serviço de destino  | Plataforma  | Aplicativo                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Exchange Online do Office 365 | Windows 10|Aplicativo calendário/mail/pessoas, do Outlook 2016, o Outlook 2013 (com autenticação moderna), o Skype for Business (com autenticação moderna)|
|Exchange Online do Office 365| Windows 8.1, Windows 7 |Outlook 2016, Outlook 2013 (com autenticação moderna), Skype for Business (com autenticação moderno)|
|Exchange Online do Office 365|iOS, Android|  Aplicativo móvel do Outlook|
|Exchange Online do Office 365|Mac OS X| Outlook 2016 para autenticação multifator e local suporte de política baseada em dispositivo planejado no futuro, Skype para Business suporte planejado para o futuro|
|SharePoint Online do Office 365|Windows 10| Aplicativos do Office 2016, aplicativos do Office Universal, Office 2013 (com autenticação moderna), OneDrive para suporte do aplicativo de negócios (próximo cliente de sincronização de geração ou NGSC) planejado no futuro, o suporte de grupos do Office planejado no futuro, o suporte do aplicativo SharePoint planejado para o futuro|
|SharePoint Online do Office 365|Windows 8.1, Windows 7|Aplicativos do Office 2016, Office 2013 (com autenticação moderna), o aplicativo OneDrive for Business (cliente de sincronização do Groove)|
|SharePoint Online do Office 365|iOS, Android|  Aplicativos móveis do Office |
|SharePoint Online do Office 365|Mac OS X| Aplicativos do Office 2016 para autenticação multifator e local suporte de política baseada em dispositivo planejado para o futuro|
|Yammer do Office 365|Windows 10, iOS e Android | Aplicativo Yammer do Office|
|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS e Android | Aplicativo do Dynamics CRM|
|Serviço de PowerBI|Windows 10, Windows 8.1, Windows 7, iOS e Android | Aplicativo de PowerBI|
|Serviço de aplicativo Remote Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android e Mac OS X |Aplicativo Remote Azure|
|Qualquer serviço de aplicativo Meus aplicativos|Android e iOS|Qualquer serviço de aplicativo Meus aplicativos |


## <a name="applications-that-do-not-use-modern-authentication"></a>Aplicativos que não usam autenticação moderna

Atualmente, você deve usar outros métodos para bloquear o acesso aos aplicativos que não usam autenticação moderna. Regras de acesso para os aplicativos que não usam autenticação moderna não são impostas pelo access condicional. Isso é principalmente uma consideração para acesso do Exchange e do SharePoint. A maioria das versões anteriores dos aplicativos usar protocolos de controle de acesso mais antigos.

### <a name="control-access-in-office-365-sharepoint-online"></a>Controlar o acesso do Office 365 SharePoint Online
Você pode desativar protocolos herdados para acesso do SharePoint usando o cmdlet Set-SPOTenant. Use esse cmdlet para impedir que os clientes do Office que usam protocolos de autenticação não moderno de acessar os recursos do SharePoint Online.

**Exemplo de comando**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Controlar o acesso no Exchange Online do Office 365

Exchange oferece duas categorias principais de protocolos. Examine as opções a seguir e, em seguida, selecione a política adequado para sua organização.

-   **O Exchange ActiveSync**. Por padrão, as políticas de acesso condicional para autenticação multifator e local não são impostas para o Exchange ActiveSync. Você precisa proteger o acesso a esses serviços por configurar política do Exchange ActiveSync diretamente ou bloqueando o Exchange ActiveSync usando regras de serviços de Federação do Active Directory (AD FS).
-   **Protocolos herdado**. Você pode bloquear protocolos herdados com o AD FS. Isso bloqueia o acesso aos clientes mais antigos do Office, como o Office 2013 sem versões moderna autenticação habilitada e em versões anteriores do Office.


### <a name="use-ad-fs-to-block-legacy-protocol"></a>Use o AD FS para bloquear o protocolo de legado

Você pode usar as regras de exemplo a seguir para bloquear o acesso de protocolo herdados no nível do AD FS. Escolha entre duas configurações comuns.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Opção 1: Permitir que o Exchange ActiveSync e permitir aplicativos herdados, mas apenas na intranet

Aplicando as seguintes três regras para o AD FS confiar confiança de festa de plataforma de identidade do Microsoft Office 365, o tráfego do Exchange ActiveSync e o navegador e o tráfego de autenticação moderno, tem acesso. Aplicativos herdados estão bloqueados da extranet.

##### <a name="rule-1"></a>Regra 1

    @RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Regra 2

    @RuleName = “Allow Exchange ActiveSync ”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Regra 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Opção 2: Permitir que o Exchange ActiveSync e bloquear aplicativos herdados

Aplicando as seguintes três regras para o AD FS confiar confiança de festa de plataforma de identidade do Microsoft Office 365, o tráfego do Exchange ActiveSync e o navegador e o tráfego de autenticação moderno, tem acesso. Aplicativos herdados são bloqueados de qualquer local.

##### <a name="rule-1"></a>Regra 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regra 2

    @RuleName = “Allow Exchange ActiveSync”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regra 3

    @RuleName = “Allow extranet browser and browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");
