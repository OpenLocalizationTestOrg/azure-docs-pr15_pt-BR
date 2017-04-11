<properties
   pageTitle="Criar EAI lógica de aplicativo usando VETR nos aplicativos de lógica no serviço de aplicativo do Azure | Microsoft Azure"
   description="Validar, codificar e transformar os recursos dos serviços do BizTalk XML"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="create-eai-logic-app-using-vetr"></a>Criar EAI lógica aplicativo usando VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

A maioria dos cenários de integração de aplicativo empresarial (EAI) mediar dados entre uma origem e um destino. Esses cenários geralmente têm um conjunto comum de requisitos:

- Certifique-se de que os dados de diferentes sistemas estão formatados corretamente.
- Realizar "pesquisa" nos dados de entrada para tomar decisões.
- Converta dados de um formato para outro. Por exemplo, converta dados do formato de dados de um sistema CRM para o formato de dados de um sistema ERP.
- Rotear os dados para o sistema ou aplicativo desejado.

Este artigo mostra um padrão de integração comum: "mediação mensagem unidirecional" ou VETR (validar, Enrich, transformação, rota). O padrão VETR faz uma mediação dados entre uma entidade de origem e uma entidade de destino. Geralmente de origem e destino são fontes de dados.

Considere a possibilidade de um site que aceita pedidos. Os usuários postam pedidos ao sistema usando HTTP. Nos bastidores, o sistema valida os dados de entrada de correção, normaliza e persiste-la em uma fila de barramento de serviço para processamento posterior. O sistema demora pedidos da fila, esperando-lo em um formato específico. Portanto, o fluxo de ponta a ponta é:

**HTTP** → **Validar** → **transformar** → **barramento de serviço**

![Fluxo VETR básica][1]

Os seguintes aplicativos de API do BizTalk ajudar construir este padrão:

* **Gatilho de HTTP** - fonte para evento de mensagem de disparadores
* **Validar** - valida correção dos dados de entrada
* **Transformar** - transformações dados de entrada para o formato requerido pelo sistema downstream
* **Conector do barramento de serviço** - entidade de destino onde os dados são enviados


## <a name="constructing-the-basic-vetr-pattern"></a>Construir o padrão VETR básico
### <a name="the-basics"></a>Noções básicas

No portal do Azure, selecione **+ novo**, selecione **Web + Mobile**e, em seguida, selecione o **Aplicativo de lógica**. Escolha um nome, localização, assinatura, grupo de recursos e um local que funciona. Grupos de recursos atuam como contêineres para seus aplicativos; todos os recursos para o aplicativo ir para o mesmo grupo de recursos.

Em seguida, vamos adicionar disparadores e ações.


## <a name="add-http-trigger"></a>Adicionar gatilho de HTTP
1. Nos **Modelos de aplicativo de lógica**, selecione **Criar do zero**.
1. Selecione o **Ouvinte HTTP** da Galeria para criar um novo ouvinte. Ligue- **HTTP1**.
2. Definir o **Enviar resposta automaticamente?** definindo como false. Configure a ação de disparador pela configuração _Método HTTP_ a _POSTAGEM_ e configuração _URL relativa_ à _/OneWayPipeline_:  
    ![HTTP disparadores][2]
3. Selecione a marca de seleção verde para concluir o disparador.

## <a name="add-validate-action"></a>Adicionar validar ação

Agora, vamos inserir ações que são executadas sempre que o disparador acionado — ou seja, sempre que uma chamada for recebida no ponto de extremidade HTTP.

1. Adicionar **Validação de XML BizTalk** da galeria e nomeie _(Validate1)_ para criar uma instância.
2. Configure um esquema XSD para validar as mensagens de entrada XML. Selecione a ação de _Validar_ e _triggers('httplistener').outputs. Conteúdo_ como o valor para o parâmetro _inputXml_ .

Agora, a ação validar é a primeira ação após o ouvinte HTTP: 

![Validação de XML BizTalk][3]

Da mesma forma, vamos adicionar o restante das ações. 

## <a name="add-transform-action"></a>Adicionar ação de transformação
Vamos configurar transformações para normalizar dados de entrada.

1. Adicione o **Serviço de transformar BizTalk** da Galeria.
2. Para configurar uma transformação para transformar as mensagens de entrada XML, selecione a ação de **transformar** como a ação para realizar quando essa API é chamada. Selecione ```triggers(‘httplistener’).outputs.Content``` como o valor para _inputXml_. *Mapa* é um parâmetro opcional, desde que os dados de entrada são correspondidos com todas as transformações configuradas, e somente aqueles que corresponde ao esquema são aplicadas.
3. Por fim, a transformação é executado somente se validar sucesso. Para configurar essa condição, selecione o ícone de engrenagem no canto superior direito e selecione _Adicionar uma condição seja atendida_. Defina a condição como ```equals(actions('xmlvalidator').status,'Succeeded')```:  

![BizTalk transformações][4]


## <a name="add-service-bus-connector"></a>Adicionar o conector de barramento de serviço
Em seguida, vamos adicionar o destino — uma fila de barramento de serviço — para gravar dados.

1. Adicione um **Conector de barramento de serviço** da Galeria. Defina o **nome** como _Servicebus1_, defina a **Cadeia de Conexão** para a cadeia de conexão para sua instância de barramento de serviço, defina o **Nome de entidade** para _fila_e ignorar o **nome da assinatura**.
2. Selecione a ação de **Enviar mensagem** e defina o campo do **conteúdo** para a ação para _actions('transformservice').outputs. OutputXml_.
3. Defina o campo de **Tipo de conteúdo** ao *aplicativo/xml*:  

![Barramento de serviço][5]


## <a name="send-http-response"></a>Enviar resposta HTTP
Depois de processamento de pipeline, volta envie uma resposta HTTP para sucesso e falha com as seguintes etapas:

1. Adicionar um **Ouvinte HTTP** da galeria e selecione a ação de **Enviar resposta de HTTP** .
2. Configure a **ID da resposta** para enviar a *mensagem*.
2. Definir o **Conteúdo de resposta** para *processamento de Pipeline concluído*.
3. **Código de Status de resposta** para *200* para indicar HTTP 200 Okey.
4. Selecione o menu suspenso no canto superior direito e selecione **Adicionar uma condição seja atendida**.  Defina a condição como a expressão a seguir:  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. Repita essas etapas para enviar uma resposta HTTP falha também. Alterar **condição** com a seguinte expressão:  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Selecione **Okey** e **criar**.



## <a name="completion"></a>Conclusão
Sempre que alguém envia uma mensagem para o ponto de extremidade HTTP, ele aciona o aplicativo e executa as ações que você acabou de criar. Para gerenciar qualquer tais aplicativos lógica que você cria, selecione **Procurar** no Portal do Azure e selecione **Aplicativos de lógica**. Selecione seu aplicativo para ver mais informações.

Alguns tópicos úteis:

[Gerenciar e monitorar seus aplicativos de API e conectores](app-service-logic-monitor-your-connectors.md)  <br/>
[Monitore seus aplicativos de lógica](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG
