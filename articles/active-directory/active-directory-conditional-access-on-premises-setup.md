<properties
    pageTitle="Configuração de acesso condicional local usando o registro de dispositivos do Azure Active Directory | Microsoft Azure"
    description="Um guia passo a passo para habilitar o acesso condicional aos aplicativos locais usando o serviço de Federação do Active Directory (AD FS) no Windows Server 2012 R2."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>Configuração de acesso condicional local usando o registro de dispositivos do Azure Active Directory

Pessoalmente pertencente dispositivos de seus usuários podem ser marcados conhecidas por sua organização exigindo a usuários para participar do local de trabalho seus dispositivos para o serviço de registro de dispositivos do Azure Active Directory. Abaixo está um guia passo a passo para habilitar o acesso condicional aos aplicativos locais usando o serviço de Federação do Active Directory (AD FS) no Windows Server 2012 R2.

> [AZURE.NOTE]
> Licença do Office 365 ou licença do Azure AD Premium é necessária quando usando dispositivos registrado em políticas de acesso condicional de serviço de registro de dispositivos do Azure Active Directory. Isso inclui políticas aplicadas pelos serviços de Federação do Active Directory (AD FS) para os recursos de locais.

Para obter mais informações sobre os cenários de acesso condicional para locais, consulte [ingressar para área de trabalho de qualquer dispositivo para SSO e perfeita segundo fator autenticação em aplicativos da empresa](https://technet.microsoft.com/library/dn280945.aspx).

Esses recursos estão disponíveis para clientes que adquirir uma licença do Azure Active Directory Premium.

<a name="supported-devices"></a>Dispositivos com suporte
-------------------------------------------------------------------------
* Dispositivos de domínio associado do Windows 7.
* Windows 8.1 personal e o domínio ingressou dispositivos.
* iOS 6 e posteriores, para navegador Safari
* Android 4.0 ou posterior, Samsung GS3 ou acima de telefones, Samsung Note2 ou acima tablets.


<a name="scenario-prerequisites"></a>Pré-requisitos de cenário
------------------------------------------------------------------------
* Assinatura do Office 365 ou Premium do Active Directory do Azure
* Um locatário do Active Directory do Azure
* Active Directory do Windows Server (Windows Server 2008 ou posterior)
* Esquema atualizado no Windows Server 2012 R2
* Licença do Active Directory do Azure Premium
* Windows Server 2012 R2 serviços de federação, configurado para SSO ao Azure AD
* Proxy Windows Server 2012 R2 Web aplicativo Microsoft Active Directory do Azure conectar (Azure AD Connect). [Baixe o Azure AD Connect aqui](http://www.microsoft.com/en-us/download/details.aspx?id=47594).
* Domínio verificado.

<a name="known-issues-in-this-release"></a>Problemas conhecidos nesta versão
-------------------------------------------------------------------------------
* Políticas de acesso condicional do dispositivo com base requerem o objeto de dispositivo write-back para o Active Directory do Azure Active Directory. Pode levar até 3 horas para objetos de dispositivo sejam gravados-back ao Active Directory
* dispositivos iOS 7 sempre solicitará que o usuário selecione um certificado durante a autenticação de certificado de cliente.
* Algumas versões do iOS8, antes de iOS 8.3 não funcionam.

## <a name="scenario-assumptions"></a>Suposições do cenário
Neste cenário supõe que você tenha um ambiente híbrido consiste em um locatário do Azure AD e um Active Directory local. Estas locatários devem estar conectados usando o Azure AD Connect e com um domínio verificado e AD FS para o SSO. A lista de verificação abaixo ajudará você a configurar seu ambiente para o estágio descrito acima.

<a name="checklist-prerequisites-for-conditional-access-scenario"></a>Lista de verificação: Pré-requisitos para o cenário de acesso condicional
--------------------------------------------------------------
Conecte seu locatário do Azure AD com seu Active Directory local.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurar o serviço de registro do dispositivo de Active Directory do Azure
Use este guia para implantar e configurar o serviço de registro de dispositivo do Active Directory do Azure para sua organização.

Este guia pressupõe que você tenha configurado o Active Directory do Windows Server e assinou o Microsoft Azure Active Directory. Consulte pré-requisitos acima.

Para implantar o serviço de registro de dispositivo do Active Directory do Azure com seu locatário do Active Directory do Azure, conclua as tarefas a seguinte lista de verificação, em ordem. Quando um link de referência leva você para um tópico conceitual, volte para esta lista de verificação depois de examinar o tópico conceitual para que você pode prosseguir com as tarefas restantes desta lista de verificação. Algumas tarefas incluirá uma etapa de validação de cenário que pode ajudá-lo a confirmar que a etapa foi concluída com êxito.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Parte 1: Habilitar o registro de dispositivo de Active Directory do Azure

Siga a lista de verificação abaixo para habilitar e configurar o serviço de registro de dispositivo do Active Directory do Azure.

| Tarefa                                                                                                                                                                                                                                                                                                                                                                                             | Referência                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Habilite o registro de dispositivos em seu locatário do Active Directory do Azure para permitir que dispositivos ingressar o local de trabalho. Por padrão, a autenticação multifator não está habilitada para o serviço. No entanto, autenticação multifator é recomendável ao registrar um dispositivo. Antes de habilitar a autenticação multifator no ADRS, certifique-se de que o AD FS está configurado para um provedor de autenticação multifator. | [Habilitar o registro de dispositivos do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md)               |
| Dispositivos descobre seu serviço de registro de dispositivo do Active Directory do Azure olhando para os registros DNS conhecidos. Você deve configurar sua empresa DNS para que dispositivos possam detectar seu serviço de registro de dispositivo do Active Directory do Azure.                                                                                                                                                   | [Configure a descoberta de registro de dispositivos do Azure Active Directory.](active-directory-conditional-access-device-registration-overview.md) |

##<a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Parte 2: Implantar e configurar o Windows Server 2012 R2 Active Directory Federation Services e configurar um relacionamento de federação com Azure AD

| Tarefa                                                                                                                                                                                                                                                                                                                                                                                             | Referência                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Implante domínio Active Directory Domain Services com as extensões de esquema do Windows Server 2012 R2. Você não precisa atualizar qualquer um dos seus controladores de domínio para o Windows Server 2012 R2. A atualização de esquema é o único requisito. | [Atualizar seu esquema de serviços de domínio Active Directory](#upgrade-your-active-directory-domain-services-schema)               |
| Dispositivos descobre seu serviço de registro de dispositivo do Active Directory do Azure olhando para os registros DNS conhecidos. Você deve configurar sua empresa DNS para que dispositivos possam detectar seu serviço de registro de dispositivo do Active Directory do Azure.                                                                                                                                                   | [Preparar seus dispositivos de suporte do Active Directory](#prepare-your-active-directory-to-support-devices) |


##<a name="part-3-enable-device-writeback-in-azure-ad"></a>Parte 3: Ativar dispositivo write-back no Azure AD

| Tarefa                                                                                                                                                                                                                                                                                                                                                                                             | Referência                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Conclua a parte 2 ativando dispositivo write-back no Azure AD Connect. Após a conclusão, retorne isso neste guia. | [Habilitando write-back de dispositivo no Azure AD Connect](#upgrade-your-active-directory-domain-services-schema)               |


##<a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcional] Parte 4: Habilitar a autenticação multifator

É altamente recomendável que você configure uma das várias opções para autenticação multifator. Se você quiser exigir MFA, confira [Escolher a solução de segurança de vários fatores para você](../multi-factor-authentication/multi-factor-authentication-get-started.md). Ele inclui uma descrição de cada solução e links para ajudá-lo a configurar a solução de sua escolha.

## <a name="part-5-verification"></a>Parte 5: verificação

Implantação está concluída. Agora, você pode experimentar alguns cenários. Siga os links abaixo para experimentar o serviço e familiarize-se com os recursos


| Tarefa                                                                                                                                                                                                                         | Referência                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Ingresse em alguns dispositivos à sua área de trabalho usando o registro de dispositivos do Azure Active Directory. Você pode ingressar, Windows, dispositivos iOS e Android                                                                                         | [Ingressar em dispositivos à sua área de trabalho usando o registro de dispositivos do Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Você pode exibir e ativar/desativar dispositivos registrados usando o Portal de administrador. Nesta tarefa, você poderá ver alguns dispositivos registrados usando o Portal de administrador.                                                        | [Visão geral sobre o registro de dispositivo do Active Directory do Azure](active-directory-conditional-access-device-registration-overview.md)                             |
| Verifique se que objetos de dispositivo são gravados do Azure Active Directory no Windows Server Active Directory.                                                                                                                  | [Verificar dispositivos registrados são gravados-back ao Active Directory](#verify-registered-devices-are-written-back-to-active-directory)                  |
| Agora que os usuários podem registrar seus dispositivos, você pode criar aplicativo diretivas de acesso do AD FS que permitem que somente os dispositivos registrados. Nesta tarefa, que você irá criar uma regra de acesso do aplicativo e um personalizado acesso negada mensagem | [Criar uma política de acesso do aplicativo e personalizada mensagem de acesso negado](#create-an-application-access-policy-and-custom-access-denied-message)            |



## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrar o Azure Active Directory com o Active Directory local
Isso ajudará você a integrar seu locatário do Azure AD com seu local do active directory, usando o Azure AD Connect. Embora as etapas estão disponíveis no portal de clássico do Azure, anote as instruções especiais listado nesta seção.

1.  Faça logon no portal do Azure clássico usando uma conta que seja um Administrador Global no Azure AD.
2.  No painel esquerdo, selecione **Active Directory**.
3.  Na guia **diretório** , selecione seu diretório.
4.  Selecione a guia de **Integração de diretório** .
5.  Na seção **implantar e gerenciar** , siga as etapas 1 a 3 para integrar o Active Directory do Azure com seu diretório local.
  1.    Adicione domínios.
  2.    Instalar e executar o Azure AD Connect: instalar o Azure AD Connect usando as instruções a seguir, [instalação personalizada do Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).
  3. Verificar e gerenciar a sincronização de diretório. Instruções de logon única estão disponíveis nesta etapa.

  > [AZURE.NOTE]
  > Configure a federação com o AD FS, conforme descrito no documento vinculado acima. Você não precisa configurar qualquer dos recursos de visualização.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Atualizar seu esquema de serviços de domínio Active Directory

> [AZURE.NOTE]
> Atualizar seu esquema do Active Directory não pode ser revertido. É recomendável que você primeiro executa isso em um ambiente de teste.

1. Faça logon em seu controlador de domínio com uma conta que tenha direitos de administrador da empresa e o administrador de esquema.
2. Copie o diretório de **\support\adprep [mídia]** e subdiretórios para um dos seus controladores de domínio do Active Directory.
3. Onde [mídia] é o caminho para a mídia de instalação do Windows Server 2012 R2.
4. Em um prompt de comando, navegue até o diretório de adprep e execute: **/forestprep adprep.exe**. Siga as instruções na tela para concluir a atualização de esquema.

## <a name="prepare-your-active-directory-to-support-devices"></a>Preparar seu Active Directory para dar suporte a dispositivos

>[AZURE.NOTE] Esta é uma operação única que você deve executar para preparar a floresta do Active Directory para dar suporte a dispositivos. Você deve estar conectado com permissões de administrador da empresa e floresta do Active Directory deve ter o esquema do Windows Server 2012 R2 para concluir este procedimento.


##<a name="prepare-your-active-directory-forest-to-support-devices"></a>Preparar a floresta do Active Directory para dar suporte a dispositivos

> [AZURE.NOTE]
>Esta é uma operação única que você deve executar para preparar a floresta do Active Directory para dar suporte a dispositivos. Você deve estar conectado com permissões de administrador da empresa e floresta do Active Directory deve ter o esquema do Windows Server 2012 R2 para concluir este procedimento.

### <a name="prepare-your-active-directory-forest"></a>Preparar a floresta do Active Directory

1.  No seu servidor de federação, abra uma janela de comando do Windows PowerShell e digite: inicializar ADDeviceRegistration
2.  Quando solicitado para **ServiceAccountName**, digite o nome da conta de serviço selecionado como a conta de serviço do AD FS. Se for uma conta de gMSA, insira a conta no formato **domain\accountname$** . Para uma conta de domínio, use o formato **domain\accountname**.



### <a name="enable-device-authentication-in-ad-fs"></a>Habilitar a autenticação de dispositivo no AD FS

1. No seu servidor de federação, abra o console de gerenciamento do AD FS e navegue até **o AD FS** > **Políticas de autenticação**.
2. Selecione **Editar Global autenticação primária …** no painel de **ações** .
3. Marque **Habilitar a autenticação do dispositivo** e selecione**Okey**.
4. Por padrão, o AD FS periodicamente removerá dispositivos não utilizados do Active Directory. Você deve desativar essa tarefa ao usar o registro de dispositivos do Azure Active Directory para que os dispositivos podem ser gerenciados no Azure.


### <a name="disable-unused-device-cleanup"></a>Desabilitar a limpeza de dispositivo não utilizados
1. No seu servidor de federação, abra uma janela de comando do Windows PowerShell e digite: Set-AdfsDeviceRegistration - MaximumInactiveDays 0

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Preparar Azure AD Connect para write-back do dispositivo

1.  Conclua a parte 1: Preparar o Azure AD conectar.


## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Ingressar em dispositivos à sua área de trabalho usando o registro de dispositivos do Azure Active Directory

### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Ingressar em um dispositivo iOS usando o registro de dispositivos do Azure Active Directory

Registro de dispositivo de diretório ativo Azure usa o processo de inscrição de perfil de Over-the-Air para dispositivos iOS. Esse processo começa com o usuário conectando-se a URL de inscrição de perfil usando o navegador Safari. O formato de URL é da seguinte maneira:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Onde `yourdomainname` é o nome de domínio que você tenha configurado com o Active Directory do Azure. Por exemplo, se seu nome de domínio for contoso.com, a URL seria:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Há várias maneiras diferentes de se comunicar esta URL para seus usuários. Uma maneira recomendada é publicar esta URL em um aplicativo personalizado acesso negado mensagem no AD FS. Isso é coberto na seção futura: [criar uma política de acesso do aplicativo e personalizada mensagem de acesso negado](#create-an-application-access-policy-and-custom-access-denied-message).

###<a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Ingressar em um dispositivo Windows 8.1 usando o registro de dispositivos do Azure Active Directory

1. No seu dispositivo Windows 8.1, vá para **Configurações do PC** > **rede** > **local de trabalho**.
2. Digite seu nome de usuário no formato UPN. Por exemplo, dan@contoso.com..
3. Selecione **ingressar**.
4. Quando solicitado, entrar com suas credenciais. O dispositivo está ingressou.

###<a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Ingressar em um dispositivo do Windows 7 usando o registro de dispositivos do Azure Active Directory
Para registrar o Windows 7 domínio ingressou dispositivos que você precisa para implantar o pacote de software de registro do dispositivo. O pacote de software é chamado participar do local de trabalho para o Windows 7 e está disponível para download no [site do Microsoft Connect](https://connect.microsoft.com/site1164). Instruções sobre como usar o pacote estão disponíveis em [Configurar o registro de dispositivos automática do Windows 7 domínio ingressou dispositivos](active-directory-conditional-access-automatic-device-registration-windows7.md).

### <a name="join-an-android-device-using-azure-active-directory-device-registration"></a>Ingressar em um dispositivo Android usando o registro de dispositivos do Azure Active Directory

O [Autenticador do Azure para Android tópico](active-directory-conditional-access-azure-authenticator-app.md) possui instruções sobre como instalar o aplicativo do Azure autenticador em seu dispositivo Android e adicionar uma conta de trabalho. Quando uma conta de trabalho é criada com êxito em um dispositivo Android, esse dispositivo é o local de trabalho associada à organização.

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>Verificar dispositivos registrados são gravados-back ao Active Directory
Você pode exibir e verificar se os objetos de dispositivo tiveram sido gravados volta ao Active Directory usando LDP.exe ou ADSI Edit. Ambos estão disponíveis com as ferramentas de administrador do Active Directory.

Por padrão, os objetos de dispositivos que estão escritos-back do Azure Active Directory serão colocados no mesmo domínio como seu farm do AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Criar uma política de acesso do aplicativo e personalizada mensagem de acesso negado
Considere o seguinte cenário: criar um aplicativo Relying confiança de festa no AD FS e configurar uma regra de autorização de emissão que permite que somente os dispositivos registrados. Agora apenas os dispositivos que são registrados têm permissão para acessar o aplicativo. Para tornar mais fácil para seus usuários acessar o aplicativo, você deve configurar uma personalizado mensagem de acesso negado que inclui instruções sobre como ingressar em seus dispositivos. Agora os usuários têm uma maneira perfeita para registrar seus dispositivos para acessar um aplicativo.

As etapas a seguir mostrará como implementar esse cenário.

>[AZURE.NOTE]
Esta seção pressupõe que você já configurou um confiar festa de confiança para o seu aplicativo no AD FS.

1. Abra a ferramenta MMC do AD FS e navegue até o AD FS > relações de confiança > confiar festa confia.
2. Localize o aplicativo do qual se aplicará essa nova regra de acesso. Clique com botão direito do aplicativo e escolha Editar regras de declaração...
3. Selecione a guia **Regras de autorização de emissão** e selecione **Adicionar regra...**
4. A **regra de declaração** modelo lista suspensa, selecione **Permitir ou negar usuários com base em uma declaração de entrada**. Selecione **Avançar**.
5. No nome da regra de declaração: tipo de campo: **Permitir o acesso de dispositivos registrados**
6. Tipo de declaração de entrada: lista suspensa, selecione **É usuário registrado**.
7. No tipo de entrada reivindicar valor:, digite: **verdadeiro**
8. Selecione o botão de rádio **Permitir acesso aos usuários com esta declaração de entrada** .
9. Selecione **Concluir** e clique em **Aplicar**.
10. Remova todas as regras que são mais permissivas a regra que você acabou de criar. Por exemplo, remova a regra de **Permitir o acesso a todos os usuários** padrão.

Seu aplicativo agora está configurado para permitir o acesso somente quando o usuário é proveniente de um dispositivo que eles registrados e Unidas para a área de trabalho. Para políticas de acesso mais avançado, consulte [Gerenciar riscos com controle de acesso de vários fatores](https://technet.microsoft.com/library/dn280949.aspx).

Em seguida, você irá configurar uma mensagem de erro personalizada para seu aplicativo. A mensagem de erro avisará os usuários que eles devem participar seus dispositivos para a área de trabalho antes de poderem acesso ao aplicativo. Você pode criar uma aplicativo personalizado mensagem de acesso negado usando o Windows PowerShell e HTML personalizados.

No seu servidor de federação, abra uma janela de comando do Windows PowerShell e digite o seguinte comando. Substitua partes do comando com itens que são específicos ao seu sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Você deve registrar seu dispositivo antes de poder acessar este aplicativo.

**Se você estiver usando um dispositivo iOS, clique neste link para ingressar em seu dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Ingresse neste dispositivo iOS para seu local de trabalho.


**Se você estiver usando um dispositivo Windows 8.1**, você pode ingressar em seu dispositivo acessando **As configurações do PC ** >  **rede** > **local de trabalho**.


Onde "**Confiar nome de confiança de festa**" é o nome do objeto de confiança de terceiros confiar seus aplicativos do AD FS.
Onde **yourdomain.com** é o nome do domínio que você tenha configurado com o Active Directory do Azure. Por exemplo, contoso.com.
Certifique-se de remover qualquer quebras de linha (se houver) do conteúdo html que você passa para o cmdlet **Set-AdfsRelyingPartyWebContent** .


Agora, quando os usuários acessam seu aplicativo em um dispositivo que não está registrado com o serviço de registro de dispositivo do Active Directory do Azure, eles receberão uma página semelhante à captura de tela abaixo.

![Screeshot de um erro quando os usuários ainda não registrado seus dispositivos com o Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
