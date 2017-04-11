<properties
    pageTitle="Configure um nome de domínio personalizado para um aplicativo web no serviço de aplicativo do Azure que usa o Gerenciador de tráfego de balanceamento de carga de."
    description="Usar um nome de domínio personalizado para um um aplicativo web no serviço de aplicativo do Azure que inclua o Gerenciador de tráfego de balanceamento de carga."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurando um nome de domínio personalizado para um aplicativo web no serviço de aplicativo do Azure usando o Gerenciador de tráfego

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções genéricas para usar um nome de domínio personalizado com o serviço de aplicativo do Azure que usam o Gerenciador de tráfego de balanceamento de carga.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## <a name="understanding-dns-records"></a>Noções básicas sobre os registros DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## <a name="configure-your-web-apps-for-standard-mode"></a>Configurar seus aplicativos web do modo padrão

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>Adicionar um registro DNS do seu domínio personalizado

> [AZURE.NOTE] Se você tiver adquirido domínio através do Azure aplicativo de serviço Web Apps pular etapas e consulte a etapa final do artigo de [Domínio comprar para aplicativos Web](custom-dns-web-site-buydomains-web-app.md) .

Para associar seu domínio personalizado um aplicativo web no serviço de aplicativo do Azure, você deve adicionar uma nova entrada na tabela de DNS para seu domínio personalizado usando ferramentas fornecidas pelo registrador de domínio que você comprou seu nome de domínio do. Use as etapas a seguir para localizar e usar as ferramentas DNS.

1. Entre na sua conta no seu registrador de domínio e procure por uma página de gerenciamento de registros de DNS. Procure links ou áreas do site rotulado como **Nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**. Geralmente um link para essa página pode ser encontrado ser exibindo informações da sua conta e, em seguida, procurando um link como **Meus domínios**.

1. Quando tiver localizado a página management para seu nome de domínio, procure um link que permite editar os registros DNS. Isso pode ser listado como um **arquivo de zona**, **Os registros DNS**, ou como um link de configuração **avançada** .

    * A página provavelmente terá alguns registros já criados, como uma entrada associando '**@**'ou'\*' com uma página de 'estacionamento de domínio'. Ele também pode conter registros de subdomínios comuns como **www**.
    * A página será mencionar **registros CNAME**ou fornecer uma lista suspensa para selecionar um tipo de registro. Ele também pode mencionar outros registros como **registros** e **registros MX**. Em alguns casos, registros CNAME serão chamados por outros nomes como um **Registro de Alias**.
    * A página também terá campos que permitem que você **mapa** de **nome do Host** ou **nome de domínio** para outro nome de domínio.

1. Enquanto as especificações de cada registrador variam, em geral você mapear *do* seu nome de domínio personalizado (como **contoso.com**,) *para* o nome de domínio do gerente de tráfego (**contoso.trafficmanager.net**) que é usado para o aplicativo web.

    > [AZURE.NOTE] Como alternativa, se um registro já está em uso e você precisa preventivamente vincular seus aplicativos a ela, você pode criar um registro CNAME adicional. Por exemplo, para vincular preventivamente **www.contoso.com** para seu aplicativo web, crie um registro CNAME de **awverify.www** para **contoso.trafficmanager.net**. Você pode adicionar "www.contoso.com" para seu aplicativo Web sem alterar o registro CNAME "www". Para obter mais informações, consulte [criar registros DNS para um aplicativo web em um domínio personalizado][CREATEDNS].

1. Depois que terminar de adicionar ou modificar os registros DNS em seu registrador de domínios, salve as alterações.

<a name="enabledomain"></a>
## <a name="enable-traffic-manager"></a>Habilitar o Gerenciador de tráfego

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o [Node Developer Center](/develop/nodejs/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
