<properties
    pageTitle="Fluxo Analytics: Detecção de fraude em tempo real | Microsoft Azure"
    description="Aprenda a criar uma solução de detecção de fraude em tempo real com a análise de fluxo. Use um hub de evento para processamento de eventos em tempo real."
    keywords="detecção de anomalias, detecção de fraude, detecção de anomalias de tempo real"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />



# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Começar a usar a análise de fluxo Azure: detecção de fraude em tempo real

Aprenda a criar uma solução de ponta a ponta para a detecção de fraude em tempo real com a análise de fluxo Azure. Exiba os eventos Hubs de evento do Azure, escrever consultas de análise de fluxo para agregação ou alertas e enviar os resultados a um receptor de saída para obter ideias sobre os dados com processamento em tempo real. Detecção de anomalias em tempo real para telecomunicações é explicada, mas a técnica de exemplo é igualmente adequada para outros tipos de detecção de fraude como cenários de roubo de cartão de crédito ou identidade.

Análise de fluxo é um serviço totalmente gerenciado que fornece o processamento de eventos de baixa latência, altamente disponível, escaláveis, complexos em streaming dados na nuvem. Para obter mais informações, consulte [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Cenário: Detecção de fraude telecomunicações e SIM em tempo real

Uma empresa de telecomunicações tem um grande volume de dados para chamadas de entrada. A empresa precisa o seguinte de seus dados:

* Reduzir os dados para um valor gerenciável e obter informações sobre o uso de cliente ao longo do tempo e entre regiões geográficas.
* Detecte fraudes SIM (múltiplas chamadas da mesma identidade ao mesmo tempo, mas em locais diferentes geograficamente) em tempo real para que a empresa pode responder facilmente notificar os clientes ou desligar o serviço.

Cenários de Internet das coisas (IoT) canônicos têm uma tonelada de telemetria ou dados de sensores. Clientes desejam agregar os dados ou receber alertas sobre problemas em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

- Baixar [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) do Centro de Download da Microsoft
- Opcional: Código-fonte de gerador de evento do [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Criar grupo de entrada e consumidor Hubs de evento do Azure

O aplicativo de exemplo gerará eventos e enviá-los para uma instância de Hubs de evento para processamento em tempo real. Serviço barramento evento Hubs são o método preferencial de inclusão de evento para análise do fluxo. Você pode saber mais sobre Hubs de evento na [documentação do barramento de serviço do Azure](/documentation/services/service-bus/).

Para criar um hub de evento:

1.  No [portal do Azure](https://manage.windowsazure.com/), clique em **novo** > **Os serviços de aplicativo** > **Barramento de serviço** > **HUB de evento** > **Criação rápida**. Forneça um nome, região e namespace nova ou existente para criar um novo hub de evento.  
2.  Como prática recomendada, leia cada trabalho de análise de fluxo de um grupo de consumidor do hub único evento. Nós orientará durante o processo de criação de um grupo de consumidor mais tarde. [Saiba mais sobre grupos do consumidor](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para criar um grupo de consumidor, vá para o hub de evento recém-criado, clique na guia **Grupos do consumidor** , clique em **criar** na parte inferior da página e, em seguida, forneça um nome para seu grupo de consumidor.
3.  Para conceder acesso para o hub de evento, precisamos criar uma política de acesso compartilhado. Clique na guia **Configurar** do seu hub de evento.
4.  Em **Políticas de acesso compartilhados**, crie uma nova política que tenha permissões de **Gerenciar** .

    ![Compartilhado onde você pode criar uma política com permissões de gerenciar as políticas de acesso.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  Clique em **Salvar** na parte inferior da página.
6.  Vá para o **Painel de controle**, clique em **Informações de conexão** na parte inferior da página e em seguida, copie e salvar as informações de conexão.

## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar o aplicativo de gerador de evento

Fornecemos um aplicativo cliente que gerará metadados de chamada de entrada de amostra e enviados por push para Hubs de evento. Use as seguintes etapas para configurar esse aplicativo.  

1.  Baixar o [arquivo TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)e descompacte-o em um diretório.

    > [AZURE.NOTE] Windows pode bloquear o arquivo zip baixado. Clique com botão direito no arquivo e selecione **Propriedades**. Se você vir a mensagem "Este arquivo veio de outro computador e pode ser bloqueado para ajudar a proteger este computador", marque a caixa de **Desbloquear** e clique em Aplicar no arquivo zip.

2.  Substitua os valores Microsoft.ServiceBus.ConnectionString e EventHubName em telcodatagen.exe.config a sua cadeia de conexão do hub de evento e o nome.

    A cadeia de conexão que você copiou do portal do Azure coloca o nome da conexão no final. Certifique-se de remover "; EntityPath =<value>"do" Adicionar chave = "campo.

3.  Inicie o aplicativo. O uso é da seguinte maneira:

    telcodatagen.exe [#NumCDRsPerHour] [probabilidade de fraude de cartão SIM] [#DurationHours]

O exemplo a seguir gerará 1.000 eventos com uma probabilidade de 20 por cento de fraude ao longo de duas horas.

    telcodatagen.exe 1000 .2 2

Você verá os registros sendo enviados para seu hub de evento. Alguns campos de chave que usaremos neste aplicativo de detecção de fraude em tempo real são definidos aqui:

| Registro | Definição |
| ------------- | ------------- |
| CallrecTime | Carimbo de hora para a hora de início de chamada. |
| SwitchNum | Opção de telefone usada para conectar a chamada. |
| CallingNum | Número de telefone do chamador. |
| CallingIMSI | Identidade do assinante móvel internacional (IMSI).  Identificador exclusivo do chamador. |
| CalledNum | Número de telefone do destinatário da chamada. |
| CalledIMSI | Identidade do assinante móvel internacional (IMSI).  Identificador exclusivo do destinatário da chamada. |


## <a name="create-a-stream-analytics-job"></a>Criar uma a análise de fluxo de trabalho
Agora que temos um fluxo de eventos de telecomunicações, podemos configurar um trabalho de análise de fluxo para analisar esses eventos em tempo real.

### <a name="provision-a-stream-analytics-job"></a>Provisionar um trabalho de análise de fluxo

1.  No portal do Azure, clique em **novo** > **Serviços de dados** > **A análise de fluxo de** > **Criação rápida**.
2.  Especifique os seguintes valores e clique em **Criar trabalho de análise de fluxo**:

    * **Nome do trabalho**: insira um nome de trabalho.

    * **Região**: selecione a região onde você deseja executar o trabalho. Considere a possibilidade de colocar o trabalho e o hub de evento na mesma região para garantir o melhor desempenho e para garantir que você não estará pagando transferir dados entre regiões.

    * **Conta de armazenamento**: escolha a conta de armazenamento do Azure que você gostaria de usar para armazenar dados de monitoramento para todos os trabalhos de análise de fluxo que são executados dentro essa região. Você tem a opção para escolher uma conta de armazenamento existente ou crie um novo.

3.  Clique em **Análise de fluxo** no painel à esquerda para listar os trabalhos de análise de fluxo.

    ![Ícone do serviço de análise de fluxo](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    O novo trabalho será exibido com um status de **criado**. Observe que o botão **Iniciar** na parte inferior da página está desativado. Você deve configurar a entrada de trabalho, saída e consulta antes de iniciar o trabalho.

### <a name="specify-job-input"></a>Especifique a entrada de trabalho
1.  No seu trabalho de análise de fluxo, clique **ENTRADAS** na parte superior da página e clique em **Adicionar entrada**. A caixa de diálogo que é aberta apresentará várias etapas para configurar a sua opinião.
2.  Clique em **fluxo de dados**e clique no botão à direita.
3.  Clique **HUB de evento**e, em seguida, clique no botão à direita.
4.  Digite ou selecione os seguintes valores na terceira página:

    * **ALIAS de entrada**: insira um nome amigável, como *CallStream*, para este trabalho. Observe que você usará esse nome na consulta mais tarde.

    * **HUB de evento**: se o hub de eventos que você criou na mesma assinatura como o trabalho de análise de fluxo, marque o namespace que o hub de evento está em.

        Se seu hub de evento estiver em uma assinatura diferente, selecione **Hub de eventos de uso de outra assinatura**e digite as informações para **O NAMESPACE de barramento de serviço**, **Nome do HUB de evento**, **Nome de política do HUB de evento**, **Chave de política de HUB de evento**e **Contagem de partição de HUB de eventos**manualmente.

    * **Nome do HUB de evento**: selecione o nome do hub do evento.

    * **Nome de política do HUB de evento**: selecione a política de hub de eventos que você criou anteriormente neste tutorial.

    * **Grupo de consumidor do HUB de evento**: digite o nome do grupo de consumidor que você criou anteriormente neste tutorial.

5.  Clique no botão à direita.
6.  Especifique os seguintes valores:

    * **Formato de SERIALIZADOR de evento**: JSON
    * **CODIFICAÇÃO**: UTF8
7.  Clique no botão **Verificar** para adicionar esta fonte e verificar que a análise de fluxo pode se conectar à hub do evento.

### <a name="specify-job-query"></a>Especificar a consulta de trabalho

Análise de fluxo é compatível com um modelo de consulta simples e declarativa que descreve transformações para processamento em tempo real. Para saber mais sobre a linguagem, consulte a [Referência de linguagem de consulta de análise do Azure fluxo](https://msdn.microsoft.com/library/dn834998.aspx). Este tutorial ajudará você a criar e testar várias consultas sobre seu fluxo em tempo real dos dados de chamada.

#### <a name="optional-sample-input-data"></a>Opcionais: Dados de entrada de amostra
Para validar sua consulta em relação aos dados de trabalho real, você pode usar o recurso de **Dados de exemplo** para extrair eventos de seu fluxo e criar um. Arquivo de JSON dos eventos para teste.  As etapas a seguir mostram como fazer isso. Também fornecemos um arquivo de [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) de exemplo para fins de teste.

1.  Selecione a entrada de hub de evento e, em seguida, clique em **Dados de exemplo** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, especifique uma **hora de início** para iniciar a coleta de dados e uma **duração** para a quantidade de dados adicional consumir.
3.  Clique no botão **Verificar** para iniciar dados de amostragem provenientes da entrada.  Pode demorar um ou dois minutos para o arquivo de dados a serem produzidos.  Quando o processo for concluído, clique em **detalhes**, baixe o gerado. JSON de arquivo e salve-o.

    ![Baixar e salvar dados processados em um arquivo JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Consulta passagem

Se você quiser arquivar todos os eventos, você pode usar uma consulta passagem para ler todos os campos na carga do evento ou da mensagem. Para começar, faça uma consulta passagem simple que todos os campos em um evento de projetos.

1.  Clique em **consulta** da parte superior da página de análise de fluxo de trabalho.
2.  Adicione o seguinte para o editor de código:

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] Certifique-se de que o nome da fonte de entrada corresponde ao nome da entrada que você especificou anteriormente.

3.  Clique em **Testar** em editor de consulta.
4.  Fornece um arquivo de teste. Use um criado usando as etapas anteriores, ou [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.  Clique no botão **Verificar** e ver os resultados exibidos abaixo a definição de consulta.

    ![Resultados de definição de consulta](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Projeção de coluna

Agora podemos vai reduzir os campos retornados para um conjunto menor.

1.  Altere a consulta no editor de código para:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Clique em **executar novamente** em editor de consultas para ver os resultados da consulta.

    ![Saída no editor de consulta.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Contagem de entrada chama por região: janela queda com agregação

Para comparar o número de chamadas de entrada por região, usaremos um [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obter a contagem de chamadas de entrada agrupados por **SwitchNum** a cada cinco segundos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Essa consulta usa a palavra-chave **Carimbo de hora por** para especificar um campo de carimbo de hora na carga a ser usado na computação temporal. Se esse campo não foi especificado, a operação de janelas seria executada usando a hora em que cada evento chegou no hub do evento. Consulte a [referência de linguagem de consulta de "Chegada tempo Vs aplicativo hora" na análise de fluxo](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Observe que você pode acessar um carimbo de hora para o final de cada janela usando a propriedade **System.Timestamp** .

2.  Clique em **executar novamente** em editor de consultas para ver os resultados da consulta.

    ![Resultados de consulta para Timestand por](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Detecção de fraudes SIM com uma associação a mesmo

Para identificar o uso potencialmente fraudulento, vamos para chamadas que se originam do mesmo usuário, mas em locais diferentes em menos de 5 segundos.  Podemos [ingressar](https://msdn.microsoft.com/library/azure/dn835026.aspx) o fluxo de eventos de chamada com o próprio para verificar se há nesses casos.

1.  Altere a consulta no editor de código para:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Clique em **executar novamente** em editor de consultas para ver os resultados da consulta.

    ![Resultados de consulta de uma junção](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Criar receptor de saída

Agora que definimos um fluxo de evento, um hub de eventos de entrada para inclusão de eventos e uma consulta para executar uma transformação sobre o fluxo, a última etapa é definir um receptor de saída para o trabalho. Vamos escrever eventos para comportamento fraudulento ao armazenamento de Blob do Azure.

Use as seguintes etapas para criar um contêiner para armazenamento de Blob se ainda não tiver uma.

1.  Usar uma conta de armazenamento existente ou crie uma nova conta de armazenamento clicando **Novo > Serviços de dados > armazenamento > criar rápida**e siga as instruções.
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

## <a name="start-job-for-real-time-processing"></a>Iniciar o trabalho para processamento em tempo real

Porque uma entrada de trabalho, a consulta e saída todos forem especificados, podemos está prontos para iniciar o trabalho de análise de fluxo para detecção de fraude em tempo real.

1.  Do **painel**de trabalho, clique em **Iniciar** na parte inferior da página.
2.  Na caixa de diálogo que é aberta, clique em **horário de trabalho Iniciar**e clique no botão **Verificar** na parte inferior da caixa de diálogo. O status do trabalho será alterado para **Iniciar** e em breve se transformará em **execução**.

## <a name="view-fraud-detection-output"></a>Saída de detecção de fraude do modo de exibição

Use uma ferramenta como [Azure Storage Explorer](http://storageexplorer.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) para exibir eventos fraudulentos como eles são gravados para a saída em tempo real.  

![Detecção de fraude: eventos fraudulentos vistos em tempo real](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [Fórum de análise de fluxo de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
