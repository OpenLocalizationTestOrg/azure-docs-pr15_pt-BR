<properties
    pageTitle="Como criar um conjunto de híbrido para Azure RemoteApp | Microsoft Azure"
    description="Aprenda a criar uma implantação de RemoteApp que se conecta à sua rede interna."
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

# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Como criar um conjunto de híbrido para RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Há dois tipos de coleções de RemoteApp Azure:

- Nuvem: completamente reside no Azure. Você pode optar por salvar todos os dados na nuvem (para um conjunto somente na nuvem) ou para conectar seu conjunto de um VNET e salvar dados lá.   
- Híbrido: inclui uma rede virtual para acesso local: isso requer o uso do Azure AD e um ambiente do Active Directory local.

Não sabe que você precisa? Confira [qual tipo de coleta de você precisa para RemoteApp do Azure](remoteapp-collections.md).

Este tutorial orienta você pelo processo de criação de uma coleção de híbrido. Há oito etapas:

1.  Decida quais [imagem](remoteapp-imageoptions.md) a ser usada para o conjunto. Você pode criar uma imagem personalizada ou usar uma das imagens Microsoft incluídas em sua assinatura.
2. Configure sua rede virtual. Confira as informações de [planejamento de VNET](remoteapp-planvnet.md) e [dimensionamento](remoteapp-vnetsizing.md) .
2.  Crie um conjunto.
2.  Ingresse em seu conjunto de seu domínio local.
3.  Adicione uma imagem de modelo à sua coleção.
4.  Configure a sincronização de diretório. Azure RemoteApp requer que você integrar com o Active Directory do Azure por qualquer um dos 1) Configurando Azure Active Directory Sync com a opção de sincronização de senha ou 2) Configurando Azure Active Directory Sync sem a opção de sincronização de senha, mas usando um domínio federado para o AD FS. Confira as [informações de configuração para o Active Directory com RemoteApp](remoteapp-ad.md).
5.  Publica aplicativos RemoteApp.
6.  Configure o acesso do usuário.

**Antes de começar**

Você precisa fazer o seguinte antes de criar o conjunto:

