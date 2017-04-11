<properties
pageTitle="Restringir o acesso de HDInsight dados usando assinaturas de acesso compartilhado"
description="Saiba como usar assinaturas de acesso compartilhado para restringir o acesso de HDInsight dados armazenados em bolhas de armazenamento do Azure."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/11/2016"
ms.author="larryfr"/>

#<a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Usar assinaturas de acesso compartilhado do Azure armazenamento para restringir o acesso aos dados com HDInsight

HDInsight usa Blobs de armazenamento do Azure para armazenamento de dados. Enquanto HDInsight deve ter acesso total ao blob usado como armazenamento padrão para o cluster, você pode restringir permissões aos dados armazenados em outras bolhas usadas pelo cluster. Por exemplo, talvez você queira tornar alguns dados somente leitura. Você pode fazer isso usando assinaturas de acesso compartilhado.

Assinaturas de acesso compartilhado (SAS) são um recurso de contas de armazenamento do Azure que permite limitar o acesso aos dados. Por exemplo, fornecendo acesso somente leitura aos dados. Neste documento, você aprenderá a usar para habilitar o acesso de leitura e somente lista em um contêiner de blob do HDInsight.

##<a name="requirements"></a>Requisitos

* Uma assinatura do Azure

* C# ou Python. Código de exemplo c# é fornecido como uma solução Visual Studio.

    * O Visual Studio deve ser da versão 2013 ou 2015
    
    * Python deve ser versão 2.7 ou superior

* Um HDInsight baseado em Linux cluster ou [Azure PowerShell] [ powershell] -se você tiver um cluster existente baseado no Linux, você pode usar Ambari para adicionar uma assinatura de acesso compartilhado ao cluster. Caso contrário, você pode usar o PowerShell do Azure para criar um novo cluster e adicionar uma assinatura de acesso compartilhado durante a criação de cluster.

* Os arquivos de exemplo de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Este repositório tem o seguinte:

    * Um projeto do Visual Studio que pode criar um contêiner de armazenamento, armazenada e a política SAS para uso com HDInsight
    
    * Um script de Python que pode criar um contêiner de armazenamento, armazenada e a política SAS para uso com HDInsight
    
    * Um script PowerShell que pode criar um novo cluster HDInsight e configure-o para usar as associações de segurança.

##<a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado

Há duas formas de assinaturas de acesso compartilhado:

* Ad-hoc: A hora de início, hora de vencimento e permissões para as associações de segurança são todos especificado no URI SAS (ou implícita, no caso onde a hora de início for omitida).

* Armazenados política de acesso: uma política de acesso armazenadas é definida em um contêiner de recurso - um contêiner de blob, tabela, fila ou compartilhamento de arquivo - e pode ser usada para gerenciar as restrições para uma ou mais assinaturas de acesso compartilhado. Quando você associa uma SAS uma política de acesso armazenadas, as associações de segurança herda as restrições - a hora de início, hora de vencimento e permissões - definidas para a política de acesso armazenadas.

A diferença entre as duas formas é importante para um cenário de chave: revogação. Uma SAS é uma URL, para que qualquer pessoa que obtém as associações de segurança possa usá-lo, independentemente de quem solicitada começar com. Se uma SAS for publicada publicamente, ele pode ser usado por qualquer pessoa no mundo. Uma associações de segurança que é distribuída é válida até que um dos quatro coisas acontece:

1. O tempo de expiração especificado nas associações de segurança é alcançado.

2. O tempo de expiração especificado na política de acesso armazenadas referenciada pelas associações de segurança é alcançado (se uma política de acesso armazenadas é referenciada, e se ele especifica uma hora de expiração). Ou isso pode ocorrer porque o intervalo é decorrido ou porque você modificou a política de acesso armazenadas para ter uma hora de expiração no passado, que é uma maneira para revogar as associações de segurança.

3. A política de acesso armazenadas referenciada pelas associações de segurança é excluída, que é outra maneira para revogar as associações de segurança. Observe que se você recria a política de acesso armazenadas com exatamente o mesmo nome, todos os tokens SAS existentes novamente será válidos de acordo com as permissões associadas a essa política de acesso armazenadas (pressupondo que o período de expiração nas associações de segurança não passou). Se você está pretendendo revogar as associações de segurança, certifique-se de usar um nome diferente se você recria a política de acesso com uma hora de expiração no futuro.

