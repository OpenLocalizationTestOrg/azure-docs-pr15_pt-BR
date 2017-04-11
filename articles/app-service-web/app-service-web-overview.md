<properties
    pageTitle="Visão geral de aplicativos da Web | Microsoft Azure"
    description="Saiba como o serviço de aplicativo do Azure ajuda você a desenvolver e hospedar aplicativos da web"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="cephalin"/>

# <a name="web-apps-overview"></a>Visão geral de aplicativos Web

*Aplicativo de serviço Web Apps* é uma plataforma de computação totalmente gerenciado que é otimizada para aplicativos da web e sites de hospedagem. Esta oferta de [plataforma como serviço](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) do Microsoft Azure permite você focalizar a lógica de negócios enquanto Azure cuida da infraestrutura para executar e dimensionar seus aplicativos.

O vídeo de 5 minutos a seguir apresenta Azure aplicativo de serviço Web Apps.

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="what-is-a-web-app-in-app-service"></a>O que é um aplicativo web no aplicativo de serviço?

No serviço de aplicativo, um *aplicativo web* é os recursos de computação que Azure fornece para um aplicativo web ou site de hospedagem.  

Os recursos de computação podem estar em compartilhado ou dedicadas VMs (máquinas virtuais), dependendo do nível de preço que você escolher. O código do seu aplicativo é executado em uma máquina virtual gerenciada que está isolada de outros clientes.

Seu código pode estar em qualquer linguagem ou estrutura que é suportada pelo [Serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), como ASP.NET, Node, Java, PHP ou Python. Você também pode executar scripts que usam o [PowerShell e outras linguagens de script](web-sites-create-web-jobs.md#acceptablefiles) em um aplicativo web.

Para obter exemplos de cenários típicos de aplicativos que você pode usar os aplicativos Web do, consulte [cenários de aplicativo da Web](https://azure.microsoft.com/documentation/scenarios/web-app/) e a seção **cenários e recomendações** de [serviço de aplicativo do Azure, comparação de máquinas virtuais, estrutura de serviço e os serviços de nuvem](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Por que usar Web Apps?

Aqui estão alguns dos principais recursos do serviço de aplicativo que se aplicam aos aplicativos Web:

- **Vários idiomas e estruturas** - serviço de aplicativo tem suporte de primeira classe para ASP.NET, Node, Java, PHP e Python. Você também pode executar o [PowerShell e outros scripts ou executáveis](../app-service-web/web-sites-create-web-jobs.md) em VMs de serviço de aplicativo.

- **Otimização de DevOps** - configure [implantação e integração contínua](../app-service-web/app-service-continuous-deployment.md) com os serviços de equipe do Visual Studio, GitHub ou BitBucket. Promova atualizações através [de teste e transferência ambientes](../app-service-web/web-sites-staged-publishing.md). Executar [A / B testes](../app-service-web/app-service-web-test-in-production-get-start.md). Gerencie seus aplicativos em um serviço de aplicativo usando o [PowerShell do Azure](../powershell-install-configure.md) ou a [interface de linha de várias plataformas (comando)](../xplat-cli-install.md).

- **Global dimensionar com alta disponibilidade** - escala [para cima](../app-service-web/web-sites-scale.md) ou [reduzir](../monitoring-and-diagnostics/insights-how-to-scale.md) automaticamente ou manualmente. Hospedar seus aplicativos em qualquer lugar na infraestrutura do data center global da Microsoft e o serviço de aplicativo [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete alta disponibilidade.

- **Conexões a dados locais e plataformas de SaaS** - escolher mais de 50 [conectores](../connectors/apis-list.md) para sistemas corporativos (como SAP, Siebel e Oracle), serviços de SaaS (como Salesforce e Office 365) e os serviços de internet (como o Facebook e Twitter). Acesso local dados usando [Híbrido conexões](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes virtuais do Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Segurança e conformidade** - serviço de aplicativo é [ISO, SOC e PCI compatível](https://www.microsoft.com/TrustCenter/).

- **Modelos de aplicativo** - escolher de uma lista abrangente de modelos de aplicativo do [Azure Marketplace](https://azure.microsoft.com/marketplace/) que permitem que você usar um Assistente para instalar o software de código-fonte aberto populares como WordPress, Joomla e Drupal.

- **Integração do visual Studio** - dedicadas ferramentas no Visual Studio simplificar o trabalho da criação, implantação e depuração.

Além disso, um aplicativo web pode tirar proveito dos recursos oferecidos pelo [API aplicativos](../app-service-api/app-service-api-apps-why-best-platform.md) (como CORS suporte) e [Aplicativos Mobile](../app-service-mobile/app-service-mobile-value-prop.md) (como as notificações por push). Para obter mais informações sobre tipos de aplicativo no aplicativo de serviço, consulte [Visão geral do serviço de aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

Além de aplicativos da Web no aplicativo de serviço, o Azure oferece outros serviços que podem ser usados para hospedar sites e aplicativos web. Para a maioria dos cenários, aplicativos Web é a melhor opção.  Para arquitetura de microservice, considere [Tecidos de serviço](https://azure.microsoft.com/documentation/services/service-fabric)e se você precisar de mais controle sobre as VMs que seu código é executado em, considere [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para obter mais informações sobre como escolher entre esses serviços Azure, consulte [o serviço de aplicativo do Azure, máquinas virtuais, estrutura de serviço e comparação de serviços de nuvem](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Guia de Introdução

Para começar a Implantando o código de exemplo para um novo aplicativo web no aplicativo de serviço, siga o tutorial de [implantar o aplicativo web do primeiro no Azure em 5 minutos](app-service-web-get-started.md) . Você precisará de uma conta gratuita do Azure.

Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.
