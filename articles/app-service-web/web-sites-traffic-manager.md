<properties
    pageTitle="Controlando o tráfego de aplicativo web Azure com o Gerenciador de tráfego do Azure"
    description="Este artigo fornece informações de resumo para o Gerenciador de tráfego do Azure relacionado à aplicativos web Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Controlando o tráfego de aplicativo web Azure com o Gerenciador de tráfego do Azure

> [AZURE.NOTE] Este artigo fornece informações de resumo para o Microsoft Azure tráfego Manager que diz respeito ao Azure aplicativo de serviço Web Apps. Obter mais informações sobre o Gerenciador de tráfego do Azure em si podem ser encontradas visitando os links no final deste artigo.

## <a name="introduction"></a>Introdução
Você pode usar o Gerenciador de tráfego do Azure para controlar como as solicitações de clientes da web são distribuídas para web apps no serviço de aplicativo do Azure. Quando os pontos de extremidade do web app são adicionados a um perfil do Gerenciador de tráfego do Azure, Gerenciador de tráfego do Azure controla os o status dos seus aplicativos da web (em execução, parado ou excluídos) para que ele possa decidir qual desses pontos de extremidade deve receber tráfego.

## <a name="load-balancing-methods"></a>Métodos de balanceamento de carga
Gerenciador de tráfego Azure usa três métodos de balanceamento de carga diferentes. Eles são descritos na lista a seguir conforme eles pertencem aos aplicativos web Azure.

* **Failover**: se você tiver web app clones em diferentes regiões, você pode usar esse método para configurar um aplicativo web para todo o tráfego de cliente web de serviço e configurar outro aplicativo da web em uma região diferente para que o tráfego de serviço, caso o primeiro web app fica indisponível.

* **Round Robin**: se você tiver web app clones em diferentes regiões, você pode usar esse método para distribuir tráfego igualmente entre os aplicativos da web em diferentes regiões.

* **Desempenho**: método de desempenho a distribui o tráfego com base em menor tempo de ida e volta aos clientes. O método de desempenho pode ser usado para aplicativos web dentro da mesma região ou em diferentes regiões.

##<a name="web-apps-and-traffic-manager-profiles"></a>Web Apps e perfis do Gerenciador de tráfego
Para configurar o controle de tráfego de aplicativo da web, você cria um perfil no Azure tráfego Manager que usa um dos três carrega balanceamento métodos descritos anteriormente e, em seguida, adicione os pontos extremos (neste caso, aplicativos web) para o qual você deseja controlar o tráfego para o perfil. Seu status de aplicativo da web (em execução, parado ou excluídos) regularmente são comunicadas ao perfil para que o Gerenciador de tráfego do Azure pode direcionar o tráfego de acordo.

Ao usar o Gerenciador de tráfego do Azure com o Azure, tenha em mente os seguintes pontos:

* Para implantações somente de aplicativo de web dentro da mesma região, aplicativos Web já oferece failover e funcionalidade de alternada sem se preocupar com o modo de aplicativo web.

* Para implantações na mesma região que usam Web Apps em conjunto com outro serviço de nuvem Azure, você pode combinar os dois tipos de pontos de extremidade para habilitar cenários de implantação híbrida.

* Você só pode especificar um web app ponto de extremidade por região em um perfil. Quando você seleciona um aplicativo web como um ponto de extremidade para uma região, aplicativos web restantes na região ficam indisponíveis para seleção para o perfil.

* Os pontos de extremidade de aplicativo web que você especificar em um perfil do Gerenciador de tráfego do Azure aparecerão na seção **Nomes de domínio** na página Configurar para o aplicativo da web no perfil, mas não será configuráveis lá.

* Depois de adicionar um aplicativo web a um perfil, a **URL do Site** no painel da página do portal do aplicativo da web exibirá a URL de domínio personalizado do aplicativo web se você tiver configurado uma. Caso contrário, ele exibirá a URL de perfil do Gerenciador de tráfego (por exemplo, `contoso.trafficmgr.com`). Tanto o nome de domínio direto do web app e a URL do Gerenciador de tráfego ficará visível na página de configurar do aplicativo web na seção **Nomes de domínio** .

* Seus nomes de domínio personalizado funcione conforme o esperado, mas além de adicioná-las aos seus aplicativos web, você também deve configurar o seu mapa DNS para apontar para a URL do Gerenciador de tráfego. Para obter informações sobre como configurar um domínio personalizado para um aplicativo web Azure, consulte [Configurar um nome de domínio personalizado para um site do Azure](web-sites-custom-domain-name.md).

* Você só pode adicionar aplicativos web que estão no modo padrão para um perfil do Gerenciador de tráfego do Azure.

## <a name="next-steps"></a>Próximas etapas

Para uma conceitual e técnica visão geral do Gerenciador de tráfego do Azure, consulte [Visão geral do Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md).

Para obter mais informações sobre como usar o Gerenciador de tráfego com Web Apps, consulte que postagens de blog [Usando o Gerenciador de tráfego do Azure com Sites do Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e [Gerenciador de tráfego do Azure agora pode integrar com Sites do Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
