<properties
    pageTitle="Análise de sentimento Twitter em tempo real com a análise de fluxo | Microsoft Azure"
    description="Saiba como usar a análise de fluxo para análise de sentimento Twitter em tempo real. Orientações passo a passo de geração de eventos para os dados em um painel de visualização."
    keywords="análise de tendência de twitter em tempo real, análise sentimento, análise de mídia social, exemplo de análise de tendência"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análise de mídia social: análise de sentimento Twitter em tempo real no Azure fluxo Analytics

Aprenda a criar uma solução de análise de sentimento para análise de mídia social por trazer eventos em tempo real do Twitter para Hubs de evento do Azure. Você vai escrever uma consulta de análise de fluxo de Azure para analisar os dados. Você irá armazenar os resultados para uma leitura minuciosa posterior ou usar um painel e [Power BI](https://powerbi.com/) para fornecer a obtenção de informações em tempo real.

Ferramentas de análise de mídia social ajudam as organizações a entender tendências tópicos, ou seja, assuntos e atitudes que tenham um alto volume de postagens na mídia social. Análise de sentimento, que também é chamado de *mineração de opinião*, usa ferramentas de análise de mídia social para determinar atitudes em direção a um produto, ideia e assim por diante. Análise de tendência de Twitter em tempo real é um ótimo exemplo porque o modelo de assinatura hashtag permite que você ouça palavras-chave específicas e desenvolver sentimento análise no feed.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Cenário: Análise de sentimento em tempo real

Uma empresa que tem um site de notícias mídia está interessada em receber uma vantagem sobre a concorrência apresentando conteúdo de site imediatamente relevante para seus leitores. A empresa usa análise de mídia social sobre tópicos que sejam relevantes para os leitores, fazendo análise sentimento em tempo real no Twitter dados. Especificamente, para identificar tendências tópicos em tempo real no Twitter, a empresa precisa análise em tempo real sobre o volume de tweet e sentimento para tópicos-chave. Portanto, basicamente, a necessidade é um mecanismo de análise de análise de sentimento baseada neste feed de mídia social.

## <a name="prerequisites"></a>Pré-requisitos
-   Conta e [token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) do Twitter
-   [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) do Centro de Download da Microsoft
-   Opcional: Código-fonte para o cliente do twitter do [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Criar um hub de eventos de entrada e um grupo de consumidor

O aplicativo de exemplo gerará eventos e enviá-los para uma instância de Hubs de evento (um hub de evento, de forma abreviada). Hubs de evento do barramento de serviço são o método preferencial de inclusão de evento para análise do fluxo. Consulte a documentação de Hubs de evento na [documentação do barramento de serviço](/documentation/services/service-bus/).

Use as seguintes etapas para criar um hub de evento.

1.  No portal do Azure, clique em **novo** > **Os serviços de aplicativo** > **Barramento de serviço** > **HUB de evento** > **Criação rápida**e forneça um nome, região e namespace nova ou existente.  
2.  Como prática recomendada, leia cada trabalho de análise de fluxo de um único grupo consumidor Hubs de evento. Nós orientará durante o processo de criação de um grupo de consumidor mais tarde. Você pode saber mais sobre grupos de consumidor a [Visão geral de Hubs de evento do Azure](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para criar um grupo de consumidor, vá para o hub de evento recém-criado, clique na guia **Grupos do consumidor** , clique em **criar** na parte inferior da página e, em seguida, forneça um nome para seu grupo de consumidor.
3.  Para conceder acesso para o hub de evento, precisamos criar uma política de acesso compartilhado. Clique na guia **Configurar** do seu hub de evento.
4.  Em **Políticas de acesso compartilhados**, crie uma nova política com **Gerenciar** permissões.

    ![Compartilhado onde você pode criar uma política com permissões de gerenciar as políticas de acesso.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  Clique em **Salvar** na parte inferior da página.
6.  Vá para o **Painel de controle**, clique em **Informações de conexão** na parte inferior da página e em seguida, copie e salvar as informações de conexão. (Use o ícone de cópia que aparece sob o ícone de pesquisa).

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar o aplicativo de cliente do Twitter

Fornecemos um aplicativo cliente que se conectará aos dados do Twitter através de [Streaming APIs do Twitter](https://dev.twitter.com/streaming/overview) para coletar eventos Tweet sobre um conjunto parametrizado de tópicos. A ferramenta de abrir origem [Sentiment140](http://help.sentiment140.com/) é usada para atribuir um valor de sentimento para cada tweet.

- 0 = negativo
- 2 = neutro
- 4 = positivo

Em seguida, eventos Tweet são enviados para o hub de evento.  

Siga estas etapas para configurar o aplicativo:

1.  [Baixar a solução de TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2.  Abra TwitterClient.exe.config e substitua oauth_consumer_key, oauth_consumer_secret, oauth_token e oauth_token_secret tokens Twitter que têm seus valores.  

    [Etapas para gerar um token de acesso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    Observe que você precisará fazer um aplicativo vazio para gerar um token.  
3.  Substitua os valores EventHubConnectionString e EventHubName em TwitterClient.exe.config a cadeia de conexão e o nome do seu hub de evento. A cadeia de conexão que você copiou anteriormente fornece a cadeia de conexão e o nome do seu hub de evento, então certifique-se de separá-los e colocá-los cada no campo correto. Por exemplo, considere a seguinte cadeia de conexão:

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    O arquivo de TwitterClient.exe.config deve conter as configurações como no exemplo seguinte:

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    É importante observar que o texto "EntityPath =" significa __não__ aparecem no valor EventHubName.

4.  *Opcional:* Ajuste as palavras-chave para pesquisar.  Como padrão, este aplicativo procura "Azure, Skype, XBox, Microsoft, Seattle".  Você pode ajustar os valores para **twitter_keywords** em TwitterClient.exe.config, se desejar.
5.  Execute o TwitterClient.exe para iniciar o aplicativo. Você verá os eventos de Tweet com os valores de **criadona**, **tópico**e **SentimentScore** sendo enviados para seu hub de evento.

    ![Análise de sentimento: valores de SentimentScore enviados a um hub de evento.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma a análise de fluxo de trabalho

Agora que eventos Tweet são streaming em tempo real do Twitter, podemos configurar um trabalho de análise de fluxo para analisar esses eventos em tempo real.

### <a name="provision-a-stream-analytics-job"></a>Provisionar um trabalho de análise de fluxo

1.  No [portal do Azure](https://manage.windowsazure.com/), clique em **novo** > **Serviços de dados** > **A análise de fluxo de** > **Criação rápida**.
2.  Especifique os seguintes valores e clique em **Criar trabalho de análise de fluxo**:

    * **Nome do trabalho**: insira um nome de trabalho.
    * **Região**: selecione a região onde você deseja executar o trabalho. Considere a possibilidade de colocar o trabalho e o hub de evento na mesma região para garantir o melhor desempenho e para garantir que você não estará pagando transferir dados entre regiões.
    * **Conta de armazenamento**: escolha a conta de armazenamento do Azure que você gostaria de usar para armazenar dados de monitoramento para todos os trabalhos de análise de fluxo que são executados dentro essa região. Você tem a opção para escolher uma conta de armazenamento existente ou crie um novo.

3.  Clique em **Análise de fluxo** no painel à esquerda para listar os trabalhos de análise de fluxo.  
    ![Ícone do serviço de análise de fluxo](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    O novo trabalho será exibido com um status de **criado**. Observe que o botão **Iniciar** na parte inferior da página está desativado. Você deve configurar a entrada de trabalho, saída e consulta antes de iniciar o trabalho.


### <a name="specify-job-input"></a>Especifique a entrada de trabalho
1.  No seu trabalho de análise de fluxo, clique **ENTRADAS** da parte superior da página e clique em **Adicionar entrada**. A caixa de diálogo que abrir o orientará por meio de um número de etapas para configurar a sua opinião.
2.  Clique em **fluxo de dados**e clique no botão à direita.
3.  Clique **HUB de evento**e, em seguida, clique no botão à direita.
4.  Digite ou selecione os seguintes valores na terceira página:

    * **ALIAS de entrada**: insira um nome amigável para este trabalho de entrada, como *TwitterStream*. Observe que você usará esse nome na consulta mais tarde.
    **HUB de evento**: se o hub de eventos que você criou na mesma assinatura como o trabalho de análise de fluxo, marque o namespace que o hub de evento está em.

        Se seu hub de evento estiver em uma assinatura diferente, clique **Hub de eventos de uso de outra assinatura**e digite as informações para **O NAMESPACE de barramento de serviço**, **Nome do HUB de evento**, **Nome de política do HUB de evento**, **Chave de política de HUB de evento**e **Contagem de partição de HUB de eventos**manualmente.

    * **Nome do HUB de evento**: selecione o nome do hub do evento.

    * **Nome de política do HUB de evento**: selecione a política de hub de eventos que você criou anteriormente neste tutorial.

    * **Grupo de consumidor do HUB de evento**: digite o nome do grupo de consumidor que você criou anteriormente neste tutorial.
5.  Clique no botão à direita.
6.  Especifique os seguintes valores:

    * **Formato de SERIALIZADOR de evento**: JSON
    * **CODIFICAÇÃO**: UTF8

7.  Clique no botão **Verificar** para adicionar esta fonte e verificar que a análise de fluxo pode se conectar à hub do evento.

### <a name="specify-job-query"></a>Especificar a consulta de trabalho

Análise de fluxo é compatível com um modelo de consulta simples e declarativa que descreve as transformações. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta de análise do Azure fluxo](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial ajudará você a criar e testar várias consultas sobre os dados do Twitter.

#### <a name="sample-data-input"></a>Entrada de dados de exemplo

Para validar sua consulta em relação aos dados de trabalho real, você pode usar o recurso de **Dados de exemplo** para extrair eventos de seu fluxo e criar um arquivo de .json dos eventos para teste.

1.  Selecione a entrada de hub de evento e, em seguida, clique em **Dados de exemplo** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, especifique uma **hora de início** para iniciar a coleta de dados e uma **duração** para a quantidade de dados adicional consumir.
3.  Clique no botão **detalhes** e clique no link **clique aqui** para baixar e salvar o arquivo de .json gerado.

#### <a name="pass-through-query"></a>Consulta passagem
Para começar, faremos uma consulta passagem simple que todos os campos em um evento de projetos.

1.  Clique em **consulta** na parte superior da página de análise de fluxo de trabalho.
2.  No editor de código, substitua o modelo de consulta inicial com o seguinte:

        SELECT * FROM TwitterStream

    Certifique-se de que o nome da fonte de entrada corresponde ao nome da entrada que você especificou anteriormente.

3.  Clique em **Testar** em editor de consulta.
4.  Vá para o seu arquivo de .json de amostra.
5.  Clique no botão **Verificar** e ver os resultados abaixo a definição de consulta.

    ![Resultados exibidos abaixo de definição de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Contagem de tweets por tópico: janela queda com agregação

Para comparar o número de referências entre os tópicos, usaremos um [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de menções por tópico cada cinco segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    Essa consulta usa a palavra-chave **Carimbo de hora por** para especificar um campo de carimbo de hora na carga a ser usado na computação temporal. Se esse campo não foi especificado, a operação de janelas seria executada usando a hora em que cada evento chegou no hub do evento.  Saiba mais na seção "Chegada tempo Vs aplicativo hora" de [Referência de consulta de análise do fluxo](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Essa consulta também acessa um carimbo de hora para o final de cada janela usando a propriedade **System.Timestamp** .

2.  Clique em **executar novamente** em editor de consultas para ver os resultados da consulta.

#### <a name="identify-trending-topics-sliding-window"></a>Identificar tópicos tendências: janela deslizante

Para identificar tendências de tópicos, vamos para tópicos que passam um valor limite para @ menções em um determinado período de tempo. Para os fins deste tutorial, veremos tópicos que são mencionadas mais de 20 vezes nos últimos cinco segundos usando um [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.  Alterar a consulta no editor de código para: selecione System.Timestamp como hora, tópico, contagem (*) como menciona de TwitterStream carimbo de hora por criadona grupo por SLIDINGWINDOW(s, 5), tópico tendo contar (*) > 20

2.  Clique em **executar novamente** em editor de consultas para ver os resultados da consulta.

    ![Saída da consulta de janela deslizante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Contagem de referências e sentimento: janela queda com agregação
A consulta final será testamos usa **TumblingWindow** para obter o número de referências, média, mínimo, máximo e o desvio padrão de pontuação sentimento para cada tópico a cada cinco segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  Clique em **executar novamente** em editor de consultas para ver os resultados da consulta.
3.  Esta é a consulta que serão usadas para nosso painel.  Clique em **Salvar** na parte inferior da página.


## <a name="create-output-sink"></a>Criar receptor de saída

Agora que definimos um fluxo de evento, um hub de eventos de entrada para inclusão de eventos e uma consulta para executar uma transformação sobre o fluxo, a última etapa é definir um receptor de saída para o trabalho.  Vamos escrever os eventos tweet agregados de nossa consulta de trabalho ao armazenamento de Blob do Azure.  Você também pode enviar seus resultados para banco de dados do SQL Azure, armazenamento de tabela do Azure, ou Hubs de evento, dependendo do seu aplicativo específico precisa.

Use as seguintes etapas para criar um contêiner para armazenamento de Blob, se ainda não tiver uma:

1.  Usar uma conta de armazenamento existente ou criar uma nova conta de armazenamento, clicando em **novo** > **Serviços de dados** > **armazenamento** > **Criar rápido**e, em seguida, seguindo as instruções na tela.
2.  Selecione a conta de armazenamento, clique em **CONTÊINERES** na parte superior da página e, em seguida, clique em **Adicionar**.
3.  Especifique um **nome** para seu contêiner e defina seu **acesso** ao **Blob público**.

## <a name="specify-job-output"></a>Especifique a saída de trabalho

1.  No seu trabalho de análise de fluxo, clique em **saída** na parte superior da página e, em seguida, clique em **Adicionar saída**. A caixa de diálogo que é aberta apresentará várias etapas para configurar a saída.
2.  Clique em **Armazenamento de BLOB**e clique no botão à direita.
3.  Digite ou selecione os seguintes valores na terceira página:

    * **ALIAS de saída**: insira um nome amigável para essa saída de trabalho.

    * **Assinatura**: se o armazenamento de Blob que você criou na mesma assinatura como o trabalho de análise de fluxo, clique em **Usar conta de armazenamento assinatura atual**. Se seu armazenamento estiver em uma assinatura diferente, clique na **Conta de armazenamento de uso de outra assinatura**e inserir manualmente informações de **Conta de armazenamento**, **Chave da conta de armazenamento**e **CONTÊINER**.

    * **Conta de armazenamento**: selecione o nome da conta de armazenamento.

    * **CONTÊINER**: selecione o nome do contêiner.

    * **O PREFIXO de nome de arquivo**: digite um prefixo de arquivo usar ao gravar saída blob.

4.  Clique no botão à direita.
5.  Especifique os seguintes valores:
    * **Formato de SERIALIZADOR de evento**: JSON
    * **CODIFICAÇÃO**: UTF8
6.  Clique no botão **Verificar** para adicionar esta fonte e verificar que a análise de fluxo pode se conectar à conta de armazenamento.

## <a name="start-job"></a>Iniciar trabalho

Porque uma entrada de trabalho, a consulta e saída todos forem especificados, podemos está prontos para iniciar o trabalho de análise de fluxo.

1.  Do **painel**de trabalho, clique em **Iniciar** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, clique em **horário de trabalho Iniciar**e clique no botão **Verificar** na parte inferior da caixa de diálogo. O status do trabalho será alterado para **Iniciar** e em breve se transformará em **execução**.


## <a name="view-output-for-sentiment-analysis"></a>Saída do modo de exibição para uma análise sentimento

Depois que seu trabalho está sendo executada e o fluxo de Twitter em tempo real de processamento, escolha como você deseja exibir a saída para análise de sentimento. Use uma ferramenta como [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) para exibir a saída de trabalho em tempo real. A partir daqui, você pode usar o [Power BI](https://powerbi.com/) para estender seu aplicativo para incluir um painel personalizado como do seguinte captura de tela.

![Análise de mídia social: saída do fluxo Analytics sentimento análise (mineração de opinião) em um painel do Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [Fórum de análise de fluxo de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