- [Inscrever-se](https://azure.microsoft.com/services/remoteapp/) para RemoteApp do Azure.
- Crie uma conta de usuário no Active Directory para usar como a conta de serviço do Azure RemoteApp. Restrinja as permissões para essa conta para que ele só pode ingressar máquinas ao domínio.
- Coletar informações sobre sua rede local: endereço IP informações e detalhes de dispositivo VPN.
- Instale o módulo do [PowerShell do Azure](../powershell-install-configure.md) .
- Colete informações sobre os usuários que você deseja conceder acesso a. Você precisará o Active Directory do Azure UPN (por exemplo, name@contoso.com) para cada usuário. Certifique-se de que o nome UPN corresponda entre Azure AD e o Active Directory.
- Escolha sua imagem de modelo. Uma imagem de modelo do Azure RemoteApp contém os aplicativos e programas que você deseja publicar para seus usuários. Consulte [Opções de imagem do Azure RemoteApp](remoteapp-imageoptions.md) para obter mais informações.
- Quer usar a Office 365 ProPlus imagem? Fazer check-out informações [aqui](remoteapp-officesubscription.md).
- [Configurar o Active Directory para RemoteApp](remoteapp-ad.md).



## <a name="step-1-set-up-your-virtual-network"></a>Etapa 1: Configurar sua rede virtual
Você pode implantar um conjunto de híbrido que usa uma rede virtual Azure existente, ou você pode criar uma nova rede virtual. Uma rede virtual permite que os dados do access de usuários em sua rede local por meio de recursos remotos RemoteApp. Usando uma rede virtual Azure fornece sua coleção acesso direto à rede a outros serviços do Azure e máquinas virtuais implantadas para a rede virtual.

Certifique-se de que você examine as informações de [planejamento de VNET](remoteapp-planvnet.md) e [tamanho VNET](remoteapp-vnetsizing.md) antes de criar sua VNET.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Criar um VNET do Azure e ingressar em sua implantação do Active Directory

Comece criando uma [rede virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Isso é feito na guia **rede** no portal do Azure. Você precisa conectar sua rede virtual a implantação do Active Directory que é sincronizada em seu locatário do Active Directory do Azure.

Para obter mais informações, consulte [criar uma rede virtual usando o portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Certificar-se de que sua rede virtual está pronto para RemoteApp do Azure
Antes de criar seu conjunto, vamos garantir que sua nova rede virtual está pronto. Você pode validar isso seguindo este procedimento:

1. Crie uma máquina virtual Azure dentro da sub-rede da rede virtual que você acabou de criar para RemoteApp.
2. Use a área de trabalho remota para se conectar à máquina virtual. (Clique em **Conectar**.)
3. Ele ingresse implantação do Active Directory mesma que você deseja usar para RemoteApp.

Que funcionou? Sua rede virtual e sub-rede estão prontos para Azure RemoteApp!

Você pode encontrar mais informações sobre como criar Azure máquinas virtuais e conectá-los com Desktop remoto [aqui](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Etapa 2: Criar um conjunto de RemoteApp do Azure ##



1. No [portal do Azure](http://manage.windowsazure.com), vá para a página de RemoteApp do Azure.
2. Clique em **Novo > criar com VNET**.
3. Insira um nome para o conjunto.
4. Escolha o plano que você deseja usar - padrão ou básica.
5. Escolha seu VNET do menu suspenso lista e, em seguida, sua sub-rede.
6. Escolha a ingressar nele para seu domínio.
5. Clique em **criar RemoteApp conjunto**.

Depois que seu conjunto de RemoteApp Azure tiver sido criado, clique duas vezes no nome da coleção. Que abrirá a página de **Início rápido** , isso é onde você concluir a configuração do conjunto.

Algo vão errado? Confira as [informações de solução de problemas de conjunto de híbrido](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Etapa 3: Vincular seu conjunto para o domínio local ##


1. Na página de **Início rápido** , clique em **ingressar em um domínio local**.
2. Adicione a conta de serviço do Azure RemoteApp para seu domínio do Active Directory local. Será necessário o nome de domínio, a unidade organizacional, o nome de usuário da conta de serviço e a senha.

    Este é as informações reunidas se você seguiu as etapas em [Configure Active Directory do Azure RemoteApp](remoteapp-ad.md).


## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Etapa 4: Link para uma imagem de RemoteApp do Azure ##

Uma imagem de modelo do Azure RemoteApp contém os programas que você deseja compartilhar com os usuários. Você pode criar uma nova [imagem de modelo de](remoteapp-imageoptions.md) ou vincular a uma imagem existente (um já importados ou carregado RemoteApp Azure). Você também pode vincular a uma das Azure RemoteApp [imagens de modelos](remoteapp-images.md) que contêm o Office 365 ou programas do Office 2013 (para uso de avaliação).

Se você estiver carregando a nova imagem, você precisa digitar o nome e escolha o local da imagem. Na próxima página do assistente, você deverá ver um conjunto de cmdlets do PowerShell - copiar e executar esses cmdlets de um prompt do Windows PowerShell para carregar a imagem especificada.

Se você estiver vinculando a uma imagem de modelo existente, basta Especifica o nome da imagem, local e assinatura do Azure associada.



## <a name="step-5-configure-active-directory-directory-synchronization"></a>Etapa 5: Configurar a sincronização de diretórios do Active Directory ##

Azure RemoteApp requer que você integrar com o Active Directory do Azure por qualquer um dos 1) Configurando Azure Active Directory Sync com a opção de sincronização de senha ou 2) Configurando Azure Active Directory Sync sem a opção de sincronização de senha, mas usando um domínio federado para o AD FS.

Confira [Conectar AD](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) - este artigo ajuda você a configurar a integração de diretório em 4 passos.

Consulte o [mapa para sincronização de diretório](http://msdn.microsoft.com//library/azure/hh967642.aspx) para planejamento etapas e informações detalhadas.

## <a name="step-6-publish-apps"></a>Etapa 6: Publicar aplicativos ##

Um aplicativo do Azure RemoteApp é o aplicativo ou o programa que você fornecer aos usuários. Ele está localizado na imagem do modelo carregado para o conjunto. Quando um usuário acessa um aplicativo, ele aparece para ser executado em seu ambiente local, mas ele realmente está em execução no Azure.

Antes que os usuários podem acessar aplicativos, você precisa publicá-los – permite aos usuários acessar os aplicativos por meio do cliente de área de trabalho remota.

Você pode publicar vários aplicativos à sua coleção. Na página de publicação, clique em **Publicar** para adicionar um aplicativo. Você pode publicar no menu **Iniciar** da imagem modelo ou especificando o caminho da imagem de modelo para o aplicativo. Se você optar por adicionar a partir do menu **Iniciar** , escolha o programa para adicionar. Se você optar por fornecer o caminho para o aplicativo, forneça um nome para o aplicativo e o caminho para onde ele está instalado na imagem do modelo.

## <a name="step-7-configure-user-access"></a>Etapa 7: Configurar o acesso de usuário ##

Agora que você criou seu conjunto, você precisa adicionar os usuários que você deseja ser capaz de usar seus recursos remotos. Os usuários que você fornece acesso para precisam existir no Active Directory locatário associado a assinatura que você usou para criar este conjunto de RemoteApp do Azure.

1.  Na página do início rápido, clique em **Configurar o acesso do usuário**.
2.  Insira a conta de trabalho (a partir do Active Directory) ou a conta da Microsoft que você deseja conceder acesso.

    **Observações:**

    Certifique-se de que você usa o “user@domain.com” formato.

    Se você estiver usando o Office 365 ProPlus no seu conjunto, você deve usar as identidades do Active Directory para seus usuários. Isso ajuda a validar licenciamento.


3.  Depois que os usuários são validados, clique em **Salvar**.


## <a name="next-steps"></a>Próximas etapas ##
Isso - ter criado com êxito e implantado sua coleção de híbrido RemoteApp do Azure. A próxima etapa é que os usuários baixar e instalar o cliente de área de trabalho remota. Você pode encontrar a URL para o cliente na página de início rápido do Azure RemoteApp. Em seguida, ter log de usuários para o cliente e acessar os aplicativos que você publicou.



### <a name="help-us-help-you"></a>Ajude-na ajudá-lo
Você sabia que, além de classificação neste artigo e fazer comentários para baixo abaixo, você pode fazer alterações para o artigo próprio? Algo ausente? Algo errado? Eu tenha escrito algo que é apenas confuso? Rolar para cima e clique em **Editar no GitHub** para fazer alterações - aqueles se torne para revisão e, em seguida, assim que podemos entrar neles, você verá suas alterações e melhorias aqui.
