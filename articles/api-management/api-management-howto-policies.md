<properties 
    pageTitle="Políticas de gerenciamento de API Azure | Microsoft Azure" 
    description="Aprenda a criar, editar e configurar políticas de gerenciamento de API." 
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


#<a name="policies-in-azure-api-management"></a>Políticas de gerenciamento de API do Azure

Em gerenciamento de API do Azure, políticas são um poderoso recurso do sistema que permitem que o publisher alterar o comportamento da API por meio de configuração. Políticas são um conjunto de instruções que são executadas sequencialmente na solicitação ou resposta de uma API. Instruções populares incluem conversão de formato de XML para JSON e taxa limitando para restringir a quantidade de chamadas de entrada de um desenvolvedor de chamadas. Muitas outras diretivas estão disponíveis prontos para uso.

Consulte a [Referência de política][] para uma lista completa de declarações de política e suas configurações.

Políticas são aplicadas dentro do gateway que fica entre o consumidor de API e a API gerenciada. O gateway recebe todas as solicitações e geralmente encaminha inalterada à API subjacente. No entanto uma política pode aplicar alterações para a solicitação de entrada e a resposta de saída.

Expressões de política podem ser usadas como valores de atributo ou valores de texto em qualquer uma das políticas de gerenciamento de API, a menos que a política especifica o contrário. Algumas políticas como as políticas de [fluxo de controle][] e [Defina variável][] são baseadas em expressões de política. Para obter mais informações, consulte [avançada políticas][] e [expressões de política][].

## <a name="scopes"> </a>Como configurar políticas
Diretivas podem ser configuradas globalmente ou no escopo de um [produto][], [API][] ou [operação][]. Para configurar uma política, navegue até o editor de diretivas no portal do publisher.

![Menu de políticas][policies-menu]

O editor de políticas consiste em três seções principais: o escopo de política (superior), a definição de política onde as diretivas são editadas (à esquerda) e as instruções de lista (à direita):

![Editor de políticas][policies-editor]

Para começar a configurar uma política que você deve primeiro selecionar o escopo no qual a política deve ser aplicada. Captura de tela abaixo a **Starter** produto está selecionado. Observe que o símbolo de quadrado ao lado do nome de política indica que uma política já é aplicada nesse nível.

![Escopo][policies-scope]

Como uma política já foi aplicada, a configuração é mostrada no modo de exibição definição.

![Configurar][policies-configure]

A política é exibida somente leitura a primeira. Para editar a definição clique na ação de **Política de configurar** .

![Editar][policies-edit]

A definição de política é um documento XML simples que descreve uma sequência de instruções de entrada e saídas. O XML pode ser editado diretamente na janela de definição. É fornecida uma lista de instruções para a direita e instruções aplicáveis ao escopo atual são habilitadas e realçadas; Como demonstrou pela instrução **A taxa de chamada de limite** na captura de tela acima.

Clicando em uma instrução enabled adicionará o XML adequado no local do cursor no modo de exibição definição. 

>[AZURE.NOTE] Se a política que você deseja adicionar não estiver habilitada, certifique-se de que você está no escopo correto para essa política. Cada instrução de política foi projetada para uso em determinados escopos e seções de política. Para revisar as seções de política e escopos para uma política, consulte a seção de **uso** para essa política na [Referência de política][].

Uma lista completa de declarações de política e suas configurações estão disponíveis na [Referência de política][].

Por exemplo, para adicionar uma nova instrução para restringir solicitações de entrada para endereços IP especificados, coloque o cursor apenas dentro do conteúdo do `inbound` elemento XML e clique na instrução de **chamador de restringir IPs** .

![Políticas de restrição][policies-restrict]

Isso adicionará um trecho XML para o `inbound` elemento que fornece orientações sobre como configurar a instrução.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Para limitar as solicitações de entrada e aceitar que somente aquelas de um endereço IP de 1.2.3.4 modificar o XML da seguinte maneira:

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Salvar][policies-save]

Quando concluir Configurando as instruções para a política, clique em **Salvar** e as alterações serão propagadas para o gateway de gerenciamento de API imediatamente.

##<a name="sections"> </a>Configuração de política de Noções básicas sobre

Uma política é uma série de instruções executadas na ordem para uma solicitação e resposta. A configuração é dividida adequadamente `inbound`, `backend`, `outbound`, e `on-error` seções conforme mostrado na configuração abaixo.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

Se houver um erro durante o processamento de uma solicitação, as etapas restantes na `inbound`, `backend`, ou `outbound` seções são ignoradas e a execução vai para as instruções na `on-error` seção. Colocando instruções de política na `on-error` seção você pode revisar o erro usando o `context.LastError` propriedade, inspecionar e personalizar a resposta de erro usando o `set-body` política e configurar o que acontece se ocorrer um erro. Há códigos de erro para etapas internas e erros que podem ocorrer durante o processamento de declarações de política. Para obter mais informações, consulte [tratamento de erro em políticas de gerenciamento de API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Como políticas podem ser especificadas em diferentes níveis (global, produto, api e operação) a configuração fornece uma maneira para especificar a ordem na qual as instruções da definição política executadas relacionadas com a política pai. 

Escopos de política são avaliados na seguinte ordem.

1. Escopo global
2. Escopo do produto
3. Escopo de API
4. Escopo de operação

As declarações dentro deles são avaliadas de acordo com o posicionamento do `base` elemento, se ele estiver presente.

Por exemplo, se você tiver uma política no nível global e uma diretiva configurada para uma API, em seguida, sempre que essa determinada API é usado ambas as diretivas serão aplicadas. Gerenciamento de API permite a ordenação determinante de instruções de política combinado através do elemento base. 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

Na definição de política de exemplo acima, o `cross-domain` instrução será executado antes de ser seguido de quaisquer políticas maiores que por sua vez, a `find-and-replace` política.

Se a mesma diretiva aparecer duas vezes na declaração da política, a política avaliada mais recentemente será aplicada. Você pode usar isso para substituir políticas que são definidas em um escopo superior. Para ver as políticas no escopo atual no editor de política, clique em **Recalcular política efetiva para escopo selecionado**.

Observe que a política global não tem nenhuma política de pai e usando o `<base>` elemento nele não tem efeito. 

## <a name="next-steps"></a>Próximas etapas

Confira seguindo o vídeo em expressões de política.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Referência de política]: api-management-policy-reference.md
[Produto]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operação]: api-management-howto-add-operations.md

[Diretivas avançadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Fluxo de controle]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Definir variável]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expressões de política]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
