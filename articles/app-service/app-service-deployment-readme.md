<properties
    pageTitle="Implantando aplicativos do serviço de aplicativo do Azure"
    description="Saiba como implantar aplicativos para o trabalho de serviço de aplicativo"
    keywords="serviço de aplicativo de serviço, azure aplicativo, implantando, implantação"
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="dariagrigoriu"/>

# <a name="azure-app-service-deployment-overview"></a>Visão geral de implantação do Azure serviço de aplicativo

Serviço de aplicativo do Azure fornece um recurso avançado e integrado definido para dar suporte a criação de fluxos de trabalho de implantação flexível e eficiente. Implantação do aplicativo pode aproveitar opções que incluem integração contínua ou controle de fonte local de publicação, WebDeploy e FTP. O método recomendado para implantação do aplicativo de produção é trocar de slot de implantação. Slots de implantação representam os ambientes de teste e integração associados com aplicativos de produção. Slots de implantação podem ser configurados e direcionadas com o tráfego da web para validação e tráfego pode ser trocado sob demanda para implantação para produção sem nenhum tempo de inatividade e automatizada aquecimento. As etapas de um fluxo de trabalho de implantação podem ser facilmente automatizadas por meio de produtos de gerenciamento de lançamento tais como gerenciamento de lançamento do Visual Studio. Isso é útil para coordenação com outros recursos de solução (por exemplo, armazenamento de dados), recorrência e replicação entre várias unidades de implantação. 

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]
