<properties
    pageTitle="Tutorial - Introdução ao cliente do Azure lote Python | Microsoft Azure"
    description="Aprender os conceitos básicos do Azure lote e como desenvolver o serviço de lote com um cenário simples"
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/27/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-python-client"></a>Introdução ao cliente do Azure lote Python

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Aprenda as Noções básicas do [Azure lote] [ azure_batch] e o [Lote Python] [ py_azure_sdk] cliente discutir um pequeno aplicativo lote escrito em Python. Examinaremos como duas uso de scripts de exemplo no serviço de lote para processar uma carga de trabalho paralela nas máquinas virtuais Linux na nuvem e como eles interagem com o [Armazenamento do Azure](./../storage/storage-introduction.md) para recuperação e transferência de arquivo. Você vai aprender um fluxo de trabalho de aplicativo lote comuns e obter uma compreensão base dos componentes principais do lote como trabalhos, tarefas, pools e nós de computadores.

![Fluxo de trabalho do lote solução (basic)][11]<br/>

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha um conhecimento de trabalho de Python e familiaridade com Linux. Ele também pressupõe que você seja capaz de satisfazer os requisitos de criação de conta que são especificados abaixo do Azure e os serviços de armazenamento e lote.

### <a name="accounts"></a>Contas

- **Conta do Microsoft Azure**: se você ainda não tiver uma assinatura do Azure, [Crie uma conta gratuita do Azure][azure_free_account].
- **Conta de lote**: depois que você tiver uma assinatura do Azure, [Crie uma conta de lote do Azure](batch-account-create-portal.md).
- **Conta de armazenamento**: consulte [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).

### <a name="code-sample"></a>Amostra de código

[Amostra de código] tutorial Python[ github_article_samples] é um dos exemplos de código de lote muitos localizado nas [amostras de lote azure] [ github_samples] repositório no GitHub. Você pode baixar todos os exemplos clicando **clonar ou download > baixar ZIP** na home page do repositório ou clicando o [azure-lote-amostras-master.zip] [ github_samples_zip] link de download direto. Depois de extrair o conteúdo do arquivo ZIP, os dois scripts para este tutorial são encontrados na `article_samples` diretório:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Ambiente de Python

Para executar o script de exemplo *python_tutorial_client.py* na sua estação de trabalho local, você precisa de um **intérprete Python** compatível com versão **2.7** ou **3,3 +**. O script foi testado no Linux e Windows.

### <a name="cryptography-dependencies"></a>dependências de criptografia

