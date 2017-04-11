<properties 
    pageTitle="Como personalizar o portal do desenvolvedor do gerenciamento de API do Azure usando modelos | Microsoft Azure" 
    description="Saiba como personalizar o portal do desenvolvedor do gerenciamento de API do Azure usando modelos." 
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


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Como personalizar o portal do desenvolvedor do gerenciamento de API do Azure usando modelos

Gerenciamento de API Azure fornece vários recursos de personalização para permitir que os administradores para [Personalizar a aparência do portal do desenvolvedor](api-management-customize-portal.md), bem como personalizar o conteúdo das páginas de portal do desenvolvedor usando um conjunto de modelos que configurar o conteúdo das páginas de si mesmo. Usando sintaxe de [DotLiquid](http://dotliquidmarkup.org/) e um conjunto fornecido de recursos de cadeia de caracteres localizada, ícones e controles de página, você tem grande flexibilidade para configurar o conteúdo das páginas ajustá-la usando esses modelos.

## <a name="developer-portal-templates-overview"></a>Visão geral de modelos de portal do desenvolvedor

Modelos de portal do desenvolvedor são gerenciados no portal do desenvolvedor por administradores da instância do serviço de gerenciamento de API. Para gerenciar modelos de desenvolvedor, navegue até sua instância de serviço de gerenciamento de API no Portal de clássico do Azure e clique em **Procurar**.

![Portal do desenvolvedor][api-management-browse]

Se você já estiver no portal do publisher, você pode acessar o portal do desenvolvedor clicando em **portal do desenvolvedor**.

![Menu de portal do desenvolvedor][api-management-developer-portal-menu]

Para acessar os modelos de portal do desenvolvedor, clique no ícone de personalizar à esquerda para exibir o menu de personalização e clique em **modelos**.

![Modelos de portal do desenvolvedor][api-management-customize-menu]

A lista de modelos exibe várias categorias de modelos que aborda as páginas diferentes no portal do desenvolvedor. Cada modelo é diferente, mas as etapas para editá-los e publicar as alterações são as mesmas. Para editar um modelo, clique no nome do modelo.

![Modelos de portal do desenvolvedor][api-management-templates-menu]

Clicando em um modelo leva você para a página de portal do desenvolvedor que é personalizável pelo modelo. Neste exemplo, a **lista de produtos** do modelo é exibido. O modelo de **lista de produtos** controla a área da tela indicada pelo retângulo vermelho. 

![Modelo de lista de produtos][api-management-developer-portal-templates-overview]

Alguns modelos, como os modelos de **Perfil de usuário** , personalizar diferentes partes da mesma página. 

![Modelos de perfil de usuário][api-management-user-profile-templates]

O editor para cada modelo de portal do desenvolvedor tem duas seções exibidas na parte inferior da página. Do lado esquerdo exibe o painel de edição para o modelo e à direita exibe o modelo de dados para o modelo. 

O modelo de painel de edição contém a marcação que controla a aparência e o comportamento da página correspondente no portal do desenvolvedor. A marcação no modelo usa a sintaxe [DotLiquid](http://dotliquidmarkup.org/) . Um editor popular para DotLiquid é [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Quaisquer alterações feitas no modelo durante a edição são exibidas em tempo real no navegador, mas não estão visíveis aos seus clientes até você [Salvar](#to-save-a-template) e [Publicar](#to-publish-a-template) o modelo.

![Marcação de modelo][api-management-template]

O painel de **dados de modelo** fornece um guia para o modelo de dados para as entidades que estão disponíveis para uso em um modelo específico. Ele fornece este guia exibindo os dados dinâmicos que são exibidos no momento no portal do desenvolvedor. Você pode expandir os painéis de modelo clicando no retângulo no canto superior direito do painel de **dados de modelo** .

![Modelo de dados de modelo][api-management-template-data]

No exemplo anterior, há dois produtos exibidos no portal do desenvolvedor que foram recuperados dos dados exibidos no painel de **dados de modelo** , conforme mostrado no exemplo a seguir.

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

A marcação no modelo de **lista de produtos** processa os dados para fornecer a saída desejada pela iteração através da coleção de produtos para exibir informações e um link para cada produto individual. Observação a `<search-control>` e `<page-control>` elementos na marcação. Esses controlam a exibição da pesquisa e paginação controles na página. `ProductsStrings|PageTitleProducts`é uma referência de cadeia de caracteres localizada que contém o `h2` texto do cabeçalho da página. Para obter uma lista de cadeia de recursos, controles de página e ícones disponíveis para uso em modelos de portal do desenvolvedor, consulte [referência de modelos de portal do desenvolvedor de gerenciamento de API](https://msdn.microsoft.com/library/azure/mt697540.aspx).

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>Para salvar um modelo

Para salvar um modelo, clique em Salvar no editor de modelo.

![Salvar modelo][api-management-save-template]

Alterações salvas não são ao vivo no portal do desenvolvedor até serem publicadas.

## <a name="to-publish-a-template"></a>Para publicar um modelo

Salvos modelos podem ser publicados individualmente, ou todos juntos. Para publicar um modelo individual, clique em Publicar no editor de modelo.

![Modelo de publicação][api-management-publish-template]

Clique em **Sim** para confirmar e tornar o modelo ao vivo no portal do desenvolvedor.

![Confirmar publicar][api-management-publish-template-confirm]

Para publicar todas as versões de modelo atualmente não publicados, clique em **Publicar** na lista de modelos. Modelos não publicados são designados por um asterisco após o nome do modelo. Neste exemplo, os modelos de **lista de produtos** e **produto** estão sendo publicados.

![Publicar modelos][api-management-publish-templates]

Clique em **publicar personalizações** para confirmar.

![Confirmar publicar][api-management-publish-customizations]

Modelos publicados recentemente são eficientes imediatamente no portal do desenvolvedor.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para reverter um modelo para a versão anterior

Para reverter um modelo para a versão publicada anterior, clique em Reverter no editor de modelo.

![Reverter modelo][api-management-revert-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-revert-template-confirm]

A versão publicada anteriormente de um modelo é ao vivo no portal do desenvolvedor assim que a operação de reversão é concluída.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar um modelo para a versão padrão

Restaurando modelos para sua versão padrão é um processo de duas etapas. Primeiro os modelos devem ser restaurados e, em seguida, as versões restauradas devem ser publicadas.

Para restaurar um único modelo para a versão padrão, clique em Restaurar no editor de modelo.

![Reverter modelo][api-management-reset-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todos os modelos para suas versões do padrão, clique em **Restaurar modelos padrão** na lista modelo.

![Restaurar modelos][api-management-restore-templates]

Os modelos de restaurado, em seguida, devem ser publicados individualmente ou ao mesmo tempo, seguindo as etapas em [para publicar um modelo](#to-publish-a-template).

## <a name="developer-portal-templates-reference"></a>Referência de modelos de portal do desenvolvedor

Para obter informações de referência do desenvolvedor modelos de portal, recursos de cadeia de caracteres, ícones e controles de página, consulte [referência de modelos de portal do desenvolvedor de gerenciamento de API](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## <a name="watch-a-video-overview"></a>Assista a uma visão geral em vídeo

Assista a este vídeo para ver como adicionar um quadro de discussão e classificações às páginas API e operação no portal do desenvolvedor usando modelos.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







