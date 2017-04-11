<properties
   pageTitle="Analisar dados no repositório de Lucerne de dados usando o Power BI | Microsoft Azure"
   description="Usar o Power BI para analisar dados armazenados no repositório de Lucerne de dados do Azure"
   services="data-lake-store" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analisar dados no repositório de Lucerne de dados usando o Power BI

Neste artigo, você aprenderá como usar o Power BI Desktop para analisar e visualizar dados armazenados no repositório de Lucerne de dados do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Conta de armazenamento de Lucerne de dados do azure**. Siga as instruções contidas em [Introdução ao armazenamento de Lucerne de dados do Azure usando o Portal do Azure](data-lake-store-get-started-portal.md). Este artigo pressupõe que você já criou uma conta de armazenamento de Lucerne de dados, chamada **mybidatalakestore**e carregar um arquivo de dados de exemplo (**Drivers.txt**) a ele. Este exemplo de arquivo está disponível para download do [Azure dados Lucerne gito repositório](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).

- **Área de trabalho do power BI**. Você pode baixá-lo do [Centro de Download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 


## <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

1. Inicie o Power BI Desktop no seu computador.

2. Na **página inicial** faixa de opções, clique em **Obter dados**e, em seguida, clique em mais. Na caixa de diálogo **Obter dados** , clique em **Azure**, clique em **Repositório de Lucerne de dados do Azure**e clique em **Conectar**.

    ![Conectar ao armazenamento de Lucerne de dados] (./media/data-lake-store-power-bi/get-data-lake-store-account.png "Conectar ao armazenamento de Lucerne de dados")

3. Se você vir uma caixa de diálogo sobre o conector sendo em uma fase de desenvolvimento, optar por continuar.

4. Na caixa de diálogo **Microsoft Azure dados Lucerne Store** , fornecer a URL de sua conta de armazenamento de Lucerne de dados e clique em **Okey**.

    ![URL do repositório de Lucerne de dados] (./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL do repositório de Lucerne de dados")

5. Na próxima caixa de diálogo, clique em **entrar** entre na conta de armazenamento de Lucerne de dados. Você será redirecionado para a página de entrada da sua organização. Siga os prompts para entrar na conta.

    ![Entrar no repositório de Lucerne de dados] (./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Entrar no repositório de Lucerne de dados")

6. Depois que você tiver conectado com êxito, clique em **Conectar**.

    ![Conectar ao armazenamento de Lucerne de dados] (./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Conectar ao armazenamento de Lucerne de dados")

7. Próxima caixa de diálogo mostra o arquivo que você carregou em sua conta de armazenamento de Lucerne de dados. Verifique se as informações e, em seguida, clique em **carregar**.

    ![Carregar dados Lucerne do armazenamento de dados] (./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Carregar dados Lucerne do armazenamento de dados")

8. Após os dados tem sido carregados com êxito no Power BI, você verá os seguintes campos na guia **campos** .

    ![Campos importados] (./media/data-lake-store-power-bi/imported-fields.png "Campos importados")

    No entanto, para visualizar e analisar os dados, podemos preferir os dados a serem disponíveis por campos a seguir

    ![Campos de desejado] (./media/data-lake-store-power-bi/desired-fields.png "Campos de desejado")

    Nas próximas etapas, atualizaremos a consulta para converter os dados importados no formato desejado.

9. Na **página inicial** faixa de opções, clique em **Editar consultas**.

    ![Editar consultas] (./media/data-lake-store-power-bi/edit-queries.png "Editar consultas")

10. No Editor de consulta, na coluna **conteúdo** , clique em **binário**.

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query1.png "Editar consultas")

11. Você verá um ícone de arquivo, que representa o arquivo de **Drivers.txt** carregado. Clique com botão direito no arquivo e clique em **CSV**.  

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query2.png "Editar consultas")

12. Você deve ver uma saída conforme mostrado abaixo. Seus dados agora estão disponíveis em um formato que você pode usar para criar visualizações.

    ![Editar consultas] (./media/data-lake-store-power-bi/convert-query3.png "Editar consultas")

13. Na **página inicial** faixa de opções, clique em **Fechar e aplicar**e, em seguida, clique em **Fechar e aplicar**.

    ![Editar consultas] (./media/data-lake-store-power-bi/load-edited-query.png "Editar consultas")

14. Quando a consulta é atualizada, na guia **campos** mostrará os novos campos disponíveis para visualização.

    ![Campos de atualização] (./media/data-lake-store-power-bi/updated-query-fields.png "Campos de atualização")

15. Vamos crie um gráfico de pizza para representar os drivers em cada cidade de um determinado país. Para fazer isso, faça as seleções a seguintes.

    1. Na guia visualizações, clique no símbolo para um gráfico de pizza.

        ![Criar gráfico de pizza] (./media/data-lake-store-power-bi/create-pie-chart.png "Criar gráfico de pizza")

    2. As colunas que vamos usar são **coluna 4** (nome da cidade) e **7 de coluna** (nome do país). Arraste essas colunas da guia **campos** para a guia de **visualizações** conforme mostrado abaixo.

        ![Criar visualizações] (./media/data-lake-store-power-bi/create-visualizations.png "Criar visualizações")

    3. O gráfico de pizza agora deve ser semelhante como a mostrada abaixo.

        ![Gráfico de pizza] (./media/data-lake-store-power-bi/pie-chart.png "Criar visualizações")

16. Selecionando um país específico dos filtros de nível de página, agora você pode ver o número de drivers em cada cidade do país selecionado. Por exemplo, na guia de **visualizações** , sob os **filtros no nível da página**, selecione **Brasil**.

    ![Selecionar um país] (./media/data-lake-store-power-bi/select-country.png "Selecionar um país")

17. O gráfico de pizza é atualizado automaticamente para exibir os drivers nas cidades do Brasil.

    ![Drivers em um país] (./media/data-lake-store-power-bi/driver-per-country.png "Drivers por país")

18. No menu **arquivo** , clique em **Salvar** para salvar a visualização como um arquivo de área de trabalho do Power BI.

## <a name="publish-report-to-power-bi-service"></a>Publicar relatório no serviço do Power BI

Quando você tiver criado as visualizações no Power BI Desktop, você pode compartilhá-la com outras pessoas publicando-o para o serviço do Power BI. Para obter instruções sobre como fazer isso, consulte [Publicar da área de trabalho do Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Consulte também

* [Analisar dados no repositório de Lucerne de dados usando a análise de dados Lucerne](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
