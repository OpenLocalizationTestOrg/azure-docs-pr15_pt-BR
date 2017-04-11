<properties 
    pageTitle="Usar os recursos de aplicativo de lógica | Microsoft Azure" 
    description="Saiba como usar os recursos avançados de aplicativos de lógica." 
    authors="stepsic-microsoft-com" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/28/2016"
    ms.author="stepsic"/> 
    
# <a name="use-logic-apps-features"></a>Usar os recursos de aplicativos de lógica

No [tópico anterior](app-service-logic-create-a-logic-app.md), você criou seu primeiro aplicativo lógica. Agora mostraremos como criar um processo mais completo usando aplicativos de lógica de serviços de aplicativo. Este tópico apresenta os seguintes conceitos lógica aplicativos:

- Lógica condicional, que executa uma ação somente quando uma determinada condição é atendida.
- Modo de exibição de código para editar um aplicativo de lógica existente.
- Opções para iniciar um fluxo de trabalho.

Antes de concluir este tópico, você deve concluir as etapas em [criar um novo aplicativo de lógica](app-service-logic-create-a-logic-app.md). No [portal do Azure], navegue até seu aplicativo de lógica e clique em **disparadores e ações** resumo de editar a definição de aplicativo de lógica.

## <a name="reference-material"></a>Material de referência

Os documentos a seguir podem ser úteis:

- [Gerenciamento e APIs REST runtime](https://msdn.microsoft.com/library/azure/mt643787.aspx) - incluindo como invocar aplicativos de lógica diretamente
- [Referência de linguagem](https://msdn.microsoft.com/library/azure/mt643789.aspx) - uma lista abrangente de todas as funções/expressões com suporte
- [Tipos de disparador e ação](https://msdn.microsoft.com/library/azure/mt643939.aspx) - os diferentes tipos de ações e as entradas que realizar
- [Visão geral do serviço de aplicativo](../app-service/app-service-value-prop-what-is.md) - descrição das quais componentes escolha quando criar uma solução

## <a name="adding-conditional-logic"></a>Adicionar lógica condicional

Embora o fluxo original funciona, há algumas áreas que poderiam ser melhoradas. 


### <a name="conditional"></a>Condicional
Este aplicativo lógica pode resultar em você recebendo um monte de emails. As etapas a seguir adicionam lógica para certificar-se de que você só receber um email quando o tweet vem de alguém com um determinado número de seguidores. 

1. Clique no sinal de adição e localizar a ação *Obter usuário* do Twitter.

2. Passe no campo **Tweeted** do disparador obter as informações sobre o usuário do Twitter.

    ![Obter o usuário](./media/app-service-logic-use-logic-app-features/getuser.png)

3. Clique no sinal de mais novamente, mas desta vez selecione **Adicionar condição**

4. Na primeira caixa, clique em **…** sob **Obter usuário** para localizar o campo de **contagem de seguidores** .

5. Na lista suspensa, selecione **maior que**

6. Na segunda caixa, digite o número de seguidores que deseja que os usuários tenham.

    ![Condicional](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  Por fim, arrastar-e-soltar o email caixa na caixa **Se Sim** . Isso significa que você só receberá emails quando a contagem do indicador é atendida.

## <a name="repeating-over-a-list-with-foreach"></a>Sobre uma lista com forEach de repetição

O loop forEach Especifica uma matriz para repetir uma ação sobre. Se não for uma matriz que falha do fluxo. Como exemplo, se você tiver action1 que produz uma matriz de mensagens, e você deseja enviar a cada mensagem, você pode incluir essa instrução forEach nas propriedades de sua ação: forEach:"@action('action1').outputs.messages"
 

## <a name="using-the-code-view-to-edit-a-logic-app"></a>Usando o modo de exibição de código para editar um aplicativo de lógica

Além de designer, você pode editar diretamente o código que define um aplicativo de lógica, da seguinte maneira. 

1. Clique no botão **modo de exibição de código** na barra de comando. 

    Isso abre um editor completo que mostra a definição que você acabou de editar.

    ![Modo de exibição de código](./media/app-service-logic-use-logic-app-features/codeview.png)

    Usando o editor de texto, você pode copiar e colar qualquer número de ações dentro do mesmo aplicativo de lógica ou entre os aplicativos de lógica. Também facilmente, você pode adicionar ou remover seções inteiras da definição, e você também pode compartilhar definições com outras pessoas.

2. Após fazer suas alterações no modo de exibição de código, basta clicar em **Salvar**. 

### <a name="parameters"></a>Parâmetros
Há alguns recursos de aplicativos de lógica que só pode ser usado no modo de exibição de código. Um exemplo desses é parâmetros. Parâmetros facilitam a reutilização valores em todo o seu aplicativo de lógica. Por exemplo, se você tiver um endereço de email que você deseja usar em várias ações, você deve defini-lo como um parâmetro.

Este procedimento atualiza seu aplicativo lógica existente para usar parâmetros para o termo de consulta.

1. No modo de exibição de código, localize a `parameters : {}` objeto e inserir o objeto de tópico a seguir:

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
    
2. Role até o `twitterconnector` ação, localize o valor de consulta e substituí-lo `#@{parameters('topic')}`.
    Você também pode usar a função **concat** para unir duas ou mais cadeias de caracteres, por exemplo: `@concat('#',parameters('topic'))` é idêntico ao acima. 
 
Parâmetros são uma boa maneira de retirar valores que você provavelmente alterar muito. Eles são especialmente úteis quando você precisa substituir parâmetros em diferentes ambientes. Para obter mais informações sobre como Substituir parâmetros com base no ambiente, consulte nossa [documentação API REST](https://msdn.microsoft.com/library/mt643787.aspx).

Agora, quando você clicar em **Salvar**, cada hora você recebe qualquer tweets novas que tem mais de 5 retweets entregues para uma pasta chamada **tweets** no seu Dropbox.

Para saber mais sobre as definições de aplicativo de lógica, consulte [criar definições de aplicativo de lógica](app-service-logic-author-definitions.md).

## <a name="starting-a-logic-app-workflow"></a>Iniciando um fluxo de trabalho do aplicativo de lógica
Há várias opções diferentes para iniciar o fluxo de trabalho definido no aplicativo do lógica. Um fluxo de trabalho sempre pode ser iniciado sob demanda no [portal do Azure].

### <a name="recurrence-triggers"></a>Gatilhos de recorrência
Um gatilho de recorrência é executado em um intervalo que você especificar. Quando o disparador tem lógica condicional, o disparador determina se ou não o fluxo de trabalho precisa executar. Um disparador indica que ele deve ser executado, retornando uma `200` código de status. Quando ele não precisa executar, ela retorna um `202` código de status.

### <a name="callback-using-rest-apis"></a>Usando APIs REST de retorno de chamada
Serviços podem chamar um ponto de extremidade do aplicativo de lógica para iniciar um fluxo de trabalho. Consulte [aplicativos de lógica como pontos de extremidade acessível](app-service-logic-connector-http.md) para obter mais informações. Para iniciar esse tipo de aplicativo de lógica sob demanda, clique no botão **Executar agora** na barra de comando. 

<!-- Shared links -->
[Portal do Azure]: https://portal.azure.com 