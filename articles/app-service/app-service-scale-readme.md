<properties
    pageTitle="Serviço de aplicativo do Azure: Dimensionamento aplicativos de serviço de aplicativo"
    description="Aprenda as vantagens e desvantagens do dimensionamento aplicativo no aplicativo de serviço."
    keywords="aplicativo, azure aplicativo serviço, escaláveis, plano de serviço de aplicativo escala, custos de serviço de aplicativo"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-scaling-app-service-applications"></a>Serviço de aplicativo do Azure: Dimensionamento aplicativos de serviço de aplicativo

Aplicativos hospedados no serviço de aplicativo do Azure podem [atingir escala enorme](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Entretanto, o dimensionamento de um aplicativo é um problema complexo que não tem uma solução "tamanho único". Para dimensionar corretamente seu aplicativo lá estão 3 áreas principais que serão contribuem para o sucesso de aplicativos:

1. Noções básicas sobre arquitetura de seu aplicativo e seus pontos fracos.
    * É o seu estado de aplicativo? Sem estado?
    * O que são todos os componentes do seu aplicativo?
        * Onde estão os gargalos no aplicativo?
    * Quando a carga é aplicada ao seu aplicativo, o que interromperá primeiro?
2. Noções básicas sobre os requisitos de desempenho e carga esperados.
    * O aplicativo precisa atender mil usuários? ou um milhão?
    * Se o tráfego torne de um único local geográfico ou globalmente?
    * Existem variações sazonais? picos de tráfego?
    * Quão rápido o aplicativo deve responder? 1 segundo? 1 milissegundos?
3. Compreendendo e corretamente aproveitar a plataforma seu aplicativo de hospedagem.
    * Quais recursos devem utilizar para atingir metas meu escala?

Esta seção ajudará você a entender todos os fatores e ajuda você planejar uma estratégia que leva aproveitar os recursos de serviço de aplicativo necessários para atingir suas metas de escalabilidade.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]
