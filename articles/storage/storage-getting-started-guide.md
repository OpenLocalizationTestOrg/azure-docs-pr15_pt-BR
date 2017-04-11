<properties
    pageTitle="Introdução ao armazenamento do Azure em cinco minutos | Microsoft Azure"
    description="Rapidamente a entender os Blobs do Microsoft Azure, tabela e filas usando inicia rápida de armazenamento do Azure, Visual Studio e o emulador de armazenamento do Azure. Execute o aplicativo de armazenamento do Azure primeiro em cinco minutos."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="get-started-with-azure-storage-in-five-minutes"></a>Introdução ao armazenamento do Azure em cinco minutos

## <a name="overview"></a>Visão geral

É fácil começar a desenvolver com o armazenamento do Azure. Este tutorial mostra como obter um aplicativo de armazenamento do Azure para cima e rapidamente. Você usará os modelos de início rápido incluídos com o SDK do Azure para .NET. Estas inicia rápida contêm código de pronto para executar que demonstra alguns cenários de programação básicos com o armazenamento do Azure.

Para saber mais sobre o armazenamento do Azure antes de mergulhar o código, consulte [Próximas etapas](#next-steps).

## <a name="prerequisites"></a>Pré-requisitos

Você terá os seguintes pré-requisitos antes de começar:

1. Para compilar e criar o aplicativo, você precisará de uma versão do [Visual Studio](https://www.visualstudio.com/) instalado no seu computador.

2. Instale a versão mais recente [Do SDK do Azure para .NET](https://azure.microsoft.com/downloads/). O SDK inclui os projetos de amostra de início rápido do Azure, o emulador de armazenamento do Azure e a [Biblioteca de cliente de armazenamento do Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Certifique-se de que você tenha [.NET Framework 4,5](http://www.microsoft.com/download/details.aspx?id=30653) instalado em seu computador, ele é requerido pelos projetos de amostra de início rápido do Azure que usaremos neste tutorial.

    Se você não tiver certeza de qual versão do .NET Framework está instalada no seu computador, consulte [como: determinar quais .NET Framework versões estão instalados](https://msdn.microsoft.com/vstudio/hh925568.aspx). Ou, pressione a tecla do Windows, ou no botão **Iniciar** , digite **Painel de controle**. Em seguida, clique em **programas** > **programas e recursos**e determine se o .NET Framework 4.5 está listado entre os programas instalados.

4. Você precisará de uma assinatura do Azure e uma conta de armazenamento do Azure.

    - Para obter uma assinatura do Azure, consulte [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/)e [Membro oferece](https://azure.microsoft.com/pricing/member-offers/) (para membros do MSDN, Microsoft Partner Network, BizSpark e outros programas da Microsoft).
    - Para criar uma conta de armazenamento no Azure, veja [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Executar o aplicativo de armazenamento do Azure primeiro contra armazenamento do Azure na nuvem

Quando você tiver uma conta, você pode criar um aplicativo de armazenamento do Azure simples usando um dos projetos de amostra Azure rápida inicia no Visual Studio. Este tutorial focaliza os projetos de amostra para o armazenamento do Azure: **armazenamento Azure: Blobs**, **armazenamento Azure: arquivos**, **armazenamento Azure: filas**, e **armazenamento Azure: tabelas**:

1. Inicie o Visual Studio.
2. No menu **arquivo** , clique em **Novo projeto**.
3. Na caixa de diálogo **Novo projeto** , clique em **instalar** > **modelos** > **Visual c#** > **nuvem** > **guias de início rápido** > **Serviços de dados**.
    a. Escolha um dos seguintes modelos: **armazenamento Azure: Blobs**, **armazenamento Azure: arquivos**, **armazenamento Azure: filas**, ou **armazenamento Azure: tabelas**.
    b. Certifique-se de que o **.NET Framework 4,5** seja selecionado como a estrutura de destino.
    - 3.c. Especifique um nome para o seu projeto e criar a nova solução do Visual Studio, conforme mostrado:

    ![Azure início rápido][Image1]

Talvez você queira examinar o código-fonte antes de executar o aplicativo. Para revisar o código, selecione **Gerenciador de solução** no menu **Exibir** no Visual Studio. Em seguida, clique duas vezes no arquivo Program.cs.

Em seguida, execute o aplicativo de exemplo:

1.  No Visual Studio, selecione **Gerenciador de solução** no menu **Exibir** . Abra o arquivo App. config e comente a cadeia de conexão para o emulador de armazenamento do Azure:

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Tire comentários a cadeia de conexão para o serviço de armazenamento do Azure e forneça a chave de acesso e nome da conta de armazenamento no arquivo App. config:`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    Para recuperar sua chave de acesso da conta de armazenamento, consulte [Gerenciar suas chaves de acesso de armazenamento](storage-create-storage-account.md#manage-your-storage-access-keys).

3.  Depois de você fornecer o nome de conta de armazenamento e tecla de acesso no arquivo App, no menu **arquivo** , clique em **Salvar tudo** para salvar todos os arquivos de projeto.
4.  No menu **Build** , clique em **Criar solução**.
5.  No menu **Depurar** , pressione **F11** para executar a solução passo a passo ou pressione **F5** para executar a solução.


## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Executar o seu aplicativo de armazenamento do Azure primeiro localmente contra o emulador de armazenamento do Azure

O [Emulador de armazenamento do Azure](storage-use-emulator.md) fornece um ambiente local que emula os serviços do Azure Blob, fila e tabela para fins de desenvolvimento. Você pode usar o emulador de armazenamento para testar seu aplicativo de armazenamento localmente, sem criar uma assinatura do Azure ou uma conta de armazenamento e sem incorrer qualquer custo.

Para experimentar, vamos criar um aplicativo de armazenamento do Azure simples usando um dos projetos de amostra Azure rápida inicia no Visual Studio. Este tutorial focaliza os projetos de amostra do **Armazenamento de Blob do Azure**, **O armazenamento de tabela do Azure**e **Armazenamento de fila do Azure** :

1. Inicie o Visual Studio.
2. No menu **arquivo** , clique em **Novo projeto**.
3. Na caixa de diálogo **Novo projeto** , clique em **instalar** > **modelos** > **Visual c#** > **nuvem** > **guias de início rápido** > **Serviços de dados**.
    a. Escolha um dos seguintes modelos: **armazenamento Azure: Blobs**, **armazenamento Azure: arquivos**, **armazenamento Azure: filas**, ou **armazenamento Azure: tabelas**.
    b. Certifique-se de que o **.NET Framework 4,5** seja selecionado como a estrutura de destino.
    c. Especifique um nome para o seu projeto e criar a nova solução do Visual Studio, conforme mostrado:

    ![Azure início rápido][Image1]

4.  No Visual Studio, selecione **Gerenciador de solução** no menu **Exibir** . Abra o arquivo App. config e comente a cadeia de conexão para a sua conta de armazenamento do Azure se você já tiver adicionado um. Em seguida, remova os comentários a cadeia de conexão para o emulador de armazenamento do Azure:

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Talvez você queira examinar o código-fonte antes de executar o aplicativo. Para revisar o código, selecione **Gerenciador de solução** no menu **Exibir** no Visual Studio. Em seguida, clique duas vezes no arquivo Program.cs.

Em seguida, execute o aplicativo de exemplo no Azure armazenamento emulador:

1.  Pressione o botão **Iniciar** ou a tecla do Windows, procure *emulador de armazenamento do Microsoft Azure*e iniciar o aplicativo. Quando o emulador for iniciado, você verá um ícone e uma notificação na área de exibição de tarefas do Windows.
2.  No Visual Studio, clique em **Build Solution** no menu **Build** .
3.  No menu **Depurar** , pressione **F11** para executar a solução passo a passo ou pressione **F5** para executar a solução do início ao fim.

## <a name="next-steps"></a>Próximas etapas

Consulte estes recursos para saber mais sobre o armazenamento do Azure:

* [Introdução ao Microsoft Azure armazenamento](storage-introduction.md)
* [Começar a usar o Gerenciador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução ao armazenamento de tabela do Azure usando .NET](storage-dotnet-how-to-use-tables.md)
* [Introdução ao armazenamento de fila do Azure usando o .NET](storage-dotnet-how-to-use-queues.md)
* [Introdução ao armazenamento de arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)
* [Transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md)
* [Documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Biblioteca de cliente de armazenamento do Windows Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Serviços de armazenamento do Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
