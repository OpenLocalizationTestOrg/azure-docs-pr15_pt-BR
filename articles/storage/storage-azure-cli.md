<properties
    pageTitle="Usando a CLI Azure com armazenamento do Azure | Microsoft Azure"
    description="Saiba como usar a Interface de linha do Azure (Azure comando) com o armazenamento do Azure para criar e gerenciar contas de armazenamento e trabalhar com arquivos e blobs Azure. O Azure é uma ferramenta de várias plataformas "
    services="storage"
    documentationCenter="na"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="using-the-azure-cli-with-azure-storage"></a>Usando a CLI Azure com armazenamento do Azure

## <a name="overview"></a>Visão geral

A CLI do Azure fornece um conjunto de código-fonte aberto, entre plataformas comandos para trabalhar com a plataforma Azure. Ele fornece praticamente a mesma funcionalidade encontrada no [portal do Azure](https://portal.azure.com) bem como funcionalidade de acesso de dados mais elaborados.

Neste guia, vamos explorar como usar a [Interface de linha do Azure (Azure comando)](../xplat-cli-install.md) para executar uma variedade de tarefas de desenvolvimento e administração com o armazenamento do Azure. Recomendamos que você baixar e instala ou atualizar para a mais recente CLI Azure antes de usar este guia.

Este guia pressupõe que você compreenda os conceitos básicos de armazenamento do Azure. Guia fornece um número de scripts para demonstrar o uso da CLI Azure com o armazenamento do Azure. Certifique-se de atualizar as variáveis de script com base em sua configuração antes de executar cada script.

> [AZURE.NOTE] Guia fornece os exemplos de comando e script do Azure CLI para contas de armazenamento clássico. Consulte [utilizando a CLI do Azure para Mac, Linux e Windows com o gerenciamento de recursos do Azure](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) para Azure CLI comandos para contas de armazenamento do Gerenciador de recursos.

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Introdução ao armazenamento do Azure e o Azure CLI em 5 minutos

Este guia usa Ubuntu para obter exemplos, mas outras plataformas de sistema operacional devem executar da mesma forma.

**Experiência com o Azure:** Obtenha uma assinatura do Microsoft Azure e uma conta da Microsoft associada a essa assinatura. Para obter informações sobre as opções de compra do Azure, consulte [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/)e [Membro oferece](https://azure.microsoft.com/pricing/member-offers/) (para membros do MSDN, Microsoft Partner Network, BizSpark e outros programas da Microsoft).

Consulte [Atribuindo funções de administrador do Azure Active Directory (AD Azure)](https://msdn.microsoft.com/library/azure/hh531793.aspx) para obter mais informações sobre as assinaturas do Azure.

**Depois de criar uma assinatura do Microsoft Azure e conta:**

1. Baixe e instale a CLI Azure seguindo as instruções descritas em [instalar o CLI do Azure](../xplat-cli-install.md).
2. Quando a CLI Azure tiver sido instalada, você poderá usar o comando azure sua interface de linha de comando (Bash, Terminal, prompt de comando) para acessar os comandos do Azure. Tipo de `azure` comando e você verá a seguinte saída.

    ![Saída do comando Azure][Image1]

3. Na interface de linha de comando, digite `azure storage` listar todos os comandos de armazenamento do azure e obter uma primeira impressão das funcionalidades de CLI Azure fornece. Você pode digitar o nome do comando com parâmetro **-h** (por exemplo, `azure storage share create -h`) para ver detalhes de sintaxe de comando.
4. Agora, você terá um script simples que mostra os comandos básicos do Azure para acessar o armazenamento do Azure. O script perguntará primeiro definir duas variáveis para sua conta de armazenamento e chave. Em seguida, o script irá criar um novo recipiente nessa nova conta de armazenamento e carregar um arquivo de imagem existente (blob) no contêiner. Após o script todos os blobs no contêiner de listas, ele irá baixar o arquivo de imagem à pasta de destino que existe no computador local.

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. No seu computador local, abra o editor de texto preferido (vim por exemplo). Digite o script acima para o editor de texto.

6. Agora, você precisa atualizar as variáveis de script com base em suas configurações.

    - **< storage_account_name >** Use o nome fornecido no script ou insira um novo nome para a sua conta de armazenamento. **Importante:** O nome da conta de armazenamento deve ser exclusivo no Azure. Ele deve ser minúsculo, demais!

    - **< storage_account_key >** A chave de acesso da sua conta de armazenamento.

    - **< container_name >** Use o nome fornecido no script ou digite um novo nome para seu contêiner.

    - **< image_to_upload >** Digite um caminho para uma imagem em seu computador local, como: "~ / images/HelloWorld.png".

    - **< destination_folder >** Digite um caminho para um diretório local para armazenar os arquivos baixados do armazenamento do Azure, como: "~/downloadImages".

7. Depois que você atualizou as variáveis necessárias no vim, pressionar combinações de teclas "Esc,:, wq!" Para salvar o script.

8. Para executar este script, basta digite o nome do arquivo de script no console bash. Depois que esse script é executado, você deve ter uma pasta de destino local que inclui o arquivo de imagem baixado. A captura de tela a seguir mostra um exemplo de saída:

Após o script ser executado, você deve ter uma pasta de destino local que inclui o arquivo de imagem baixado.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Gerenciar contas de armazenamento com a CLI do Azure

### <a name="connect-to-your-azure-subscription"></a>Conectar-se a sua assinatura do Azure

Embora a maioria dos comandos de armazenamento funcionará sem uma assinatura do Azure, recomendamos que você se conecte à sua assinatura da CLI Azure. Para configurar a ILC do Azure para trabalhar com sua assinatura, siga as etapas em [conectar a uma assinatura do Azure da CLI Azure](../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

Para usar o armazenamento do Azure, você precisará de uma conta de armazenamento. Você pode criar uma nova conta de armazenamento do Azure após você ter configurado seu computador para se conectar à sua assinatura.

        azure storage account create <account_name>

O nome da sua conta de armazenamento deve estar entre 3 e 24 caracteres e use números e apenas letras minúsculas.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Definir uma conta de armazenamento do Azure padrão em variáveis de ambiente

Você pode ter várias contas de armazenamento em sua assinatura. Você pode escolher um deles e configurá-la nas variáveis de ambiente para todos os comandos de armazenamento na mesma sessão. Isso permite que você execute os comandos de armazenamento do Azure CLI sem especificar a conta de armazenamento e chave explicitamente.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

Outra maneira de definir uma conta de armazenamento padrão está usando a cadeia de conexão. Primeiramente, obtenha a cadeia de conexão, comando:

        azure storage account connectionstring show <account_name>

Em seguida, copie a cadeia de conexão de saída e defina-a variável de ambiente:

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## <a name="create-and-manage-blobs"></a>Criar e gerenciar blobs

Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como dados binários ou texto, que podem ser acessados a partir de qualquer lugar do mundo via HTTP ou HTTPS. Esta seção pressupõe que você já esteja familiarizado com os conceitos de armazenamento de Blob do Azure. Para obter informações detalhadas, consulte [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md) e [Conceitos de serviços de Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Criar um contêiner

Cada blob no armazenamento do Azure deve estar em um contêiner. Você pode criar um contêiner particular usando o `azure storage container create` comando:

        azure storage container create mycontainer

> [AZURE.NOTE] Há três níveis de acesso de leitura anônimo: **Desativar** **Blob**e **contêiner**. Para impedir o acesso anônimo em blobs, defina o parâmetro de permissão para **desativado**. Por padrão, o novo contêiner é particular e pode ser acessado somente pelo proprietário da conta. Para permitir acesso de leitura público anônimo blob recursos, mas não para metadados de contêiner ou lista de blobs no contêiner, defina o parâmetro de permissão ao **Blob**. Para permitir o acesso de leitura público completa para recursos, metadados de contêiner e a lista de blobs no contêiner de blob, defina o parâmetro de permissão ao **contêiner**. Para obter mais informações, consulte [Gerenciar acesso anônimo de leitura para contêineres e blobs](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner

Armazenamento de Blob do Azure suporta blobs de bloco e blobs de página. Para obter mais informações, consulte [Noções básicas sobre bloco Blobs, Blobs acrescentar e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar blobs em um contêiner, você pode usar o `azure storage blob upload`. Por padrão, este comando carrega os arquivos locais para um blob de bloco. Para especificar o tipo de blob, você pode usar o `--blobtype` parâmetro.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### <a name="download-blobs-from-a-container"></a>Baixar blobs de um contêiner

O exemplo a seguir demonstra como baixar blobs de um contêiner.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### <a name="copy-blobs"></a>Copiar blobs

Você pode copiar blobs dentro ou entre regiões e contas de armazenamento assíncrona.

O exemplo a seguir demonstra como copiar blobs de uma conta de armazenamento para outro. Neste exemplo, podemos criar um contêiner de blobs anotados publicamente, podem ser acessadas anonimamente.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

Este exemplo executa uma cópia assíncrona. Você pode monitorar o status de cada operação de cópia executando o `azure storage blob copy show` operação.

Observe que a URL de origem fornecida para a operação de cópia deve ser acessível publicamente, ou incluir um token SAS (assinatura de acesso compartilhado).

### <a name="delete-a-blob"></a>Excluir um blob

Para excluir um blob, use o abaixo comando:

        azure storage blob delete mycontainer myBlockBlob2

## <a name="create-and-manage-file-shares"></a>Criar e gerenciar compartilhamentos de arquivos

Armazenamento de arquivos Azure oferece armazenamento compartilhado para aplicativos que usam o protocolo SMB padrão. Máquinas virtuais do Microsoft Azure e serviços de nuvem, bem como aplicativos de locais, podem compartilhar dados de arquivo por meio de compartilhamentos montados. Você pode gerenciar compartilhamentos de arquivos e dados de arquivos por meio da CLI do Azure. Para obter mais informações sobre o armazenamento de arquivos do Azure, consulte [Introdução ao armazenamento de arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md) ou [como usar o armazenamento de arquivos do Azure com Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Criar um compartilhamento de arquivo

Um compartilhamento de arquivo do Azure é um compartilhamento de arquivo SMB no Azure. Todos os arquivos e pastas devem ser criados em um compartilhamento de arquivo. Uma conta pode conter um número ilimitado de compartilhamentos e um compartilhamento pode armazenar um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento. O exemplo a seguir cria um compartilhamento de arquivo chamado **meu_compartilhamento**.

        azure storage share create myshare

### <a name="create-a-directory"></a>Criar um diretório

Um diretório fornece uma estrutura hierárquica opcional para um compartilhamento de arquivo Azure. O exemplo a seguir cria um diretório chamado **myDir** no compartilhamento de arquivo.

        azure storage directory create myshare myDir

Observação esse caminho de diretório pode incluir vários níveis, *por exemplo*, **um / b**. No entanto, você deve garantir que todos os diretórios pai existe. Por exemplo, para o caminho **um / b**, você deve criar diretório **um** primeiro e depois criar diretório **b**.

### <a name="upload-a-local-file-to-directory"></a>Carregar um arquivo local no diretório

O exemplo a seguir carrega um arquivo de **~/temp/samplefile.txt** ao diretório **myDir** . Edite o caminho do arquivo para que ele aponte para um arquivo válido no computador local:

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Observe que um arquivo no compartilhamento pode ter até 1 TB em tamanho.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Listar os arquivos na raiz do compartilhamento ou diretório

Você pode listar os arquivos e subpastas em uma raiz de compartilhamento ou um diretório usando o seguinte comando:

        azure storage file list myshare myDir

Observe que o nome do diretório é opcional para a operação de listagem. Se omitido, o comando lista o conteúdo da pasta raiz do compartilhamento.

### <a name="copy-files"></a>Copiar arquivos

Começando com a versão 0.9.8 do Azure CLI, você pode copiar um arquivo para outro arquivo, um arquivo para um blob ou um blob para um arquivo. Abaixo, demonstraremos como realizar essas operações de cópia usando comandos. Para copiar um arquivo para o novo diretório:

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

Para copiar um blob em um diretório de arquivo:

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## <a name="next-steps"></a>Próximas etapas

Aqui estão alguns artigos relacionados e recursos para aprender mais sobre o armazenamento do Azure.

- [Documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Referência da API REST armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
