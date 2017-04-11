<properties 
    pageTitle="Perguntas Frequentes de RemoteApp Azure | Microsoft Azure" 
    description="Saiba respostas para as perguntas frequentes sobre RemoteApp do Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="swadhwa" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="azure-remoteapp-faq"></a>Perguntas Frequentes do Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Que ouvimos as seguintes perguntas sobre RemoteApp do Azure. Ter outras pessoas? Visite os [fóruns do RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) e diga o que você precisa saber ou um comentário lista suspensa abaixo.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>Não consegue encontrar o que você está procurando? Tenho uma pergunta que nós não atender?
Se você não encontrar as informações necessárias ou você tem uma pergunta adicional que não estamos abordando aqui, vá para o [Fórum do Azure RemoteApp](http://aka.ms/araforum) e faça sua pergunta lá. Podemos sempre adicionar mais respostas aqui.

## <a name="what-is-azure-remoteapp"></a>O que é Azure RemoteApp? ##


- **O que é Azure RemoteApp?** RemoteApp é que um serviço Azure ajuda você a fornecer acesso seguro e remoto aos aplicativos de muitos dispositivos de usuário diferentes. Leia mais sobre [RemoteApp do Azure](remoteapp-whatis.md).
- **Quais são as opções de implantação?** Existem dois tipos de coleções de RemoteApp: nuvem e híbridos. Qual delas você precisa depende de vários fatores, como se você precisa de ingresso no domínio. Vamos falar sobre todas essas decisões [aqui](remoteapp-collections.md).

## <a name="quick-tips-on-using-azure-remoteapp"></a>Dicas rápidas sobre como usar o RemoteApp do Azure ##
- **Quanto tempo até que eu estou desconectado? Por quanto tempo eu possível ocioso antes de você me dar a inicialização?** 4 horas. Se você ou um de seus usuários está ocioso para 4 horas, você será automaticamente conectado fora do Azure RemoteApp. Confira as outras configurações padrão na [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- **É possível experimentar esse serviço gratuitamente?** Sim. Há uma avaliação gratuita disponível por 30 dias. Após a avaliação terminar, você pode fazer a transição para uma conta paga (que você pode usar em produção) ou parar de usar o serviço. Comece sua avaliação gratuita acessando [portal.azure.com](http://portal.azure.com) - criar uma nova instância do RemoteApp. Com a versão de avaliação gratuita, você pode criar 2 instâncias de RemoteApp com 10 usuários por instância. Lembre-se de que esta versão de avaliação reside apenas por 30 dias.
## <a name="azure-remoteapp-subscription-details"></a>Detalhes de assinatura do Azure RemoteApp ##

- **Quais são os limites do serviço?** Você pode aprender sobre as configurações padrão e limites de serviço do Azure RemoteApp na [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md). Fale conosco se você tiver mais dúvidas.
- **Quantos usuários é preciso ter?** Há um mínimo de 20 usuários. Deixe-me repetir que, para ser bastante claro - o mínimo é 20. Você será cobrado para 20. 
- **Quanto o custo de RemoteApp?** Veja os [Detalhes de preços do Azure RemoteApp ](https://azure.microsoft.com/pricing/details/remoteapp/).
- **Um tipo de coleção custa mais do que outro?** Sim, ele pode, dependendo das suas necessidades de conjunto. Uma coleção de híbrido requer uma conexão do Azure RemoteApp à sua rede local. Se você usa uma rota VNET/Express existente, não há nenhum custo adicional. Mas se você usar um novo VNET do Azure e um gateway ou via expressa, você será cobrado para o [gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) ou [Via expressa](https://azure.microsoft.com/pricing/details/expressroute/). Esse custo (detalhado nos links de) está na parte superior de sua RemoteApp mensal do Azure custo.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Coleções - o que é suportado, que você deve usar e outras pessoas
- **Aplicativos personalizados linha de negócios (LOB) são suportados?** Sim. Para usar um aplicativo personalizado no Azure RemoteApp, criar uma [imagem de modelo personalizado](remoteapp-create-custom-image.md)e, em seguida, carregue-o para o conjunto de RemoteApp.
- **Meu aplicativo LOB personalizado funcionam Azure RemoteApp?** A melhor maneira de figura que check-out é testá-lo. Confira o [Centro de compatibilidade de RD](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Qual método de implantação (nuvem ou híbrido) é a melhor para minha organização?** Coleções de híbrido fornecem a experiência mais completa se desejar integração completa com logon único (SSO) e conectividade de rede local seguro. Coleções de nuvem fornecem uma maneira fácil e ágil para isolar sua implantação usando vários métodos de autenticação. Leia mais sobre as [Opções de implantação](remoteapp-whatis.md).
- **Temos SQL ou outro banco de dados locais ou no Azure. Que tipo de implantação devemos utilizar?** Isso depende onde está o seu banco de dados SQL ou back-end. Se o banco de dados estiver em uma rede privada, use a coleção de híbrido. Se o banco de dados é exposto à Internet e permite que o cliente conexões para se conectar a ele, você pode usar a coleção de nuvem.
- **E o mapeamento de unidade, USB e porta serial, compartilhamento de área de transferência e redirecionamento de impressora?** Todos esses recursos são compatíveis com RemoteApp do Azure. Redirecionamento de impressora e compartilhamento de área de transferência é ativado por padrão. Você pode saber mais sobre redirecionamento [aqui](remoteapp-redirection.md). 


## <a name="template-images"></a>Imagens de modelo
- **Posso usar um computador de virtual existente ou de nuvem como o modelo para meu conjunto de RemoteApp?** Sim! Você pode criar uma imagem com base em uma máquina virtual do Azure, use uma das imagens incluídas com sua assinatura ou criar uma imagem personalizada. Confira as [Opções de imagem de RemoteApp](remoteapp-imageoptions.md).


## <a name="network-options"></a>Opções de rede
- **O conjunto de híbrido requer um VNET. Podemos usar nosso VNET existente?** Você pode se o VNET existente é um VNET do Azure. Consulte "etapa 1: configurar sua rede virtual" nas [instruções de conjunto de híbrido](remoteapp-create-hybrid-deployment.md) para obter mais informações.
- **Pode usar um VNET com um conjunto de nuvem?** Na verdade, é possível. Consulte [criar um conjunto de nuvem](remoteapp-create-cloud-deployment.md), particularmente etapa 1, para obter mais informações.

## <a name="authentication-options"></a>Opções de autenticação



- **E quanto a autenticação? Quais métodos são suportados?** A coleção de nuvem suporta contas da Microsoft e contas do Active Directory do Azure, o que são contas do Office 365 também. A coleção de híbrido suporta somente as contas do Active Directory do Azure que foram sincronizadas (usando uma ferramenta como [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) de uma implantação do Active Directory do Windows Server; Especificamente, seja sincronizado com a opção de sincronização de senha ou sincronizado com a federação de serviços de Federação do Active Directory (AD FS) configurada. Você também pode configurar a [Autenticação multifator (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/).

>[AZURE.NOTE]Os usuários do Active Directory do Azure devem ser de locatário que está associada a sua assinatura. (Você pode exibir e modificar sua assinatura na guia **configurações** no portal. Consulte [alterar o locatário do Azure Active Directory usados pelo RemoteApp](remoteapp-changetenant.md) para obter mais informações.)

- **Por que não posso enviar meu acesso à conta do Active Directory do Azure?** Os usuários do Active Directory do Azure devem ser do diretório que está associada a sua assinatura. Você pode exibir ou modificar diretório na guia configurações no portal. Para obter mais informações, consulte [alterar o locatário do Azure Active Directory usados pelo RemoteApp](remoteapp-changetenant.md) .

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Clientes - dispositivo que pode usar para acessar o Azure RemoteApp?
Você pode encontrar informações de cliente boa, incluindo etapas para instalar os clientes diferentes acessar [seus aplicativos do Azure RemoteApp](remoteapp-clients.md).

- **Quais dispositivos e sistemas operacionais têm suporte os aplicativos cliente?**
Primeiro, os computadores e tablets: 
    - Windows 10 (visualização de cliente)
    - Windows 8.1 e Windows 8
    - Windows 7 Service Pack 1
    - Mac OS X
    - Windows RT
    - Tablets Android
    - iPads e os telefones:
    - iPhone
    - Telefone Android
    - Windows Phone
 
    [Baixe](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) um cliente RemoteApp agora.
- **Azure RemoteApp suporta clientes finos?** Sim, há suporte para os seguintes clientes finos incorporado do Windows:
    - Incorporadas no Windows 7 padrão
    - Incorporadas no Windows 8 padrão
    - Incorporadas no Windows 8.1 setor Pro
    - Windows 10 IoT Enterprise

- **Qual versão do Windows Server é suportada para o Host de sessão de área de trabalho (RDSH) remoto?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Suporte e comentários


- **O que é o plano de suporte para RemoteApp?** Suporte para gerenciamento de cobrança e assinatura é fornecido sem custo. Suporte técnico está disponível através de do [plano de serviço Azure](https://azure.microsoft.com/support/plans/). Você também pode obter suporte da comunidade gratuito por meio de nosso [Fórum de discussão Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Como posso enviar comentários?** Visite o [Fórum de comentários](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **Quem pode falar para saber mais sobre Azure RemoteApp?** Além de nosso [Fórum de discussão](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), que é um ótimo lugar para postar perguntas, você pode ingressar a semanal [Ask o webinar de especialistas](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), onde falarmos sobre tudo RemoteApp.
- **E sobre a documentação de RemoteApp?** Estamos feliz pela pergunta. Além do conteúdo de ajuda na Ajuda do portal registradora (basta clicar a **?** em qualquer página no portal), os artigos a seguir estão disponíveis para ensinam tudo sobre RemoteApp:
    - **Introdução:**
        - [O que é o RemoteApp?](remoteapp-whatis.md)
        - [Quais são as imagens de modelo de RemoteApp?](remoteapp-images.md)
        - [Como o licenciamento funciona?](remoteapp-licensing.md)
        - [Como RemoteApp e Office funcionam juntos?](remoteapp-o365.md)
        - [Como redirecionamento funciona em RemoteApp](remoteapp-redirection.md)?
    - **Implante:**
        - [Criar uma imagem de modelo personalizado](remoteapp-create-custom-image.md)
        - [Criar um conjunto de híbrido](remoteapp-create-hybrid-deployment.md)
        - [Criar um conjunto de nuvem](remoteapp-create-cloud-deployment.md)
        - [Configurar o Active Directory do Azure para RemoteApp](remoteapp-ad.md)
        - [Publicar um aplicativo no RemoteApp](remoteapp-publish.md)
    - **Gerencie:**
        - [Adicionar usuários](remoteapp-user.md)
        - [Práticas recomendadas para configurar e usar RemoteApp](remoteapp-bestpractices.md)  

    Vídeos! Também temos um número de vídeos sobre o RemoteApp. Alguns fornecem Introdução ([Introdução ao Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) enquanto outras pessoas orientá-lo na implantação ([implantação de nuvem](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [implantação híbrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Fazer check-out!

 
### <a name="help-us-help-you"></a>Ajude-na ajudá-lo 
Você sabia que, além de classificação neste artigo e fazer comentários para baixo abaixo, você pode fazer alterações para o artigo próprio? Algo ausente? Algo errado? Eu tenha escrito algo que é apenas confuso? Rolar para cima e clique em **Editar no GitHub** para fazer alterações - aqueles se torne para revisão e, em seguida, assim que podemos entrar neles, você verá suas alterações e melhorias aqui.