Você deve instalar as dependências para a [criptografia] [ crypto] biblioteca, necessária para o `azure-batch` e `azure-storage` pacotes de Python. Execute uma das seguintes operações apropriadas para sua plataforma ou consulte a [instalação de criptografia] [ crypto_install] detalhes para obter mais informações:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] Se instalar para Python 3.3 + no Linux, use os equivalentes de python3 para as dependências de Python. Por exemplo, em Ubuntu:`apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### <a name="azure-packages"></a>Pacotes Azure

Em seguida, instale os pacotes de **Lote do Azure** e Python de **Armazenamento do Azure** . Você pode fazer isso com **pip** e *requirements.txt* encontradas aqui:

`/azure-batch-samples/Python/Batch/requirements.txt`

Problema **pip** comandos para instalar os pacotes de armazenamento e lote a seguir:

`pip install -r requirements.txt`

Ou, você pode instalar o [azure-lote] [ pypi_batch] e [armazenamento do azure] [ pypi_storage] Python pacotes manualmente:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [AZURE.TIP] Talvez você precise prefixo seus comandos com `sudo` se você estiver usando uma conta sem privilégios. Por exemplo, `sudo pip install -r requirements.txt`. Para obter mais informações sobre como instalar pacotes de Python, consulte [Instalar pacotes] [ pypi_install] em readthedocs.io.

## <a name="batch-python-tutorial-code-sample"></a>Amostra de código tutorial de Python em lotes

O exemplo de código tutorial do lote Python consiste em dois scripts Python e alguns arquivos de dados.

- **python_tutorial_client.py**: interage com os serviços de armazenamento e lote para executar uma carga de trabalho paralela em nós de computação (máquinas virtuais). O script *python_tutorial_client.py* executará na sua estação de trabalho local.

- **python_tutorial_task.py**: nós Azure para realizar o trabalho real do script que é executado em computadores. No exemplo, *python_tutorial_task.py* analisa o texto em um arquivo baixado do armazenamento do Azure (o arquivo de entrada). Em seguida, ela produz um arquivo de texto (o arquivo de saída) que contém uma lista das três palavras principais que aparecem no arquivo de entrada. Após criar o arquivo de saída, *python_tutorial_task.py* carrega o arquivo ao armazenamento do Azure. Isso torna disponíveis para download para o script de cliente em execução na estação de trabalho. O script *python_tutorial_task.py* é executado em paralelo em vários nós de computação no serviço em lotes.

- **./data/taskdata\*. txt**: esses arquivos de três texto fornecem a entrada para as tarefas que são executadas em nós de computação.

O diagrama a seguir ilustra as operações principais que são executadas pelos scripts de cliente e tarefa. Este fluxo de trabalho básico é típico de muitas soluções de computação que são criadas com o lote. Enquanto não Demonstre cada recurso disponível no serviço de lote, quase todos os cenários de lote incluem partes deste fluxo de trabalho.

![Fluxo de trabalho de exemplo em lotes][8]<br/>

[**Etapa 1.**](#step-1-create-storage-containers) Crie **contêineres** no armazenamento de Blob do Azure.<br/>
[**Etapa 2.**](#step-2-upload-task-script-and-data-files) Carregar arquivos de script e entrada de tarefa para contêineres.<br/>
[**Etapa 3.**](#step-3-create-batch-pool) Crie um lote de **pool**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** O pool **StartTask** downloads o script de tarefa (python_tutorial_task.py) para nós ao entrarem o pool.<br/>
[**Etapa 4.**](#step-4-create-batch-job) Crie um lote de **trabalho**.<br/>
[**Etapa 5.**](#step-5-add-tasks-to-job) Adicione **tarefas** ao trabalho.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** As tarefas são agendadas para executar em nós.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarefa downloads de seus dados de entrada do armazenamento do Azure e começa a execução.<br/>
[**Etapa 6.**](#step-6-monitor-tasks) Monitorar tarefas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Como as tarefas são concluídas, elas carregue seus dados de saída ao armazenamento do Azure.<br/>
[**Etapa 7.**](#step-7-download-task-output) Baixe a saída de tarefa do armazenamento.

Conforme mencionada, não cada solução lote executa estas etapas exatas e pode incluir muitos mais, mas este demonstra processos comuns encontrados em uma solução de lote.

## <a name="prepare-client-script"></a>Preparar o script de cliente

Antes de executar o exemplo, adicione suas credenciais de conta de armazenamento e lote para *python_tutorial_client.py*. Se você tiver não fez isso, abra o arquivo no seu editor favorito e atualize as seguintes linhas com as suas credenciais.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Você pode encontrar suas credenciais de conta de armazenamento e lote dentro a lâmina de conta de cada serviço no [portal do Azure][azure_portal]:

![Credenciais no portal de lote][9]
![credenciais de armazenamento no portal][10]<br/>

Nas seções a seguir, vamos analisar as etapas usadas pelos scripts para processar uma carga de trabalho no serviço em lotes. Recomendamos que você para consultar regularmente os scripts em seu editor enquanto você progredir o restante do artigo.

Navegue até a seguinte linha no **python_tutorial_client.py** iniciar com etapa 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Etapa 1: Criar contêineres de armazenamento

![Criar contêineres em armazenamento do Azure][1]
<br/>

Lote inclui suporte interno para interagir com o armazenamento do Azure. Contêineres na sua conta de armazenamento fornecerá os arquivos necessários às tarefas que são executadas em sua conta do lote. Os contêineres também fornecem um local para armazenar os dados de saída que produzem as tarefas. A primeira coisa que o script *python_tutorial_client.py* faz é criar três contêineres no [Armazenamento de Blob do Azure](../storage/storage-introduction.md#blob-storage):

- **aplicativo**: este contêiner armazenará o script Python executado pelas tarefas, *python_tutorial_task.py*.
- **entrada**: tarefas serão baixadas os arquivos de dados para processar a partir do contêiner de *entrada* .
- **saída**: quando tarefas concluir processamento de arquivo de entrada, elas serão carregadas os resultados ao contêiner de *saída* .

Para interagir com uma conta de armazenamento e criar contêineres, podemos usar o [armazenamento do azure] [ pypi_storage] pacote para criar um [BlockBlobService] [ py_blockblobservice] objeto – o "cliente do blob". Vamos criar três contêineres na conta de armazenamento usando o cliente de blob.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Depois que os contêineres foram criados, o aplicativo agora pode carregar os arquivos que serão usados pelas tarefas.

> [AZURE.TIP] [Como usar o armazenamento de Blob do Azure do Python](../storage/storage-python-how-to-use-blob-storage.md) fornece uma boa visão geral de trabalhar com blobs e contêineres de armazenamento do Azure. Ele deve estar na parte superior da sua lista de leitura como começar a trabalhar com lote.

## <a name="step-2-upload-task-script-and-data-files"></a>Etapa 2: Carregar arquivos de script e dados de tarefa

![Carregar arquivos de entrada (dados) e aplicativo de tarefa para contêineres][2]
<br/>

Na operação de carregamento de arquivo, *python_tutorial_client.py* primeiro define coleções dos caminhos de arquivo de **entrada** de **aplicativos** e como elas existem no computador local. Em seguida, ele carrega esses arquivos para os contêineres que você criou na etapa anterior.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Usando a compreensão da lista, o `upload_file_to_container` função é chamada para cada arquivo nas coleções de arquivos e dois [ResourceFile] [ py_resource_file] coleções são preenchidas. O `upload_file_to_container` função aparece abaixo:

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles

Uma [ResourceFile] [ py_resource_file] fornece tarefas em lote com a URL para um arquivo em armazenamento do Azure que são baixadas para um nó de computação antes dessa tarefa é executada. O [ResourceFile][py_resource_file]. propriedade de **blob_source** Especifica a URL completa do arquivo conforme ela existe no armazenamento do Azure. A URL também pode incluir uma assinatura de acesso compartilhado (SAS) que fornece acesso seguro para o arquivo. A maioria dos tipos de tarefa no lote incluem uma propriedade de *ResourceFiles* , incluindo:

- [CloudTask][py_task]
- [StartTask][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

Este exemplo não usa os tipos de tarefa JobPreparationTask ou JobReleaseTask, mas você pode ler mais sobre eles em [nós de computadores de tarefas de conclusão e preparação de trabalho de executar em lote do Azure](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Assinatura de acesso compartilhado (SAS)

Assinaturas de acesso compartilhado são cadeias de caracteres que fornecem acesso seguro a contêineres e blobs no armazenamento do Azure. O script *python_tutorial_client.py* usa os dois blob e contêiner compartilhado assinaturas de acesso e demonstra como obter essas cadeias de caracteres de assinatura de acesso compartilhado do serviço de armazenamento.

- **Blob compartilhados assinaturas de acesso**: usos de StartTask do pool blob assinaturas de acesso compartilhado ao baixar os arquivos de dados de entrada e de script da tarefa do armazenamento (consulte a [etapa 3](#step-3-create-batch-pool) abaixo). O `upload_file_to_container` função *python_tutorial_client.py* contém o código que obtém a assinatura de acesso compartilhado de cada blob. Ela faz isso chamando [BlockBlobService.make_blob_url] [ py_make_blob_url] no módulo de armazenamento.

- **Assinatura de acesso compartilhado contêiner**: que cada tarefa terminar seu trabalho no nó de computadores, ele carrega o seu arquivo de saída para o contêiner de *saída* no armazenamento do Azure. Para fazer isso, *python_tutorial_task.py* usa uma assinatura de acesso compartilhado de contêiner que fornece acesso de gravação para o contêiner. O `get_container_sas_token` função no *python_tutorial_client.py* obtém assinatura de acesso compartilhado do contêiner, que é então passada como um argumento de linha de comando para as tarefas. Etapa 5 de #, [Adicionar tarefas a um trabalho](#step-5-add-tasks-to-job), discute o uso do contêiner SAS.

> [AZURE.TIP] Confira a série de duas partes sobre assinaturas de acesso compartilhado, [parte 1: Noções básicas sobre o modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) e [parte 2: criar e usar um com o serviço de Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), para saber mais sobre como fornecer acesso seguro a dados em sua conta de armazenamento.

## <a name="step-3-create-batch-pool"></a>Etapa 3: Criar o pool de lote

![Criar um pool de lote][3]
<br/>

Um lote **pool** é uma coleção de nós de computação (máquinas virtuais) no qual o lote executa tarefas de um trabalho.

Depois que ele carrega os arquivos de script e dados de tarefa para a conta de armazenamento, *python_tutorial_client.py* começa sua interação com o serviço de lote usando o módulo de lote Python. Para fazer isso, uma [BatchServiceClient] [ py_batchserviceclient] é criada:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Em seguida, um pool de nós de computação é criado na conta lote com uma chamada para `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Quando você cria um pool, você define um [PoolAddParameter] [ py_pooladdparam] que especifica várias propriedades para o pool:

