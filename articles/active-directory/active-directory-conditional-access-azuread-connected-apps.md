<properties
    pageTitle="Azure acesso condicional para aplicativos SaaS | Microsoft Azure"
    description="Acesso condicional no Azure AD permite que você configure as regras de acesso de autenticação multifator de por aplicativo e a capacidade de bloquear o acesso de usuários não em uma rede confiável. "
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
    ms.date="09/26/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Introdução ao acesso condicional do Azure Active Directory

Azure Active Directory acesso condicional para aplicativos [SaaS](https://azure.microsoft.com/overview/what-is-saas/) e Azure AD conectados permite que aplicativos que você configura o acesso condicional com base em grupo, local e sensibilidade do aplicativo. 

Com acesso condicional com base na sensibilidade do aplicativo, você pode definir regras de acesso de autenticação multifator (MFA) por aplicativo. MFA por aplicativo fornece a capacidade de bloquear o acesso de usuários que não estão em uma rede confiável. Você pode aplicar regras MFA para todos os usuários que estejam atribuídos ao aplicativo ou apenas para os usuários em grupos de segurança especificados.  Os usuários podem ser excluídos da necessidade MFA se eles estiverem acessando o aplicativo de um endereço IP que está dentro da rede da organização.

Esses recursos estarão disponíveis para os clientes que compraram uma licença do Azure Active Directory Premium.

## <a name="scenario-prerequisites"></a>Pré-requisitos de cenário
* Licença do Active Directory do Azure Premium

* Federados ou gerenciado locatário do Active Directory do Azure

* Locatários federados requerem que a autenticação multifator ser habilitada.

## <a name="configure-per-application-access-rules"></a>Configurar regras de acesso por aplicativo

Esta seção descreve como configurar regras de acesso por aplicativo.

1. Entre portal do Azure clássico usando uma conta que é um administrador global para Azure AD.
2. No painel esquerdo, selecione **Active Directory**.
3. Na guia diretório, selecione seu diretório.
4. Selecione a guia de **aplicativos** .
5. Selecione o aplicativo que a regra será definida para.
6. Selecione a guia **Configurar** .
7. Role para baixo até a seção de regras de acesso. Selecione a regra de acesso desejado.
8. Especifique os usuários que a regra será aplicada.
9. Habilite a política selecionando **habilitado para estar ligado**.

##<a name="understanding-access-rules"></a>Noções básicas sobre regras de acesso

Esta seção fornece uma descrição detalhada das regras de acesso compatíveis com o acesso de aplicativo condicional do Azure.

### <a name="specifying-the-users-the-access-rules-apply-to"></a>Os usuários que especifica as regras de acesso se aplicam a

Por padrão, a política será aplicada a todos os usuários que têm acesso ao aplicativo. No entanto, você também pode restringir a política aos usuários que são membros de grupos de segurança especificados. O botão **Adicionar grupo** é usado para selecionar um ou mais grupos da caixa de diálogo de seleção de grupo que a regra de acesso será aplicada. Esta caixa de diálogo também pode ser usada para remover os grupos selecionados. Quando as regras são selecionadas para aplicar a grupos, as regras de acesso só serão impostas para usuários que fazem parte de um dos grupos de segurança especificados.

Grupos de segurança também podem ser excluídos explicitamente da política selecionando a opção **, exceto** e especificando um ou mais grupos. Os usuários que são membros de um grupo na lista **, exceto** não serão sujeito a exigência de autenticação multifator, mesmo se eles são membros de um grupo que a regra de acesso se aplica.
A regra de acesso mostrada abaixo exigirão todos os usuários do grupo Gerentes para usar a autenticação multifator quando estiver acessando o aplicativo.

![Configuração de regras de acesso condicional com MFA](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>Regras de acesso condicional com MFA
Se um usuário tiver sido configurado usando o recurso de autenticação multifator por usuário, essa configuração no usuário combinará com as regras de autenticação multifator do aplicativo. Isso significa que um usuário que foi configurado para a autenticação multifator por usuário deverão executar autenticação multifator, mesmo se eles foram isentos das regras de autenticação multifator do aplicativo. Saiba mais sobre as configurações de autenticação e por usuário multifatores.

### <a name="access-rule-options"></a>Opções de regra de acesso
As opções a seguir são suportadas:

* **Exigir autenticação multifator**: os usuários aos quais as regras de acesso se aplicam, será solicitado a autenticação multifator concluída antes de acessar o aplicativo para o qual a política se aplica.

* **Exigir autenticação multifator quando não estiver no trabalho**: um usuário provenientes de um endereço IP confiável não precisarão realizar autenticação multifator. Os intervalos de endereços IP confiáveis podem ser configurados na página de configurações de autenticação multifator.

* **Bloquear o acesso quando não estiver no trabalho**: um usuário que não é proveniente de um endereço IP confiável será bloqueado. Os intervalos de endereços IP confiáveis podem ser configurados na página de configurações de autenticação multifator.

### <a name="setting-rule-status"></a>Definir status de regra
Status da regra de acesso permite ativar ou desativar o as regras. Quando as regras de acesso estejam desativadas, a exigência de autenticação multifator não será aplicada.

### <a name="access-rule-evaluation"></a>Avaliação de regras de acesso

Regras de acesso são avaliadas quando um usuário acessa um aplicativo federado que usa OAuth 2.0, OpenID conectar, SAML ou Web Services Federation. Além disso, as regras de acesso são avaliadas quando os OAuth 2.0 e OpenID conectar-se usar um token de atualização adquirir um token de acesso. Se a avaliação de política falha quando um token de atualização é usado, será retornado o erro **invalid_grant** , isso indica que o usuário precisa autenticar novamente para o cliente.

###<a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Configurar serviços de federação para fornecer autenticação multifator

Para federados locatários, MFA pode ser executada por Azure Active Directory ou o local servidor do AD FS.

Por padrão, MFA ocorrerá em uma página hospedada pelo Azure Active Directory. Para configurar MFA local, a propriedade de **– SupportsMFA** deve ser definida como **true** no Active Directory do Azure, usando o módulo Azure AD para Windows PowerShell.

O exemplo a seguir mostra como habilitar MFA locais usando o [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) no locatário contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Além de definir esse sinalizador, a instância do locatário federados AD FS deverá ser configurada para executar a autenticação multifator. Siga as instruções para a [implantação do Azure multi-Factor Authentication local](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="related-articles"></a>Artigos relacionados

- [Proteger o acesso ao Office 365 e outros aplicativos conectado ao Azure Active Directory](active-directory-conditional-access.md)
- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
