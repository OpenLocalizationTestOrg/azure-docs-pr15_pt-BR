<properties
    pageTitle="Painel do Power BI em análise de fluxo | Microsoft Azure"
    description="Use um painel de Power BI streaming em tempo real para reunir inteligência comercial e analisar dados de grande volume de um trabalho de análise de fluxo."
    keywords="Painel de ferramentas de análise, painel em tempo real"
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
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

#  <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Transmitir Analytics & Power BI: um painel de análise em tempo real para fluxo de dados

Azure análise de fluxo permite que você tire proveito de um das principais ferramentas de business intelligence, Microsoft Power BI. Saiba como usar a análise de fluxo Azure para analisar grande volume, streaming dados e obter a visão em um painel de análise do Power BI em tempo real.

Use [O Microsoft Power BI](https://powerbi.com/) para criar um painel live rapidamente. [Assista ao vídeo ilustrando o cenário](https://www.youtube.com/watch?v=SGUpT-a99MA).

Neste artigo, saiba como criar suas próprias ferramentas de inteligência de negócios personalizados usando o Power BI como uma saída para seus trabalhos de análise de fluxo do Azure e utilizar um painel em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

* Conta do Microsoft Azure
* Uma entrada para o trabalho de análise de fluxo consumir dados streaming de. Análise de fluxo aceita entrada do armazenamento de Hubs de evento do Azure ou Azure Blob.  
* Conta corporativa ou escolar do Power BI

## <a name="create-azure-stream-analytics-job"></a>Criar Azure a análise de fluxo de trabalho

No [Portal de clássico do Azure](https://manage.windowsazure.com), clique em **novo, serviços de dados, a análise de fluxo, criar rápida**.

Especifique os seguintes valores, clique em **criar a análise de fluxo de trabalho**:

* **Nome do trabalho** - Insira um nome de trabalho. Por exemplo, **DeviceTemperatures**.
* **Região** - selecione a região onde deseja que o trabalho localizado. Considere a possibilidade de colocar o trabalho e o hub de evento na mesma região para garantir um desempenho ideal e evitar os custos de transferência de dados entre regiões decorridos.
* **Conta de armazenamento** - escolha a conta de armazenamento que você gostaria de usar para armazenar dados de monitoramento para todos os trabalhos de análise de fluxo executando a essa região. Você tem a opção para escolher uma conta de armazenamento existente ou crie um novo.

Clique em **Análise de fluxo** no painel à esquerda para listar os trabalhos de análise de fluxo.

![graphic1][graphic1]

> [AZURE.TIP] O novo trabalho será listado com um status **Não iniciado**. Observe que o botão **Iniciar** na parte inferior da página está desativado. Este é o comportamento esperado como você deve configurar a entrada de trabalho, a saída, a consulta, e assim por diante antes de iniciar o trabalho.

## <a name="specify-job-input"></a>Especifique a entrada de trabalho

Para este tutorial, estamos supondo que você estiver usando o Hub de evento como uma entrada com serialização JSON e codificação UTF-8.

* Clique no nome de trabalho.
* Clique em **entradas** da parte superior da página e clique em **Adicionar entrada**. A caixa de diálogo que abre o orientará por meio de um número de etapas para configurar a sua opinião.
*   Selecione o **fluxo de dados**e clique no botão à direita.
*   Selecione **Hub de evento**e, em seguida, clique no botão à direita.
*   Digite ou selecione os seguintes valores na terceira página:
  * **Alias de entrada** - Insira um nome amigável para este trabalho de entrada. Observe que você usará esse nome na consulta posteriormente.
  * **Hub de evento** - se o Hub de eventos que você criou é na mesma assinatura como o trabalho de análise de fluxo, selecione o namespace que o hub de evento está em.
*   Se seu hub de evento estiver em uma assinatura diferente, selecione **Hub de eventos de uso de outra assinatura** e inserir manualmente as informações para o **Serviço barramento Namespace**, **Nome do Hub de evento**, **Nome de política do Hub de evento**, **Chave de política de Hub de evento**e **Contagem de partição de Hub de eventos**.

> [AZURE.NOTE]  Este exemplo usa o número padrão de partições, que é 16.

* **Nome do evento Hub** - selecione o nome do Hub de evento do Azure tiver.
* **Nome do evento Hub política** - selecione a política de Hub de evento para o Hub de evento que você está usando. Certifique-se de que essa política tem gerenciar permissões.
*   **Grupo de consumidor do Hub de evento** – você pode deixar essa vazia ou especificar um grupo de consumidor que você tem no seu Hub de evento. Observe que cada grupo de consumidor de um Hub de evento pode ter apenas 5 leitores por vez. Portanto, decida grupo consumidor certo para o seu trabalho adequadamente. Se você deixar o campo em branco, ela usará o grupo de consumidor do padrão.

*   Clique no botão à direita.
*   Especifique os seguintes valores:
  * **Formato de serializador de evento** - JSON
  * **Codificação** - UTF8
*   Clique no botão de seleção para adicionar esta fonte e verificar que a análise de fluxo pode se conectar à Hub do evento.

## <a name="add-power-bi-output"></a>Adicionar saída do Power BI

1.  Clique em **saída** da parte superior da página e, em seguida, clique em **Adicionar saída**. Você verá que Power BI listado como uma opção de saída.

    ![graphic2][graphic2]  

2.  Selecione **Power BI** e clique no botão à direita.
3.  Você verá uma tela semelhante ao seguinte:

    ![graphic3][graphic3]  

4.  Nesta etapa, fornece uma conta corporativa ou de estudante para a saída de trabalho de análise de fluxo. Se você já tiver conta do Power BI, selecione **Autorizar agora**. Caso contrário, escolha **Inscreva-se agora**. [Aqui está um bom blog examinando detalhes de inscrição do Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]  

5.  Em seguida, você verá uma tela semelhante ao seguinte:

    ![graphic4][graphic4]  

Forneça os valores conforme abaixo:

* **Alias de saída** – você pode colocar qualquer alias de saída que seja fácil de fazer referência. Este alias de saída é especialmente útil se você decidir ter várias saídas para o seu trabalho. Nesse caso, você precisa se referir a essa saída em sua consulta. Por exemplo, vamos usar o valor de alias de saída = "OutPbi".
* **Nome do Dataset** - forneça um nome de conjunto de dados que deseja que seu Power BI saída ter. Por exemplo, vamos usar "pbidemo".
*   **Nome de tabela** - forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Digamos que chamamos "pbidemo". Atualmente, a saída do Power BI de trabalhos de análise de fluxo só pode ter uma tabela em um conjunto de dados.
*   **Espaço de trabalho** – selecione um espaço de trabalho em seu locatário do Power BI em qual conjunto de dados será criado.

>   [AZURE.NOTE] Você não explicitamente deve criar esse conjunto de dados e a tabela em sua conta do Power BI. Ela serão automaticamente criadas quando você iniciar o seu trabalho de análise de fluxo e o trabalho começa saída bombeamento no Power BI. Se sua consulta de trabalho não retornar nenhum resultado, o conjunto de dados e a tabela não serão criados.

*   Clique em **Okey** **Conexão de teste** e agora você saída configuração está concluída.

>   [AZURE.WARNING] Além disso, lembre-se de que se Power BI já tinha um conjunto de dados e a tabela com o mesmo nome fornecida neste trabalho de análise de fluxo, os dados existentes serão substituídos.


## <a name="write-query"></a>Escrever consulta

Vá para a guia da **consulta** do seu trabalho. Escreva sua consulta, que a saída dos quais você deseja no seu Power BI. Por exemplo, pode ser algo como a seguinte consulta SQL:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



Inicie o seu trabalho. Confirme se o seu hub de evento está recebendo eventos e sua consulta gera os resultados esperados. Se sua consulta saídas 0 linhas, tabelas e conjunto de dados do Power BI serão não ser criadas automaticamente.

## <a name="create-the-dashboard-in-power-bi"></a>Criar o painel do Power BI

Vá para [Powerbi.com](https://powerbi.com) e faça logon com sua conta corporativa ou escolar. Se a consulta de trabalho de análise de fluxo apresenta resultados, você verá que o dataset já foi criado:

![graphic5][graphic5]

Para criar o painel, vá para a opção de painéis e crie um novo painel.

![graphic6][graphic6]

Neste exemplo, podemos será ursos-"Demonstração Dashboard".

Agora, clique no conjunto de dados criado pelo seu a análise de fluxo de trabalho (pbidemo em nosso exemplo atual). Você será levado a uma página para criar um gráfico na parte superior deste dataset. Este é somente um exemplo dos relatórios que você pode criar:

Selecione Σ temp e campos de horário. Automaticamente, eles irão para valor e eixo do gráfico:

![graphic7][graphic7]

Com isso, você obterá automaticamente um gráfico como abaixo:

![graphic8][graphic8]

Na seção valor, clique no menu suspenso para baixo para temp e escolha **média** para a temperatura e no gráfico, clique em **visualização** e escolha o **gráfico de linhas**:

![graphic9][graphic9]

Agora, você obterá um gráfico de linha média ao longo do tempo.  Usando a opção de pin como abaixo, você pode fixar isso ao seu painel que você criou anteriormente:

![graphic10][graphic10]

Agora quando você vê o painel com este relatório fixado, você verá relatório atualizando em tempo real. Tente alterar os dados em seus eventos – especial temp ou algo parecido e você verá o efeito em tempo real de refletidas no seu painel.

Observação Este tutorial demonstrou como criar, mas um tipo de gráfico de um conjunto de dados. Power BI pode ajudá-lo a criar outras ferramentas de business intelligence do cliente para sua organização. Outro exemplo de um painel do Power BI, assista ao vídeo de [Introdução ao Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Para obter mais informações sobre como configurar uma saída de Power BI e utilizar grupos de Power BI, examine a [seção de Power BI](stream-analytics-define-outputs.md#power-bi) [saídas de análise de fluxo de Noções básicas sobre](stream-analytics-define-outputs.md "Noções básicas sobre a análise de fluxo de saídas"). Outro recurso útil para saber mais sobre como criar painéis com o Power BI é [painéis do Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limitações e práticas recomendadas

Power BI utiliza restrições concorrência e produtividade conforme descrito aqui: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Preços do Power BI")

Devido àqueles Power BI chega próprio mais naturalmente a casos onde a análise de fluxo Azure faz uma redução de carga de dados significativos.
Recomendamos o uso TumblingWindow ou HoppingWindow para garantir que o envio de dados seria no máximo 1 push/segundo e que sua consulta chega dentro dos requisitos de produtividade – você pode usar a seguinte equação para calcular o valor para dar sua janela em segundos:

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Como exemplo – se você tiver 1.000 dispositivos enviando dados cada segundo, você está no Power BI Pro SKU que ofereça suporte a 1.000.000 linhas/hora e você deseja obter a média de dados por dispositivo no Power BI você pode fazer no máximo um envio cada 4 segundos por dispositivo (conforme mostrado abaixo):  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Que significa que seria alterado a consulta original para:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### <a name="powerbi-view-refresh"></a>Atualização do modo de exibição de PowerBI

Uma pergunta comum é "Por que não o painel de atualização automática no PowerBI?".

Para fazer isso, no PowerBI utilizar p e r e fazer uma pergunta como "Valor máximo por temp onde o carimbo de hora é hoje" e fixar peça ao painel.

### <a name="renew-authorization"></a>Renovar autorização

Você precisará autenticar novamente a sua conta do Power BI se sua senha foi alterado desde que seu trabalho foi criado ou última autenticado. Se a autenticação multifator (MFA) está configurado no seu locatário do Azure Active Directory (AAD) você também precisará renovar autorização de Power BI cada 2 semanas. Um sintoma desse problema é nenhuma saída de trabalho e um "Erro de usuário autenticar" em Logs de operação:

![graphic12][graphic12]

Da mesma forma, se um trabalho tentar iniciar enquanto o token expirou, ocorrerá um erro e o início do trabalho falhará. O erro será parecido, como abaixo:

![Erro de validação de PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 
 

Para resolver esse problema, interromper o seu trabalho em execução e vá para a saída do Power BI.  Clique no link "Renovar autorização" e reinicie o trabalho da última vez interrompido para evitar a perda de dados.

![PowerBI renovação de validação](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

Depois que a autorização é atualizada com o Power BI, você verá um alerta verde na área de autorização:

![PowerBI renovação de validação](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png
