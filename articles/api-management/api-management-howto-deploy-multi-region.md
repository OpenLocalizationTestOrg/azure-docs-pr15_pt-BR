<properties
    pageTitle="Como implantar uma instância de serviço de gerenciamento de API do Azure para várias regiões Azure"
    description="Saiba como implantar uma instância de serviço de gerenciamento de API do Azure para várias regiões Azure." 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Como implantar uma instância de serviço de gerenciamento de API do Azure para várias regiões Azure

Gerenciamento de API oferece suporte a implantação de várias regiões que permite que fornecedores de API distribuir um único serviço de gerenciamento de API em qualquer número de regiões Azure desejados. Isso ajuda a reduzir a solicitação de latência detectada pelo distribuído geograficamente consumidores de API e também melhora a disponibilidade do serviço se uma região ficar offline. 

Quando um serviço de gerenciamento de API é criado inicialmente, ele contém apenas uma [unidade][] e reside em uma única região Azure, que é designada como a região principal. Regiões adicionais podem ser facilmente adicionados por meio do Portal de clássico do Azure. Servidor de gerenciamento de API de gateway é implantado para cada região e o tráfego de chamada será roteado para o gateway mais próximo. Se uma região ficar offline, o tráfego é automaticamente redirecionado para o próximo gateway mais próximo. 

> [AZURE.IMPORTANT] Implantação de várias regiões só está disponível no nível **[Premium][]** .

## <a name="add-region"> </a>Implantar uma instância de serviço de gerenciamento de API para uma nova região

Para começar, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Isso o leva para o portal de fornecedor de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

Navegue até a guia de **escala** no Portal de clássico do Azure para a sua instância de serviço de gerenciamento de API. 

![Guia escala][api-management-scale-service]

Para implantar uma nova região, clique na lista suspensa abaixo da região principal e escolha uma região na lista.

![Adicionar região][api-management-add-region]

Quando a região estiver selecionada, escolha o número de unidades para a nova região na lista suspensa de unidade.

![Especifique as unidades][api-management-select-units]

Quando as regiões desejados e unidades estão configuradas, clique em **Salvar**.

## <a name="remove-region"> </a>Excluir uma instância de serviço de gerenciamento de API de uma região

Para remover uma instância do serviço de gerenciamento de API de uma região, navegue até a guia de **escala** no Portal de clássico do Azure para a sua instância de serviço de gerenciamento de API. 

![Guia escala][api-management-scale-service]

Clique no **X** à direita da região desejada para remover.  

![Remover região][api-management-remove-region]

Depois que as regiões desejadas são removidas, clique em **Salvar**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance
[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unidade]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

