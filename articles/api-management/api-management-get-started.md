<properties
    pageTitle="Gerenciar sua primeira API no gerenciamento de API do Azure | Microsoft Azure"
    description="Aprenda a criar APIs, adicionar operações e começar a usar o gerenciamento de API."
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
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="manage-your-first-api-in-azure-api-management"></a>Gerenciar sua primeira API no gerenciamento de API do Azure

## <a name="overview"> </a>Visão geral

Este guia mostra como começar a usar o gerenciamento de API do Azure e fazer sua primeira chamada API rapidamente.

## <a name="concepts"> </a>o que é gerenciamento de API do Azure?

Você pode usar o gerenciamento de API do Azure para fazer qualquer back-end e iniciar um programa de API completo com base nele.

Cenários comuns incluem:

* **Protegendo móvel infraestrutura** por gate access com chaves de API, impedindo DOS ataques usando a limitação ou usando as políticas de segurança avançada como validação de token de JWT.
* **Habilitando ISV ecossistemas de parceiro** oferecendo integração de parceiro rápida por meio do portal do desenvolvedor e criando uma fachada API desassociar de implementações internas que não estão pronta para consumo de parceiro.
* **Executando um programa de API interno** , oferecendo um local centralizado para a organização para se comunicar sobre a disponibilidade e as alterações mais recentes para APIs, gate acesso baseado em contas organizacionais, todos baseados em um canal seguro entre o gateway de API e back-end.


O sistema é composto dos seguintes componentes:

* O **gateway API** é o ponto de extremidade que:
  * Aceita API chama e direciona-os para seu back-ends.
  * Verifica API chaves, JWT tokens, certificados e outras credenciais.
  * Impõe as cotas de uso e limites de taxa.
  * Transforma sua API dinamicamente sem modificações no código.
  * Armazena as respostas de back-end onde configurar.
  * Logs de chamadas metadados para fins de análise.

* O **portal do publisher** é a interface administrativa onde você configura seu programa de API. Use-o para:
    * Definir ou Importar esquema API.
    * Pacote APIs em produtos.
    * Configure as políticas como cotas ou transformações sobre as APIs.
    * Obter informações da análise.
    * Gerencie usuários.

* O **portal do desenvolvedor** serve como a presença de web principal para desenvolvedores, onde eles podem:
    * Documentação da API de leitura.
    * Experimente uma API por meio do console interativo.
    * Criar uma conta e inscrever-se para obter chaves de API.
    * Análise de acesso no seu próprio uso.


## <a name="create-service-instance"> </a>Criar uma instância de gerenciamento de API

>[AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, você pode criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].

A primeira etapa em trabalhar com gerenciamento de API é criar uma instância do serviço. Entre no [Portal clássico do Azure][] e clique em **novo**, **Os serviços de aplicativo**, **Gerenciamento de API**, **criar**.

![Nova instância do gerenciamento de API][api-management-create-instance-menu]

Para **URL**, especifique um nome exclusivo subdomínio para usar para a URL do serviço.

Escolha a **assinatura** e **região** desejada para sua instância de serviço. Depois de fazer suas seleções, clique no botão **Avançar** .

![Novo serviço de gerenciamento de API][api-management-create-instance-step1]

Insira **Contoso Ltda.** para o **Nome da organização**e insira seu endereço de email no campo **Email do administrador** .

>[AZURE.NOTE] Este endereço de email é usado para notificações do sistema de gerenciamento de API. Para obter mais informações, consulte [como configurar modelos de email no gerenciamento de API do Azure e notificações][].

![Novo serviço de gerenciamento de API][api-management-create-instance-step2]

Instâncias de serviço de gerenciamento de API estão disponíveis em três níveis: desenvolvedor, Standard e Premium. Por padrão, novas instâncias de serviço de gerenciamento de API são criadas no nível de desenvolvedor. Para selecionar a camada Standard ou Premium, marque a caixa de seleção **Configurações avançadas** e selecione o nível desejado na tela seguinte.

