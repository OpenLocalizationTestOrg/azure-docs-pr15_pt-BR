<properties
   pageTitle="Registrar dados Lucerne do armazenamento de dados no catálogo de dados do Azure | Microsoft Azure"
   description="Registrar dados Lucerne do armazenamento de dados no catálogo de dados do Azure"
   services="data-lake-store,data-catalog" 
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Registrar dados Lucerne do armazenamento de dados no catálogo de dados do Azure

Neste artigo, você aprenderá como integrar o armazenamento do Azure dados Lucerne ao catálogo de dados do Azure para fazer seus dados detectáveis dentro de uma organização por integração com o catálogo de dados. Para obter mais informações sobre o catálogo de dados, consulte [Catálogo de dados do Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Para compreender cenários em que você pode usar o catálogo de dados, consulte [cenários comuns do catálogo de dados do Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura de um Azure**. Consulte [avaliação gratuita do Azure obter](https://azure.microsoft.com/pricing/free-trial/).

- **Ativar sua assinatura do Azure** para dados Lucerne Store Public Preview. Consulte [as instruções](data-lake-store-get-started-portal.md#signup).

- **Conta de armazenamento de Lucerne de dados do azure**. Siga as instruções contidas em [Introdução ao armazenamento de Lucerne de dados do Azure usando o Portal do Azure](data-lake-store-get-started-portal.md). Para este tutorial, vamos crie uma conta de armazenamento de Lucerne de dados chamada **datacatalogstore**. 

    Depois de criar a conta, carregar um conjunto de dados de amostra-lo. Para este tutorial, vamos carregar todos os arquivos. csv sob a pasta **AmbulanceData** no [Repositório de gito do Azure dados Lucerne](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Você pode usar vários clientes, como o [Gerenciador de armazenamento do Azure](http://storageexplorer.com/), para carregar dados em um contêiner de blob.

- **Catálogo de dados do azure**. Sua organização já deve ter um catálogo de dados do Azure criada para sua organização. Apenas um catálogo é permitido para cada organização.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Registrar dados Lucerne Store como uma fonte para o catálogo de dados

>[AZURE.VIDEO adcwithadl] 

1. Vá para `https://azure.microsoft.com/services/data-catalog`e clique em **começar**.

2. Faça logon no portal do catálogo de dados do Azure e clique em **publicar dados**.

    ![Registrar uma fonte de dados] (./media/data-lake-store-with-data-catalog/register-data-source.png "Registrar uma fonte de dados")

3. Na próxima página, clique em **Iniciar aplicativo**. Isso fará o download do arquivo de manifesto do aplicativo em seu computador. Clique duas vezes no arquivo manifesto para iniciar o aplicativo.

4. Na página de boas-vindas, clique em **entrar**e insira suas credenciais.

    ![Tela de boas-vindas] (./media/data-lake-store-with-data-catalog/welcome.screen.png "Tela de boas-vindas")

5. Na página Selecionar um fonte de dados, selecione **Lucerne de dados do Azure**e clique em **Avançar**.

    ![Selecionar fonte de dados] (./media/data-lake-store-with-data-catalog/select-source.png "Selecionar fonte de dados")

6. Na próxima página, forneça o nome da conta de armazenamento de Lucerne de dados que você deseja registrar no catálogo de dados. Deixe as outras opções como padrão e clique em **Conectar**.

    ![Conectar-se à fonte de dados] (./media/data-lake-store-with-data-catalog/connect-to-source.png "Conectar-se à fonte de dados")

7. A próxima página pode ser dividida nos seguintes segmentos.

    a. A caixa de **Hierarquia de servidor** representa a estrutura de pasta da conta de armazenamento de Lucerne de dados. **$Root** representa a raiz de conta de armazenamento de Lucerne de dados e **AmbulanceData** representa a pasta criada na raiz da conta de armazenamento de Lucerne de dados.

    b. A caixa de **objetos disponíveis** lista os arquivos e pastas na pasta **AmbulanceData** .

    c. **Objetos sejam registrados** lista os arquivos e pastas que você deseja registrar no catálogo de dados do Azure.

    ![Estrutura de dados do modo de exibição] (./media/data-lake-store-with-data-catalog/view-data-structure.png "Estrutura de dados do modo de exibição")

8. Para este tutorial, você deve registrar todos os arquivos no diretório. Para isso, clique no botão (![mover objetos](./media/data-lake-store-with-data-catalog/move-objects.png "mover objetos")) para mover todos os arquivos para caixa de **objetos sejam registrados** . 

    Como os dados serão registrados em um catálogo de dados de toda a organização, é uma abordagem de recomendados para adicionar alguns metadados que você pode usar para localizar rapidamente os dados mais tarde. Por exemplo, você pode adicionar um endereço de email para o proprietário dos dados (por exemplo, um quem está carregando os dados) ou adicionar uma marca para identificar os dados. A captura de tela abaixo mostra uma marca que adicionamos aos dados.

    ![Estrutura de dados do modo de exibição] (./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Estrutura de dados do modo de exibição")

    Clique em **registrar**.

8. A captura de tela a seguir indica se os dados com êxito estão registrados no catálogo de dados.

    ![Registro concluído] (./media/data-lake-store-with-data-catalog/registration-complete.png "Estrutura de dados do modo de exibição")

9. Clique em **Exibir Portal** para voltar para o portal do catálogo de dados e verifique se você agora pode acessar os dados registrados no portal do. Para pesquisar os dados, você pode usar a marca que você usou ao registrar os dados.

    ![Pesquisar dados no catálogo] (./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Pesquisar dados no catálogo")

10. Agora você pode executar operações como adicionar anotações e documentação para os dados. Para obter mais informações, consulte os links a seguir.
    * [Anotar fontes de dados no catálogo de dados](../data-catalog/data-catalog-how-to-annotate.md)
    * [Fontes de dados do documento no catálogo de dados](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Consulte também

* [Anotar fontes de dados no catálogo de dados](../data-catalog/data-catalog-how-to-annotate.md)
* [Fontes de dados do documento no catálogo de dados](../data-catalog/data-catalog-how-to-documentation.md)
* [Integre dados Lucerne Store com outros serviços do Azure](data-lake-store-integrate-with-other-services.md)
