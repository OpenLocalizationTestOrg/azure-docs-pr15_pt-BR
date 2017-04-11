<properties
    pageTitle="Definir a política de acesso condicional baseado no dispositivo para aplicativos conectados Azure Active Directory | Microsoft Azure"
    description="Definir políticas de acesso condicional baseado no dispositivo para aplicativos do Azure AD conectados."
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
    ms.date="09/14/2016"
    ms.author="markvi"/>


# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Definir a política de acesso condicional baseado no dispositivo para aplicativos conectados Active Directory do Azure


Azure acesso condicional baseado no dispositivo do Active Directory (AD Azure) pode ajudá-lo a proteger os recursos de organização de:

- Tentativas de acesso de dispositivos desconhecidos ou não gerenciados.
- Dispositivos que não atendem as políticas de segurança da sua organização.

Para obter uma visão geral de acesso condicional, consulte [acesso condicional do Active Directory do Azure](active-directory-conditional-access.md).

Você pode definir políticas de acesso condicional baseado no dispositivo para proteger esses aplicativos:

- O Office 365 SharePoint Online, proteger documentos e sites da sua organização
- O Office 365 Exchange Online, proteger emails da sua organização
- Software como um aplicativo de serviço (SaaS) que estão conectadas ao Azure AD para autenticação
- Aplicativos que são publicados usando os serviços de Proxy de aplicativo do Azure AD local

Para definir uma política de acesso condicional baseado no dispositivo, no portal do Azure, vá para o aplicativo específico no diretório.


  ![Lista de aplicativos no diretório de portal Azure] (./media/active-directory-conditional-access-policy-connected-applications/01.png "Aplicativos")


Selecione o aplicativo e clique na guia **Configurar** para definir a política de acesso condicional.  


  ![Configurar o aplicativo] (./media/active-directory-conditional-access-policy-connected-applications/02.png "Dispositivo com base em regras de acesso")




**Para definir uma política de acesso condicional baseado no dispositivo, na seção **dispositivo com base em regras de acesso** , em **Habilitar regras de acesso**, selecione.**

Uma política de acesso condicional baseado no dispositivo tem três componentes:

- **Aplicar a**. O escopo de usuários que a política se aplica ao.

- **Regras de dispositivo**. As condições um dispositivo deve atender antes que ele possa acessar o aplicativo.

- **Imposição de aplicativo**. Os aplicativos cliente (nativos versus navegador) a política se aplica ao.

  ![Os três componentes de uma política de acesso baseado em dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/03.png "Dispositivo com base em regras de acesso")


## <a name="select-the-users-the-policy-applies-to"></a>Selecione os usuários a que a política se aplica

Na seção **Aplicar a** , você pode selecionar o escopo de usuários que essa política se aplica ao.

Você tem duas opções quando você cria um escopo de política de acesso de usuários:

- **Todos os usuários**. Aplica a política para todos os usuários que acessarem o aplicativo.

- **Grupos**. Limite a política para os usuários que for membro de um grupo específico.

![Política de aplicar a todos os usuários ou a um grupo] (./media/active-directory-conditional-access-policy-connected-applications/11.png "Aplicar a")


 Para excluir um usuário de uma política, marque a caixa de seleção **exceto** . Isso é útil quando você precisa conceder permissões a um usuário específico para acessar temporariamente o aplicativo. Selecione esta opção, por exemplo, se alguns dos seus usuários tem dispositivos que não estão prontos para acesso condicional. Os dispositivos podem não ser registrados ou estão prestes a ser ausência conformidade.


## <a name="select-the-conditions-that-devices-must-meet"></a>Selecione as condições que devem atender a dispositivos

Usar **Regras de dispositivo** para definir as condições um dispositivo deve atender para acessar o aplicativo.

Você pode definir acesso condicional baseado no dispositivo para esses tipos de dispositivo:

- Atualização de aniversário do Windows 10, Windows 8.1 e Windows 7
- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2
- dispositivos iOS (iPad, iPhone)
- Dispositivos Android

Suporte para Mac é em breve.

  ![Política de aplicar a dispositivos] (./media/active-directory-conditional-access-policy-connected-applications/04.png "Aplicativos")

 >[AZURE.NOTE] Para obter informações sobre as diferenças entre dispositivos de domínio e Azure AD ingressaram, consulte [usando o Windows 10 dispositivos no seu local de trabalho](active-directory-azureadjoin-windows10-devices.md).

