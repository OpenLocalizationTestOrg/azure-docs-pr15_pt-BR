<properties 
    pageTitle="Como criar um trabalho de processamento de análise de dados para a análise de fluxo | Microsoft Azure" 
    description="Criar um trabalho de processamento de análise de dados para a análise de fluxo | segmento de caminho de aprendizagem."
    keywords="processamento de análise de dados"
    documentationCenter=""
    services="stream-analytics"
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

# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Como criar um trabalho de processamento de análise de dados para a análise de fluxo

O recurso de nível superior no Azure fluxo Analytics é um trabalho de análise do fluxo.  Ele consiste em uma ou mais fontes de dados de entrada, uma consulta expressando a transformação de dados e um ou mais destinos de saída que os resultados são gravados. Juntas essas habilitar o usuário executar a análise de dados de processamento de streaming cenários de dados.

Para começar a usar a análise de fluxo, comece criando uma nova a análise de fluxo de trabalho.  Observe que esta ação não tem nenhuma cobranças implicações até o trabalho é iniciado.

1.  Entre no online do [Azure portal clássico](http://manage.windowsazure.com) ou o [portal do Azure](https://portal.azure.com/).
2.  No portal do: **Clique em novo**, clique em **Serviços de dados** ou **A análise de dados** dependendo do seu portal e clique em **Azure fluxo análises** ou **A análise de fluxo** e, em seguida, **Criar rápida**.

    ![Assistente de trabalho de processamento de análise de dados](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  

    ![Criar trabalho de processamento de análise de dados](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  

3.  Especifique as configurações desejadas para o trabalho de análise de fluxo.
    - Na caixa **Nome do trabalho** , insira um nome para identificar a a análise de fluxo de trabalho. Quando o **Nome do trabalho** é validada, uma marca de seleção verde aparece na caixa Nome do trabalho. O **Nome do trabalho** pode conter apenas caracteres alfanuméricos e a '-' caracteres e deve estar entre 3 e 63 caracteres.
    - Use **região** no portal do Azure ou **local** no portal do Azure para especificar a localização geográfica onde você deseja executar o trabalho.
    - Se usando o portal do Azure, selecione ou crie uma conta de armazenamento para usar como a **Conta de armazenamento de monitoramento regionais**. Esta conta de armazenamento é usada para armazenar dados de monitoramento para todos os trabalhos de análise de fluxo executando nesta região.
    - Se usando o portal do Azure, especifique um **Grupo de recursos** novos ou existentes para armazenar recursos relacionados para seu aplicativo.

4.  Quando as novas opções de trabalho de análise de fluxo estão configuradas, clique em **Criar trabalho de análise de fluxo**. Pode levar alguns minutos para o trabalho de análise de fluxo a ser criado. Para verificar o status, você pode monitorar o progresso no hub do notificações.

    ![Hub de notfications trabalho do processamento de análise de dados](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  

    ![Análise de dados de portal Azure processar trabalho cria trabalho](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  

5.  O novo trabalho será exibido com um status de **criado**. Observe que o botão **Iniciar** está desativado. Você deve configurar a entrada de trabalho, a consulta e a saída antes de iniciar o trabalho.

    ![Trabalho de processamento de análise de dados trabalho Status](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  

    ![Análise de dados de portal Azure status de trabalho do trabalho de processamento](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