>[AZURE.NOTE] O nível de desenvolvedor é para desenvolvimento, teste e programas de API piloto onde alta disponibilidade não é um problema. Nos níveis padrão e Premium, você pode dimensionar a contagem de unidade reservadas para lidar com mais tráfego. As camadas Standard e Premium fornecem seu serviço de gerenciamento de API com mais capacidade de processamento e desempenho. Você pode concluir este tutorial usando qualquer nível. Para obter mais informações sobre níveis de gerenciamento de API, consulte [preços de gerenciamento de API][].

Clique na caixa de seleção para criar a instância de serviço.

![Novo serviço de gerenciamento de API][api-management-instance-created]

Depois que a instância do serviço é criada, a próxima etapa é criar ou importar uma API.

## <a name="create-api"> </a>Importar uma API

Uma API consiste em um conjunto de operações que podem ser chamados de um aplicativo cliente. Operações de API são proxies para serviços da web existentes.

APIs podem ser criados (e operações podem ser adicionadas) manualmente, ou eles podem ser importados. Neste tutorial, podemos importará a API para um serviço de web de Calculadora de exemplo fornecidos pela Microsoft e hospedado no Azure.

>[AZURE.NOTE] Para obter orientação sobre como criar uma API e adicionando operações manualmente, consulte [como criar APIs](api-management-howto-create-apis.md) e [como adicionar operações para uma API](api-management-howto-add-operations.md).

APIs são configurados no portal de fornecedor, que é acessado por meio do Portal de clássico do Azure. Para acessar o portal do publisher, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API.

![Portal do Publisher][api-management-management-console]

Para importar a Calculadora API, clique **APIs** no menu **Gerenciamento de API** à esquerda e, em seguida, clique em **Importar API**.

![Botão Importar API][api-management-import-api]

Execute as seguintes etapas para configurar a Calculadora API:

1. Clique **Na URL**, digite **http://calcapi.cloudapp.net/calcapi.json** na caixa de texto **URL do documento de especificação** e clique no botão de rádio **Swagger** .
2. Digite o **cálculo** na caixa de texto **sufixo de Web API URL** .
3. Clique na caixa de **produtos (opcionais)** e escolha **Starter**.
4. Clique em **Salvar** para importar a API.

![Adicionar nova API][api-management-import-new-api]

