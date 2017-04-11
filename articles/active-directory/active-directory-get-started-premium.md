<properties
    pageTitle="Introdução ao Azure Active Directory Premium"
    description="Um tópico que explica como se inscrever para edição de Azure Active Directory Premium através do site de licenciamento por Volume."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila" 
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-premium"></a>Introdução ao Azure Active Directory Premium


Para inscrever-se para o Active Directory Premium, você tem várias opções: 

**Azure ou Office 365** - como assinante Azure ou o Office 365, você pode comprar Active Directory Premium online. Para obter etapas detalhadas, consulte [como compra Azure Active Directory Premium - clientes existentes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) ou [como compra Azure Active Directory Premium - novos clientes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

**Mobilidade corporativos + segurança** - mobilidade corporativos + segurança (antigo Enterprise mobilidade Suite) é uma forma econômica para as organizações utilizar os seguintes serviços juntos em um plano de licenciamento: Active Directory Premium, Azure Rights Management, Microsoft Intune. Para obter mais informações, consulte o site de [mobilidade corporativos + segurança](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) . Para avaliação gratuita de 30 dias e, clique [aqui](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).


**Microsoft Volume Licensing** - Azure Active Directory Premium está disponível por meio de um [Contrato Enterprise da Microsoft](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 ou mais licenças) ou o programa de [Licenciamento por Volume aberto](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5 – 250 licenças).


Este tópico mostra como começar com um Azure Active Directory Premium que foram compradas por meio do programa de licenciamento por Volume. Se você ainda não estiver familiarizado com as edições diferentes do Azure Active Directory, consulte [edições do Active Directory do Azure](active-directory-editions.md).  

> [AZURE.NOTE]
Azure Premium de diretório ativo e edições básicas estão disponíveis para clientes na China usando a instância em todo o mundo do Azure Active Directory. Azure Premium de diretório ativo e edições básicas não são suportadas no momento no serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, entre em contato no [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).




## <a name="step-1-sign-up-for-active-directory-premium"></a>Etapa 1: Inscrever-se para o Active Directory Premium

Para se inscrever, consulte [como comprar por meio de licenciamento por Volume](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).



## <a name="step-2-activate-your-license-plan"></a>Etapa 2: Ativar seu plano de licença

Esta é sua primeira compra de plano de licença por meio do programa de licenciamento por Volume Enterprise da Microsoft?
Nesse caso, você obtém um email de confirmação quando sua compra foi concluída.
Você precisa de e-mail para ativar o seu plano de licença primeiro.

Em qualquer compra subsequente para este diretório, as licenças são ativadas automaticamente no mesmo diretório.



**Para ativar o seu plano de licença, execute uma das etapas a seguir:**


1. Para iniciar a ativação, clique em **Entrar** ou **Inscrever-se**.

    ![Entrar][1]



    - Se você tiver um locatário existente, clique **Entrar** para entrar com sua conta de administrador existente. Você precisa entrar com as credenciais de administrador global do diretório onde as licenças devem ser ativadas.

    - Se você deseja criar um novo locatário Azure Active Directory para usar com o seu plano de licenciamento, clique em **Inscrever-se** para abrir a caixa de diálogo **Criar perfil de conta** .

        ![Criar perfil de conta][2]

Quando terminar, a caixa de diálogo seguinte aparece como confirmação para a ativação do plano de licença de seu locatário.

![Confirmação][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>Etapa 3: Ativar o acesso do Active Directory do Azure

Se você tiver usado o Microsoft Azure antes, você pode continuar a [etapa 4](#step-4-assign-license-to-user-accounts). 

Quando as licenças são provisionadas ao seu diretório, um **email de boas-vindas** é enviada para você. O email confirma que você pode iniciar administrando seu Azure Active Directory Premium ou Enterprise mobilidade Suite licenças e recursos. 

Se você fizer uma tentativa de ativar o acesso ao Azure Active Directory antes de receber o email de boas-vindas, receberá a seguinte mensagem de erro. 

![Acesso não está disponível][9]

Se você por favor tente novamente após alguns minutos de uma vez que você recebeu o email.

Novos administradores na sua assinatura também podem ativar o acesso ao portal do clássico Azure por este link.






**Para ativar o acesso do Active Directory do Azure, execute as seguintes etapas:**

1. Em seu **email de boas-vindas**, clique em **Entrar**. 
    
    ![Email de boas-vindas][4]

2. Quando você tiver conectado com êxito, você precisa concluir uma segunda autenticação de fator no formulário de uma verificação móvel:

    ![Verificação móvel][5]

A ativação pode levar alguns minutos. Assim que seu acesso estiver ativo, a barra de marrom desaparece, e é possível clicar em **Portal**.

![Aguarde enquanto podemos configurar][6]

Nesse caso, seu acesso Azure é limitado ao Azure Active Directory.

![Recursos do Azure][7]

Talvez você já tivesse acesso ao Azure de uso anterior; Além disso, você pode atualizar seu acesso Active Directory do Azure para Azure acesso completo ativando assinaturas Azure adicionais. Nesses casos, o portal de clássico Azure tem mais recursos.

![Recursos do Azure][8]



## <a name="step-4-assign-license-to-user-accounts"></a>Etapa 4: Atribuir a licença para contas de usuário

Antes de começar a usar o plano que você comprou, você precisa atribuir manualmente licenças para contas de usuário de sua organização para que eles podem usar os recursos avançados fornecidos com Premium. Use as etapas a seguir para atribuir licenças aos usuários para que eles possam usar os recursos Premium do Azure Active Directory.

**Para atribuir licenças a usuários, execute as seguintes etapas:**

1. Entrar no portal do clássico Azure como administrador global do diretório que você deseja personalizar.
2. Clique em **Active Directory**e, em seguida, selecione a pasta onde você deseja atribuir licenças.
3. Selecione a guia **licenças** , selecione **Active Directory Premium** ou o **Pacote de mobilidade do Enterprise**e, em seguida, clique em **atribuir**.

    ![Planos de licença][10]

4. Na caixa de diálogo, selecione os usuários que você deseja atribuir licenças e clique no ícone de marca de seleção para salvar as alterações.

    ![Atribuir licenças][11]

### <a name="license-restrictions"></a>Restrições de licença

Alguns planos de licença são subconjuntos ou superconjuntos de outros planos de licença. Normalmente, um usuário não pode ser atribuído um plano de licença que já foi atribuído a elas. Se for sua intenção para atribuir um plano de licença que está contida, é necessário primeiro remover o plano de licença subconjunto.

### <a name="license-requirements"></a>Requisitos de licença

Quando você atribui uma licença a um usuário, você pode especificar um local de uso primário nas propriedades da sua conta. Se um local de uso não for especificado, o local do locatário é automaticamente atribuído ao usuário.

![Local do usuário][12]

A disponibilidade de serviços e recursos para um serviço de nuvem da Microsoft varia por país ou região. Um serviço, como voz sobre IP (VoIP), pode estar disponível em um país ou região e não está disponível em outro. Recursos dentro de um serviço podem ser restritos por razões legais em certos países ou regiões. Para ver se um serviço ou o recurso está disponível com ou sem restrições, procure por seu país ou região no site de restrições de licença de um serviço.

## <a name="whats-next"></a>Qual é a próxima

- [Adicionar marca para entrar e painel de acesso páginas da empresa](active-directory-add-company-branding.md)
- [Exibir seus relatórios do access e o uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png
