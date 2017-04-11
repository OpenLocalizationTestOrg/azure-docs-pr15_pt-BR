<properties
   pageTitle="Utilizando o conector de recurso Azure nos aplicativos de lógica | Serviço de aplicativo do Microsoft Azure"
   description="Como criar e configurar o aplicativo de conector de recurso do Azure ou API e usá-lo em um aplicativo de lógica no serviço de aplicativo do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>

# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Comece a usar o conector de recurso do Azure e adicioná-lo ao seu aplicativo de lógica
>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica.

Use o conector de recurso do Azure para gerenciar facilmente os recursos do Azure dentro de seu aplicativo de lógica.

## <a name="create-the-azure-resource-connector"></a>Criar o conector de recurso Azure
Para usar o aplicativo do Azure API de conector de recurso, você precisa primeiro criar uma instância dela. Isso pode ser feito em linha durante a criação de um aplicativo de lógica ou selecionando o aplicativo de API de conector do Azure recurso Gerenciador do Azure Marketplace.

Para configurá-lo, você tem que você precisa definir para cima uma entidade de serviço com permissões para fazer tudo o que você deseja fazer no Azure. Todas as chamadas serão feitas em nome de capital este serviço que você configurar. Isso permite que você o conector para usar somente exatamente o que você deseja fazer o escopo e nada mais.

David Ebbo escreveu [uma postagem de blog excelente](http://blog.davidebbo.com/2014/12/azure-service-principal.html) sobre como configurar isso. Por favor, siga as instruções lá e obter sua **ID de locatário**, a **ID do cliente** e o **segredo**. Estas três campos, além da **ID da assinatura**, são o que são necessário para configurar o conector.

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>Usando o conector de recurso do Azure no designer de aplicativos de lógica
### <a name="trigger"></a>Disparadores
Há dois disparadores que são compatíveis com o conector:

Nome | Descrição
---- | -----------
Evento ocorre | Disparador quando ocorre um evento a um recurso em sua assinatura.
Métrica exceder o limite |  Disparador quando uma métrica satisfizer um determinado limite.

### <a name="action"></a>Ação

Da mesma forma, você pode fornecer um grande número de ações dentro de sua assinatura do Azure:

**Grupos** de recursos, você pode:

Nome | Descrição
---- | -----------
Grupos de recursos de lista | Liste todos os grupos de recursos na assinatura.
Obter o grupo de recursos | Obtenha um grupo de recursos pela sua id.
Criar grupo de recursos | Criar ou atualizar um grupo de recursos.
Excluir grupo de recursos | Exclua um grupo de recursos.

Para os **recursos** , você pode:

Nome | Descrição
---- | -----------
Recursos de lista | Recursos de lista em sua assinatura por diferentes tipos de filtros.
Obter recurso | Obter um único recurso pelo seu recurso ID.
Criar ou atualizar recursos | Criar um recurso ou, atualize um recurso existente. Você deve fornecer todas as propriedades para o recurso.
Ação de recursos |  Execute qualquer outra ação em um recurso. Você precisa saber o nome da ação e a carga que esta ação leva (se houver).
Excluir recurso | Exclua um recurso.

Para **Provedores de recurso** , você pode:

Nome | Descrição
---- | -----------
Provedores de lista de recursos | Lista todos os provedores de recursos disponíveis na assinatura.

Para **Implantações de grupo de recursos** , você pode:

Nome | Descrição
---- | -----------
Implantações de lista | Lista todas as implantações em um grupo de recursos.
Obter implantação | Obtenha uma implantação do modelo pela sua id.
Criar implantação | Crie uma nova implantação de grupo de recursos, fornecendo um modelo.

Para **eventos** sobre os recursos, você pode:

Nome | Descrição
---- | -----------
Obter eventos | Obtenha eventos em uma assinatura ou um recurso.

Para **métricas** sobre recursos você pode:

Nome | Descrição
---- | -----------
Obter métricas | Obtenha uma métrica para um recurso de ID.

## <a name="do-more-with-your-connector"></a>Faça mais com o conector
Agora que o conector for criado, você pode adicioná-lo a um fluxo de negócios usando um aplicativo de lógica. Consulte [quais são os aplicativos de lógica?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se quiser começar a usar aplicativos do Azure lógica antes de se inscrever para uma conta do Azure, vá para o [aplicativo tente lógica](https://tryappservice.azure.com/?appservice=logic), onde você pode criar imediatamente um aplicativo de lógica de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

Exibir a referência de API de REST Swagger em [conectores e API aplicativos referência](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md
