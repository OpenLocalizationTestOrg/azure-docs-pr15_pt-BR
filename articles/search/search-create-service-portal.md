<properties
    pageTitle="Criar um serviço de pesquisa do Azure usando o Portal do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Saiba como provisionar um serviço de pesquisa do Azure usando o Portal do Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Criar um serviço de pesquisa do Azure usando o Portal do Azure

Este guia o orientará durante o processo de criação (ou provisionamento) um serviço de pesquisa do Azure usando o [Portal do Azure](https://portal.azure.com/).

Este guia pressupõe que você já tiver uma assinatura do Azure e pode efetuar logon no Portal do Azure.

## <a name="find-azure-search-in-the-azure-portal"></a>Localizar pesquisa Azure no Portal do Azure
1. Acesse o [Portal do Azure](https://portal.azure.com/) e faça logon.
1. Clique no sinal de mais ("+") no canto superior esquerdo.
2. Selecione **dados + armazenamento**.
3. Selecione **Pesquisar do Azure**.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>Selecione um nome de serviço e o ponto de extremidade de URL para seu serviço
1. O nome do serviço será parte da URL do ponto de extremidade do seu serviço de pesquisa do Azure em relação à qual você fará suas chamadas de API para gerenciar e usar o serviço de pesquisa.
2. Digite seu nome de serviço no campo **URL** . O nome do serviço:
  * deve conter apenas letras minúsculas, dígitos ou traços ("-")
  * não é possível usar um traço ("-") como o último caractere único ou 2 primeiros caracteres
  * não pode conter traços consecutivos ("-")
  * está limitado entre 2 e 60 caracteres


## <a name="select-a-subscription-where-you-will-keep-your-service"></a>Selecione uma assinatura onde você manterá seu serviço
Se você tiver mais de uma assinatura, você pode selecionar qual incluirá esse serviço de pesquisa do Azure.

## <a name="select-a-resource-group-for-your-service"></a>Selecione um grupo de recursos para o seu serviço
Crie um novo grupo de recursos ou selecione uma existente. Um grupo de recursos é uma coleção de serviços do Azure e recursos que são usados juntos. Por exemplo, se você estiver usando a pesquisa do Azure para indexar um banco de dados do SQL, dois desses serviços devem ser parte do mesmo grupo de recursos.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>Selecione o local onde seu serviço será hospedado
Como um serviço Azure, Azure pesquisa está disponível para ser hospedados em data centers em todo o mundo. Observe que [os preços podem diferir](https://azure.microsoft.com/pricing/details/search/) por geografia.

## <a name="select-your-pricing-tier"></a>Selecione sua camada de preços
[Pesquisa do Azure atualmente é oferecida em vários níveis de preços](https://azure.microsoft.com/pricing/details/search/): livre, Basic ou padrão. Cada nível tem seu próprio [capacidade e limites](search-limits-quotas-capacity.md). Consulte [Escolha um nível de preços ou SKU](search-sku-tier.md) para obter orientação.

Nesse caso, estamos escolheu a camada padrão do nosso serviço.

## <a name="select-the-create-button-to-provision-your-service"></a>Selecione o botão "Criar" para provisionar seu serviço

![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>Dimensionar seu serviço

Depois que o serviço é fornecido, você poderá dimensioná-lo para atender suas necessidades. Se você escolheu a camada padrão para o serviço de pesquisa do Azure, você pode dimensionar seu serviço em duas dimensões: réplicas e partições. Se você escolheu a camada básica, você só pode adicionar réplicas.

*__As partições__* permitem seu serviço armazenar e pesquisar mais documentos.

*__Réplicas__* permitem que o seu serviço lidar com uma carga maior de consultas de pesquisa - [um serviço requer 2 réplicas alcançar um SLA somente leitura e requer 3 réplicas alcançar um SLA de leitura/gravação](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Vá para blade de gerenciamento do seu serviço de pesquisa do Azure no Portal do Azure.
2. Na lâmina **configurações** , selecione **escala**.
3. Você pode dimensionar seu serviço adicionando réplicas ou partições.
  * Você não consegue dimensionar seu serviço passaram unidades de pesquisa 36. O número total de unidades de pesquisa é o produto de suas réplicas e partições (réplicas * partições = Total de unidades de pesquisa).
  * Se você escolheu a camada básica, você só pode chegar a 3 réplicas. Serviços básicos são vinculados a uma única partição.

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>Próximo
Após a configuração de um serviço de pesquisa do Azure, você estará pronto para [definir um índice de pesquisa do Azure](search-what-is-an-index.md) para que possa carregar e pesquisar seus dados.

Consulte [Introdução ao Azure pesquisa no portal](search-get-started-portal.md) para um tutorial rápido.