4. A chave de conta que foi usada para criar as associações de segurança é gerada novamente. Observe que isso fará com que todos os componentes de aplicativo usando essa chave da conta falha autenticar até que eles sejam atualizados para usar a chave de conta você trabalhou ou a outra chave de conta válida.

> [AZURE.IMPORTANT] Uma assinatura de acesso compartilhado URI está associada com a chave de conta usada para criar a assinatura e associada armazenados política de acesso (se houver). Se nenhuma diretiva de acesso armazenadas for especificada, a única maneira de revogar uma assinatura de acesso compartilhado é alterar a chave da conta. 

É recomendável que você sempre usar políticas de acesso armazenadas, para que você possa revogar assinaturas ou estender a data de vencimento, conforme necessário. As etapas neste documento utilizarem armazenados políticas de acesso para gerar SAS.

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [Compreendendo o modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md).

##<a name="create-a-stored-policy-and-generate-a-sas"></a>Criar uma política armazenada e gerar uma SAS

No momento você deve criar uma política armazenada programaticamente. Você pode encontrar o c# e exemplo de Python de criar uma política armazenada e SAS em [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###<a name="create-a-stored-policy-and-sas-using-c"></a>Criar uma política armazenada e SAS usando C\#

1. Abra a solução no Visual Studio.

2. No Solution Explorer, clique com botão direito no projeto __SASToken__ e selecione __Propriedades__.

