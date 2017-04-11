<properties
    pageTitle="Acesso condicional para aplicativos publicados com o Proxy de aplicativo do Azure AD"
    description="Discute como configurar o acesso condicional para aplicativos que você publicar a ser acessado remotamente usando o Proxy de aplicativo do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-conditional-access"></a>Trabalhando com acesso condicional

Você pode configurar regras de acesso para conceder acesso condicional aos aplicativos publicados usando o Proxy de aplicativo. Isso permite que você:

- Exigir autenticação multifator por aplicativo
- Exigir autenticação multifator somente quando os usuários não estão no trabalho
- Impedir usuários de acessar o aplicativo quando não estiverem no trabalho

Essas regras podem ser aplicadas a todos os usuários e grupos ou somente para usuários e grupos específicos. Por padrão, a regra será aplicada a todos os usuários que têm acesso ao aplicativo. No entanto, a regra também pode ser restritos aos usuários que são membros de grupos de segurança especificados.  

Regras de acesso são avaliadas quando um usuário acessa um aplicativo federado que usa OAuth 2.0, OpenID conectar, SAML ou Web Services Federation. Além disso, as regras de acesso são avaliadas com OAuth 2.0 e OpenID conectar quando um token de atualização é usado para adquirir um token de acesso.

## <a name="conditional-access-prerequisites"></a>Pré-requisitos de acesso condicional

- Assinatura Premium do Active Directory do Azure
- Um locatário do Active Directory do Azure federado ou gerenciado
- Locatários federados requerem que a autenticação multifator (MFA) ser habilitada  
    ![Configurar regras de acesso - exigir autenticação multifator](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurar a autenticação multifator por aplicativo
1. Entrar como um administrador no portal de clássico do Azure.
2. Vá para o Active Directory e selecione a pasta na qual você deseja habilitar o Proxy de aplicativo.
3. Clique em **aplicativos** e role até a seção de **Regras de acesso** . A seção de regras de acesso aparece apenas para aplicativos publicados usando o Proxy de aplicativo que usam autenticação federada.
4. Habilite a regra selecionando **Habilitar regras de acesso** para **em**.
5. Especifique os usuários e grupos aos quais as regras serão aplicadas. Use o botão **Adicionar grupo** para selecionar um ou mais grupos aos quais a regra de acesso será aplicada. Esta caixa de diálogo também pode ser usada para remover os grupos selecionados.  Quando as regras são selecionadas para aplicar a grupos, as regras de acesso só serão impostas para usuários que fazem parte de um dos grupos de segurança especificados.  

  - Para excluir explicitamente grupos de segurança da regra, marque **exceto** e especifique um ou mais grupos. Os usuários que são membros de um grupo na lista exceto não precisarão realizar autenticação multifator.  

  - Se um usuário tiver sido configurado usando o recurso de autenticação multifator por usuário, essa configuração terá precedência sobre as regras de autenticação multifator do aplicativo. Isso significa que um usuário que foi configurado para a autenticação multifator por usuário será necessário realizar autenticação multifator, mesmo se eles foram isentos das regras de autenticação multifator do aplicativo. Saiba mais sobre [autenticação multifator e configurações por usuário](../multi-factor-authentication/multi-factor-authentication.md).

6. Selecione a regra de acesso que você deseja definir:
    - **Autenticação multifator exigir**: ao qual aplicam as regras de acesso de usuários serão necessários para autenticação multifator concluída antes de acessar o aplicativo ao qual a regra se aplica.
    - **Autenticação multifator exigir quando não estiver no trabalho**: os usuários que tentarem acessar o aplicativo de um endereço IP confiável não precisarão realizar autenticação multifator. Os intervalos de endereços IP confiáveis podem ser configurados na página de configurações de autenticação multifator.
    - **Bloquear o acesso quando não estiver no trabalho**: os usuários que tentarem acessar o aplicativo de fora da sua rede corporativa não poderão acessar o aplicativo.


## <a name="configuring-mfa-for-federation-services"></a>Configurando MFA para serviços de Federação
Para federados locatários, autenticação multifator (MFA) pode ser executada por Azure Active Directory ou o local servidor do AD FS. Por padrão, MFA ocorrerá em qualquer página hospedada pelo Active Directory do Azure. Para configurar MFA local, executar o Windows PowerShell e use a propriedade – SupportsMFA para definir o módulo Azure AD.

O exemplo a seguir mostra como habilitar MFA locais usando o [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) no locatário contoso.com:`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Além de definir esse sinalizador, a instância do locatário federados AD FS deverá ser configurada para executar a autenticação multifator. Siga as instruções para [implantar o Microsoft Azure autenticação multifator local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## <a name="see-also"></a>Consulte também

- [Trabalhando com aplicativos de reconhecimento de declarações](active-directory-application-proxy-claims-aware-apps.md)
- [Publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Ativar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Publicar aplicativos usando seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
