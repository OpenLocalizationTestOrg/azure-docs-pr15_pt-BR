<properties
   pageTitle="Usando funções Azure com aplicativos de lógica | Microsoft Azure"
   description="Veja como usar funções do Azure com os aplicativos de lógica"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="using-azure-functions-with-logic-apps"></a>Usando funções Azure com aplicativos de lógica

Você pode executar personalizados trechos de c# ou Node usando funções de Azure de dentro de um aplicativo de lógica.  [Funções do Azure](../azure-functions/functions-overview.md) oferece sem servidor computação no Microsoft Azure. Isso é útil para executar as seguintes tarefas:

* Formatação avançada ou computação de campos dentro de um aplicativo de lógica
* Executando cálculos dentro de um fluxo de trabalho
* Estender a funcionalidade de aplicativos de lógica com funções que são compatíveis com o c# ou Node

## <a name="create-a-function-for-logic-apps"></a>Criar uma função para aplicativos de lógica

Recomendamos que você crie uma nova função no portal do Azure funções usando os modelos **Genéricos Webhook - nó** ou **Webhook - genérico c#** . Isso autopopula um modelo que aceita `application/json` partir de um aplicativo de lógica.  Se você selecionar a guia de **integrar** em funções do Azure ele deve ter o **modo** definido como **Webhook** e o **tipo de Webhook** de **JSON genérico**.  Funções que usam esses modelos automaticamente são descobertas e listadas no designer de aplicativos de lógica em **funções do Azure em minha região.**

Funções de Webhook aceitar uma solicitação e passe para o método por meio de um `data` variável. Você pode acessar as propriedades da carga usando a notação de ponto como `data.foo`.  Por exemplo, uma função de JavaScript simples que converte um valor DateTime em uma cadeia de caracteres de data aparência o exemplo a seguir:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>Chamam funções de Azure a partir de um aplicativo de lógica

No designer, se você clicar no menu **ações** , você pode selecionar **Funções do Azure em minha região**.  Esta lista os contêineres em sua assinatura e permite que você escolha a função que você deseja chamar.  

Depois de selecionar a função, você precisará especificar um objeto de carga de entrada. Esta é a mensagem que o aplicativo de lógica envia para a função e ele deve ser um objeto JSON. Por exemplo, se você quiser passar a data da **Última modificação** de um disparador de Salesforce, a carga de função talvez esta aparência:

![Última data modificado][1]

## <a name="trigger-logic-apps-from-a-function"></a>Aplicativos de lógica de gatilho de uma função

Também é possível disparar um aplicativo de lógica de dentro de uma função.  Para fazer isso, basta crie um aplicativo de lógica com um disparador manual. Para obter mais informações, consulte [aplicativos de lógica como pontos de extremidade acessível](app-service-logic-http-endpoint.md).  Em seguida, dentro de sua função, gere um HTTP POST para a URL de disparador manual com a carga que você deseja enviar para o aplicativo de lógica.

### <a name="create-a-function-from-the-designer"></a>Criar uma função do designer

Você também pode criar uma função de webhook Node de dentro do designer. Primeiro, selecione **Funções do Azure em minha região** e escolha um contêiner de sua função.  Se você ainda não tiver um contêiner, você precisa criar um a partir do [portal de funções do Azure](https://functions.azure.com/signin). Em seguida, selecione **Criar novo**.  

Para gerar um modelo com base nos dados que você deseja calcular, especifique o objeto de contexto que você planeja passar para uma função. Isso deve ser um objeto JSON. Por exemplo, se você passar o conteúdo do arquivo de uma ação de FTP, a carga de contexto ficará assim:

![Carga de contexto][2]

>[AZURE.NOTE] Porque esse objeto não foi convertido como uma cadeia de caracteres, o conteúdo será adicionado diretamente para a carga JSON. No entanto, ele emite um erro se não for um token JSON (ou seja, uma cadeia de caracteres ou um objeto/matriz JSON). Para convertê-la como uma cadeia de caracteres, basta adicione cotações, conforme mostrado na ilustração a primeira neste artigo.

Em seguida, o designer gera um modelo de função que você pode criar embutida. Variáveis são previamente criados com base no contexto que você planeja passar para a função.




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png
