<properties
    pageTitle="Único gerenciamento de logon para aplicativos empresariais na visualização do Active Directory do Azure | Microsoft Azure"
    description="Saiba como gerenciar o logon único em para aplicativos empresariais utilizando o Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="asmalser"/>

# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Visualização: Gerenciando single sign-on para aplicativos empresariais no novo portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-enterprise-apps-manage-sso.md)
- [Azure portal clássico](active-directory-sso-integrate-saas-apps.md)

Este artigo descreve como usar o [portal do Azure](https://portal.azure.com) para gerenciar configurações de logon única para aplicativos, especialmente aquelas que foram adicionados na [Galeria de aplicativo do Azure Active Directory (AD Azure)](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). A experiência de gerenciamento do Azure AD para logon único está na visualização de pública, e este artigo descreve os novos recursos, além de algumas limitações temporárias que serão in-loco somente durante o período de visualização. [O que é no preview?](active-directory-preview-explainer.md)

##<a name="finding-your-apps-in-the-new-portal"></a>Localizando seus aplicativos no novo portal

A partir de setembro de 2016, todos os aplicativos que foram configurados para único logon em um diretório, por um administrador de diretório usando a [Galeria de aplicativo do Active Directory do Azure](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) dentro do [Azure portal clássico](https://manage.windowsazure.com), podem agora ser visualizados e gerenciados no portal do Azure.

Esses aplicativos podem ser encontrados na seção **Aplicativos corporativos** do portal do Azure, um link para o qual pode ser encontrado na lista **Mais serviços** no [portal](https://portal.azure.com). Aplicativos empresariais são aplicativos que foram implantados e estão sendo usados por usuários de sua organização.

![Blade de aplicativos corporativos][1]

Selecione **todos os aplicativos** para exibir uma lista de todos os aplicativos que foram configurados, incluindo aplicativos que tiveram sido adicionados da Galeria. Selecionar um aplicativo carrega a lâmina de recursos para esse aplicativo, onde relatórios podem ser exibidos para esse aplicativo e uma variedade de configurações pode ser gerenciada.

Para gerenciar as configurações de logon única, selecione **logon único**.

![Blade de recurso do aplicativo][2]


##<a name="single-sign-on-modes"></a>Modos de logon único

O **logon único** blade começa com um menu **modo** , que permite que o modo de logon único para ser configurado. As opções disponíveis incluem:

* **Baseada em SAML logon** - essa opção estará disponível se o aplicativo oferece suporte completo federado logon único com o Azure Active Directory usando o protocolo de SAML 2.0.

* **Baseada em senha logon** - essa opção estará disponível se o Azure AD dá suporte a senha de preenchimento de formulário para este aplicativo.

* **Vinculada entrar em** - anteriormente conhecido como "Existente single sign-on", essa opção permite que administradores para colocar um link para este aplicativo no inicializador de aplicativo de painel de acesso do Azure AD ou do Office 365 de seus usuário.

Para obter mais informações sobre esses modos, consulte [como um único logon com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).


##<a name="saml-based-sign-on"></a>Baseada em SAML logon

A opção **baseada em SAML logon** exibe uma lâmina é dividida em quatro seções:

###<a name="domains-and-urls"></a>URLs e domínios
Isso é onde todos os detalhes sobre o domínio e URLs do aplicativo são adicionados ao seu diretório Azure AD. Todas as entradas necessárias para tornar o aplicativo de trabalho de logon único são exibidas diretamente na tela, enquanto todas as entradas opcionais podem ser visualizadas, marcando a caixa de seleção **Mostrar URL configurações avançadas** . A lista completa de entradas com suporte incluem:

* **Entrar na URL** – onde o usuário vai entrar para este aplicativo. Se o aplicativo estiver configurado para executar o serviço iniciado pelo provedor de logon único em, em seguida, quando um usuário navega para essa URL, o provedor de serviços faz o redirecionamento necessário ao Azure AD para autenticar e entrar do usuário. Se esse campo estiver preenchido, o Azure AD usará essa URL para iniciar o aplicativo do Office 365 e o painel de acesso do Azure AD. Se esse campo for omitido, o Azure AD executa em vez disso, o provedor de identidade-iniciadas logon quando o aplicativo é iniciado do Office 365, o painel de Azure AD acesso, ou do Azure AD único logon URL.

* **Identificador** - URI este exclusivamente deve identificar o aplicativo para o qual SSO está sendo configurado. Este é o valor que Azure AD envia voltar ao aplicativo como o parâmetro de audiência do token SAML e o aplicativo espera validá-la. Este valor também é exibida como a ID de entidade em qualquer metadados SAML fornecido pelo aplicativo.

* **URL de resposta** - a URL de resposta é onde o aplicativo espera receber o token SAML. Isso também é conhecido como a URL de declaração consumidor ACS (serviço). Depois que esses tiverem sido inseridas, clique em Avançar para prosseguir para a próxima tela. Esta tela fornece informações sobre o que precisa ser configurado no lado do aplicativo para ativá-lo aceitar um token SAML do Azure AD.

* **Estado de retransmissão** - o estado de retransmissão é um parâmetro opcional que pode ajudar a informar o aplicativo onde redirecionar o usuário após a autenticação. Normalmente o valor é uma URL válida no aplicativo, porém alguns aplicativos usam este campo de forma diferente (Consulte logon único do aplicativo na documentação para obter detalhes). A capacidade de definir o estado de retransmissão é um novo recurso exclusivo para o novo portal Azure.

###<a name="user-attributes"></a>Atributos de usuário
Isso é onde os administradores podem exibir e editar os atributos que são enviados no token SAML que emite Azure AD para o aplicativo de cada vez usuários entrar.

Para a primeira versão de visualização, o atributo somente são editável com suporte é o atributo de **Identificador de usuário** . O valor do atributo é o campo no Azure AD que identifica exclusivamente cada usuário dentro do aplicativo. Por exemplo, se o aplicativo foi implantado usando o endereço de Email"" como o nome de usuário e o identificador exclusivo, em seguida, o valor deve ser definido para o campo "user.mail" no Azure AD.

Edição de atributos adicionais será suportado em uma visualização de subsequente.

###<a name="saml-signing-certificate"></a>Certificado de autenticação de SAML
Esta seção mostra os detalhes do certificado que Azure AD usa para assinar tokens SAML que são emitidos pelo aplicativo de cada vez a autenticação do usuário. Ele permite que as propriedades do certificado atual para inspecionar, incluindo a data de expiração.

A capacidade de sobreposição o certificado e gerenciar certificados adicionais opções terá suporte em uma versão de visualização subsequentes. Observe que o gerenciamento completo de certificados ainda pode ser realizado no [Azure portal clássico](active-directory-sso-certs.md).

###<a name="application-configuration"></a>Configuração do aplicativo
A seção final fornece a documentação e/ou controles necessários para configurar o aplicativo para usar o Active Directory do Azure como um provedor de identidade.

O **Aplicativo de configuração** menu suspenso fornece novas instruções concisas, inseridas para configurar o aplicativo. Este é outro recurso novo exclusivo para o novo portal Azure.

> [AZURE.NOTE] Para um exemplo completo da documentação inserida, consulte o aplicativo Salesforce.com. Documentação para aplicativos adicionais está sendo adicionada continuamente durante a visualização.

![Docs inseridas][3]

##<a name="password-based-sign-on"></a>Baseada em senha de logon
Se houver suporte para o aplicativo, selecionando o modo SSO baseada em senha e **Salve** instantaneamente configura para fazer SSO baseada em senha. Para obter mais informações sobre como implantar o SSO baseada em senha, consulte [como um único logon com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Baseada em senha de logon][4]


##<a name="linked-sign-on"></a>Logon vinculadas
Se houver suporte para o aplicativo, selecionar o modo SSO vinculado permite que você insira a URL que você deseja o painel de acesso do Azure AD ou o Office 365 para redirecionar para quando os usuários clicam neste aplicativo. Para obter mais informações sobre SSO vinculada (anteriormente conhecido como "SSO existente"), consulte [como um único logon com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Logon vinculado][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
