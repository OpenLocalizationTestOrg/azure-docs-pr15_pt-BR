<properties
    pageTitle="Serviço de aplicativo Azure para web, celular e aplicativos de API | Microsoft Azure"
    description="Saiba como o serviço de aplicativo do Azure ajuda você a desenvolver, implantar e gerenciar web e aplicativos móveis."
    keywords="serviço de aplicativo, serviço de aplicativo do azure, o serviço de aplicativo custo, escala, escalável, implantação do aplicativo, implantação de aplicativo do azure, paas, plataforma como serviço, site, site da web, web, azure móvel"
    services="app-service"
    documentationCenter=""
    authors="omarkmsft"
    manager="erikre"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="omark"/>

# <a name="what-is-azure-app-service"></a>O que é o serviço de aplicativo do Azure?

*Serviço de aplicativo* é uma [plataforma como serviço](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) oferecendo do Microsoft Azure. Crie web e aplicativos móveis para qualquer plataforma ou dispositivo. Integre seus aplicativos com soluções SaaS, conectar-se com aplicativos de locais e automatizar seus processos de negócios. Azure executa seus aplicativos em totalmente gerenciadas máquinas virtuais (VMs), com sua opção de recursos compartilhados de máquina virtual ou VMs dedicadas.

Serviço de aplicativo inclui a web e recursos móveis que estamos anteriormente entregues separadamente como sites do Azure e os serviços do Azure Mobile. Ele também inclui novos recursos para automatizar processos de negócios e nuvem APIs de hospedagem. Como um serviço único integrado, o serviço de aplicativo permite que você compor vários componentes – sites, back-ends do aplicativo móvel, APIs RESTful e processos de negócios – em uma única solução.

O vídeo de 4 minutos a seguir fornece uma breve explicação de como o serviço de aplicativo se relaciona a ofertas Azure anteriores e o que há de novo no-lo.

+[AZURE.VIDEO app-service-history-lesson]

## <a name="why-use-app-service"></a>Por que usar o serviço de aplicativo?

Aqui estão alguns dos principais recursos e recursos de serviço de aplicativo:

- **Vários idiomas e estruturas** - serviço de aplicativo tem suporte de primeira classe para ASP.NET, Node, Java, PHP e Python. Você também pode executar o [Windows PowerShell e outros scripts ou executáveis](../app-service-web/web-sites-create-web-jobs.md) em VMs de serviço de aplicativo.

- **Otimização de DevOps** - configure [implantação e integração contínua](../app-service-web/app-service-continuous-deployment.md) com os serviços de equipe do Visual Studio, GitHub ou BitBucket. Promova atualizações através [de teste e transferência ambientes](../app-service-web/web-sites-staged-publishing.md). Executar [A / B testes](../app-service-web/app-service-web-test-in-production-get-start.md). Gerencie seus aplicativos em um serviço de aplicativo usando o [PowerShell do Azure](../powershell-install-configure.md) ou a [interface de linha de várias plataformas (comando)](../xplat-cli-install.md).

- **Global dimensionar com alta disponibilidade** - escala [para cima](../app-service-web/web-sites-scale.md) ou [reduzir](../monitoring-and-diagnostics/insights-how-to-scale.md) automaticamente ou manualmente. Hospedar seus aplicativos em qualquer lugar na infraestrutura do data center global da Microsoft e o serviço de aplicativo [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) promete alta disponibilidade.

- **Conexões a dados locais e plataformas de SaaS** - escolher mais de 50 [conectores](../connectors/apis-list.md) para sistemas corporativos (como SAP, Siebel e Oracle), serviços de SaaS (como Salesforce e Office 365) e os serviços de internet (como o Facebook e Twitter). Acesso local dados usando [Híbrido conexões](../biztalk-services/integration-hybrid-connection-overview.md) e [Redes virtuais do Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Segurança e conformidade** - serviço de aplicativo é [ISO, SOC e PCI compatível](https://www.microsoft.com/TrustCenter/).

- **Modelos de aplicativo** - escolher de uma lista abrangente de modelos de aplicativo do [Azure Marketplace](https://azure.microsoft.com/marketplace/) que permitem que você usar um Assistente para instalar o software de código-fonte aberto populares como WordPress, Joomla e Drupal.

- **Integração do visual Studio** - dedicadas ferramentas no Visual Studio simplificar o trabalho da criação, implantação e depuração.

## <a name="app-types-in-app-service"></a>Tipos de aplicativo no aplicativo de serviço

Serviço de aplicativo oferece diversos *tipos de aplicativo*, cada um dos quais é destinada a hospedar um tipo específico de carga de trabalho:

- [**Web Apps**](../app-service-web/app-service-web-overview.md) - para aplicativos da web e sites de hospedagem.

- [**Aplicativos móveis**](../app-service-mobile/app-service-mobile-value-prop.md) Para hospedar o aplicativo móvel volta termina.

- [**Aplicativos de API**](../app-service-api/app-service-api-apps-why-best-platform.md) - para APIs RESTful de hospedagem.

- [**Lógica de aplicativos**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - para automatizar processos de negócios e a integração de sistemas e dados nuvens sem escrever código.

Word *app* aqui refere-se aos recursos de hospedagem dedicados à execução de uma carga de trabalho. Levando "web app" como exemplo, você está acostumado provavelmente pensando em um aplicativo web como os recursos de computação e o código de aplicativo que juntos fornecer funcionalidade para um navegador. Mas em um serviço de aplicativo de um *aplicativo web* é os recursos de computação Azure fornece para hospedar seu código do aplicativo. Se seu aplicativo é composto de uma web front-end e uma API RESTful back-end, você pode implantar ambos para um aplicativo web ou você pode implantar seu código front-end para um aplicativo web e seu código de back-end para um aplicativo de API. Seu aplicativo pode ser composto de vários aplicativos de serviço de aplicativo tipos diferentes.

## <a name="app-service-plans"></a>Planos de serviço de aplicativo

[Planos de serviço de aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview.md) especificar o tipo de recursos de computação seus aplicativos executados em. Se você espera cargas de tráfego light, você pode usar VMs compartilhadas (**livre** e **compartilhado** níveis de preços). Para cargas maiores, você pode escolher entre vários tamanhos de VMs dedicados (níveis**básicos**, **Standard**e **Premium** ). Vários aplicativos de serviço de aplicativo podem compartilhar o mesmo plano, e eles dimensionar para os níveis de preços juntos no plano.

Se precisar de mais escalabilidade e isolamento da rede, você pode executar seus aplicativos em um [Ambiente de serviço de aplicativo](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Preços

Para obter informações sobre quanto custos de serviço de aplicativo, consulte [Preços de serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Testar o serviço de aplicativo

[Criar um aplicativo da web de amostra, o aplicativo móvel ou o aplicativo de lógica](http://go.microsoft.com/fwlink/?LinkId=523751) e reprodução com ele para 1 hora, com nenhum cartão de crédito necessários, sem compromissos, sem aborrecimentos.

Ou abrir uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/)e experimente um dos nossos tutoriais de Introdução:

* [Tutorial: Criar um aplicativo web](../app-service-web/app-service-web-get-started.md)
* [Tutorial: Criar um aplicativo móvel](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Tutorial: Criar um aplicativo de API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Tutorial: Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
