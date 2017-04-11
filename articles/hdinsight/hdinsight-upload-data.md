<properties
    pageTitle="Carregar dados para trabalhos de Hadoop em HDInsight | Microsoft Azure"
    description="Saiba como carregar e acessar os dados para trabalhos de Hadoop em HDInsight usando a CLI do Azure, Gerenciador de armazenamento do Azure, Azure PowerShell, a linha de comando do Hadoop ou Sqoop."
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>



#<a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Carregar dados para trabalhos de Hadoop em HDInsight

Azure HDInsight fornece um sistema de arquivo do Hadoop distribuído de completo (HDFS) sobre o armazenamento de Blob do Azure. Ele foi projetado como uma extensão HDFS para fornecer uma experiência perfeita aos clientes. Ele permite que o conjunto completo de componentes no ecossistema do Hadoop para operar diretamente nos dados gerencia. Azure armazenamento de Blob e HDFS são sistemas de arquivos distintos que são otimizados para armazenamento de dados e cálculos com os dados. Para obter informações sobre os benefícios do uso de armazenamento de Blob do Azure, consulte [usar Azure Blob storage com HDInsight][hdinsight-storage].

**Pré-requisitos**

Antes de começar, observe os seguintes requisitos:

* Um cluster de Azurehdinsight. Para obter instruções, consulte [Introdução ao Azure HDInsight] [ hdinsight-get-started] ou [clusters provisionar HDInsight][hdinsight-provision].

##<a name="why-blob-storage"></a>Por que armazenamento de blob?

Azure HDInsight clusters são normalmente implantados para executar trabalhos de MapReduce e clusters são descartados depois de concluir essas tarefas. Manter os dados na HDFS clusters depois cálculos estiverem finalizados seria uma maneira dispendiosa para armazenar esses dados. Armazenamento de Blob do Azure é altamente disponível, altamente escalável, alta capacidade, opção de armazenamento de baixo custo e compartilhável para dados que deve ser processado usando HDInsight. Armazenar dados em um blob habilita os clusters HDInsight que são usados para computação com segurança lançados sem perder dados.

###<a name="directories"></a>Diretórios

Azure contêineres de armazenamento de Blob para armazenar dados como pares chave/valor, e há uma hierarquia de diretório. No entanto o caractere "/" pode ser usado dentro do nome de chave para torná-la como se um arquivo está armazenado em uma estrutura de diretório. HDInsight vê esses como se fossem diretórios reais.

Por exemplo, a chave de um blob sejam *input/log1.txt*. Nenhum diretório de "entrada" real existe, mas devido a presença do caractere "/" no nome da chave, ela tem a aparência de um caminho de arquivo.

Por isso, se você usar ferramentas de Azure Explorer você pode perceber alguns arquivos de 0 byte. Esses arquivos têm duas finalidades:

- Se houver pastas vazias, eles marcam da existência de pasta. Armazenamento de Blob do Azure é inteligente o suficiente saber que, se um blob chamado foo/barra existir, há uma pasta chamada **foo**. Mas é a única maneira de significam uma pasta vazia chamada **foo** fazendo este arquivo especial 0 byte no lugar.

- Eles mantenha metadados especial que é necessária pelo sistema de arquivos Hadoop, especialmente as permissões e proprietários para as pastas.

##<a name="command-line-utilities"></a>Utilitários de linha de comando

A Microsoft fornece os seguintes utilitários para trabalhar com o armazenamento de Blob do Azure:

| Ferramenta | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interface de linha de comando Azure][azurecli] | ✔ | ✔ | ✔ |
| [PowerShell Azure][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Comando Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Enquanto o Azure CLI, PowerShell do Azure e AzCopy podem ser usados do Azure externo, o Hadoop comando só está disponível no cluster HDInsight e só permite carregar dados do sistema de arquivos local para armazenamento de Blob do Azure.

###<a id="xplatcli"></a>CLI Azure

O Azure é uma ferramenta de plataforma cruzada que permite que você gerencie os serviços do Azure. Use as seguintes etapas para carregar dados para o armazenamento de Blob do Azure:

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalar e configurar o CLI do Azure para Mac, Linux e Windows](../xplat-cli-install.md).

2. Abra um prompt de comando, bash ou outro shell e use o seguinte para autenticar à sua assinatura do Azure.

        azure login

    Quando solicitado, digite o nome de usuário e senha para sua assinatura.

3. Digite o seguinte comando para listar as contas de armazenamento para sua assinatura:

        azure storage account list

4. Selecione a conta de armazenamento que contém o blob que você deseja trabalhar e, em seguida, use o seguinte comando para recuperar a chave para essa conta:

        azure storage account keys list <storage-account-name>

    Isso deve retornar chaves **primárias** e **secundárias** . Copie o valor da chave **primária** porque ele será usado nas próximas etapas.

