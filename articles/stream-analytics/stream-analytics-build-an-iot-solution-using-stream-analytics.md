<properties
    pageTitle="Criar uma solução IoT usando a análise de fluxo | Microsoft Azure"
    description="Tutorial guia de Introdução para a solução de IoT de análise de fluxo de um cenário de pedágio"
    keywords="solução de IOT, funções de janela"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Criar uma solução IoT usando a análise de fluxo

## <a name="introduction"></a>Introdução

Neste tutorial, você aprenderá como usar a análise de fluxo Azure para obter ideias em tempo real dos seus dados. Os desenvolvedores podem facilmente combinar fluxos de dados, como fluxos de clique, logs e eventos gerados pelo dispositivo com registros históricos ou dados de referência para gerar ideias de negócios. Como um serviço de computação fluxo totalmente gerenciado, em tempo real que está hospedado no Microsoft Azure, a análise de fluxo Azure fornece resiliência interna, baixa latência e escalabilidade obter você para cima e em execução em minutos.

Depois de concluir este tutorial, você será capaz de:

-   Familiarize-se com o portal de análise de fluxo de Azure.
-   Configurar e implantar um fluxo de trabalho.
-   Articular problemas reais e resolvê-los usando a linguagem de consulta de análise do fluxo.
-   Desenvolva soluções de fluxo para seus clientes usando a análise de fluxo com confiança.
-   Use o monitoramento e a experiência de log para solucionar problemas.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará os seguintes pré-requisitos para concluir este tutorial:

-   A versão mais recente do [PowerShell do Azure](../powershell-install-configure.md)
-   Visual Studio 2015 ou o gratuito para a [Comunidade do Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   Uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilégios administrativos no computador
-   Download do [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) do Centro de Download da Microsoft
-   Opcional: Código-fonte para o gerador de evento TollApp em [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>Introdução ao cenário: "Olá, Chamada Tarifada!"


Uma estação de Chamada Tarifada é um fenômeno comuns. Você os encontre em muitos expressways, pontes e encapsulamentos em todo o mundo. Cada estação de Chamada Tarifada tem vários estande de Chamada Tarifada. No estande manual, você parar para pagar Tarifada para um atendedor. No estande automatizado, um sensor na parte superior de cada estande digitaliza uma placa RFID que é fixada o para-brisa do seu veículo conforme você passa o estande de Chamada Tarifada. É fácil visualizar a passagem de veículos por meio dessas estações de Chamada Tarifada como um fluxo de evento sobre quais operações interessantes podem ser realizadas.

![Imagem de carros no estande de Chamada Tarifada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Dados de entrada

Este tutorial funciona com dois fluxos de dados. Sensores instalados na entrada e saída das estações Chamada Tarifada produzem o primeiro fluxo. O segundo fluxo é um conjunto de dados de pesquisa estático que contém dados de registro do veículo.

### <a name="entry-data-stream"></a>Fluxo de dados de entrada

O fluxo de dados de entrada contém informações sobre carros como inserirem estações de Chamada Tarifada.


| TollID | EntryTime               | LicensePlate | Estado | Fazer   | Modelo   | VehicleType | VehicleWeight | Chamada Tarifada | Marca       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 12:01:00.000 de 2014-09-10 | JNB 7001     | NY    | Honda  | CRV     | 1           | 0             | 7    |           |
| 1      | 12:02:00.000 de 2014-09-10 | YXZ 1001     | NY    | Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 12:02:00.000 de 2014-09-10 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 12:03:00.000 de 2014-09-10 | XYZ 1003     | CT    | Toyota | Corolla | 1           | 0             | 4    |           |
| 1      | 12:03:00.000 de 2014-09-10 | BNJ 1007     | NY    | Honda  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 12:05:00.000 de 2014-09-10 | CDE 1007     | NJ    | Toyota | 4 x 4     | 1           | 0             | 6    | 321987654 |


Aqui está uma breve descrição das colunas:

| Coluna | Descrição |
|--------------|--------------------------------------------------------------------|
| TollID       | A ID de estande de Chamada Tarifada que identifica exclusivamente um estande de Chamada Tarifada            |
| EntryTime    | A data e hora de entrada do veículo para o estande de Chamada Tarifada no UTC |
| LicensePlate | O número de placa de licença do veículo                            |
| Estado        | Um estado nos Estados Unidos                                           |
| Fazer         | O fabricante do automóvel                                 |
| Modelo        | O número de modelo do automóvel                                 |
| VehicleType  | 1 para veículos passageiro ou 2 para veículos comerciais       |
| WeightType   | Peso de veículo em toneladas; 0 para veículos passageiro                   |
| Chamada Tarifada         | O valor de Chamada Tarifada em USD                                              |
| Marca          | A marca e sobre o automóvel que automatiza pagamento; espaço em branco onde o pagamento foi feito manualmente |


### <a name="exit-data-stream"></a>Fluxo de dados de saída

O fluxo de dados de saída contém informações sobre carros deixando a estação de Chamada Tarifada.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CDE 1007         |

Aqui está uma breve descrição das colunas:


| Coluna | Descrição |
|--------------|-----------------------------------------------------------------|
| TollID       | A ID de estande de Chamada Tarifada que identifica exclusivamente um estande de Chamada Tarifada         |
| ExitTime     | A data e hora de sair do veículo do estande de Chamada Tarifada no UTC |
| LicensePlate | O número de placa de licença do veículo                         |

### <a name="commercial-vehicle-registration-data"></a>Dados de registro do veículo comercial

O tutorial usa um instantâneo estático de um banco de dados de registro do veículo comercial.


| LicensePlate | RegistrationId | Expirado |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| OAT 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Aqui está uma breve descrição das colunas:


| Coluna | Descrição |
|--------------|-----------------------------------------------------------------|
| LicensePlate | O número de placa de licença do veículo                         |
| RegistrationId     | Identificação de registro do veículo |
| Expirado | O status de registro do veículo: 0 se registro do veículo estiver ativo, 1 se o registro está vencido                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar o ambiente para análise de fluxo do Azure

Para concluir este tutorial, você precisa de uma assinatura do Microsoft Azure. A Microsoft oferece uma avaliação gratuita para serviços do Microsoft Azure.

Se você não tiver uma conta do Azure, você pode [solicitação de uma versão de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Para inscrever-se para uma avaliação gratuita, você precisa de um dispositivo móvel que possa receber mensagens de texto e um cartão de crédito válido.

Certifique-se de seguir as etapas na seção "Limpar sua conta do Azure" no final deste artigo para que você possa fazer o melhor uso de seus US $200 livre crédito Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Provisionar Azure recursos necessários para o tutorial

Este tutorial requer dois hubs de evento para receber fluxos de dados de *entrada* e *Sair* . Banco de dados do SQL Azure mostra os resultados dos trabalhos de análise de fluxo. Armazenamento do Azure armazena dados de referência sobre registros de veículo.

Você pode usar o script Setup.ps1 na pasta TollApp no GitHub para criar todos os recursos necessários. Devido ao tempo, recomendamos que você executá-lo. Se você gostaria de saber mais sobre como configurar esses recursos no portal do Azure, consulte o apêndice "Configurar recursos tutoriais no portal Azure".

Baixar e salve a pasta de [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) de suporte e arquivos.

Abra um **Microsoft Azure PowerShell** janela _como administrador_. Se você ainda não tiver Azure PowerShell, siga as instruções em [instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalá-lo.

Como o Windows automaticamente bloqueia. ps1,. dll e arquivos de .exe, você precisa definir a política de execução antes de executar o script. Verifique se que a janela do PowerShell do Azure está executando _como administrador_. Execute **Set-ExecutionPolicy irrestrito**. Quando solicitado, digite **Y**.

![Captura de tela de "Set-ExecutionPolicy irrestrito" executando na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Execute **Get-ExecutionPolicy** para certificar-se de que o comando funcionou.

![Captura de tela de "Get-ExecutionPolicy" executando na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Vá para o diretório que tem o aplicativo de gerador e scripts.

![Captura de tela de "cd .\TollApp\TollApp" executando na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Tipo de **.\\ Setup.ps1** para configurar sua conta do Azure, criar e configurar os recursos necessários e comece a gerar eventos. O script seleciona aleatoriamente uma região para criar seus recursos. Para especificar explicitamente uma região, você pode passar o **-local** parâmetro como no exemplo seguinte:

**. \\Setup.ps1-local "Central EUA"**

![Captura de tela da página de entrada Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

O script abre **Na** página de entrada do Microsoft Azure. Insira suas credenciais de conta.

> [AZURE.NOTE] Se sua conta tem acesso a várias assinaturas, você será solicitado a digitar o nome da assinatura que você deseja usar para o tutorial.

O script pode levar alguns minutos para executar. Depois de terminar, a saída deve ter a aparência seguinte captura de tela.

![Captura de tela de saída do script na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Você também verá outra janela que é semelhante à seguinte captura de tela. Este aplicativo está enviando eventos para Hubs de evento do Azure, que é necessário para executar o tutorial. Portanto, não interromper o aplicativo ou fechar esta janela até concluir o tutorial.

![Captura de tela de "Dados do hub de evento enviando"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Você deve ser capaz de ver todos os recursos criados no portal Azure agora. Vá para <https://manage.windowsazure.com>e entre com suas credenciais de conta.

### <a name="azure-event-hubs"></a>Hubs de evento Azure

Clique em **Barramento de serviço** no lado esquerdo do portal do Azure para ver hubs de evento que o script criado na seção anterior.

![Barramento de serviço](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Você verá todos os namespaces disponíveis em sua assinatura. Clique naquele que começa com *tolldata* (tolldata4637388511 em nosso exemplo). Clique na guia **Evento HUBS** .

![Guia de Hubs de evento no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Você verá dois hubs de evento chamados de *entrada* e *Sair* criado neste namespace.

![Captura de tela de "entrada" e "saída" hubs de evento no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Contêiner de armazenamento de Azure

1. Clique em **armazenamento** no lado esquerdo do portal do Azure para ver o contêiner de armazenamento do Azure que é usado no tutorial.

    ![Item de menu de armazenamento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Clique naquele que começam com *tolldata* (tolldata4637388511 em nosso exemplo). Clique na guia **CONTÊINERES** para ver o contêiner criado.

    ![Guia de contêineres no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Clique no contêiner de **tolldata** para ver o arquivo JSON carregado com dados de registro do veículo.

    ![Captura de tela do arquivo registration.json no contêiner](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Banco de dados do SQL Azure

1. Clique em **Bancos de dados SQL** no lado esquerdo do portal do Azure para ver o banco de dados do SQL que será usado no tutorial.

    ![Bancos de dados SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Clique em **tolldatadb**.

    ![Captura de tela do banco de dados SQL criado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Copiar o nome do servidor sem o número da porta (*nomedoservidor*. database.windows.net, por exemplo).

## <a name="connect-to-the-database-from-visual-studio"></a>Conectar-se ao banco de dados do Visual Studio

Use o Visual Studio para acessar os resultados de consulta do banco de dados de saída.

Conectar-se ao banco de dados SQL (o destino) do Visual Studio:

1. Abra o Visual Studio e, em seguida, clique em **Ferramentas** > **conectar ao banco de dados**.

2. Se solicitado, clique em **Microsoft SQL Server** como uma fonte de dados.

    ![Caixa de diálogo Alterar fonte de dados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. No campo **nome do servidor** , cole o nome que você copiou na seção anterior do portal do Azure (ou seja, *nomedoservidor*. database.windows.net).

4. Clique em **usar autenticação do SQL Server**.

5. Digite **tolladmin** no campo **nome de usuário** e **123toll!** no campo **senha** .

6. Clique em **Selecionar ou digite um nome de banco de dados**e selecione **TollDataDB** como o banco de dados.

    ![Adicionar caixa de diálogo de Conexão](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Clique em **Okey**.

8. Abra o Explorador de servidor.

    ![Explorador de servidor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Consulte quatro tabelas do banco de dados de TollDataDB.

    ![Tabelas do banco de dados de TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Gerador de evento: TollApp projeto de amostra

O script do PowerShell automaticamente começa a enviar eventos usando o programa de aplicativo de amostra de TollApp. Você não precisa executar as etapas adicionais.

No entanto, se você estiver interessado em detalhes de implementação, você pode encontrar o código-fonte do aplicativo TollApp GitHub [Amostras/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Captura de tela do código de amostra exibido no Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma a análise de fluxo de trabalho

1. No portal do Azure, abra a análise de fluxo e clique em **novo** no canto inferior esquerdo da página para criar um novo trabalho de análise.

    ![Novo botão](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Clique em **criar rápida**. Selecione a mesma região onde seus outros recursos são criados pelo script.

3. Para a configuração de **Conta de armazenamento de monitoramento REGIONAL** , selecione **Criar nova conta de armazenamento** e dê um nome exclusivo. Azure Analytics fluxo usará essa conta para armazenar informações de monitoramento para todos os trabalhos futuras.

4. Clique em **Criar trabalho de análise de fluxo** na parte inferior da página.

    ![Opção de trabalho de análise de fluxo criar](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Defina as fontes de entrada

1. Clique no trabalho analytics criados no portal.

    ![Captura de tela do trabalho analytics no portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Clique na guia de **ENTRADAS** para definir a fonte de dados.

    ![Na guia de entradas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Clique em **Adicionar uma entrada**.

    ![Adicionar uma opção de entrada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. Clique em **fluxo de dados** na primeira página.

    ![A opção de fluxo de dados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. Clique em **HUB de evento** na segunda página do assistente.

    ![A opção de Hub de evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Digite **EntryStream** como **ALIAS de entrada**.

7. Clique na lista suspensa do **Hub de evento** e selecione aquela que começa com "TollData" (por exemplo, TollData9518658221).

8. Selecione a **entrada** como o nome do evento hub e **tudo** como o nome de política do hub de evento.

    Suas configurações serão parecida com:

    ![Configurações de hub de evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. Na próxima página, selecione **JSON** para o **Formato de SERIALIZAÇÃO do evento** e **UTF8** para **CODIFICAÇÃO**.

    ![Configurações de serialização](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Clique em **Okey** na parte inferior da página para concluir o assistente.

    ![Captura de tela de EntryStream entrada no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    Agora que você criou o fluxo de entrada, você seguirá as mesmas etapas para criar o fluxo de saída. Na terceira página do assistente, certifique-se de inserir valores como no seguinte captura de tela.

    ![Configurações para o fluxo de saída](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    Você definiu dois fluxos de entrada:

    ![Fluxos de entrada definidos no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    Em seguida, você irá adicionar referência a entrada de dados para o arquivo de blob que contém dados de registro de carro.

11. Clique em **Adicionar entrada**e, em seguida, clique em **Dados de referência**.

    ![Opções de "Adicionar uma entrada" com dados de referência selecionada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. Na próxima página, selecione a conta de armazenamento que começa com **tolldata**. O nome do contêiner deve ser **tolldata**e o nome de blob em **Caminho padrão** deve ser **registration.json**. Esse nome de arquivo diferencia maiusculas de minúsculas e deve estar em minúscula.

    ![Configurações de armazenamento do blog](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Selecione os valores conforme mostrado na seguinte captura de tela na próxima página e, em seguida, clique em **Okey** para concluir o assistente.

    ![Seleção de JSON para "formato de serialização par" e UTF8 para "Codificação"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Agora todas as entradas são definidas.

![Captura de tela das três entradas definidas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Definir saída

1. Clique na guia de **saída** e clique em **Adicionar uma saída**.

    ![A guia de saída e a opção "Adicionar uma saída"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Clique em **banco de dados SQL**.

3. Selecione o nome do servidor que foi usado na seção "Conectar ao banco de dados do Visual Studio" do artigo. O nome do banco de dados é **TollDataDB**.

4. Digite **tolladmin** no campo **nome de usuário** , **123toll!** no campo **senha** e **TollDataRefJoin** no campo da **tabela** .

    ![Configurações de banco de dados SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Consulta de análise do Azure fluxo

Guia **consulta** contém uma consulta SQL que transforma os dados de entrada.

![Uma consulta adicionada à guia consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Este tutorial tenta perguntas várias empresas que estão relacionadas à chamada Tarifada consultas de análise de fluxo de dados e construções que podem ser usadas em análise de fluxo de Azure para fornecer uma resposta relevante.

Antes de iniciar o primeiro trabalho de análise de fluxo, vamos explorar alguns cenários e a sintaxe de consulta.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introdução a linguagem de consulta de análise de fluxo do Azure
-----------------------------------------------------

Digamos que você precisa contar o número de veículos que insere um estande de Chamada Tarifada. Porque este é um fluxo contínuo de eventos, você precisa definir um "período de tempo". Vamos modificar a pergunta para ser "veículos quantos inserir um estande de Chamada Tarifada cada três minutos?". Isso é normalmente chamado a contagem de queda.

Vamos examinar a consulta de análise de fluxo de Azure que responde a essa pergunta:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Como você pode ver, a análise de fluxo Azure usa uma linguagem de consulta como SQL e adiciona algumas extensões para especificar relacionadas ao tempo aspectos da consulta.

Para obter mais detalhes, leia sobre construções de [Gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) usadas na consulta do MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Teste de consultas de análise de fluxo do Azure

Agora que você escreveu sua primeira consulta de análise de fluxo de Azure, é hora de testá-lo usando arquivos de dados de exemplo localizados na pasta TollApp no caminho a seguir:

**.. \\TollApp\\TollApp\\dados**

Esta pasta contém os arquivos a seguir:

- Entry.JSON
- Exit.JSON
- Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Pergunta 1: Número de veículos inserindo um estande de Chamada Tarifada

1. Abra o portal do Azure e vá para o seu trabalho de análise de fluxo de Azure criado. Clique na guia da **consulta** e cole a consulta na seção anterior.

    ![Consulta colada na guia Query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Para validar essa consulta em relação a dados de exemplo, clique no botão de **teste** . Na caixa de diálogo que é aberta, vá para Entry.json, um arquivo que possui dados de exemplo do fluxo de evento **EntryTime** .

    ![Captura de tela do arquivo Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Valide que a saída da consulta é conforme esperado:

    ![Resultados do teste](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Pergunta 2: Relatar o tempo total para cada carro ao passar o estande de Chamada Tarifada

O tempo médio necessário para um carro passem Tarifada ajuda para avaliar a eficiência do processo e a experiência do cliente.

Para localizar o tempo total, você precisará ingressar o fluxo de EntryTime com o fluxo de ExitTime. Você ingressará os fluxos nas colunas TollId e LicencePlate. Operador **JOIN** requer que você especificar temporal movimento que descreve a diferença de tempo aceitável entre os eventos unidas. Função **DATEDIFF** vai ser usado para especificar que eventos devem ser não mais de 15 minutos entre si. Você também se aplicarão a função **DIFDATA** sair e horários de entrada para calcular o tempo real que um carro gasta na estação de Chamada Tarifada. Observe a diferença do uso de **DIFDATA** quando ele é usado em uma instrução **SELECT** em vez de uma condição **INGRESSAR** .

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Para testar esta consulta, atualize a consulta na guia **consulta** do seu trabalho:

    ![Consulta atualizada na guia Query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Clique em **Testar** e especifique os arquivos de entrada de exemplo para EntryTime e ExitTime.

    ![Captura de tela dos arquivos de entrada selecionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Marque a caixa de seleção para testar a consulta e exibir a saída:

    ![Saída do teste](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Pergunta 3: Relatar todos os veículos comerciais com registro vencido

A análise de fluxo Azure podem usar estáticos instantâneos dos dados para participar com fluxos de dados temporais. Para demonstrar esse recurso, use a pergunta de exemplo a seguir.

Se um veículo comercial estiver registrado com a empresa de Chamada Tarifada, ele pode passar através do estande de Chamada Tarifada sem ser interrompido para inspeção. Tabela de pesquisa de registro do veículo comercial vai ser usado para identificar todos os veículos comerciais que expiraram registros.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Para testar uma consulta usando dados de referência, você precisa definir uma fonte de entrada para os dados de referência, que você tenha feito.

1. Para testar esta consulta, cole a consulta na guia **consulta** , clique em **Testar**e especifique as duas fontes de entrada:

    ![Captura de tela dos arquivos de entrada selecionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Exiba a saída da consulta:

    ![Captura de tela da saída da consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Iniciar a a análise de fluxo de trabalho


Agora é hora de finalizar a configuração e iniciar o trabalho. Salvar a consulta da pergunta 3, que produzirá saída que coincida com o esquema da tabela de saída **TollDataRefJoin** .

Vá para o **painel**de trabalho e clique em **Iniciar**.

![Captura de tela do botão Iniciar no painel de trabalho](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Na caixa de diálogo que é aberta, altere a hora de **Saída iniciar** tempo **personalizada**. Altere a hora para uma hora antes da hora atual. Essa alteração garante que todos os eventos do hub evento são processados desde o início gerar os eventos no início do tutorial. Agora, clique na marca de seleção para iniciar o trabalho.

![Seleção de tempo personalizado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Iniciar o trabalho pode levar alguns minutos. Você pode ver o status na página de nível superior para a análise de fluxo.

![Captura de tela do status do trabalho](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Resultados da verificação no Visual Studio

1. Abra o Visual Studio Server Explorer e a tabela de **TollDataRefJoin** de atalho.
2. Clique em **Mostrar dados da tabela** para ver a saída do seu trabalho.

    ![Seleção de "Mostrar dados de tabela" no Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Dimensionar a análise de fluxo Azure trabalhos

A análise de fluxo Azure foi projetada para consumidor escala para que ele pode lidar com muitos dados. A consulta de análise de fluxo de Azure pode usar uma cláusula **Partição por** informar ao sistema que esta etapa será dimensionada check-out. **PartitionId** é uma coluna especial que o sistema adiciona para corresponder a ID de partição da entrada (hub de evento).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Parar o trabalho atual, atualize a consulta na guia **QUERY** e abra a guia **escala** .

    **Unidades de STREAMING** definem a quantidade de energia de computação que o trabalho pode receber.

2. Mova o controle deslizante para 6.

    ![Captura de tela da seleção 6 unidades streaming](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Vá para a guia de **SAÍDAS** e altere o nome da tabela SQL para **TollDataTumblingCountPartitioned**.

Se você iniciar o trabalho agora, a análise de fluxo Azure pode distribuir o trabalho em mais recursos de computação e atingir melhor taxa de transferência. Observe que o aplicativo de TollApp também está enviando eventos particionados por TollId.

## <a name="monitor"></a>Monitor

Na guia **MONITOR** contém estatísticas sobre o trabalho em execução.

![Captura de tela de estatísticas sobre trabalhos em execução](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Você pode acessar **Logs de operação** a partir da guia de **painel** .

![A opção "Logs de operação"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Captura de tela de logs de operação onde você pode ver o status de trabalhos](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Para ver informações adicionais sobre um evento específico, clique no evento e, em seguida, clique no botão **detalhes** .

![Captura de tela de detalhes sobre um evento selecionado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusão

Este tutorial introduziu você para o serviço de análise de fluxo de Azure. Ele demonstrou como configurar entradas e saídas para o trabalho de análise de fluxo. Usando o cenário de Chamada Tarifada dados, o tutorial explicado tipos comuns de problemas que podem surgir no espaço de dados em movimento e como eles podem ser resolvidos com simples consultas SQL no Azure fluxo Analytics. O tutorial descrito construções de extensão SQL para trabalhar com dados temporais. Ele mostrava como ingressar em fluxos de dados, como enriquecer o fluxo de dados com dados de referência estática e como dimensionar uma consulta para obter maior produtividade.

Embora esse tutorial fornece uma boa introdução, não é completa por qualquer meio. Você pode encontrar mais padrões de consulta usando a linguagem SAQL exemplos de [consulta para padrões de uso de análise de fluxo comuns](stream-analytics-stream-analytics-query-patterns.md).
Consulte a [documentação online](https://azure.microsoft.com/documentation/services/stream-analytics/) para saber mais sobre a análise de fluxo Azure.

## <a name="clean-up-your-azure-account"></a>Limpar a sua conta do Azure

1. Pare o trabalho de análise de fluxo no portal do Azure.

    O script Setup.ps1 cria dois hubs de evento e um banco de dados do SQL. As seguintes instruções ajudarão-lo a limpar recursos no final do tutorial.

2. Em uma janela do PowerShell, digite **.\\ CleanUp.ps1** para iniciar o script que exclui recursos usados no tutorial.

    > [AZURE.NOTE] Recursos são identificados pelo nome. Verifique se que você revisar cada item cuidadosamente antes de confirmar a remoção.

    ![Captura de tela do processo de limpeza](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)