- **ID** do pool (*id* - obrigatório)<p/>Como com a maioria das entidades em lote, seu novo pool deve ter uma ID exclusiva dentro de sua conta de lote. Seu código se refere a esse pool usando sua ID, e é como você identificar o pool no [portal]do Azure[azure_portal].

- **Número de nós de computação** (*target_dedicated* - obrigatório)<p/>Esta propriedade especifica quantos VMs devem ser implantadas no pool. É importante observar que todas as contas de lote tem padrão **cota** que limita o número de **cores** (e, assim, nós de computação) em uma conta de lote. Você pode encontrar as cotas padrão e instruções sobre como [Aumentar cota](batch-quota-limit.md#increase-a-quota) (como o número máximo de cores em sua conta do lote) [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md). Se você achar que se perguntando "Por que não meu pool atinja mais de X nós?" a causa pode ser essa cota de núcleo.

- **Sistema operacional** para nós (*virtual_machine_configuration* **ou** *cloud_service_configuration* - obrigatório)<p/>No *python_tutorial_client.py*, podemos criar um pool de nós do Linux usando um [VirtualMachineConfiguration][py_vm_config]. O `select_latest_verified_vm_image_with_node_agent_sku` funcionar no `common.helpers` simplifica o trabalho com o [Azure Marketplace de máquinas virtuais] [ vm_marketplace] imagens. Consulte [Linux provisionar nós em lote Azure pools de computadores](batch-linux-nodes.md) para obter mais informações sobre como usar imagens do Marketplace.

- **Tamanho de nós de computação** (*vm_size* - obrigatório)<p/>Já que estamos especificando nós Linux para nosso [VirtualMachineConfiguration][py_vm_config], podemos especificar um tamanho de máquina virtual (`STANDARD_A1` neste exemplo) de [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md). Novamente, consulte [nós em lote Azure pools de computadores provisionar Linux](batch-linux-nodes.md) para obter mais informações.

- **Iniciar tarefa** (*start_task* - não obrigatório)<p/>Junto com o acima de propriedades de nó físico, você também pode especificar um [StartTask] [ py_starttask] para o pool (não é necessário). O StartTask é executado em cada nó conforme o nó ingressa o pool e cada vez que um nó seja reiniciado. O StartTask é especialmente útil para preparar nós de computação para a execução de tarefas, como instalar os aplicativos que executam suas tarefas.<p/>Neste aplicativo de exemplo, o StartTask copia os arquivos que baixa de armazenamento (que são especificados usando a propriedade de **resource_files** do StartTask) a StartTask *diretório de trabalho* para a pasta *compartilhada* que todas as tarefas em execução no nó podem acessar. Essencialmente, isso copia `python_tutorial_task.py` à pasta compartilhada em cada nó como o nó ingressará pool, para que todas as tarefas que executar no nó pode acessar que ele.

Você pode observar a chamada para a `wrap_commands_in_shell` função auxiliar. Esta função leva uma coleção de comandos separados e cria uma única linha de comando apropriado para a propriedade de linha de comando de uma tarefa.

Também notável no trecho de código acima é o uso de duas variáveis de ambiente na propriedade **linha_de_comando** do StartTask: `AZ_BATCH_TASK_WORKING_DIR` e `AZ_BATCH_NODE_SHARED_DIR`. Cada nó de computação dentro de um pool de lote é configurado automaticamente com diversas variáveis de ambiente que são específicas ao lote. Qualquer processo que é executado por uma tarefa tem acesso a essas variáveis de ambiente.

> [AZURE.TIP] Para saber mais sobre as variáveis de ambiente que estão disponíveis em nós de computação em um pool de lote, bem como informações sobre pastas de trabalho de tarefa, consulte **configurações de ambiente para tarefas** e **arquivos e pastas** na [Visão geral dos recursos de lote do Azure](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Etapa 4: Criar trabalho em lotes

![Criar trabalho em lotes][4]<br/>

Um lote de **trabalho** é um conjunto de tarefas e está associado a um pool de nós de computação. As tarefas em um trabalho executar em nós de computação do pool associado.

Você pode usar um trabalho não só para organizar e controlar tarefas em cargas de trabalho relacionadas, mas também para impor determinadas restrições, como o tempo de execução máximo para o trabalho (e por extensão, suas tarefas) e prioridade em relação a outros trabalhos na conta do lote de trabalho. No entanto, neste exemplo, o trabalho está associado apenas com o pool criado na etapa 3 de #. Sem propriedades adicionais são configuradas.

Todos os trabalhos de lote estão associados um determinado pool. Esta associação indica quais nós tarefas do trabalho executar em. Especificar o pool usando o [PoolInformation] [ py_poolinfo] propriedade, conforme mostrado no trecho de código abaixo.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Agora que um trabalho tiver sido criado, as tarefas são adicionadas para realizar o trabalho.

## <a name="step-5-add-tasks-to-job"></a>Etapa 5: Adicionar tarefas ao trabalho

![Adicionar tarefas ao trabalho][5]<br/>
*(1) tarefas são adicionadas ao trabalho, (2) as tarefas são agendadas para executar em nós e (3) as tarefas baixar os arquivos de dados para processar*

Em lotes **tarefas** são as unidades individuais de trabalho que são executados em nós de computação. Uma tarefa tem uma linha de comando e executa os scripts ou executáveis que você especificar nessa linha de comando.

Para realizar o trabalho realmente, tarefas devem ser adicionadas a um trabalho. Cada [CloudTask] [ py_task] está configurado com uma propriedade de linha de comando e [ResourceFiles] [ py_resource_file] (como com StartTask do pool) que a tarefa baixa a nó antes de sua linha de comando é executada automaticamente. No exemplo, a cada tarefa processa somente um arquivo. Portanto, sua coleção de ResourceFiles contém um único elemento.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] Quando acessam variáveis de ambiente como `$AZ_BATCH_NODE_SHARED_DIR` ou executar um aplicativo não encontrado no nó `PATH`, linhas de comando de tarefa deve chamar o shell explicitamente, como com `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Esse requisito é desnecessário se suas tarefas executar um aplicativo no nó `PATH` e não fazem referência a qualquer variável de ambiente.

Dentro do `for` loop no trecho de código acima, você pode ver que a linha de comando para a tarefa é criada com cinco argumentos de linha de comando que são passados para *python_tutorial_task.py*:

1. **caminho de arquivo**: Este é o caminho local para o arquivo conforme ela existe no nó. Quando o ResourceFile objeto no `upload_file_to_container` foi criado na etapa 2 acima, o nome de arquivo foi usado para essa propriedade (o `file_path` parâmetro no construtor ResourceFile). Isso indica que o arquivo pode ser encontrado no mesmo diretório no nó *python_tutorial_task.py*.

2. **NUMWORDS**: as palavras de *N* superiores devem ser escritas para o arquivo de saída.

3. **storageaccount**: O nome da conta de armazenamento que possui o contêiner para o qual a saída de tarefa deve ser carregada.

4. **storagecontainer**: O nome do contêiner de armazenamento para o qual os arquivos de saída devem ser carregados.

5. **sastoken**: A assinatura de acesso compartilhado (SAS) que fornece acesso de gravação ao contêiner de **saída** no armazenamento do Azure. O script *python_tutorial_task.py* usa esta assinatura de acesso compartilhado quando cria sua referência de BlockBlobService. Fornece acesso de gravação para o contêiner sem a necessidade de uma tecla de acesso para a conta de armazenamento.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Etapa 6: Tarefas de Monitor

![Monitorar tarefas][6]<br/>
*O script (1) monitora as tarefas de status de conclusão e (2) as tarefas carregar dados de resultado ao armazenamento do Azure*

Quando tarefas são adicionadas a um trabalho, eles são automaticamente na fila e agendados para execução em nós de computação dentro do pool associados ao trabalho. Com base nas configurações que você especificar, o lote manipula fila de todas as tarefas, agendamento, repetindo e outras tarefas de administração de tarefa para você.

Há muitas abordagens para monitorar a execução da tarefa. O `wait_for_tasks_to_complete` função no *python_tutorial_client.py* fornece um exemplo simples de monitoramento de tarefas para um determinado estado, nesse caso, a [concluídas] [ py_taskstate] estado.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Etapa 7: Baixar saída de tarefa

![Baixar saída tarefas de armazenamento][7]<br/>

Agora que o trabalho for concluído, a saída das tarefas pode ser baixada do armazenamento do Azure. Isso é feito com uma chamada para `download_blobs_from_container` em *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] A chamada para `download_blobs_from_container` em *python_tutorial_client.py* Especifica que os arquivos devem ser baixados para seu diretório inicial. Fique à vontade para modificar esse local de saída.

## <a name="step-8-delete-containers"></a>Etapa 8: Contêineres de excluir

Porque cobrado para dados que reside no armazenamento do Azure, sempre é uma boa ideia para remover qualquer bolhas que não são mais necessários para seus trabalhos em lotes. Em *python_tutorial_client.py*, isso é feito com três chamadas para [BlockBlobService.delete_container][py_delete_container]:

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Etapa 9: Excluir o trabalho e o pool

Na etapa final, você será solicitado a excluir o trabalho e o pool que foram criados pelo script *python_tutorial_client.py* . Embora você não é cobrados para trabalhos e tarefas, *são* cobrados para nós de computação. Portanto, recomendamos que você alocar nós conforme necessário. Excluir pools não utilizados pode ser parte do processo de manutenção.

Do BatchServiceClient [JobOperations] [ py_job] e [PoolOperations] [ py_pool] têm correspondente métodos de exclusão, que são chamados se você confirmar exclusão:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] Tenha em mente que cobrado para recursos de computação-- excluir pools não utilizados minimizará custo. Além disso, esteja ciente de que a exclusão de um pool exclui todos os nós de computação dentro desse pool e que os dados em nós serão irrecuperáveis depois que o pool é excluído.

## <a name="run-the-sample-script"></a>Execute o script de amostra

Quando você executar o script *python_tutorial_client.py* tutorial [amostra de código][github_article_samples], a saída de console é semelhante à seguinte. Há uma pausa em `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` enquanto nós de computação do pool são criados, iniciado, e os comandos na tarefa do início do pool são executados. Usar o [portal do Azure] [ azure_portal] para monitorar seu pool, calcular nós, trabalho e tarefas durante e após a execução. Usar o [portal do Azure] [ azure_portal] ou do [Microsoft Azure Storage Explorer] [ storage_explorer] para exibir os recursos de armazenamento (contêineres e blobs) que são criados pelo aplicativo.

>[AZURE.TIP] Execute o script *python_tutorial_client.py* de dentro do `azure-batch-samples/Python/Batch/article_samples` diretório. Ele usa um caminho relativo para o `common.helpers` importação de módulo, portanto, você poderá ver `ImportError: No module named 'common'` se você não executar o o script a partir dentro dessa pasta.

Tempo de execução típico é **aproximadamente 5 a 7 minutos** quando você executa o exemplo em sua configuração padrão.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Próximas etapas

Fique à vontade para fazer alterações *python_tutorial_client.py* e *python_tutorial_task.py* para experimentar com cenários de computação diferentes. Por exemplo, tente adicionar um atraso de execução para *python_tutorial_task.py* para simular tarefas de longa execução e monitorá-los no portal. Tente adicionar mais tarefas ou ajustando o número de nós de computação. Adicione lógica para verificar e permite o uso de um pool existente para acelerar o tempo de execução.

Agora que você estiver familiarizado com o fluxo de trabalho básico de uma solução de lote, é hora de se aprofundar nos recursos adicionais do serviço do lote.

- Examine o artigo [recursos de visão geral do Azure lote](batch-api-basics.md) , que é recomendável se você está começando a usar o serviço.
- Iniciar nos outros artigos de desenvolvimento do lote em **desenvolvimento profundamente** o [caminho de aprendizagem do lote][batch_learning_path].
- Fazer check-out de uma implementação diferente de processamento a carga de trabalho de "palavras primeiras N" com lote na [TopNWords] [ github_topnwords] amostra.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Criar contêineres em armazenamento do Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Carregar arquivos de entrada (dados) e aplicativo de tarefa para contêineres"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Criar pool de lote"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Criar trabalho em lotes"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Adicionar tarefas ao trabalho"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Monitorar tarefas"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Baixar saída tarefas de armazenamento"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Fluxo de trabalho do lote solução (diagrama completo)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Credenciais de lote no Portal"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Credenciais de armazenamento no Portal"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Fluxo de trabalho do lote solução (diagrama mínimo)"
