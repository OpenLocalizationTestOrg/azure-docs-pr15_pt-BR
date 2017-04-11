<properties
    pageTitle="Gerenciar recursos de armazenamento de Blob do Azure com o Gerenciador de armazenamento (visualização) | Microsoft Azure"
    description="Gerenciar contêineres de blob do Microsoft Azure e Blobs com o Storage Explorer (prévia)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Gerenciar recursos de armazenamento de Blob do Azure com o Gerenciador de armazenamento (prévia)

## <a name="overview"></a>Visão geral

[Armazenamento de Blob do Azure](./storage/storage-dotnet-how-to-use-blobs.md) é um serviço para armazenar grandes quantidades de dados não estruturados, como dados binários ou texto, que podem ser acessados a partir de qualquer lugar do mundo via HTTP ou HTTPS.
Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo, ou para armazenar dados de aplicativo em particular. Neste artigo, você aprenderá como usar o Gerenciador de armazenamento (visualização) para trabalhar com contêineres de blob e blobs.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, será necessário o seguinte:

- [Baixe e instale o Gerenciador de armazenamento (prévia)](http://www.storageexplorer.com)
- [Conectar a uma conta de armazenamento do Azure ou serviço](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Criar um contêiner de blob

Todos os blobs devem residem em um contêiner de blob, que é simplesmente um agrupamento lógico de blobs. Uma conta pode conter um número ilimitado de contêineres, e cada contêiner pode armazenar um número ilimitado de blobs.

As etapas a seguir ilustram como criar um contêiner de blob dentro do armazenamento Explorer (visualização).

1.  Abra o Explorador de armazenamento (visualização).
1.  No painel esquerdo, expanda a conta de armazenamento no qual você deseja criar o contêiner de blob.
1.  Clique com botão direito **Contêineres de Blob**e - no menu de contexto - selecione **Criar contêiner de Blob**.

    ![Criar menus de contexto de contêineres de blob][0]

1.  Será exibida uma caixa de texto abaixo da pasta de **Contêineres de Blob** . Insira o nome de seu contêiner de blob. Consulte a seção de [regras de nomenclatura de contêiner](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) para obter uma lista de regras e restrições sobre nomeação contêineres de blob.

    ![Criar caixa de texto de contêineres de Blob][1]

1.  Pressione **Enter** quando terminar de criar o contêiner de blob ou **Esc** para cancelar. Depois que o contêiner de blob tiver sido criado com êxito, ele será exibido sob a pasta de **Contêineres de Blob** da conta de armazenamento selecionado.

    ![Contêiner de blob criado][2]

## <a name="view-a-blob-containers-contents"></a>Exibir conteúdo de um contêiner blob

Contêineres de blob contêm blobs e pastas (que também podem conter blobs).

As etapas a seguir ilustram como exibir o conteúdo de um contêiner de blob dentro do armazenamento Explorer (visualização):

1.  Abra o Explorador de armazenamento (visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blob que você deseja exibir.
1.  Expanda a conta de armazenamento **Blob contêineres**.
1.  Clique com botão direito do contêiner de blob que você deseja exibir e - no menu de contexto - selecione **Abrir Editor de contêiner de Blob**.
Você também pode clicar duas vezes o contêiner de blob que você deseja exibir.

    ![Menu de contexto do editor de contêiner de blob aberto][19]

1.  Painel principal exibirá o blob conteúdo do contêiner.

    ![Editor de contêiner de blob][3]

## <a name="delete-a-blob-container"></a>Excluir um contêiner de blob

Contêineres de blob podem ser facilmente criados e excluídos conforme necessário. (Para ver como excluir blobs individuais, consulte a seção, [Gerenciando blobs em um contêiner de blob](./#managing-blobs-in-a-blob-container).)

As etapas a seguir ilustram como excluir um contêiner de blob dentro Storage Explorer (visualização):

1.  Abra o Explorador de armazenamento (visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blob que você deseja exibir.
1.  Expanda a conta de armazenamento **Blob contêineres**.
1.  Clique com botão direito do contêiner de blob que você deseja excluir e - no menu de contexto - selecione **Excluir**.
Você também pode pressionar **Excluir** para excluir o contêiner de blob atualmente selecionada.

    ![Excluir o menu de contexto do contêiner de blob][4]

1.  Selecione **Sim** na caixa de diálogo de confirmação.

    ![Excluir blob confirmação de contêiner][5]

## <a name="copy-a-blob-container"></a>Copiar um contêiner de blob

Gerenciador de armazenamento (visualização) permite que você copiar um contêiner de blob na área de transferência e cole contêiner blob outra conta de armazenamento. (Para ver como copiar blobs individuais, consulte a seção, [Gerenciando blobs em um contêiner de blob](./#managing-blobs-in-a-blob-container).)

As etapas a seguir ilustram como copiar um contêiner de blob de conta de um armazenamento para outro.

1.  Abra o Explorador de armazenamento (visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blob que você deseja copiar.
1.  Expanda a conta de armazenamento **Blob contêineres**.
1.  Clique com botão direito do contêiner de blob que você deseja copiar e - no menu de contexto - selecione **Copiar Blob contêiner**.

    ![Menu de contexto do contêiner de blob de cópia][6]

1.  Clique com botão direito a conta de armazenamento de "destino" desejado no qual você deseja colar o contêiner de blob e - no menu de contexto - selecione **Colar contêiner de Blob**.

    ![Menu de contexto do contêiner de blob de colagem][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obter as associações de segurança para um contêiner de blob

Uma [assinatura de acesso compartilhado (SAS)](./storage/storage-dotnet-shared-access-signature-part-1.md) fornece acesso delegado aos recursos na sua conta de armazenamento.
Isso significa que você pode conceder a que um cliente limitado permissões para objetos em sua conta de armazenamento por um determinado período de tempo e com um conjunto específico de permissões, sem precisar compartilhar suas chaves de acesso de conta.

As etapas a seguir ilustram como criar uma SAS para um contêiner de blob:

1.  Abra o Explorador de armazenamento (visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blob para o qual você deseja obter uma SAS.
1.  Expanda a conta de armazenamento **Blob contêineres**.
1.  Clique com botão direito do contêiner de blob desejado e - no menu de contexto - selecione **Obter assinatura de acesso compartilhado**.

    ![Obter o menu de contexto SAS][8]

1.  Na caixa de diálogo **Assinatura de acesso compartilhado** , especifique a política, datas de início e vencimento, fuso horário e acessar os níveis que você deseja para o recurso.

    ![Obter as opções de SAS][9]

1.  Quando tiver terminado de especificar as opções de SAS, selecione **criar**.

1.  Uma segunda caixa de diálogo de **Assinatura de acesso compartilhado** , em seguida, exibirá que lista o contêiner de blob juntamente com a URL e QueryStrings você pode usar para acessar o recurso de armazenamento.
Selecione **Copiar** ao lado da URL que você deseja copiar para a área de transferência.

    ![Copiar URLs SAS][10]

1.  Quando terminar, selecione **Fechar**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gerenciar políticas de acesso para um contêiner de blob

As etapas a seguir ilustram como gerenciar (Adicionar e remover) acessar as políticas para um contêiner de blob:

1.  Abra o Explorador de armazenamento (visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blob cujas políticas de acesso que você deseja gerenciar.
1.  Expanda a conta de armazenamento **Blob contêineres**.
1.  Selecione o contêiner de blob desejado e - no menu de contexto - selecione **Gerenciar políticas de acesso**.

    ![Gerenciar o menu de contexto de políticas de acesso][11]

1.  A caixa de diálogo de **Políticas de acesso** listará quaisquer políticas de acesso já criadas para o contêiner de blob selecionado.

    ![Opções de política de acesso][12]        

1.  Siga estas etapas dependendo a tarefa de gerenciamento de política de acesso:

    - **Adicionar uma nova política de acesso** - selecione **Adicionar**. Uma vez gerado, a caixa de diálogo de **Políticas de acesso** exibirão a política de acesso recém-adicionado (com configurações padrão).
    - **Editar uma política de acesso** - faça qualquer edições desejadas e selecione **Salvar**.
    - **Remover uma política de acesso** - selecione **Remover** ao lado da política de acesso que você deseja remover.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Definir o nível de acesso público para um contêiner de blob

Por padrão, cada contêiner de blob é definida como "Sem acesso público".

As etapas a seguir ilustram como especificar um nível de acesso público para um contêiner de blob.

1.  Abra o Explorador de armazenamento (visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blob cujas políticas de acesso que você deseja gerenciar.
1.  Expanda a conta de armazenamento **Blob contêineres**.
1.  Selecione o contêiner de blob desejado e - no menu de contexto - selecione **Definir o nível de acesso público**.

    ![Menu de contexto do nível do conjunto de acesso público][13]

1.  Na caixa de diálogo **Definir o nível de acesso público contêiner** , especifica o nível de acesso desejado.

    ![Definir opções de nível de acesso público][14]

1.  Selecione **Aplicar**.

## <a name="managing-blobs-in-a-blob-container"></a>Gerenciando blobs em um contêiner de blob

Depois de criar um contêiner de blob, você pode carregar um blob no contêiner blob, baixe um blob no seu computador local, abra um blob em seu computador local e muito mais.

As etapas a seguir ilustram como gerenciar os blobs (e pastas) dentro de um contêiner de blob.

1.  Abra o Explorador de armazenamento (visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blob que você deseja gerenciar.
1.  Expanda a conta de armazenamento **Blob contêineres**.
1.  Clique duas vezes o contêiner de blob que você deseja exibir.
1.  Painel principal exibirá o blob conteúdo do contêiner.

    ![Contêiner de exibição de blob][3]

1.  Painel principal exibirá o blob conteúdo do contêiner.

1.  Siga estas etapas dependendo da tarefa que você deseja executar:

    - **Carregar arquivos para um contêiner de blob**

        1.  Na barra de ferramentas do painel principal, selecione **carregar**e, em seguida, **Carregar arquivos** no menu suspenso.

            ![Carregar o menu de arquivos][15]

        1.  Na caixa de diálogo **carregar arquivos** , selecione o botão de reticências (**…**) no lado direito da caixa de texto de **arquivos** para selecionar os arquivos que você deseja carregar.

            ![Opções de arquivos de carregamento][16]

        1.  Especifique o tipo de **Blob**. O artigo [Introdução ao armazenamento de Blob do Azure usando .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica as diferenças entre os vários tipos de blob.

        1.  Opcionalmente, especifique uma pasta de destino em que os arquivos selecionados serão carregados. Se a pasta de destino não existir, ele será criado.

        1.  Selecione **carregar**.

    - **Carregar uma pasta em um contêiner de blob**

        1.  Na barra de ferramentas do painel principal, selecione **carregar**e, em seguida, **Carregue pasta** no menu suspenso.

            ![Carregar o menu da pasta][17]

        1.  Na caixa de diálogo **carregar a pasta** , selecione o botão de reticências (**…**) no lado direito da caixa de texto **pasta** para selecionar a pasta cujo conteúdo você deseja carregar.

            ![Opções de pasta de carregamento][18]

        1.  Especifique o tipo de **Blob**. O artigo [Introdução ao armazenamento de Blob do Azure usando .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica as diferenças entre os vários tipos de blob.

        1.  Opcionalmente, especifique uma pasta de destino na qual conteúdo da pasta selecionada será carregado. Se a pasta de destino não existir, ele será criado.

        1.  Selecione **carregar**.

    - **Baixe um blob no computador local**

        1.  Selecione o blob que você deseja baixar.

        1.  Na barra de ferramentas do painel principal, selecione **Download**.

        1.  Na caixa de diálogo **especificar onde deseja salvar o blob baixado** , especifique o local onde deseja que o blob baixado e o nome que você deseja dar a ele.  

        1.  Selecione **Salvar**.

    - **Abrir um blob no computador local**

        1.  Selecione o blob que você deseja abrir.

        1.  Na barra de ferramentas do painel principal, selecione **Abrir**.

        1.  O blob será baixado e abertos usando o aplicativo associado ao tipo de arquivo do blob subjacente.

    - **Copiar um blob para a área de transferência**

        1.  Selecione o blob que você deseja copiar.

        1.  Na barra de ferramentas do painel principal, selecione **Copiar**.

        1.  No painel esquerdo, navegue para o outro contêiner de blob e clique duas vezes nela para exibi-la no painel principal.

        1.  Na barra de ferramentas do painel principal, selecione **Colar** para criar uma cópia do blob.

    - **Excluir um blob**

        1.  Selecione o blob que você deseja excluir.

        1.  Na barra de ferramentas do painel principal, selecione **Excluir**.

        1.  Selecione **Sim** na caixa de diálogo de confirmação.

## <a name="next-steps"></a>Próximas etapas

- Exiba o [Gerenciador de armazenamento mais recente (visualização) notas de versão e vídeos](http://www.storageexplorer.com).
- Saiba como [criar aplicativos usando arquivos, tabelas, filas e blobs do Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png