>[AZURE.NOTE] **Gerenciamento de API** atualmente oferece suporte a versão 1.2 e 2.0 do documento de Swagger para importação. Verifique se, apesar de [especificação Swagger 2.0](http://swagger.io/specification) declara que `host`, `basePath`, e `schemes` propriedades são opcionais, seu documento Swagger 2.0 **deve** conter essas propriedades; Caso contrário, ele não será importado. 

Depois que a API é importada, a página de resumo para a API é exibida no portal do publisher.

![Resumo de API][api-management-imported-api-summary]

A seção API tem várias guias. Na guia **Resumo** exibe informações sobre a API e métricas básicas. Na guia [configurações](api-management-howto-create-apis.md#configure-api-settings) é usada para exibir e editar a configuração para uma API. Guia [operações](api-management-howto-add-operations.md) é usada para gerenciar operações do API. Na guia **segurança** pode ser usada para configurar a autenticação de gateway para o servidor back-end usando autenticação básica ou a [autenticação de certificado comum](api-management-howto-mutual-certificates.md)e configurar a [autorização de usuário usando OAuth 2.0](api-management-howto-oauth2.md).  Guia **problemas** é usada para exibir problemas relatados pelos desenvolvedores que usam seu APIs. Na guia **produtos** é usada para configurar os produtos que contêm essa API.

Por padrão, cada instância de gerenciamento de API vem com dois produtos de amostra:

-   **Starter**
-   **Ilimitado**

Neste tutorial, a API de calculadora básica foi adicionada ao produto Starter quando a API foi importada.

Para fazer chamadas a uma API, os desenvolvedores devem primeiro assine um produto que oferece acesso a ele. Os desenvolvedores podem se inscrever para produtos no portal do desenvolvedor ou os administradores podem se inscrever para os desenvolvedores de produtos no portal do publisher. Você é um administrador, desde que você criou a instância de gerenciamento de API nas etapas anteriores no tutorial, para que você já está inscrito para cada produto por padrão.

## <a name="call-operation"> </a>Uma operação de chamadas do portal do desenvolvedor

Operações podem ser chamadas diretamente do portal do desenvolvedor, que fornece uma maneira conveniente para visualizar e testar as operações de uma API. Nesta etapa tutorial, você irá chamar operação de **Adicionar dois inteiros** da API de calculadora básica. Clique em **portal do desenvolvedor** do menu na parte superior direita do portal do publisher.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** do menu superior e clique em **Calculadora básica** para ver as operações disponíveis.

![Portal do desenvolvedor][api-management-developer-portal-calc-api]

Observe os parâmetros que foram importados juntamente com a API e operações, fornecendo a documentação para os desenvolvedores que usará essa operação e descrições de amostra. Essas descrições também podem ser adicionadas quando operações são adicionadas manualmente.

Para chamar a operação de **Adicionar dois inteiros** , clique em **experimentá-lo**.

![Experimente][api-management-developer-portal-calc-api-console]

Você pode inserir alguns valores para os parâmetros ou manter os padrões e clique em **Enviar**.

![HTTP Get][api-management-invoke-get]

Depois de uma operação é invocada, o portal do desenvolvedor exibe o **status de resposta**, os **cabeçalhos de resposta**e qualquer **conteúdo de resposta**.

![Resposta][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Exibir a análise

Para visualizar a análise de calculadora básica, alterne novamente para o portal do publisher selecionando **Gerenciar** no menu na parte superior direita do portal do desenvolvedor.

![Gerenciar][api-management-manage-menu]

O modo de exibição padrão para o portal de fornecedor é o **Painel de controle**, que fornece uma visão geral da sua instância de gerenciamento de API.

![Painel de controle][api-management-dashboard]

Passe o mouse sobre o gráfico para **Calculadora básica** ver as métricas específicas para o uso da API de um determinado período de tempo.

>[AZURE.NOTE] Se você não vir todas as linhas em seu gráfico, alterne para o portal do desenvolvedor e fazer algumas chamadas para a API, aguarde alguns minutos e, em seguida, volte para o painel.

Clique em **Exibir detalhes** para exibir a página de resumo para a API, incluindo uma versão maior das métricas exibidas.

![Análise][api-management-mouse-over]

![Resumo][api-management-api-summary-metrics]

Para métricas detalhadas e relatórios, clique em **análise** no menu **Gerenciamento de API** à esquerda.

![Visão geral][api-management-analytics-overview]

A seção de **análise** tem quatro guias a seguir:

-   **Em um relance** fornece métricas de uso e integridade gerais, bem como os desenvolvedores superiores, principais produtos, APIs superior e operações superiores.
-   **Uso** fornece uma visão detalhada chamadas API e largura de banda, incluindo uma representação geográfica.
-   **Integridade** concentra-se em códigos de status, taxas de sucesso, tempos de resposta e API de cache e tempos de resposta do serviço.
-   **Atividade** fornece relatórios que fazer drill down em atividade específico pelo desenvolvedor, produto, API e operação.

## <a name="next-steps"> </a>Próximos passos

- Saiba como [proteger sua API com limites de taxa](api-management-howto-product-with-rules.md).

[Versão de avaliação gratuita do Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Como configurar notificações e modelos de email no gerenciamento de API do Azure]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Preços de gerenciamento de API]: http://azure.microsoft.com/pricing/details/api-management/

[Azure Portal clássico]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
