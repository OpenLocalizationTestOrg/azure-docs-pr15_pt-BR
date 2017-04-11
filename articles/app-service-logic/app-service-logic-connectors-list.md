<properties
    pageTitle="Lista de disponível conectores e aplicativos de API | Serviço de aplicativo do Microsoft Azure"
    description="Leia sobre os conectores e aplicativos de API no serviço de aplicativo do Azure"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>


# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Lista de conectores e aplicativos de API para usar em seus aplicativos de lógica
>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica. Para a versão de lógica aplicativos disponibilidade geral (GA), consulte [Nova lista de conectores](../connectors/apis-list.md).

Saiba mais sobre todos os conectores disponíveis e os aplicativos de API criado pela Microsoft para usar em seus aplicativos de lógica.

Para preços informações e uma lista do que está incluído com cada nível de serviço, consulte [Preços de serviço de aplicativo do Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Para começar com aplicativos de lógica antes de se inscrever para uma conta do Azure, vá para [Experimentar o aplicativo de lógica](https://tryappservice.azure.com/?appservice=logic). Imediatamente, você pode criar um aplicativo de lógica de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="core-connectors"></a>Conectores de núcleo
A tabela a seguir lista todos os conectores disponíveis e API aplicativos criado pela Microsoft disponíveis como conectores principais:

Nome | Descrição
--- | ---
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Use o Bing para traduzir texto em outro idioma.
[HTTP](app-service-logic-connector-http.md) | O ouvinte HTTP abre um ponto de extremidade que atua como um servidor HTTP e ouve solicitações de entrada de HTTP ou HTTPS. A ação de HTTP não requer um App API e é suportada nativamente dentro de aplicativos de lógica.
[Margem de atraso](app-service-logic-connector-slack.md) | Conectar-se a margem de atraso e postar mensagens para canais de margem de atraso.


## <a name="enterprise-integration-connectors"></a>Conectores de integração do Enterprise
A tabela a seguir lista todos os conectores e aplicativos de API criado pela Microsoft disponível como conectores de integração do Enterprise disponíveis:

Nome  | Descrição
------------- | -------------
[Regras de BizTalk](app-service-logic-use-biztalk-rules.md) | Use regras de BizTalk para definir e controlar a lógica de negócios em uma organização. Políticas de negócios podem ser atualizadas sem recompilar ou sem reimplantar os aplicativos associados.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Procura e extrai dados de conteúdo XML com base em um XPath que você escolher.
[Conector de DB2](app-service-logic-connector-db2.md) | Conecta um IBM DB2 do banco de dados local e em uma máquina virtual Azure executando um sistema operacional Windows. Pode mapear operações de Web API e API de OData para comandos Informix Structured Query Language. <br/><br/>Nenhum disparadores. Ações incluem tabela selecione, inserir, atualizar, excluir e instrução personalizada<br/><br/>Esse conector também inclui o cliente Microsoft para DRDA para se conectar a um servidor de Informix em uma rede TCP/IP.
[Arquivo](app-service-logic-connector-file.md) | Usando esse conector, você pode se conectar ao sistema de arquivo local ou rede e tarefas de arquivo diferentes completo, incluindo carregando, excluindo, listando arquivos e muito mais.
[Informix](app-service-logic-connector-informix.md) | Conecta a um banco de dados IBM Informix, local e em uma máquina virtual Azure executando um sistema operacional Windows. Pode mapear operações de Web API e API de OData para comandos Informix Structured Query Language.<br/><br/>Nenhum disparadores. Ações incluem tabela selecione, inserir, atualizar, excluir e instrução personalizada.<br/><br/>Ao usar o local, VPN ou rota expressa do Azure pode ser usada. Esse conector também inclui um cliente Microsoft para DRDA para se conectar a um servidor de Informix em uma rede TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Conecta-se ao local do SQL Server ou um banco de dados do SQL Azure. Você pode criar, atualizar, obtenha e excluir entradas em uma tabela de banco de dados SQL.
MQ | Se conecta ao servidor IBM WebSphere MQ versão 8, local e em uma máquina virtual Azure executando um sistema operacional Windows. Ao usar o local, VPN ou rota expressa do Azure pode ser usada. O conector também inclui o cliente Microsoft para MQ.<br/><br/>Nenhum disparadores. Nenhuma ação.<br/><br/>**Observação** Atualmente, não podem ser usados com aplicativos de lógica.

## <a name="connectors-as-triggers"></a>Conectores como disparadores
Vários conectores fornecem disparadores para aplicativos de lógica. Esses disparadores são de dois tipos:

1. Gatilhos de votação: Esses disparadores poll seu serviço em uma frequência especificada para verificar se há novos dados. Quando novos dados estiverem disponíveis, uma nova instância do seu aplicativo de lógica é executado com os dados como entrada. Para impedir que os mesmos dados consumida várias vezes, o disparador pode limpeza dados que foi ler e passados para o aplicativo de lógica. Exemplos de tais conectores são arquivo, SQL e armazenamento do Azure.
2. Gatilhos de envio: Esses disparadores ouvem para dados em uma empresa ou um evento ocorrer. Em seguida, acione uma nova instância de um aplicativo de lógica. Exemplos de tais conectores são ouvinte HTTP e Twitter.

## <a name="connectors-as-actions"></a>Conectores como ações
Conectores também podem ser usados como ações dentro de seu aplicativo de lógica. Ações são úteis para pesquisar dados dentro do aplicativo de lógica que podem então usado na execução. Por exemplo, talvez você precise procurar dados de um banco de dados SQL para obter informações adicionais sobre um cliente quando uma ordem de processamento. Ou, talvez você precise escrever, atualizar ou excluir dados em um destino. Você pode fazer isso usando as ações fornecidas pelos conectores. Ações mapeiam para operações nos aplicativos de API (conforme definido pelo seus metadados Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Criar seus próprios conectores e aplicativos de API
[Referência de aplicativos de API e conectores](http://aka.ms/appservicesconnectorreference)  
[Gatilhos de aplicativo do Azure API do serviço de aplicativo](../app-service-api/app-service-api-dotnet-triggers.md)  
[Referência de aplicativo de lógica](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Mais informações sobre aplicativos de API e conectores
[O que são conectores e BizTalk API aplicativos](app-service-logic-what-are-biztalk-api-apps.md)  
[Usando o Gerenciador de Conexão de híbrido no serviço de aplicativo do Azure](app-service-logic-hybrid-connection-manager.md)  
[Gerenciar e monitorar seus aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md)