3. Selecione __configurações__ e adicionar valores para as seguintes entradas:

    * StorageConnectionString: A cadeia de conexão da conta de armazenamento que você deseja criar uma política armazenada e SAS para. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` onde `myaccount` é o nome da sua conta de armazenamento e `mykey` é a chave para a conta de armazenamento.
    
    * ContainerName: O contêiner na conta de armazenamento que você deseja restringir o acesso.
    
    * SASPolicyName: O nome a ser usado para a política armazenada que será criada.
    
    * FileToUpload: O caminho para um arquivo que será carregado para o contêiner.
    
4. Execute o projeto. Será exibida uma janela de console e informações semelhantes à seguinte serão exibidas depois que as associações de segurança tem sido gerada:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Salve o token de política SAS, pois você precisará isto ao associar a conta de armazenamento cluster HDInsight. Você precisará também o nome de conta de armazenamento e o nome do contêiner.
    
###<a name="create-a-stored-policy-and-sas-using-python"></a>Criar uma política armazenada e SAS usando Python

1. Abra o arquivo SASToken.py e altere os seguintes valores:

    * política de\_nome: O nome para a política armazenada que será criada.
    
    * armazenamento\_conta\_nome: O nome da sua conta de armazenamento.
    
    * armazenamento\_conta\_chave: A chave para a conta de armazenamento.
    
    * armazenamento\_contêiner\_nome: O contêiner na conta de armazenamento que você deseja restringir o acesso.
    
    * exemplo\_arquivo\_caminho: O caminho para um arquivo que será carregado para o contêiner

2. Execute o script. Quando o script for concluído, ele exibirá o token SAS similar ao seguinte:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Salve o token de política SAS, pois você precisará isto ao associar a conta de armazenamento cluster HDInsight. Você precisará também o nome de conta de armazenamento e o nome do contêiner.
    
##<a name="use-the-sas-with-hdinsight"></a>Usar as associações de segurança com HDInsight

Ao criar um cluster de HDInsight, você deverá especificar uma conta de armazenamento principal e, opcionalmente, você pode especificar contas de armazenamento adicional. Ambos os métodos de adicionar armazenamento exigem acesso total a contas de armazenamento e contêineres que são usados.

Para poder usar uma assinatura de acesso compartilhado para limitar o acesso a um contêiner, é necessário adicionar uma entrada personalizada para a configuração de __sites principais__ para o cluster.

* Para __Windows__ ou __baseado no Linux__ HDInsight clusters, você pode fazer isso durante a criação de cluster usando o PowerShell.

* __Baseado em Linux__ HDInsight clusters, você alterar a configuração após a criação de cluster usando Ambari.

###<a name="create-a-new-cluster-that-uses-the-sas"></a>Criar um novo cluster que usa as associações de segurança

Um exemplo de criação de um cluster de HDInsight que usa as associações de segurança será incluído no `CreateCluster` diretório do repositório. Para usá-lo, use as seguintes etapas:

1. Abrir o `CreateCluster\HDInsightSAS.ps1` arquivo em um editor de texto e modificar os seguintes valores no início do documento.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Por exemplo, alterar `'mycluster'` para o nome do cluster que você deseja criar. Os valores SAS devem coincidir os valores das etapas anteriores ao criar uma conta de armazenamento e o token SAS.
    
    Depois que você alterou os valores, salve o arquivo.

1. Abra um novo prompt do PowerShell do Azure. Se você estiver familiarizado com o Azure PowerShell ou não tiver instalado, consulte [instalar e configurar o Azure PowerShell][powershell].

2. No prompt, use o seguinte para autenticar à sua assinatura do Azure:

        Login-AzureRmAccount
    
    Quando solicitado, faça logon com a conta para sua assinatura do Azure.
    
    Se seu logon estiver associado a várias assinaturas Azure, você pode precisar usar `Select-AzureRmSubscription` para selecionar a assinatura que deseja usar.

2. No prompt, altere diretórios para o `CreateCluster` diretório que contém o arquivo HDInsightSAS.ps1. Em seguida, use o seguinte para executar o script
        
        .\HDInsightSAS.ps1
    
    Como o script é executado, ele registrará saída prompt do PowerShell como ela cria o recurso contas de grupo e de armazenamento. Ele solicitará que você insira o usuário HTTP para cluster HDInsight. Esta é a conta de usuário usada para proteger o acesso HTTP/s ao cluster.
    
    Se você estiver criando um cluster baseado no Linux, você também será solicitado um nome de conta de usuário SSH e sua senha. Isso é usado para o login remotamente cluster.
    
    > [AZURE.IMPORTANT] Quando solicitado a HTTP/s ou SSH nome de usuário e senha, você deve fornecer uma senha que atenda aos seguintes critérios:
    >
    > - Deve ter pelo menos 10 caracteres de comprimento
    > - Deve conter pelo menos um dígito
    > - Deve conter pelo menos um caractere não alfanuméricos
    > - Deve conter pelo menos uma maiuscula ou letra minúscula


Vai demorar um tempo para este script ser concluída, geralmente cerca de 15 minutos. Quando o script for concluído sem erros, o cluster foi criado.

###<a name="update-an-existing-cluster-to-use-the-sas"></a>Atualizar um cluster existente para usar as associações de segurança

Se você tiver um cluster existente baseado no Linux, você pode adicionar as associações de segurança na configuração de __núcleo sites__ usando as seguintes etapas:

1. Abra a web Ambari da interface do usuário para o seu cluster. O endereço para esta página é https://YOURCLUSTERNAME.azurehdinsight.net. Quando solicitado, autenticar o cluster usando o nome do administrador (admin) e senha usada durante a criação do cluster.

2. Do lado esquerdo da web Ambari da interface do usuário, selecione __HDFS__ e selecione a guia __configurações__ no meio da página.

3. Selecione a guia __Avançado__ e role até encontrar a seção __Custom core-site__ .

4. Expanda a seção de __sites personalizados core__ , e em seguida, role até o fim e selecione o link __Adicionar propriedade...__ . Use os seguintes valores para os campos de __chave__ e __valor__ :

    * __Chave__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Valor__: O SAS retornado pelo aplicativo c# ou Python você executou anteriormente
    
    Substitua o nome de contêiner usado com o c# ou aplicativo SAS __CONTAINERNAME__ . Substitua o nome da conta de armazenamento que você usou __STORAGEACCOUNTNAME__ .

5. Clique no botão __Adicionar__ para salvar esta chave e valor, e em seguida, clique no botão __Salvar__ para salvar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("Adicionando acesso de armazenamento SAS" por exemplo) e clique em __Salvar__.

    Clique em __Okey__ quando as alterações foram concluídas.

    > [AZURE.IMPORTANT] Isso salva as alterações de configuração, mas você deve reiniciar vários serviços antes que a alteração tenha efeito.

6. Na web Ambari da interface do usuário, selecione __HDFS__ na lista à esquerda e selecione __Reinicie todos__ na lista à direita suspensa __Ações de serviço__ . Quando solicitado, selecione __Ativar o modo de manutenção__ e, em seguida, selecione __Conform reinicie todos".

    Repita esse processo para as entradas MapReduce2 e fio COLORIDO na lista à esquerda da página.

7. Depois que essas tem reiniciado, selecione cada um deles e desabilitam o modo de manutenção das __Ações de serviço__ lista suspensa.

##<a name="test-restricted-access"></a>Testar o acesso restrito

Para verificar se você tiver acesso restrito, use os seguintes métodos:

* Para clusters de HDInsight __baseados no Windows__ , use a área de trabalho remota para se conectar ao cluster. Consulte [conectar ao HDInsight usando RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) para obter mais informações.

    Uma vez conectado, use o ícone de __linha de comando do Hadoop__ na área de trabalho para abrir um prompt de comando.

* __Baseado em Linux__ HDInsight clusters, use SSH para se conectar ao cluster. Consulte um dos procedimentos a seguir para obter informações sobre como usar SSH com clusters baseados em Linux:

    * [Use SSH com baseado em Linux Hadoop em HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Use SSH com baseado em Linux Hadoop em HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Uma vez conectado ao cluster, use as seguintes etapas para confirmar que você pode somente leitura e lista de itens na conta de armazenamento de SAS:

1. No prompt, digite o seguinte. Substitua __SASCONTAINER__ com o nome do contêiner criado para a conta de armazenamento SAS. Substitua __SASACCOUNTNAME__ com o nome da conta de armazenamento usado para as associações de segurança:

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Esta lista o conteúdo do contêiner, que deve incluir o arquivo que foi carregado quando o contêiner e SAS foi criado.
    
2. Use o seguinte para verificar que você pode ler o conteúdo do arquivo. Substitua o __SASCONTAINER__ e __SASACCOUNTNAME__ como na etapa anterior. Substitua o __nome do arquivo__ com o nome do arquivo exibido no comando anterior:

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    Isso listará o conteúdo do arquivo.
    
3. Use o seguinte para baixar o arquivo para o sistema de arquivo local:

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Isso irá baixar o arquivo para um arquivo local chamado __Testfile. txt__.

4. Use o seguinte para carregar o arquivo local em um novo arquivo chamado __testupload.txt__ no armazenamento SAS:

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Você receberá uma mensagem semelhante à seguinte:
    
        put: java.io.IOException
        
    Este erro ocorre porque o local de armazenamento é leitura + lista somente. Use o seguinte para colocar os dados no armazenamento padrão para o cluster, que é gravável:
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    Neste momento, a operação deve ser concluído.
    
##<a name="troubleshooting"></a>Solução de problemas

###<a name="a-task-was-canceled"></a>Uma tarefa foi cancelada

__Sintomas__: ao criar um cluster usando o script do PowerShell, você pode receber a seguinte mensagem de erro:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Causa__: este erro pode ocorrer se você usar uma senha para o usuário administrador/HTTP para cluster ou (para baseados em Linux clusters,) o usuário SSH.

__Resolução__: usar uma senha que atenda aos seguintes critérios:

- Deve ter pelo menos 10 caracteres de comprimento
- Deve conter pelo menos um dígito
- Deve conter pelo menos um caractere não alfanuméricos
- Deve conter pelo menos uma maiuscula ou letra minúscula

##<a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como adicionar armazenamento de acesso limitado ao seu cluster HDInsight, saiba outras maneiras de trabalhar com dados em seu cluster:

* [Use a seção com HDInsight](hdinsight-use-hive.md)

* [Usar porco com HDInsight](hdinsight-use-pig.md)

* [Usar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md
