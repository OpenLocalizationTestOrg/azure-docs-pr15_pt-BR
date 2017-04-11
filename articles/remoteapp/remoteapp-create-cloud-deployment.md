<properties 
    pageTitle="Como criar um conjunto de nuvem do Azure RemoteApp | Microsoft Azure" 
    description="Saiba como criar uma implantação do Azure RemoteApp que salva dados na nuvem Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>Como criar uma coleção de nuvem de RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Há dois tipos de [coleções de RemoteApp Azure](remoteapp-collections.md): 

- Nuvem: completamente reside no Azure. Você pode optar por salvar todos os dados na nuvem (para um conjunto somente na nuvem) ou para conectar seu conjunto de um VNET e salvar dados lá.   
- Híbrido: inclui uma rede virtual para acesso local: isso requer o uso do Azure AD e um ambiente do Active Directory local.

Este tutorial orienta você pelo processo de criação de uma coleção de nuvem. Há quatro etapas: 

1.  Crie um conjunto de RemoteApp do Azure.
2.  Opcionalmente, configure a sincronização de diretório. Se você estiver usando o Azure AD + Active Directory, você precisa sincronizar usuários, contatos e senhas de seu local Active Directory seu locatário do Azure AD.
5.  Publica aplicativos.
6.  Configure o acesso do usuário.


**Antes de começar**

Você precisa fazer o seguinte antes de criar o conjunto:

