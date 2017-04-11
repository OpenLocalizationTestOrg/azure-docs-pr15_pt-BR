<properties 
    pageTitle="Configurando o logon único para aplicativos que não estão na Galeria de aplicativo do Active Directory do Azure | Microsoft Azure" 
    description="Saiba como a autoatendimento conectar aos aplicativos ao Azure Active Directory usando SAML e SSO baseada em senha" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configurando o logon único para aplicativos que não estão na Galeria de aplicativo do Active Directory do Azure

Este artigo é sobre um recurso que permite aos administradores configurar o logon único para aplicativos não presentes no Active Directory do Azure aplicativo galeria *sem escrever código*. Este recurso foi lançado da visualização técnica em 18 de novembro de 2015 e está incluído no [Azure Active Directory Premium](active-directory-editions.md). Se, em vez disso, você está procurando orientação de desenvolvedor sobre como integrar aplicativos personalizados com o Azure AD através de código, consulte [Cenários de autenticação do Azure AD](active-directory-authentication-scenarios.md).

A Galeria de aplicativo do Active Directory do Azure fornece uma listagem dos aplicativos que são conhecidos para dar suporte a um formulário de logon único com o Azure Active Directory, conforme descrito [neste](active-directory-appssoaccess-whatis.md)artigo. Quando (como IT specialist ou sistema integrador em sua organização) tiver localizado o aplicativo que você deseja se conectar, você possa começar pelo siga as instruções passo a passo apresentadas no portal de gerenciamento do Azure para habilitar o logon único.

Os clientes com licenças do [Azure Active Directory Premium](active-directory-editions.md) também obtêm esses recursos adicionais:

* Integração de autoatendimento de qualquer aplicativo que ofereça suporte a provedores de identidade SAML 2.0 (SP iniciadas ou iniciadas IdP)
* Integração de autoatendimento de qualquer aplicativo web que tem uma baseado em HTML página de entrada usando o [SSO baseada em senha](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Conexão de autoatendimento de aplicativos que usam o protocolo SCIM para usuário provisionamento ([descrito aqui](active-directory-scim-provisioning.md))
* Capacidade de adicionar links a qualquer aplicativo no [inicializador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [Painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Isso pode incluir não apenas aplicativos SaaS que você usar, mas tiver não ainda foi em-hospedados na Galeria de aplicativo do Azure AD, mas aplicativos da web de terceiros que sua organização implantou para os servidores de que controle, tanto na nuvem ou local.

Esses recursos, também conhecido como *modelos de integração de aplicativos*, fornecem pontos de conexão baseada em padrões para os aplicativos que suportam SAML, SCIM ou a autenticação baseada em formulários e incluem opções flexíveis e as configurações de compatibilidade com um grande número de aplicativos. 

##<a name="adding-an-unlisted-application"></a>Adicionando um aplicativo não listado

Para se conectar a um aplicativo usando um modelo de integração do aplicativo, entre no portal de gerenciamento Azure usando sua conta de administrador do Active Directory do Azure e navegue até o **do Active Directory > [diretório] > aplicativos** seção, selecione **Adicionar**e, em seguida, **Adicionar um aplicativo da Galeria**. 

![][1]

Na Galeria de aplicativo, você pode adicionar um aplicativo não listado usando na categoria **personalizada** à esquerda ou selecionando o link **Adicionar um aplicativo não listado** que é mostrado nos resultados da pesquisa se o aplicativo desejado não foi encontrado. Depois de inserir um nome para seu aplicativo, você pode configurar as opções de logon única e comportamento. 

**Dica rápida**: como uma prática recomendada, use a função Localizar para verificar se o aplicativo já existe na Galeria de aplicativo. Se o aplicativo for encontrado e sua descrição menciona "logon único", em seguida, o aplicativo já tem suporte para logon único federado. 

![][2]

Adicionando um aplicativo dessa maneira fornece uma experiência muito semelhante à disponíveis para aplicativos integrados previamente. Para começar, selecione **Configurar Single Sign-On**. A próxima tela apresenta as seguintes três opções para configurar o logon único, que são descritos nas seções a seguir.

![][3]


##<a name="azure-ad-single-sign-on"></a>Azure AD Single Sign-On

Selecione esta opção para configurar a autenticação baseada em SAML para o aplicativo. Isso requer que o suporte do aplicativo SAML 2.0 e você deve coletar informações sobre como usar os recursos SAML do aplicativo antes de continuar. Depois de selecionar **próxima**, você será solicitado a inserir três diferentes URLs correspondente para os pontos de extremidade SAML para o aplicativo. 

![][4]
 
Estas são:

* **Entrada na URL (SP iniciadas apenas)** – onde o usuário vai entrar para este aplicativo. Se o aplicativo está configurado para executar o serviço iniciado pelo provedor de logon único em, em seguida, quando um usuário navega para esta URL, o provedor de serviços fará o redirecionamento necessário ao Azure AD para autenticar e fazer logon do usuário no. Se esse campo estiver preenchido, o Azure AD usará essa URL para iniciar o aplicativo do Office 365 e o painel de acesso do Azure AD. Se esse campo é omitido, o Azure AD executará em vez disso, o provedor de identidade-iniciadas logon quando o aplicativo é iniciado do Office 365, o painel de Azure AD acesso, ou do Azure AD único URL logon (copiable na guia Painel).

* **URL do emissor** - o emissor URL exclusivamente deve identificar o aplicativo para o qual logon único em está sendo configurado. Este é o valor que Azure AD envia voltar ao aplicativo como o parâmetro de **audiência** do token SAML e o aplicativo espera validá-la. Este valor também é exibida como a **ID de entidade** em qualquer metadados SAML fornecido pelo aplicativo. Verifique a documentação do aplicativo SAML para obter detalhes sobre o que é a ID de entidade ou valor de audiência é. Abaixo está um exemplo de como a URL de audiência é exibido no token SAML retornado para o aplicativo:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **URL de resposta** - a URL de resposta é onde o aplicativo espera receber o token SAML. Isso também é conhecido como a **URL de declaração consumidor ACS (serviço)**. Verifique a documentação do aplicativo SAML para obter detalhes sobre o que é a sua resposta de token SAML URL ou ACS.
 Depois que esses tiverem sido inseridas, clique em **Avançar** para passar para a próxima tela. Esta tela fornece informações sobre o que precisa ser configurado no lado do aplicativo para ativá-lo aceitar um token SAML do Azure AD. 

![][5]

Quais valores são necessários variar dependendo do aplicativo, portanto, verifique a documentação do aplicativo SAML para obter detalhes. **Logon** e **saída** URL ambos resolvem para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitação SAML para sua instância do Azure AD de serviço. A URL do emissor é o valor que aparece como o "emissor" dentro do token SAML emitido pelo aplicativo. 

Depois que seu aplicativo tiver sido configurado, clique botão **Avançar** e em seguida **Concluir** para fechar a caixa de diálogo. 

##<a name="assigning-users-and-groups-to-your-saml-application"></a>Atribuir usuários e grupos para o seu aplicativo de SAML 

Quando seu aplicativo tiver sido configurado para usar o Azure AD como um provedor de identidade baseada em SAML, está quase pronto para testar. Como um controle de segurança, Azure AD não irá emitir um token permitindo a eles entrar no aplicativo, a menos que eles possui acesso usando o Azure AD. Os usuários podem ter acesso diretamente, ou por meio de um grupo que eles são um membro do. 

Para atribuir um usuário ou grupo para seu aplicativo, clique no botão **Atribuir usuários** . Selecione o usuário ou grupo que você deseja atribuir e, em seguida, selecione o botão **atribuir** . 

![][6]

Atribuir um usuário permitirá Azure AD emitir um token para o usuário, bem como causando uma peça para este aplicativo seja exibido no painel de acesso do usuário. Um bloco de aplicativo também aparecerão no inicializador de aplicativos do Office 365, se o usuário estiver usando o Office 365. 

Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar logotipo** na guia **Configurar** para o aplicativo. 

###<a name="customizing-the-claims-issued-in-the-saml-token"></a>Personalizando as declarações emitidas no token SAML 

Quando um usuário autenticado para o aplicativo, o Azure AD emitir um token SAML para o aplicativo que contém informações (ou declarações) sobre o usuário que identifica exclusivamente-los. Por padrão, isso inclui o usuário nome de usuário, endereço de email, nome e sobrenome. 

Você pode exibir ou editar as declarações enviadas no token SAML para o aplicativo na guia **atributos** . 

![][7]

Há dois motivos possíveis por que você pode precisar editar as declarações emitidas no token SAML: •The aplicativo foram gravado para exigir um conjunto diferente de declaração URIs ou valores de declaração • seu aplicativo foi implantado em uma forma que requer o identificador de nome parecem ser algo diferente do nome de usuário (conhecido como nome de usuário principal) armazenada no Active Directory do Azure. 

Para obter informações sobre como adicionar e editar declarações para esses cenários, consulte este [artigo sobre personalização de declarações](active-directory-saml-claims-customization.md). 

###<a name="testing-the-saml-application"></a>Testar o aplicativo de SAML 

Quando o certificado e URLs de SAML tiveram sido configuradas no Azure AD e no aplicativo, usuários ou grupos têm foi atribuídos ao aplicativo no Azure, as declarações foram revisadas e editadas se for necessário, e o usuário está pronto para entrar no aplicativo. 

Para testar, simplesmente entrada ao painel de acesso Azure AD em https://myapps.microsoft.com usando uma conta de usuário atribuída a você para o aplicativo, e, em seguida, clique no bloco para o aplicativo para iniciar o processo de logon único. Como alternativa, você pode navegar diretamente para a URL de logon para o aplicativo e entrar a partir daí. 

Para dicas de depuração, consulte este [artigo sobre como depurar baseada em SAML logon único para aplicativos](active-directory-saml-debugging.md) 

##<a name="password-single-sign-on"></a>Senha Single Sign-On

Selecione esta opção para configurar [baseada em senha logon único](active-directory-appssoaccess-whatis.md) para um aplicativo web que tem uma página de entrada HTML. SSO baseada em senha, também conhecido como senha compartimento, permite que você gerencie o acesso de usuário e senhas para aplicativos web que não há suporte para a federação de identidade. Também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como a contas de aplicativo de mídia social da sua organização. 

Depois de selecionar **próxima**, você será solicitado a inserir a URL do baseado na web página de entrada do aplicativo. Observe que isso deve ser a página que inclua os campos de entrada do usuário e senha. Depois de inserido, o Azure AD inicia um processo para analisar a página de entrada para uma entrada de nome de usuário e uma senha de entrada. Se o processo não for bem-sucedido, em seguida, ele orienta você a um processo alternativo de instalar uma extensão de navegador (requer o Internet Explorer, Chrome ou Firefox) que permitirá que você capture manualmente os campos.

Quando a página de entrada é capturada, podem ser atribuídos a usuários e grupos e políticas de credencial podem ser definidas como regular [senha SSO aplicativos](active-directory-appssoaccess-whatis.md).

Observação: Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar logotipo** na guia **Configurar** para o aplicativo. 

##<a name="existing-single-sign-on"></a>Logon único existente

Selecione esta opção para adicionar um link a um aplicativo para o portal do painel de acesso do Azure AD ou do Office 365 da sua organização. Você pode usar isso para adicionar links aos aplicativos web personalizado que atualmente usam Azure Active Directory Federation Services (ou outro serviço de federação) em vez do Azure AD para autenticação. Ou, você pode adicionar profundidade links para páginas específicas do SharePoint ou outras páginas da web que você deseja apenas aparecem em painéis de acesso do usuário. 

Depois de selecionar **próxima**, você será solicitado a insira a URL do aplicativo para vincular a. Quando concluído, os usuários e grupos podem ser atribuídos para o aplicativo, o que faz com que o aplicativo seja exibido no [inicializador de aplicativos do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [Painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) para esses usuários.

Observação: Você pode carregar um logotipo de bloco para o aplicativo usando o botão **Carregar logotipo** na guia **Configurar** para o aplicativo.

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Como personalizar declarações emitidas no Token SAML para aplicativos previamente integrados](active-directory-saml-claims-customization.md)
- [Solução de problemas baseada em SAML Single Sign-On](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