5. Use o seguinte comando para recuperar uma lista de contêineres de blob dentro da conta de armazenamento:

        azure storage container list -a <storage-account-name> -k <primary-key>

6. Use os seguintes comandos para carregar e baixar arquivos para o blob:

    * Para carregar um arquivo:

            azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

    * Para baixar um arquivo:

            azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Se você sempre estará trabalhando com a mesma conta de armazenamento, você pode definir as seguintes variáveis de ambiente em vez de especificar a conta e chave para cada comando:
>
> * **AZURE\_armazenamento\_conta**: O nome da conta de armazenamento
>
> * **AZURE\_armazenamento\_acesso\_chave**: A chave da conta de armazenamento

###<a id="powershell"></a>PowerShell Azure

PowerShell Azure é um ambiente de script que você pode usar para controlar e automatizar a implantação e gerenciamento de suas cargas de trabalho no Azure. Para obter informações sobre como configurar sua estação de trabalho para executar Azure PowerShell, consulte [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Carregar um arquivo local para armazenamento de Blob do Azure**

1. Abra o console do PowerShell do Azure conforme as instruções em [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
2. Defina os valores das cinco primeiros variáveis no script a seguir:

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Cole o script console do PowerShell do Azure para executá-lo para copiar o arquivo.

Por exemplo, scripts do PowerShell criados para trabalhar com HDInsight, consulte [Ferramentas de HDInsight](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy é uma ferramenta de linha de comando que foi projetada para simplificar a tarefa de transferir dados dentro e fora de uma conta de armazenamento do Azure. Você pode usá-la como uma ferramenta autônoma ou incorporar essa ferramenta em um aplicativo existente. [Baixar AzCopy][azure-azcopy-download].

A sintaxe de AzCopy é:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para obter mais informações, consulte [AzCopy - Carregando/baixando arquivos para Blobs do Azure][azure-azcopy].


###<a id="commandline"></a>Linha de comando do Hadoop

A linha de comando do Hadoop só é útil para armazenar dados para o armazenamento de blob quando os dados já estão presentes no nó principal cluster.

Para poder usar o comando Hadoop, você deve primeiro conectar ao headnode usando um dos seguintes métodos:

* **Baseado no Windows HDInsight**: [Conectar usando a área de trabalho remota](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight baseados em Linux**: conectar usando SSH ([o comando SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) ou [Acabamento](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Uma vez conectado, você pode usar a seguinte sintaxe para carregar um arquivo no armazenamento.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

Por exemplo,`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de arquivo padrão para HDInsight é no armazenamento de Blob do Azure, /example/data.txt é realmente no armazenamento de Blob do Azure. Você também pode se referir ao arquivo como:

    wasbs:///example/data/data.txt

ou

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para uma lista de outros comandos Hadoop que funcionam com arquivos, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [AZURE.WARNING] Em clusters de HBase, o padrão usado quando gravar dados é 256KB de tamanho do bloco. Enquanto isso funciona bem quando HBase APIs ou APIs REST, usando a `hadoop` ou `hdfs dfs` comandos para escrever dados maiores que ~ 12GB resultam em um erro. Consulte a seção de [exceção de armazenamento para gravação no blob](#storageexception) abaixo para obter mais informações.

##<a name="graphical-clients"></a>Clientes de gráficos

Também existem vários aplicativos que fornecem uma interface gráfica para trabalhar com o armazenamento do Azure. A seguir é uma lista de alguns desses aplicativos:

| Cliente | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Ferramentas do Microsoft Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Explorador de armazenamento do Azure](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Armazenamento de nuvem Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Explorer Azure](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) |  | ✔ | ✔ |

###<a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools for HDInsight

Para obter mais informações, consulte [navegar os recursos vinculados](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

###<a id="storageexplorer"></a>Explorador de armazenamento do Azure

*Gerenciador de armazenamento do Azure* é uma ferramenta útil para inspecionar e alterar os dados em bolhas. É uma ferramenta de origem gratuito, que pode ser baixada em [http://storageexplorer.com/](http://storageexplorer.com/). O código-fonte está disponível nesse link também.

Antes de usar a ferramenta, você deve saber a sua chave de nome e uma conta da conta armazenamento do Azure. Para obter instruções sobre como obter essas informações, consulte o "como: modo de exibição, copiar e armazenamento gerar as teclas de acesso" seção de [criar, gerenciar, ou excluir uma conta de armazenamento][azure-create-storage-account].  

1. Execute o Explorer de armazenamento do Azure. Se esta for a primeira vez que você tiver executado o Storage Explorer, você será solicitado para o ___nome de conta de armazenamento__ e __chave da conta de armazenamento__. Se você tiver executado-la antes, use o botão __Adicionar__ para adicionar um novo nome de conta de armazenamento e chave.

    Insira o nome e chave da conta de armazenamento usada pelo seu cluster HDinsight e selecione __Salvar e abrir__.

    ![HDI. AzureStorageExplorer][image-azure-storage-explorer]

5. Na lista de contêineres à esquerda da interface, clique no nome do contêiner que está associado ao seu cluster HDInsight. Por padrão, esse é o nome do cluster HDInsight, mas pode ser diferente se você inseriu um nome específico ao criar o cluster.

6. Na barra de ferramentas, selecione o ícone de carregamento.

    ![Barra de ferramentas com ícone de carregamento realçado](./media/hdinsight-upload-data/toolbar.png)

7. Especifique um arquivo para carregar e clique em **Abrir**. Quando solicitado, selecione __carregar__ para carregar o arquivo na raiz do contêiner de armazenamento. Se você deseja carregar o arquivo em um caminho específico, digite o caminho no campo de __destino__ e selecione __carregar__.

    ![Caixa de diálogo de carregamento de arquivo](./media/hdinsight-upload-data/fileupload.png)
    
    Quando o arquivo tiver terminado carregando, você pode usá-lo de trabalhos no cluster HDInsight.

##<a name="mount-azure-blob-storage-as-local-drive"></a>Montar o armazenamento de blob do Microsoft Azure como unidade Local

Consulte o [armazenamento de Blob de montagem Azure como unidade Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##<a name="services"></a>Serviços

###<a name="azure-data-factory"></a>Fábrica de dados do Azure

O serviço de fábrica de dados do Azure é um serviço totalmente gerenciado para compor serviços de movimentação de dados, processamento de dados e armazenamento de dados em canais de produção de dados simplificada, flexível e confiável.

Azure fábrica de dados pode ser usada para mover dados para armazenamento de Blob do Azure ou criar canais de dados que usar recursos de HDInsight como seção e porco diretamente.

Para obter mais informações, consulte a [documentação de fábrica de dados do Azure](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop é uma ferramenta projetada para transferir dados entre Hadoop e bancos de dados relacionais. Você pode usá-lo para importar dados de um sistema de gerenciamento de banco de dados relacional (RDBMS), como SQL Server, MySQL ou Oracle no sistema de arquivos distribuído do Hadoop (HDFS), transformar os dados em Hadoop com MapReduce ou seção e, em seguida, exporte os dados em um RDBMS.

Para obter mais informações, consulte [Usar Sqoop com HDInsight][hdinsight-use-sqoop].

##<a name="development-sdks"></a>SDKs de desenvolvimento

Armazenamento de Blob do Azure também pode ser acessado usando um SDK do Azure de programação seguintes idiomas:

* .NET
* Java
* Node
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar o SDK do Azure, consulte [downloads do Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Solução de problemas

### <a id="storageexception"></a>Exceção de armazenamento para gravação no blob

__Sintomas__: ao usar o `hadoop` ou `hdfs dfs` comandos para gravar os arquivos que estão ~ 12 GB ou mais em um cluster de HBase, você pode encontrar o erro a seguir: 

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

__Causa__: HBase em HDInsight clusters padrão para um tamanho de bloco de 256 KB durante a gravação de armazenamento do Azure. Enquanto isso funciona para HBase APIs ou APIs REST, resultará em um erro ao usar o `hadoop` ou `hdfs dfs` utilitários de linha de comando.

__Resolução__: Use `fs.azure.write.request.size` para especificar um tamanho maior de bloco. Você pode fazer isso em uma base por uso usando o `-D` parâmetro. A seguir é um exemplo utilizando este parâmetro com o `hadoop` comando:

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

Você também pode aumentar o valor de `fs.azure.write.request.size` globalmente usando Ambari. As etapas a seguir podem ser usadas para alterar o valor na interface do usuário do Ambari Web:

1. No seu navegador, vá para a interface do usuário do Ambari Web para o seu cluster. Isso é https://CLUSTERNAME.azurehdinsight.net, onde __CLUSTERNAME__ é o nome do seu cluster.

    Quando solicitado, digite o nome do administrador e senha para o cluster.

2. Do lado esquerdo da tela, selecione __HDFS__e selecione a guia __configurações__ .

3. No campo de __filtro__ , insira `fs.azure.write.request.size`. Isso exibirá o campo e o valor atual no meio da página.

4. Altere o valor de 262144 (256KB) para o novo valor. Por exemplo, 4194304 (4MB).

![Imagem de como alterar o valor por meio de Ambari Web UI](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para obter mais informações sobre como usar o Ambari, consulte [Gerenciar HDInsight clusters usando a interface do usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como obter dados para o HDInsight, leia os artigos a seguir para saber como executar análise:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Enviar trabalhos de Hadoop programaticamente][hdinsight-submit-jobs]
* [Use a seção com HDInsight][hdinsight-use-hive]
* [Usar porco com HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
