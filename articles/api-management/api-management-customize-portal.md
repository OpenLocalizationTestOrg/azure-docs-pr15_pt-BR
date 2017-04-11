<properties
    pageTitle="Personalizar o portal do desenvolvedor do gerenciamento de API do Azure | Microsoft Azure"
    description="Saiba como personalizar o portal do desenvolvedor do gerenciamento de API do Azure."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="customize-the-developer-portal-in-azure-api-management"></a>Personalizar o portal do desenvolvedor do gerenciamento de API do Azure

Este guia mostra como modificar a aparência do portal do desenvolvedor do gerenciamento de API do Azure para consistência com sua marca.

## <a name="change-page-headers"> </a>Alterar o texto ou o logotipo no cabeçalho da página

Um dos principais aspectos de personalização portal está substituindo o texto na parte superior de todas as páginas com seu nome de empresa ou logotipo.

Conteúdo dentro do portal do desenvolvedor é modificado por meio do portal do publisher, que é acessado por meio do Portal de clássico do Azure. Para acessar o portal de API do publisher, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

Portal do desenvolvedor do baseia-se em um sistema de gerenciamento de conteúdo ou CMS. O cabeçalho que aparece em cada página é um tipo especial de conteúdo conhecido como um widget. Para editar o conteúdo de que widget, clique **Widgets** do menu **Portal do desenvolvedor** à esquerda e, em seguida, selecione o widget do **cabeçalho** da lista.

![Cabeçalho de widgets][api-management-widgets-header]

O conteúdo do cabeçalho é editável de dentro do campo de **corpo** . Alterar o texto para "Portal do desenvolvedor Fabrikam" e clique em **Salvar** na parte inferior da página.

Agora você deve ser capaz de ver o novo cabeçalho em cada página dentro do portal do desenvolvedor.

> Para abrir o portal do desenvolvedor enquanto no portal do publisher, clique em **portal do desenvolvedor** na barra superior.

## <a name="change-headers-styling"> </a>Alterar o estilo dos cabeçalhos

As cores, fontes, tamanhos, espaços e outros elementos relacionados a estilo de qualquer página do portal são definidos pelas regras de estilo. Para editar os estilos, clique em **aparência** , no menu do **portal do desenvolvedor** no portal do publisher e clique em **começar a personalização** para habilitar o editor de estilo.

Seu navegador alterna para uma página oculta dentro do portal do desenvolvedor que contém exemplos de conteúdo, com exemplos para todas as regras de estilo usados em qualquer lugar no site. Para abrir o editor de estilos, mova o cursor sobre a linha vertical cinza fina na parte mais à esquerda da página. Barra de ferramentas editor deverá aparecer.

![Barra de ferramentas de personalização][api-management-customization-toolbar]

Há dois modos principais de edição de regras de estilo - **Editar todas as regras** exibe uma lista de todas as regras de estilo usado em qualquer lugar, enquanto **Escolha elemento** permite que você selecione um elemento de página em que você está e estilos somente para esse elemento.

Nesta seção, queremos alterar o estilo de apenas os cabeçalhos. Clique na opção de **Escolha elemento** da barra de ferramentas do editor de estilos e clique em **Selecionar um elemento para personalizar**. Elementos agora ficam realçados quando você focaliza-los com o mouse para indicar estilos do qual elemento Comece editando se você clicou. Passe o mouse sobre o texto que representa o nome da empresa no cabeçalho ("Fabrikam Developer Portal" Se você seguiu as instruções na seção anterior) e clique nele. Um conjunto de regras de estilo nomeados e categorizados aparece dentro do editor de estilo.

Cada regra representa uma propriedade de estilo do elemento selecionado. Por exemplo, para o texto de cabeçalho selecionado acima, o tamanho do texto está em @font-size-h1 enquanto o nome da fonte com alternativas está em @headings-font-family.

> Se você estiver familiarizado com [inicialização][], essas regras são de fato [menos variáveis][] dentro do tema de inicialização usado pelo portal do desenvolvedor.

Vamos alterar a cor do texto do título. Selecione a entrada na **@headings-color** campo e digite **#000000**. Este é o código hexadecimal para a cor preta. Como fazer isso, você vê que um indicador de quadrado de cor aparece no final da caixa de texto. Se você clicar nesse indicador, um seletor de cores permite que você para escolher uma cor.

![Seletor de cores][api-management-customization-toolbar-color-picker]

Quando terminar de fazer alterações nos estilos do elemento selecionado, clique em **Visualizar alterações** para ver os resultados na tela. No momento, eles são visíveis somente para administradores. Para fazer essas alterações visível para todos, clique no botão **Publicar** no editor de estilos e confirme as alterações.

![Menu Publicar][api-management-customization-toolbar-publish-form]

> Para alterar as regras de estilo que se aplicam a qualquer outro elemento na página, siga o mesmo procedimento que você fez para o cabeçalho. Clique em **Escolher um elemento** do editor de estilos, selecione o elemento que você está interessado e começar a modificar os valores das regras de estilo exibidas na tela.

## <a name="edit-page-contents"> </a>Editar o conteúdo de uma página

Portal do desenvolvedor do consiste em páginas geradas automaticamente como APIs, produtos, aplicativos, problemas e conteúdo gravado manualmente. Como é baseado em um sistema de gerenciamento de conteúdo, você pode criar tal conteúdo conforme necessário.

Para ver a lista de todas as páginas de conteúdo existentes, clique em **conteúdo** no menu do **portal do desenvolvedor** no portal do publisher.

![Gerenciar conteúdo][api-management-customization-manage-content]

Clique na página de **boas-vindas** para editar o que é exibido na home page do portal do desenvolvedor. Faça as alterações que você deseja, visualizá-las, se necessário e, em seguida, clique em **Publicar agora** para torná-las visíveis para todos.

> A home page usa um layout especial que permite que ela exibir uma faixa na parte superior. Essa manchete não é editável da seção de **conteúdo** . Para editar esta faixa, clique **Widgets** do menu **portal do desenvolvedor** , selecione **página inicial** na lista suspensa de **Camada atual** e, em seguida, abra o item de **faixa** sob a **seção em destaque**. O conteúdo deste widget é editável como qualquer outra página.

## <a name="next-steps"> </a>Próximos passos

-   Saiba como personalizar o conteúdo de páginas de portal do desenvolvedor usando [modelos de portal do desenvolvedor](api-management-developer-portal-templates.md).

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[inicialização]: http://getbootstrap.com/
[MENOS variáveis]: http://getbootstrap.com/css/