Você tem duas opções para as regras de dispositivo:

- **Todos os dispositivos devem ser compatíveis**. Todas as plataformas de dispositivo que acessarem o aplicativo devem ser compatíveis. Dispositivos que executam em plataformas que não suportam o acesso condicional baseado no dispositivo têm acesso negados.

- **Somente os dispositivos selecionados devem ser compatíveis**. Somente plataformas de dispositivos específicos devem ser compatíveis. Outras plataformas ou outras plataformas que podem acessar o aplicativo, tem acesso.

  ![Definir o escopo para as regras de dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/05.png "Aplicativos")

Azure AD ingressaram dispositivos são compatíveis com se eles estiverem marcados como **compatível** no diretório por Intune ou por um sistema de gerenciamento de dispositivo móvel de terceiros que se integra ao Azure AD.

Um dispositivo de domínio está em conformidade se:

- Ele é registrado com o Azure AD. Muitas organizações tratam dispositivos de domínio como dispositivos confiáveis.
- Ele está marcado como **compatível com** no Azure AD pelo sistema central Gerenciador de configuração 2016.

 ![Domínio dispositivos que são compatíveis com] (./media/active-directory-conditional-access-policy-connected-applications/06.png "Regras de dispositivo")

Dispositivos pessoais do Windows são compatíveis com se eles estiverem marcados como **compatível** no diretório por Intune ou por um sistema de gerenciamento de dispositivo móvel de terceiros que se integra ao Azure AD.

Dispositivos não Windows são compatíveis com se eles estiverem marcados como **compatíveis** no diretório pelo Intune.

 >[AZURE.NOTE] Para obter mais informações sobre como configurar o Azure AD para fins de conformidade do dispositivo em sistemas de gerenciamento de diferentes, consulte [acesso condicional do Active Directory do Azure](active-directory-conditional-access.md).


Você pode selecionar uma ou várias plataformas de dispositivos para uma política de acesso baseado em dispositivo. Isso inclui iOS, Windows Mobile (Windows 8.1 telefones e tablets), Android e Windows (todos os outros dispositivos do Windows, incluindo todos os dispositivos Windows 10).
Avaliação de política ocorre apenas em plataformas selecionadas. Se um dispositivo que tenta acesso não estiver executando uma das plataformas selecionadas, o dispositivo pode acessar o aplicativo se o usuário tem acesso. Nenhuma política de dispositivo é avaliada.

![Selecionar plataformas para regras de dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/07.png "Regras de dispositivo")


## <a name="set-policy-evaluation-for-a-type-of-application"></a>Avaliação de política de conjunto de um tipo de aplicativo

Na seção **Imposição de aplicativo** , defina o tipo de aplicativos que irá avaliar a política de acesso de usuário ou dispositivo.

Você tem duas opções para o tipo de aplicativo para incluir:

- Navegador e aplicativos nativos
- Somente aplicativos nativos

![Escolha navegador ou aplicativos nativos] (./media/active-directory-conditional-access-policy-connected-applications/08.png "Aplicativos")

Para impor a política de acesso para aplicativos, selecione **para navegador e aplicativos nativos**. Em seguida, você pode incluir:

- Navegadores (por exemplo, Microsoft Edge no Windows 10) ou do Safari no iOS.
- Aplicativos que usam a biblioteca de autenticação do Active Directory (ADAL) em qualquer plataforma, ou que usam o WebAccountManager (WAM) API no Windows 10.

>[AZURE.NOTE] Para obter mais informações sobre o suporte ao navegador e outras considerações para um usuário que está acessando um baseados em dispositivos, aplicativo de protegidos por autoridade de certificação, consulte [acesso condicional do Active Directory do Azure](active-directory-conditional-access.md).

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Ajudar a proteger o acesso de email de aplicativos baseados no Exchange ActiveSync

Em aplicativos do Exchange Online do Office 365, você pode usar o Exchange ActiveSync para bloquear o acesso de email aos aplicativos de email baseada no Exchange ActiveSync.

![Opções de conformidade do Exchange ActiveSync] (./media/active-directory-conditional-access-policy-connected-applications/09.png "Aplicativos")

![Exigir um dispositivo compatível para acessar o email] (./media/active-directory-conditional-access-policy-connected-applications/10.png "Aplicativos")


## <a name="next-steps"></a>Próximas etapas

- [Acesso condicional do Azure Active Directory](active-directory-conditional-access.md)