- [Inscrever-se](https://azure.microsoft.com/services/remoteapp/) para RemoteApp do Azure. 
- Colete informações sobre os usuários que você deseja conceder acesso a. Isso pode ser informações de conta da Microsoft ou informações de conta de trabalho do Active Directory para os usuários.
- Este procedimento pressupõe que você está ambos vai usar uma das imagens de modelo fornecidas como parte de sua assinatura ou que você já carregou a imagem de modelo que você deseja usar. Se você precisar fazer o upload de uma imagem de modelo diferente, você pode fazer isso na página de imagens de modelo. Basta clicar **carregar uma imagem de modelo** e siga as etapas no assistente. 
- Quer usar a Office 365 ProPlus imagem? Fazer check-out informações [aqui](remoteapp-officesubscription.md).
- Deseja fornecer aplicativos personalizados ou LOB programas? Criar uma nova [imagem](remoteapp-imageoptions.md) e usá-lo no seu conjunto de nuvem.
- Descubra se você precisa se conectar a um VNET. Se você escolher para se conectar a um VNET, verifique se ele atende as [diretrizes de dimensionamento](remoteapp-vnetsizing.md) e que ele [pode se conectar a RemoteApp](remoteapp-vnet.md). Confira o [artigo de planejamento de VNET ](remoteapp-planvnet.md)para obter mais informações.
- Se você estiver usando um VNET, decida se deseja ingressar nele para seu domínio do Active Directory local.

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>Etapa 1: Criar um conjunto de nuvem - com ou sem um VNET##


Use as seguintes etapas para **criar uma coleção somente na nuvem**:

1. No portal de gerenciamento, vá para a página de RemoteApp.
2. Clique em **Novo > criar rápido**.
3. Insira um nome para o conjunto e selecione sua região.
4. Escolha o plano que você deseja usar - padrão ou básica.
5. Escolha o modelo para usar para este conjunto. 

    **Dica:** Sua assinatura do RemoteApp vem com [imagens de modelos](remoteapp-images.md) que contêm o Office 365 ou Office 2013 (para uso de avaliação) programas, alguns publicadas (como Word) e outros pronto para publicar. Você também pode criar uma nova [imagem](remoteapp-imageoptions.md) e usá-lo em seu conjunto de nuvem.


1. Clique em **criar RemoteApp conjunto**.
    
    **Importante:** Pode levar até 30 minutos para provisionar seu conjunto.

Depois que seu conjunto de RemoteApp Azure tiver sido criado, clique duas vezes no nome da coleção. Que abrirá a página de **Início rápido** , isso é onde você concluir a configuração do conjunto.

Use as seguintes etapas para criar uma **nuvem + coleção de VNET**:

1. No portal de gerenciamento, vá para a página de RemoteApp do Azure.
2. Clique em **novo** > **criar com VNET**.
3. Insira um nome para o conjunto.
4. Escolha o plano que você deseja usar - padrão ou básica.
5. Escolha o VNET você já tiver criado. Não sabe como fazer isso? Por agora, as etapas são no tópico [híbrido](remoteapp-create-hybrid-deployment.md) .
6. Decida se deseja ingressar em seu conjunto de seu domínio. Se Sim, você precisará usar AD Connect para integrar o Azure AD e seu ambiente do Active Directory. Que é coberta de abaixo na **etapa 2**.
6. Clique em **criar RemoteApp conjunto**.


## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>Etapa 2: Configurar a sincronização de diretórios do Active Directory (opcional) ##

Se você quiser usar o Active Directory, o Azure RemoteApp requer sincronização de diretório entre o Active Directory do Azure e seu local Active Directory para sincronizar usuários, contatos e senhas seu locatário do Active Directory do Azure. Consulte [Configurando o Active Directory do Azure RemoteApp](remoteapp-ad.md) para planejamento de informações. Você também pode ir diretamente para o [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) para obter informações.

## <a name="step-3-publish-apps"></a>Etapa 3: Publicar aplicativos ##

Um aplicativo do Azure RemoteApp é o aplicativo ou o programa que você fornecer aos usuários. Ele está localizado na imagem do modelo carregado para o conjunto. Quando um usuário acessa um aplicativo, o aplicativo aparece para ser executado em seu ambiente local, mas ele realmente é executado em uma máquina virtual no Azure. 

Antes que os usuários podem acessar aplicativos, você precisa publicá-los – publicar aplicativos permite aos usuários acessam os aplicativos por meio do cliente de área de trabalho remota.
 
Você pode publicar vários aplicativos à sua coleção de RemoteApp do Azure. Na página de publicação, clique em **Publicar** para adicionar um programa. Você pode publicar no menu **Iniciar** da imagem modelo ou especificando o caminho da imagem de modelo para o aplicativo. Se você optar por adicionar a partir do menu **Iniciar** , escolha o aplicativo para publicar. Se você optar por fornecer o caminho para o aplicativo, forneça um nome para o aplicativo e o caminho para onde ele está instalado na imagem do modelo.

## <a name="step-4-configure-user-access"></a>Etapa 4: Configurar o acesso de usuário ##

Agora que você criou seu conjunto, você precisa adicionar os usuários que você deseja ser capaz de usar seus recursos remotos. Se você estiver usando o Active Directory, os usuários que você fornece acesso para precisam existir no Active Directory locatário associado a assinatura que você usou para criar esse conjunto.

1.  Na página do início rápido, clique em **Configurar o acesso do usuário**. 
2.  Insira a conta de trabalho (a partir do Active Directory) ou a conta da Microsoft que você deseja conceder acesso.

    **Observações:** 

    Certifique-se de que você usa o “user@domain.com” formato.

    Se você estiver usando o Office 365 ProPlus no seu conjunto, você deve usar as identidades do Active Directory para seus usuários. Isso ajuda a validar licenciamento. 

3.  Depois que os usuários são validados, clique em **Salvar**.


## <a name="next-steps"></a>Próximas etapas ##

Isso - ter criado e implantado sua coleção de nuvem do Azure RemoteApp com êxito. A próxima etapa é que os usuários baixar e instalar o cliente de área de trabalho remota. Você pode encontrar a URL para o cliente na página de início rápido do Azure RemoteApp. Em seguida, ter log de usuários para o cliente e acessar os aplicativos que você publicou.

### <a name="help-us-help-you"></a>Ajude-na ajudá-lo 
Você sabia que, além de classificação neste artigo e fazer comentários para baixo abaixo, você pode fazer alterações para o artigo próprio? Algo ausente? Algo errado? Eu tenha escrito algo que é apenas confuso? Rolar para cima e clique em **Editar no GitHub** para fazer alterações - aqueles se torne para revisão e, em seguida, assim que podemos entrar neles, você verá suas alterações e melhorias aqui.