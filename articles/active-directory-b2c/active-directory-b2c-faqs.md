<properties
    pageTitle="Active Directory do Azure B2C: Perguntas frequentes | Microsoft Azure"
    description="Perguntas frequentes sobre o Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-faqs"></a>Active Directory do Azure B2C: perguntas frequentes

Esta página responde perguntas frequentes sobre o B2C Azure Active Directory (AD Azure). Manter verificando as atualizações.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Pode usar os recursos do Azure AD B2C em meu locatário do Azure AD baseados em funcionário, existente?

Atualmente Azure AD B2C recursos não podem ser ativados no seu locatário existente do Azure AD. Recomendamos que você crie um locatário separado para usar recursos do Azure AD B2C para gerenciar seus consumidores.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Posso usar o Azure AD B2C para fornecer social login (Facebook e Google +) para o Office 365?

Azure AD B2C não podem ser usados com o Microsoft Office 365. Em geral, ele não pode ser usado para fornecer autenticação para qualquer aplicativos SaaS (Office 365, Salesforce, DIATRABALHO, etc.). Ele fornece o gerenciamento de identidades e acesso somente para web voltados para o consumidor e aplicativos móveis e não é aplicável a cenários de funcionário ou parceiro.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>O que são contas locais no Azure AD B2C? Como eles estão diferentes de contas de trabalho ou da escola no Azure AD?

Em um locatário do Azure AD, cada usuário no locatário (exceto usuários com contas do Microsoft existentes) entra com um endereço de email do formulário `<xyz>@<tenant domain>`, onde `<tenant domain>` é um dos domínios verificados no locatário ou a inicial `<...>.onmicrosoft.com` domínio. Esse tipo de conta é uma conta corporativa ou escolar.

Deseja que o usuário para entrar com qualquer endereço de email aleatório em um locatário do Azure AD B2C, a maioria dos aplicativos (por exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com, ou jim@live.com). Esse tipo de conta é uma conta local. Hoje, podemos também dá suporte a nomes de usuário aleatório (simplesmente cadeias de caracteres) como contas locais (por exemplo, José, Paulo, sarah ou jim). Você pode escolher um desses dois tipos de conta local no serviço do Azure AD B2C.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Quais provedores de identidade social você oferece suporte agora? Quais você planeja suportar no futuro?

Atualmente, suportamos Facebook, Google +, LinkedIn e Amazon. Adicionaremos suporte para outros provedores de identidade social populares com base na demanda de cliente.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Pode configurar escopos para obter mais informações sobre os consumidores de vários provedores de identidade social?

Não, mas esse recurso é nosso roteiro. Os escopos de padrão usados para o nosso conjunto compatível de provedores de identidade social são:

- Facebook: email
- Google +: email
- Conta da Microsoft: openid perfil de e-mail
- Amazon: perfil
- LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Meu aplicativo precisa ser executado no Azure para que ele funcione com o Azure AD B2C?

Não, você pode hospedar seu aplicativo em qualquer lugar (na nuvem ou locais). Tudo o que precisa para interagir com o Azure AD B2C é a capacidade de enviar e receber solicitações HTTP em pontos de extremidade publicamente acessíveis.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tenho vários locatários do Azure AD B2C. Como posso gerenciá-los no Portal do Azure?

Cada locatário Azure AD B2C tem seu próprio blade de recursos B2C no portal do Azure. Consulte [Azure AD B2C: registrar seu aplicativo](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) para saber como você pode navegar para blade de recursos do locatário um específico B2C no portal do Azure. Alternando entre Azure AD B2C diretórios no portal do Azure não manterá seus recursos B2C blade abrir na maioria dos navegadores.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como personalizar a verificação emails (o conteúdo e a "de:" campo) enviadas por Azure AD B2C?

Você pode usar o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md) para personalizar o conteúdo de emails de verificação. Especificamente, esses dois elementos de email podem ser personalizados:

