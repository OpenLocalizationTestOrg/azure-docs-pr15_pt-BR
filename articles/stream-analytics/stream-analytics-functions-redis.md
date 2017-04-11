<properties
    pageTitle="A saída para um Azure relacionada Cache, usando funções do Azure, do Azure fluxo Analytics | Microsoft Azure"
    description="Saiba como usar uma função do Azure conectado a uma fila de barramento de serviço, para preencher um Azure relacionada Cache da saída de um trabalho de análise de fluxo."
    keywords="fluxo de dados, cache relacionada, fila de barramento de serviço"
    services="stream-analytics"
    authors="ryancrawcour"
    manager="jhubbard"
    documentationCenter=""
    />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/26/2016"
    ms.author="ryancraw"/>

# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Como armazenar dados de análise de fluxo de Azure em um Cache relacionada Azure usando funções do Azure

A análise de fluxo Azure lhe permite desenvolver e implantar soluções de baixo custo para obter percepções em tempo real do dispositivos, sensores, infraestrutura e aplicativos ou qualquer fluxo de dados rapidamente. Ele permite que vários casos de uso como gerenciamento em tempo real e monitoramento, comando e controle, detecção de fraudes, carros conectados e muito mais. Em muitos cenários, você talvez queira armazenar dados saídos pela análise de fluxo de Azure em um armazenamento de dados distribuídos como um cache Azure relacionada.

Suponha que você fizer parte de uma empresa de telecomunicações. Você está tentando detectar fraude SIM onde várias chamadas provenientes a mesma identidade, ao mesmo tempo, mas diferentes geograficamente locais. Você está designado armazenar todos os possíveis fraudulentos telefonemas em um cache Azure relacionada. Nesse blog, podemos orientam sobre como você pode facilmente concluir sua tarefa. 

## <a name="prerequisites"></a>Pré-requisitos
Concluir a [Detecção de fraude em tempo real] [ fraud-detection] passo a passo para ASA

## <a name="architecture-overview"></a>Visão geral da arquitetura
![Captura de tela da arquitetura](./media/stream-analytics-functions-redis/architecture-overview.png)

Como mostrado na figura anterior, permite que a análise de fluxo de dados de entrada para ser consultados e enviadas para uma saída de fluxo. Com base na saída, funções do Azure podem disparar algum tipo de evento. 

Neste blog, podemos concentrar-se em parte do Azure funções esse pipeline ou mais especificamente disparo de um evento que armazena dados fraudulentos no cache.
Depois de concluir a [Detecção de fraude em tempo real] [ fraud-detection] tutorial, você tem uma entrada (um hub de evento), uma consulta e uma saída (armazenamento de blob) já configurado e em execução. Nesse blog, podemos alterar a saída para usar uma fila de barramento de serviço, em vez disso. Depois disso, podemos conectar uma função do Azure para essa fila. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Criar e conectar uma saída de fila de barramento de serviço
Para criar uma fila de barramento de serviço, siga as etapas 1 e 2 da seção .NET em [Introdução ao serviço barramento filas][servicebus-getstarted].
Agora vamos conectar fila para o trabalho de análise de fluxo que foi criado na orientação sobre detecção de fraude anterior.



1. No portal do Azure, vá para a lâmina **saídas** do seu trabalho e selecione **Adicionar** na parte superior da página.

    ![Adicionando saídas](./media/stream-analytics-functions-redis/adding-outputs.png)

2. Escolha **Fila de barramento de serviço** como o **PIA** e siga as instruções na tela. Certifique-se de escolher o namespace dos fila de barramento de serviço criado na [Introdução ao serviço barramento filas][servicebus-getstarted]. Clique no botão "direito" quando tiver terminado.
3. Especifique os seguintes valores:
    - **Formato de serializador de evento**: JSON
    - **Codificação**: UTF8
    - **Formato**: linha separado

4. Clique no botão **criar** para adicionar esta fonte e verificar que a análise de fluxo pode se conectar à conta de armazenamento.

