<properties 
    pageTitle="Como usar propriedades em políticas de gerenciamento de API do Azure" 
    description="Saiba como usar propriedades em políticas de gerenciamento de API do Azure." 
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


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Como usar propriedades em políticas de gerenciamento de API do Azure

Políticas de gerenciamento de API são um poderoso recurso do sistema que permitem que o publisher alterar o comportamento da API por meio de configuração. Políticas são um conjunto de instruções que são executadas sequencialmente na solicitação ou resposta de uma API. Instruções de política podem ser construídas usando valores de texto literal, expressões de política e propriedades. 

Cada instância do serviço de gerenciamento de API tem uma coleção de propriedades de pares de chave/valor que são globais para a instância do serviço. Essas propriedades podem ser usadas para gerenciar os valores de cadeia de caracteres constante em toda a configuração de API e políticas. Cada propriedade tem os seguintes atributos.


| Atributo | Tipo            | Descrição                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Nome      | cadeia de caracteres          | O nome da propriedade. Ele pode conter apenas letras, dígitos, período, traço e sublinhados. |
| Valor     | cadeia de caracteres          | O valor da propriedade. Ele não pode estar vazio ou consistir somente de espaço em branco.                           |
| Segredo    | booliano         | Determina se o valor é um segredo e deve ser criptografado ou não.                                |
| Marcas      | matriz de cadeia de caracteres | Opcional tags que, quando fornecido pode ser usado para filtrar a lista de propriedade.                               |

Propriedades são configuradas no portal do publisher na guia **Propriedades** . No exemplo a seguir, três propriedades são configuradas.

![Propriedades][api-management-properties]

Valores de propriedade podem conter cadeias de caracteres literais e [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx). A tabela a seguir mostra as propriedades de três amostra anterior e seus atributos. O valor de `ExpressionProperty` é uma expressão de política que retorna uma cadeia de caracteres que contém a data e hora atuais. A propriedade `ContosoHeaderValue` está marcado como um segredo, para que seu valor não é exibido.

| Nome               | Valor                      | Segredo | Marcas    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | FALSO  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | True   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | FALSO  |         |

## <a name="to-use-a-property"></a>Usar uma propriedade

Para usar uma propriedade em uma política, coloque o nome da propriedade dentro de um duplo par de chaves como `{{ContosoHeader}}`, conforme mostrado no exemplo a seguir.

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma `set-header` política, e `ContosoHeaderValue` é usado como o valor dessa cabeçalho. Quando esta política é avaliada durante uma solicitação ou resposta para o gateway de gerenciamento de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídos por seus valores de propriedade respectivos.

Propriedades podem ser usadas como atributo concluído ou valores de elemento conforme mostrado no exemplo anterior, mas eles também podem ser inseridos ou combinados com parte de uma expressão de texto literal, conforme mostrado no exemplo a seguir:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Propriedades também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é usado.

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

Quando esta política é avaliada, `{{ExpressionProperty}}` é substituído pelo valor: `@(DateTime.Now.ToString())`. Como o valor for uma expressão de política, a expressão será avaliada e a política prossegue com sua execução.

Você pode testar este out no portal do desenvolvedor chamando uma operação que tenha uma política com propriedades no escopo. No exemplo a seguir, uma operação é chamada com o exemplo anterior dois `set-header` políticas com propriedades. Observe que a resposta contém dois cabeçalhos personalizados que foram configurados usando políticas com propriedades.

![Portal do desenvolvedor][api-management-send-results]

Se você examinar o [rastreamento do Inspetor de API](api-management-howto-api-inspector.md) para uma chamada que inclui as duas diretivas de amostra anterior com propriedades, você pode ver os dois `set-header` políticas com os valores de propriedade inseridos, além da avaliação de expressão de política para a propriedade que continha a expressão de política.

![Rastreamento de API Inspetor][api-management-api-inspector-trace]

Observe que, enquanto valores de propriedade podem conter expressões de política, valores de propriedade não podem conter outras propriedades. Se o texto que contém uma referência de propriedade é usado para um valor de propriedade, tais como `Property value text {{MyProperty}}`, essa referência de propriedade não será substituída e será incluída como parte do valor da propriedade.

## <a name="to-create-a-property"></a>Para criar uma propriedade

Para criar uma propriedade, clique em **Adicionar propriedade** na guia **Propriedades** .

![Adicionar propriedade][api-management-properties-add-property-menu]

**Nome** e **valor** são valores necessários. Se este valor da propriedade é um segredo, marque a caixa de seleção **Este é um segredo** . Insira uma ou mais marcas opcionais para ajudar a organizar suas propriedades e clique em **Salvar**.

![Adicionar propriedade][api-management-properties-add-property]

Quando uma nova propriedade é salva, a caixa de texto de **propriedade de pesquisa** é preenchida com o nome da nova propriedade e a nova propriedade é exibida. Para exibir todas as propriedades, desmarque a caixa de texto de **propriedade de pesquisa** e pressione enter.

![Propriedades][api-management-properties-property-saved]

Para obter informações sobre como criar uma propriedade usando a API REST, consulte [criar uma propriedade usando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Para editar uma propriedade

Para editar uma propriedade, clique em **Editar** ao lado da propriedade para editar.

![Editar propriedade][api-management-properties-edit]

Faça as alterações desejadas e clique em **Salvar**. Se você alterar o nome da propriedade, qualquer políticas que fazem referência a essa propriedade são atualizadas automaticamente para usar o novo nome.

![Editar propriedade][api-management-properties-edit-property]

Para obter informações sobre como editar uma propriedade usando a API REST, consulte [Editar uma propriedade usando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Para excluir uma propriedade

Para excluir uma propriedade, clique em **Excluir** ao lado da propriedade excluir.

![Excluir propriedade][api-management-properties-delete]

Clique em **Sim, excluí-la** para confirmar.

![Confirmar a exclusão][api-management-delete-confirm]

>[AZURE.IMPORTANT] Se a propriedade é referenciada por qualquer políticas, não será possível excluí-la com êxito até que você remova a propriedade de todas as diretivas que usá-lo.

Para obter informações sobre como excluir uma propriedade usando a API REST, consulte [Excluir uma propriedade usando a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Pesquisa e filtro de propriedades

Na guia **Propriedades** inclui pesquisando e recursos para ajudá-lo a gerenciar suas propriedades de filtragem. Para filtrar a lista de propriedade por nome da propriedade, insira um termo de pesquisa na caixa de texto **a propriedade de pesquisa** . Para exibir todas as propriedades, desmarque a caixa de texto de **propriedade de pesquisa** e pressione enter.

![Pesquisa][api-management-properties-search]

Para filtrar a lista de propriedade por valores de marca, insira uma ou mais marcas a caixa de texto de **filtro por marcas** . Para exibir todas as propriedades, desmarque a caixa de texto **Filtrar por marcas** e pressione enter.

![Filtro][api-management-properties-filter]

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre como trabalhar com políticas
    -   [Políticas de gerenciamento de API](api-management-howto-policies.md)
    -   [Referência de política](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [Expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Assista a uma visão geral em vídeo

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

