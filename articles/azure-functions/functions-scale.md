<properties
   pageTitle="Como escalar funções Azure | Microsoft Azure"
   description="Compreenda como as funções do Azure dimensionar para atender às necessidades dos suas cargas de trabalho controlada por evento."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# <a name="how-to-scale-azure-functions"></a>Como escalar funções Azure

## <a name="introduction"></a>Introdução

Uma vantagem de funções do Azure é que os recursos de computação são consumidos somente quando necessário. Isso significa que você não pagar para VMs ociosas ou tiver que reservar capacidade para quando você pode precisar. Em vez disso, a plataforma aloca computação power quando seu código está em execução, dimensionamento conforme necessário para lidar com a carga e então escalas quando o código não está sendo executado.

O mecanismo para esse novo recurso é o plano de serviço dinâmico.  

Este artigo fornece uma visão geral de como funciona o plano de serviço dinâmico e como a plataforma escalas sob demanda para executar seu código.

Se você ainda não estiver familiarizado com funções do Azure, verifique o artigo de [Visão geral de funções do Azure](functions-overview.md) para entender melhor seus recursos.

## <a name="configure-azure-functions"></a>Configurar funções Azure

Duas configurações principais estão relacionadas ao dimensionamento:

* [Plano de serviço de aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ou dinâmico plano de serviço
* Tamanho da memória para o ambiente de execução

O custo de uma função altera dependendo do plano de serviço que você selecionar. Com um plano de serviço dinâmico, o custo é uma função de tempo de execução, tamanho de memória e número de execuções. Encargos acumulam somente quando seu código está sendo executado.

Um plano de serviço de aplicativo hospeda suas funções em VMs existentes, que também podem ser usadas para executar outro código. Depois de você pagar essas VMs cada mês, não há nenhum custo adicional para funções de execução neles.

## <a name="choose-a-service-plan"></a>Escolha um plano de serviço

Quando você cria funções, você pode selecionar para executá-las em um plano de serviço dinâmico ou um [plano de serviço de aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
No plano de serviço de aplicativos, suas funções serão executado em uma máquina virtual dedicada, como o trabalho de aplicativos web hoje para Basic, padrão ou SKUs Premium.
Esta máquina virtual dedicado alocado para seus aplicativos e funções e sempre estará disponível se está sendo ativamente executado código ou não. Isso é uma boa opção se você tiver VMs existentes, em utilizados que já estejam executando o outro código ou se você pretende executar funções continuamente ou quase continuamente. Uma máquina virtual separa custo do tamanho runtime e memória. Como resultado, você pode limitar o custo de muitas funções de execução longa no custo de um ou mais VMs que eles são executados no.

[AZURE.INCLUDE [Dynamic Service plan](../../includes/functions-dynamic-service-plan.md)]