5. Na guia **consulta** , substitua a consulta atual com o seguinte. Substitua *[Nome do barramento seu serviço]* com o nome de saída que você criou na etapa 3. 

    ```    

        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## <a name="create-an-azure-redis-cache"></a>Criar um Cache relacionada Azure
Criar um cache Azure relacionada seguindo a seção .NET [como usar Azure relacionada Cache] [ use-rediscache] até que a seção chamado ***Configurar os clientes de cache***.
Após a conclusão, você tem um novo Cache relacionada. Em **todas as configurações**, selecione **teclas de acesso** e de anotação para baixo a ***cadeia de conexão principal***.

![Captura de tela da arquitetura](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Criar uma função Azure
Siga a [criar sua primeira função Azure] [ functions-getstarted] tutorial para começar a usar funções do Azure. Se você já tiver uma função Azure que você gostaria de usar, em seguida, pular para a [gravação em Cache relacionada](#Writing-to-Redis-Cache)

1. No portal do, selecione os serviços de aplicativo de navegação à esquerda, clique em seu nome de aplicativo de função Azure para acessar o site do aplicativo da função.
    ![Captura de tela da lista de funções de serviços de aplicativo](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. Clique em **nova função > ServiceBusQueueTrigger – c#**. Para os seguintes campos, siga estas instruções:
    - **Nome da fila**: O mesmo nome que o nome que você inseriu quando você criou a fila na [Introdução ao serviço barramento filas] [ servicebus-getstarted] (não o nome do barramento serviço). Verifique se que você usa a fila que está conectada à saída da análise de fluxo.
    - **Conexão de barramento de serviço**: selecione **Adicionar uma cadeia de conexão**. Para localizar a cadeia de conexão, acesse o portal clássico, selecione **Barramento de serviço**, o barramento de serviço que você criou e **Informações de conexão** na parte inferior da tela. Verifique se que você está na tela principal nesta página. Copie e cole a cadeia de conexão. Fique à vontade para inserir qualquer nome de conexão.
    
        ![Captura de tela de conexão de barramento de serviço](./media/stream-analytics-functions-redis/servicebus-connection.png)
    - **AccessRights**: escolha **Gerenciar**


3. Clique em **criar**

## <a name="writing-to-redis-cache"></a>Gravar relacionada Cache
Agora, criamos uma função do Azure que lê de uma fila de barramento de serviço. Tudo o que falta para fazer é usar a função gravar esses dados para o Cache relacionada. 

1. Selecione seu recém-criado **ServiceBusQueueTrigger**e clique em **configurações do aplicativo de função** no canto superior direito. Selecione **vá para configurações de serviço de aplicativo > Configurações > configurações do aplicativo**

2. Na seção de cadeias de caracteres de Conexão, crie um nome na seção **nome** . Cole a cadeia de conexão principal que você encontrou na etapa **criar um Cache relacionada** na seção **valor** . Selecione **personalizado** onde está escrito **Banco de dados SQL**.

3. Na parte superior, clique em **Salvar** .

    ![Captura de tela de conexão de barramento de serviço](./media/stream-analytics-functions-redis/function-connection-string.png)

4. Agora, volte para as configurações de serviço de aplicativo e selecione **Ferramentas > Editor de serviço de aplicativo (visualização) > em > Ir**.

    ![Captura de tela de conexão de barramento de serviço](./media/stream-analytics-functions-redis/app-service-editor.png)

5. Em um editor de sua escolha, crie um arquivo JSON chamado **project.json** com o seguinte e salvá-lo em seu disco local.

        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }

6. Carregar o arquivo no diretório raiz da sua função (não WWWROOT). Você deverá ver um arquivo denominado **project.lock.json** automaticamente aparecer, confirmando que o Nuget pacotes "StackExchange.Redis" e "Newtonsoft.Json" foram importados.

7. No arquivo **run.csx** , substitua o código previamente gerado com o seguinte código. Na função lazyConnection, substitua "Con nome" com o nome que você criou na etapa 2 de **dados de armazenamento em cache relacionada**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Iniciar a a análise de fluxo de trabalho

1. Inicie o aplicativo telcodatagen.exe. O uso é da seguinte maneira:````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. Da lâmina do trabalho de análise de fluxo no portal, clique em **Iniciar** na parte superior da página.

    ![Captura de tela do trabalho inicial](./media/stream-analytics-functions-redis/starting-job.png)

3. Na lâmina **Iniciar trabalho** que aparece, selecione **agora** e, em seguida, clique no botão **Iniciar** na parte inferior da tela. As alterações de status do trabalho inicial e após a execução de algumas alterações de tempo.
 
    ![Captura de tela da seleção de início de horário de trabalho](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Executar a solução e verificar resultados
Voltar à sua página de **ServiceBusQueueTrigger** , agora você deve ver instruções de log. Esses logs mostram que você tem algo na fila de barramento de serviço, colocá-lo no banco de dados e buscada-lo usando o tempo como chave!

Para verificar se seus dados estiverem em seu cache relacionada, vá até a página de cache relacionada no novo portal (conforme mostrado na etapa anterior [Crie um Cache do Azure relacionada](#Create-an-Azure-Redis-Cache) ) e selecione Console.

Agora você pode escrever relacionada comandos para confirmar que os dados sejam na verdade no cache.

![Captura de tela do Console relacionada](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Próximas etapas
Estamos animados coisas novas funções do Azure e a análise de fluxo pode fazer juntos e esperamos que isso desbloqueie novas possibilidades para você. Se você tiver quaisquer comentários sobre o que você deseja, fique à vontade para usar o [site de UserVoice do Azure](https://feedback.azure.com/forums/270577-stream-analytics).

Se você é novo Microsoft Azure, convida você para testá-la por se inscrever para uma [conta de avaliação gratuita Azure](https://azure.microsoft.com/pricing/free-trial/). Se você estiver começando a usar a análise de fluxo, em seguida, podemos convidá-lo para [criar seu primeiro a análise de fluxo de trabalho](stream-analytics-create-a-job.md).

Se você precisar de Ajuda ou tem perguntas, postar nos fóruns do [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) ou [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) . 

Você também pode ver os seguintes recursos:

- [Referência do desenvolvedor funções Azure](../azure-functions/functions-reference.md)
- [Referência de desenvolvedor do Azure funções c#](../azure-functions/functions-reference-csharp.md)
- [Referência de desenvolvedor do Azure funções F #](../azure-functions/functions-reference-fsharp.md)
- [Referência do desenvolvedor funções NodeJS Azure](../azure-functions/functions-reference.md)
- [Ligações e azure gatilhos de funções](../azure-functions/functions-triggers-bindings.md)
- [Como monitorar Cache relacionada do Azure](../redis-cache/cache-how-to-monitor.md)

Para se manter atualizado sobre todas as últimas notícias e recursos, siga [@AzureStreaming](https://twitter.com/AzureStreaming) no Twitter.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md
