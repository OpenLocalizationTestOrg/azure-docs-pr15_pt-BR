<properties 
    pageTitle="O que é Azure RemoteApp? | Microsoft Azure" 
    description="Saiba como compartilhar aplicativos e recursos para qualquer dispositivo através de RemoteApp do Azure." 
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
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="what-is-azure-remoteapp"></a>O que é Azure RemoteApp?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp traz a funcionalidade do programa Microsoft RemoteApp local, feito pelos serviços de área de trabalho remota, no Azure. RemoteApp Azure ajuda você a fornecer acesso seguro e remoto aos aplicativos de muitos dispositivos de usuário diferentes. Azure RemoteApp basicamente hospeda sessões de Terminal Server não persistente na nuvem e começar a usá-los e compartilhá-los com seus usuários.

Com o Azure RemoteApp, você pode compartilhar aplicativos e recursos com usuários em praticamente qualquer dispositivo. Podemos hospedar seus aplicativos na nuvem, significando que estamos cuidam do hardware e a escala para atender às exigências de usuário. Tudo o que você precisa fazer é carregar os aplicativos que você deseja compartilhar e, em seguida, obter seus usuários usem esses aplicativos. [Os usuários acessar manter seus próprios dispositivos](remoteapp-clients.md), embora você gerenciar tudo por meio do portal Azure. Você ainda tem a opção de usando suas credenciais corporativas, permitindo-lhe a garantir a segurança de aplicativos e dados.

Leia mais para saber mais sobre Azure RemoteApp, ou, se nós já tiver convencido você, [Experimente agora](https://azure.microsoft.com/services/remoteapp/).

Tem dúvidas sobre o Azure RemoteApp? Confira nosso [perguntas Frequentes](remoteapp-faq.md).

Azure RemoteApp é parte da [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novo!** Deseja saber mais sobre o Azure RemoteApp? Ou pronto para validar Azure RemoteApp em escala? Participar de nosso semanal [pergunte aos especialistas webinar](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## <a name="azure-remoteapp-collections"></a>Coleções de RemoteApp Azure
Há dois tipos de [coleções de RemoteApp Azure](remoteapp-collections.md):


- Uma **coleção de nuvem** é hospedado em e armazena dados para programas na nuvem. Os usuários podem acessar aplicativos por entrar com sua conta da Microsoft ou credenciais corporativas sincronizados ou federado com o Active Directory do Azure.

    Escolha um conjunto de nuvem quando o aplicativo que você deseja compartilhar não requer uma conexão a qualquer recurso rede privada da sua empresa (por exemplo, por meio de um dispositivo VPN). Se o aplicativo usa recursos na Internet, OneDrive ou do Azure, uma coleção de nuvem irá funcionar para você. Também é mais rápida criar.

- Uma **coleção de híbrido** é hospedado em e armazena dados na nuvem Azure, mas também permite que os usuários acessar dados e recursos armazenados em sua rede local. Os usuários podem acessar aplicativos efetuando com suas credenciais corporativas sincronizados ou federado com o Active Directory do Azure.

    Escolha um conjunto de híbrido se você precisar de uma conexão a recursos em uma rede privada da sua empresa. Por exemplo, se o aplicativo precisa acessar um destes procedimentos:

    - Servidores de arquivo localizados na sua intranet
    - Quicken
    - Bancos de dados atrás de um firewall

    Isso é geralmente mais útil para empresas de grandes porte com muitos recursos em suas redes privadas que não podem ser movidas para a nuvem.

Os conjuntos de diferentes têm diferentes opções, incluindo redes, para que descobrir [qual coleção](remoteapp-collections.md) funciona melhor para você. 


### <a name="updating-your-collection"></a>Atualizando seu conjunto
Uma das principais diferenças entre as coleções híbrido e nuvem é como são tratadas as atualizações de software. Com uma coleção de nuvem que usa a imagem pré-instalada do Office 365 ProPlus ou Office 2013, você não precisa se preocupar sobre todas as atualizações. O serviço mantém a mesmo e passa atualizações continuamente, aplicativos e o sistema operacional.

Para conjuntos de híbrido, bem como coleções de nuvem que usam uma imagem de modelo personalizado, for responsável pelo mantendo a imagem e aplicativos. Para imagens de domínio, você pode controlar atualizações usando ferramentas como Windows Update, política de grupo ou System Center.

Após atualizar sua imagem de modelo personalizado, você carregar a nova imagem na nuvem Azure e atualize o conjunto para usar a nova imagem. (Você pode fazer isso do Azure RemoteApp **Quick Start** page ou painel.)

Para obter mais informações, consulte [atualizar seu conjunto](remoteapp-update.md) .

## <a name="supported-remoteapp-clients"></a>Clientes de RemoteApp com suporte
Azure RemoteApp é suportado em aplicativos cliente RemoteApp para Windows e Windows RT, bem como os aplicativos de área de trabalho remota Microsoft para Mac, iOS e Android. Seus usuários podem usar esses aplicativos em seu celular ou calcular dispositivos para acessar os novos programas do Azure RemoteApp.

Consulte [acessar seus aplicativos do Azure RemoteApp](remoteapp-clients.md) para obter mais informações sobre os clientes.

## <a name="next-steps"></a>Próximas etapas
Vá! Experimente! Estes artigos ajudar você começar com o Azure RemoteApp:

- [Que tipo de coleção você precisa do Azure RemoteApp?](remoteapp-collections.md)
- [Criar uma imagem de RemoteApp do Azure](remoteapp-imageoptions.md)
- [Como criar uma coleção de nuvem de RemoteApp do Azure](remoteapp-create-cloud-deployment.md)
- [Como criar uma coleção de híbrido de RemoteApp do Azure](remoteapp-create-hybrid-deployment.md)
- [Como o licenciamento funciona no Azure RemoteApp?](remoteapp-licensing.md)
- [Práticas recomendadas para usar RemoteApp do Azure](remoteapp-bestpractices.md)
- [Perguntas Frequentes do Azure RemoteApp](remoteapp-faq.md)
 

### <a name="help-us-help-you"></a>Ajude-na ajudá-lo 
Você sabia que, além de classificação neste artigo e fazer comentários para baixo abaixo, você pode fazer alterações para o artigo próprio? Algo ausente? Algo errado? Eu tenha escrito algo que é apenas confuso? Rolar para cima e clique em **Editar no GitHub** ou **Editar** para fazer alterações - aqueles se torne para revisão e, em seguida, assim que podemos entrar neles, você verá suas alterações e melhorias aqui.