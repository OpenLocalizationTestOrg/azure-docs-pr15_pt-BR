<properties
    pageTitle="Introdução ao Azure fluxo análise para processar dados de dispositivos de IoT. | Microsoft Azure"
    description="Fluxos de marcas e dados de sensor de IoT com análises de fluxo e processamento de dados em tempo real"
    keywords="solução de IOT, Introdução ao iot"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/19/2016"
    ms.author="jeffstok"
/>

# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introdução ao Azure fluxo análise para processar dados de dispositivos de IoT

Neste tutorial, você aprenderá a criar a lógica de processamento de fluxo para coletar dados de dispositivos de Internet das coisas (IoT). Usaremos um caso de uso de Internet das coisas (IoT) reais para demonstrar como criar uma solução rápida e econômica.

## <a name="prerequisites"></a>Pré-requisitos

-   [Assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Arquivos de consulta e dados de exemplo para download do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Cenário

Contoso, que é uma empresa no espaço de automação industrial, completamente tem automatizadas seu processo de fabricação. O maquinário nesta planta tem sensores que são capazes de emitir fluxos de dados em tempo real. Neste cenário, um gerente de plantas de produção quer tenha ideias em tempo real dos dados sensor para procurar padrões e executar ações em-los. Usaremos o idioma de consulta de análise de fluxo (SAQL) sobre os dados de sensor para localizar padrões interessantes do fluxo de entrada de dados.

Veja os dados estão sendo gerados em um dispositivo de marca de sensor Texas instrumentos.

![Marca de sensor Texas instrumentos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

A carga de dados está no formato JSON e a seguinte aparência:


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

Em um cenário de mundo real, você poderia ter centenas desses sensores gerar eventos como um fluxo. Ideal seria um dispositivo gateway seria executar código para esses eventos por push para [Hubs de evento do Azure](https://azure.microsoft.com/services/event-hubs/) ou [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). O trabalho de análise de fluxo seria esses eventos de evento Hubs de inclusão e executar consultas de análise em tempo real contra os fluxos. Em seguida, você pode enviar os resultados para uma das [saídas de suporte](stream-analytics-define-outputs.md).

Para facilidade de uso, este guia de Introdução ao obter fornece um arquivo de dados de exemplo, que foi capturado de dispositivos de marca de sensor real. Você pode executar consultas nos dados de exemplo e ver os resultados. Tutoriais subsequente, você aprenderá como conectar seu trabalho às entradas e saídas e implantá-los para o serviço do Azure.

## <a name="create-a-stream-analytics-job"></a>Criar uma a análise de fluxo de trabalho

1. No [portal do Azure](http://portal.azure.com), clique no sinal e digite **A análise de fluxo** na janela de texto para a direita. Selecione **a análise de fluxo de trabalho** na lista de resultados.

    ![Criar uma nova a análise de fluxo de trabalho](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Insira um nome de trabalho exclusiva e verifique se que a assinatura é o correto para o seu trabalho. Em seguida, crie um novo grupo de recursos ou selecione um existente na sua assinatura.

3. Selecione um local para o seu trabalho. Para a velocidade de processamento e redução dos custos em dados recomenda-se transferência selecionando no mesmo local como o grupo de recursos e a conta de armazenamento pretendido.

    ![Criar um novo trabalho de análise de fluxo detalhes](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

    > [AZURE.NOTE] Você deve criar essa conta de armazenamento apenas uma vez por região. Esse armazenamento será compartilhado entre todos os trabalhos de análise de fluxo que são criados nessa região.

4. Marque a caixa para colocar seu trabalho em seu painel e, em seguida, clique em **criar**.

    ![criação de trabalho em andamento](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)

5. Você deve ver uma implantação' iniciada...' exibida no canto superior direito da janela do navegador. Assim será alterado para uma janela concluída conforme mostrado abaixo.

    ![criação de trabalho em andamento](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>Criar uma consulta de análise de fluxo do Azure

Depois que seu trabalho é criado é hora para abri-la e criar uma consulta. Você pode acessar facilmente seu trabalho clicando no bloco para ele.

![Bloco do trabalho](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

No painel **Topologia de trabalho** , clique na caixa de **consulta** para ir para o Editor de consulta. O editor de **consulta** permite que você insira uma consulta de T-SQL que executa a transformação sobre os dados de eventos de entrada.

![Caixa de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Consulta: Arquivar seus dados brutos

A forma mais simples de consulta é uma consulta passagem que arquivará todos os dados de entrada para sua saída designado. Baixe o arquivo de dados de exemplo do [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) para um local no seu computador. 

1. Cole a consulta do arquivo PassThrough.txt. 

    ![Fluxo de entrada de teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

2. Clique nas reticências ao lado de sua entrada e selecione a caixa **carregar dados de exemplo do arquivo** .

    ![Fluxo de entrada de teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)

3. Um painel é aberto à direita como resultado, em Selecione o arquivo de dados de HelloWorldASA InputStream.json de seu local baixado e clique **Okey** na parte inferior do painel.

    ![Fluxo de entrada de teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)

4. Clique na engrenagem **Testar** na parte superior esquerda da área da janela e processar a sua consulta de teste contra o conjunto de dados de exemplo. Será aberta uma janela de resultados abaixo de sua consulta conforme o processamento é concluído.

    ![Resultados de teste](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Consulta: Filtrar os dados com base em uma condição

Vamos experimentar filtrar os resultados com base em uma condição. Queremos mostrar resultados para somente os eventos que vêm "sensorA". A consulta é no arquivo Filtering.txt.

![Filtrando um fluxo de dados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Observe que a consulta diferencia maiusculas de minúsculas se compara a um valor de cadeia de caracteres. Clique na engrenagem **teste** novamente para executar a consulta. A consulta deve retornar 389 linhas fora 1860 eventos.

![Segundo resultados de saída de teste de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Consulta: Alerta disparar um fluxo de trabalho de negócios

Vamos fazer nossa consulta mais detalhada. Para cada tipo de sensor, queremos monitorar a temperatura média por 30 segundos janela e exibir resultados somente se a temperatura média for acima de 100 graus. Podemos gravará a seguinte consulta e, em seguida, clique em **teste** para ver os resultados. A consulta é no arquivo ThresholdAlerting.txt.

![consulta de filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Agora você deve ver resultados que contenham apenas 245 linhas e nomes de sensores onde a temperatura média é maior que 100. Essa consulta agrupa o fluxo de eventos por **dspl**, que é o nome de sensor, em uma **Janela de queda** de 30 segundos. Consultas temporais devem Declare como queremos hora de andamento. Usando a cláusula **Carimbo de hora por** , podemos ter especificado a coluna **OUTPUTTIME** para associar vezes todos os cálculos Temporais. Para obter informações detalhadas, leia os artigos do MSDN sobre [Gerenciamento de tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [funções de janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Consulta: Ausência de eventos de detecção

Como podemos escrever uma consulta para encontrar uma falta de eventos de entrada? Vamos localizar a última vez em que um sensor enviado dados e, em seguida, não enviou eventos para o próximo minuto. A consulta é no arquivo AbsenseOfEvent.txt.

![Detectar ausência de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Aqui, usamos uma junção **Externa esquerda** para o mesmo fluxo de dados (associação a mesmo). Para uma junção **interna** , um resultado é retornado apenas quando uma correspondência for encontrada.  Para uma junção **Externa esquerda** , se um evento do lado esquerdo da junção é não coincidente, uma linha que tem nulo para todas as colunas do lado direito é retornada. Essa técnica é muito útil para encontrar uma ausência de eventos. Consulte nossa documentação MSDN para obter mais informações sobre como [INGRESSAR](https://msdn.microsoft.com/library/azure/dn835026.aspx).

## <a name="conclusion"></a>Conclusão

O objetivo deste tutorial é demonstrar como escrever consultas de linguagem de consulta do fluxo Analytics diferentes e ver os resultados no navegador. No entanto, isso está começando a. Você pode fazer muito mais com a análise de fluxo. Análise de fluxo dá suporte a uma variedade de entradas e saídas e ainda pode usar funções em aprendizado de máquina do Azure para torná-lo uma ferramenta robusta para analisar fluxos de dados. Você pode começar a explorar mais sobre a análise de fluxo, usando nosso [mapa de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Para obter mais informações sobre como escrever consultas, leia o artigo sobre [padrões comuns de consulta](./stream-analytics-stream-analytics-query-patterns.md).