- **Logotipo do banner**: mostrado no canto inferior direito.
- **Cor de fundo**: mostrada na parte superior.

    ![Captura de tela de um email de verificação personalizada](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

A assinatura de email contém o nome do locatário B2C que você forneceu quando você criou primeiro o locatário B2C. Você pode alterar o nome usando estas instruções:

- Entrar no [portal clássico Azure](https://manage.windowsazure.com/) como o administrador de assinatura.
- Navegue até seu locatário B2C.
- Clique na guia **Configurar** .
- Altere o campo de **nome** na seção **Propriedades do diretório** .
- Clique em **Salvar** na parte inferior da página.

Atualmente não há nenhuma maneira de alterar o "de:" campo no email. Se você estiver interessado nesse recurso e em personalizar totalmente o corpo do email verificação, vote para o recurso em [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar meu existente nomes de usuário, senhas e perfis de meu banco de dados para Azure AD B2C?

Você pode usar a API do Azure AD Graph para gravar sua ferramenta de migração. Consulte as [amostras de API do gráfico](active-directory-b2c-devquickstarts-graph-dotnet.md) para obter detalhes. Forneceremos várias opções de migração e ferramentas-de-prontos no futuro.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Qual diretiva de senha é usada para contas locais no Azure AD B2C?

A política de senha do Azure AD B2C para contas locais se baseia a política do Azure AD. Azure AD B2C da inscrição, inscrição ou entrar e a senha redefinir políticas usa a força de senha "forte" e não expirarem quaisquer senhas. Leia a [política de senha do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obter mais detalhes.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Pode usar o Azure AD Connect para migrar identidades que estejam armazenadas na minha do Active Directory local ao Azure AD B2C?

Não, Azure AD Connect não foi projetado para funcionar com o Azure AD B2C. Forneceremos várias opções de migração e ferramentas-de-prontos no futuro.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C funciona com sistemas CRM como Microsoft Dynamics?

Não atualmente. Integrar esses sistemas é nosso roteiro.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Faz Azure AD B2C funcionam com o SharePoint 2016 local ou anterior?

Não atualmente. Azure AD B2C não tem suporte para tokens de SAML 1.1 portais e aplicativos de comércio baseadas na necessidade de local do SharePoint. Observe que Azure AD B2C não é destinado o cenário SharePoint externo compartilhamento de parceiro; consulte [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) em vez disso.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Deve usar Azure AD B2C ou B2B para gerenciar identidades externas?

Leia este artigo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para saber mais sobre como aplicar os recursos apropriados para seus cenários de identidades externo.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quais relatórios e recursos de auditoria é fornecido com o Azure AD B2C? Eles são iguais aos Azure AD Premium?

Não, o Azure AD B2C não suporta o mesmo conjunto de relatórios que Azure AD Premium. Azure AD B2C lançaremos relatórios básicos e auditoria APIs em breve.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Para localizar a interface de usuário das páginas servidas por Azure AD B2C? Quais idiomas são suportados?

Atualmente, Azure AD B2C é otimizada para inglês somente. Pretendemos implementar recursos de localização assim que possível.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Pode usar Meus próprio URLs nas minhas páginas de inscrição e entrar servidas por Azure AD B2C? Por exemplo, altere a URL de login.microsoftonline.com para login.contoso.com?

Não atualmente. Esse recurso é nosso roteiro. Observe também que verificação do seu domínio na guia **domínios** do seu locatário no portal do clássico Azure não fará isso.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como excluo meu locatário do Azure AD B2C?

Siga estas etapas para excluir seu locatário do Azure AD B2C:

- Siga estas etapas para [navegar até a lâmina de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
- Navegue até as lâminas de **aplicativos**, **provedores de identidade** e **todas as diretivas** e excluir todas as entradas em cada um deles.
- Agora entre no [portal clássico Azure](https://manage.windowsazure.com/) como o administrador de assinatura. (Isso é o mesmo trabalho ou conta de escola ou a mesma conta da Microsoft que você usou para se inscrever no Azure.)
- Navegue até a extensão do Active Directory à esquerda e clique em seu locatário B2C.
- Clique na guia **usuários** .
- Selecione cada usuário em vez (excluir o usuário que você estiver conectado no momento como, ou seja, a assinatura do administrador). Clique em **Excluir** na parte inferior da página e clique em **Sim** quando solicitado.
- Clique na guia **aplicativos** .
- Selecione **aplicativos minha empresa possui** no campo **Mostrar** lista suspensa e clique na marca de seleção.
- Você verá um aplicativo chamado **b2c-extensões-app** listadas abaixo. Clique em **Excluir** na parte inferior da página e clique em **Sim** quando solicitado.
- Navegue até a extensão do Active Directory novamente e selecione seu locatário B2C.
- Clique em **Excluir** na parte inferior da página. Siga as instruções na tela para concluir o processo.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>É possível obter Azure AD B2C como parte do pacote de mobilidade do Enterprise?

Não, o Azure AD B2C é um serviço Azure pré-pago e não faz parte do pacote de mobilidade do Enterprise.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como faço para relatar problemas com o Azure AD B2C?

Consulte [arquivo solicitações de Azure Active Directory B2C de suporte](active-directory-b2c-support.md).

## <a name="more-information"></a>Mais informações

Você também pode querer revisar atuais [restrições, restrições e limitações do serviço](active-directory-b2c-limitations.md).
