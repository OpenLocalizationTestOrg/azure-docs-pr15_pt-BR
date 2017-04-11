<properties 
    pageTitle="Visão geral do pacote de integração de Enterprise | Serviço de aplicativo do Microsoft Azure | Microsoft Azure" 
    description="Usar os recursos do pacote de integração de Enterprise para habilitar cenários de processo e integração de negócios usando o serviço de aplicativo do Microsoft Azure" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-xml-transforms"></a>Integração do Enterprise com transformações XML

## <a name="overview"></a>Visão geral
O conector de transformação de integração do Enterprise converte dados de um formato para outro formato. Por exemplo, você pode ter uma mensagem de entrada que contém a data atual no formato YearMonthDay. Você pode usar uma transformação para reformatar a data para estar no formato MonthDayYear.

## <a name="what-does-a-transform-do"></a>O que faz uma transformação?
Uma transformação, que também é conhecido como um mapa, consiste em um esquema XML de origem (a entrada) e um esquema XML de destino (a saída). Você pode usar funções internas diferentes para ajudar a manipular ou controlar os dados, incluindo manipulação de cadeia de caracteres, atribuições condicionais, expressões aritméticas, formatadores de tempo de data e até mesmo construções de loop.

## <a name="how-to-create-a-transform"></a>Como criar uma transformação?
Você pode criar um mapa de transformação/usando o Visual Studio [Enterprise integração SDK](https://aka.ms/vsmapsandschemas). Quando terminar de criar e testar a transformação, você carregar a transformação para sua conta de integração. 

## <a name="how-to-use-a-transform"></a>Como usar uma transformação
Depois de carregar a transformação para sua conta de integração, você pode usá-lo para criar um aplicativo de lógica. O aplicativo de lógica correrá transformações sempre que o aplicativo de lógica é disparado (e há conteúdo entrado que precisa ser transformado).

**Estas são as etapas para usar uma transformação**:

### <a name="prerequisites"></a>Pré-requisitos 
Na visualização, você precisará:  

-  [Criar um contêiner de funções do Azure] (https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Criar um contêiner de funções do Azure")  
-  [Adicionar uma função ao contêiner de funções do Azure] (https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "Este modelo cria uma webhook com base azure função c# com recursos de transformação para usar em cenários de integração de aplicativos de lógica")    
-  Criar uma conta de integração e adicionar um mapa-lo  

>[AZURE.TIP] Anote o nome do contêiner de funções do Azure e a função Azure, você precisará na próxima etapa.  

Agora que você já tratado os pré-requisitos, é hora de criar seu aplicativo de lógica:  

1. Crie um aplicativo de lógica e [vinculá-lo à sua conta de integração](./app-service-logic-enterprise-integration-accounts.md "Aprenda como vincular uma conta de integração para um aplicativo de lógica") que contém o mapa.
2. Adicionar um disparador **solicitação - solicitação HTTP um quando for recebida** para o seu aplicativo de lógica  
![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)    
3. Adicionar a ação de **Transformar XML** pelo primeiro selecionando **Adicionar uma ação**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)   
4. Digite a palavra *transformar* na caixa de pesquisa para filtrar todas as ações ao que você deseja usar  
![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)  
5. Selecione a ação de **Transformar XML**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)  
6. Selecione o **CONTÊINER de função** que contém a função que você usará. Este é o nome do contêiner de funções do Azure que você criou anteriormente estas etapas.
7. Selecione a **função** que você deseja usar. Este é o nome da função Azure que você criou anteriormente.
8. Adicione o **conteúdo** que você transformem de XML. Observe que você pode usar quaisquer dados XML que você recebe na solicitação HTTP como o **conteúdo**. Neste exemplo, selecione o corpo da solicitação HTTP que disparou o aplicativo de lógica.
9. Selecione o nome do **mapa** que você deseja usar para executar a transformação. O mapa já deve estar em sua conta de integração. Em uma etapa anterior, você já deu seu acesso de aplicativo lógica à sua conta de integração que contém o mapa.
10. Salvar seu trabalho  
![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png) 

Neste ponto, você tiver terminado de configuração de seu mapa. Em um aplicativo do mundo real, talvez você queira armazenar os dados transformados em um aplicativo de LOB como SalesForce. Você pode facilmente como uma ação para enviar a saída da transformação à equipe de vendas. 

Agora você pode testar sua transformação fazendo uma solicitação para o ponto de extremidade HTTP.  

## <a name="features-and-use-cases"></a>Recursos e casos de uso

- A transformação criada em um mapa pode ser simple, como copiar um nome e endereço de um documento para outro. Ou, você pode criar transformações mais complexas usando as operações de mapa de fora da caixa.  
- Várias operações de mapa ou funções estão disponíveis, incluindo cadeias de caracteres, funções de tempo de data e assim por diante.  
- Você pode fazer uma cópia de dados direto entre os esquemas. No mapeador incluído no SDK, isso é tão simple quanto desenhando uma linha que conecta os elementos no esquema de origem com seus correspondentes no esquema de destino.  
- Ao criar um mapa, você vê uma representação gráfica do mapa, que mostram todas as relações e os links que você criar.
- Use o recurso de mapa de teste para adicionar uma mensagem XML de amostra. Com um simples clique, você pode testar o mapa que você criou e ver a saída gerada.  
- Carregar mapas existentes  
- Inclui suporte para o formato XML.


## <a name="learn-more"></a>Saiba Mais
- [Saiba mais sobre o pacote de integração do Enterprise] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa")  
- [Saiba mais sobre mapas] (./app-service-logic-enterprise-integration-maps.md "Saiba mais sobre a integração do enterprise mapas")  
 