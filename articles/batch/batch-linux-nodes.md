<properties
    pageTitle="Nós Linux em pools do Azure lote | Microsoft Azure"
    description="Saiba como processar suas cargas de trabalho de computação paralela em pools de máquinas virtuais de Linux em lote do Azure."
    services="batch"
    documentationCenter="python"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="marsma" />

# <a name="provision-linux-compute-nodes-in-azure-batch-pools"></a>Provisionar nós de computação Linux em pools de lote do Azure

Você pode usar o lote do Azure para executar cargas de trabalho de computação paralela em máquinas virtuais Linux e Windows. Este artigo detalha como criar pools de nós de computação Linux no serviço em lotes usando os dois [Lote Python] [ py_batch_package] e [Lote .NET] [ api_net] bibliotecas de cliente.

> [AZURE.NOTE] [Pacotes de aplicativos](batch-application-packages.md) estão atualmente não suportado em nós de computação Linux.

## <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

Quando você cria um pool de nós de computação no lote, você tem duas opções do qual deseja selecionar o tamanho de nó e o sistema operacional: configuração de serviços de nuvem e configuração de máquina Virtual.

**Configuração de serviços de nuvem** fornece que computação nós *somente*do Windows. Tamanhos de nós de computação disponíveis são listados em [tamanhos para os serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md)e sistemas operacionais disponíveis estão listados na [versões de SO de convidado do Azure e matriz de compatibilidade SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando você cria um pool que contém nós de serviços de nuvem do Azure, você precisa especificar apenas o tamanho de nó e sua "família de sistema operacional", que são encontrados nos artigos mencionados anteriormente. Para nós de computadores pools do Windows, serviços de nuvem é mais comumente usado.

**Configuração de máquina virtual** fornece imagens Linux e Windows para nós de computação. Tamanhos de nós de computação disponíveis são listados em [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e [tamanhos para máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Quando você cria um pool que contém nós de configuração de máquina Virtual, especifique o tamanho de nós, a referência de imagem de máquina virtual e o agente de nó lote SKU para ser instalados em nós.

### <a name="virtual-machine-image-reference"></a>Referência de imagem de máquina virtual

O serviço de lote usa a [escala de máquina Virtual define](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para fornecer nós de computação Linux. As imagens de sistema operacional para essas máquinas virtuais são fornecidas do [Azure Marketplace][vm_marketplace]. Quando você configura uma referência de imagem de máquina virtual, você pode especificar as propriedades de uma imagem de máquina virtual do Marketplace. As seguintes propriedades são necessárias quando você cria uma referência de imagem de máquina virtual:

| **Propriedades de referência de imagem** | **Exemplo** |
| ----------------- | ------------------------ |
| Publisher         | Canônico                |
| Oferta             | UbuntuServer             |
| SKU               | 14.04.4-LTS              |
| Versão           | mais recente                   |

> [AZURE.TIP] Você pode saber mais sobre essas propriedades e como listar imagens do Marketplace no [navegar e selecionar imagens de máquina virtual Linux no Azure com CLI ou PowerShell](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Observe que nem todas as imagens do Marketplace são atualmente compatíveis com o lote. Para obter mais informações, consulte [agente de nó SKU](#node-agent-sku).

### <a name="node-agent-sku"></a>Agente de nó SKU

O agente de nó lote é um programa que é executado em cada nó no pool e fornece a interface de comando e controle entre o nó e o serviço de lote. Há implementações diferentes do agente de nó, conhecido como SKUs, para diferentes sistemas operacionais. Essencialmente, quando você cria uma configuração de máquina Virtual, primeiro você especificar a referência de imagem de máquina virtual e em seguida, especifique o agente de nó instalar na imagem. Normalmente, cada agente de nó SKU é compatível com várias imagens de máquina virtual. Aqui estão alguns exemplos de agente de nó SKUs:

* batch.Node.Ubuntu 14.04
* batch.Node.centos 7
* batch.Node.Windows amd64

> [AZURE.IMPORTANT] Nem todas as imagens de máquinas virtuais que estão disponíveis no mercado são compatíveis com os agentes de nó lote atualmente disponíveis. Você deve usar o SDK do lote para listar o agente de nó disponível SKUs e as imagens de máquina virtual com as quais eles são compatíveis. Consulte a [lista de máquina Virtual imagens](#list-of-virtual-machine-images) neste artigo para obter mais informações.

## <a name="create-a-linux-pool-batch-python"></a>Criar um pool de Linux: Python em lotes

O trecho de código a seguir mostra um exemplo de como usar a [Biblioteca de cliente do Microsoft Azure lote para Python] [ py_batch_package] para criar um pool do servidor de Ubuntu nós de computação. Documentação de referência para o módulo do lote Python pode ser encontrada no [pacote de azure.batch] [py_batch_docs] na leitura a documentos.

Este trecho de código cria um [ImageReference] [ py_imagereference] explicitamente e especifica cada uma das suas propriedades (publisher, oferta, SKU, versão). No código de produção, no entanto, recomendamos que você use o [list_node_agent_skus] [ py_list_skus] método para determinar e selecione as imagem e nó agente SKU combinações disponíveis em tempo de execução.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Conforme mencionado anteriormente, é recomendável que em vez de criar o [ImageReference] [ py_imagereference] explicitamente, você pode usar o [list_node_agent_skus] [ py_list_skus] método selecionem dinamicamente as combinações de imagem do agente/Marketplace nó aceitos atualmente. O trecho de Python a seguir mostra o uso desse método.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Criar um pool de Linux: lote .NET

O trecho de código a seguir mostra um exemplo de como usar o [Lote .NET] [ nuget_batch_net] biblioteca de cliente para criar um pool do servidor de Ubuntu nós de computação. Você pode encontrar a [documentação de referência de lote .NET] [ api_net] no MSDN.

O trecho de código a seguir usa a [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] método para selecionar atualmente na lista de suporte para combinações de SKU de agente de imagem e nó de Marketplace. Essa técnica é desejável, pois a lista de combinações com suporte pode ser alteradas de vez em quando. Mais comumente, combinações suportadas são adicionadas.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Embora o trecho de código anterior usa a [PoolOperations][net_pool_ops]. [ListNodeAgentSkus] [net_list_skus] método de lista e selecione dinamicamente suportado imagem nó agente SKU combinações e (recomendadas), você também pode configurar uma [ImageReference] [ net_imagereference] explicitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista de imagens de máquina virtual

A tabela a seguir lista as imagens de máquina virtual do Marketplace que são compatíveis com os agentes de nó lote disponíveis quando este artigo foi atualizada pela última vez. É importante observar que essa lista não é definitiva porque agentes de nó e imagens podem ser adicionados ou removidos a qualquer momento. Recomendamos que seus serviços e aplicativos de lote sempre usem [list_node_agent_skus] [ py_list_skus] (Python) e [ListNodeAgentSkus] [ net_list_skus] (lote .NET) para determinar e selecione as SKUs atualmente disponíveis.

> [AZURE.WARNING] A lista a seguir pode mudar a qualquer momento. Sempre use os métodos de **agente de nó lista SKU** disponíveis em APIs do lote para listar e selecione o compatível máquina virtual e o nó agente SKUs quando você executa seus trabalhos em lotes.

| **Publisher** | **Oferta** | **Imagem SKU** | **Versão** | **Agente de nó ID de SKU** |
| ------- | ------- | ------- | ------- | ------- |
| Canônico | UbuntuServer | 14.04.0-LTS | mais recente | batch.Node.Ubuntu 14.04 |
| Canônico | UbuntuServer | 14.04.1-LTS | mais recente | batch.Node.Ubuntu 14.04 |
| Canônico | UbuntuServer | 14.04.2-LTS | mais recente | batch.Node.Ubuntu 14.04 |
| Canônico | UbuntuServer | 14.04.3-LTS | mais recente | batch.Node.Ubuntu 14.04 |
| Canônico | UbuntuServer | 14.04.4-LTS | mais recente | batch.Node.Ubuntu 14.04 |
| Canônico | UbuntuServer | 14.04.5-LTS | mais recente | batch.Node.Ubuntu 14.04 |
| Canônico | UbuntuServer | 16.04.0-LTS | mais recente | batch.Node.Ubuntu 16.04 |
| Credativ | Debian | 8 | mais recente | batch.Node.Debian 8 |
| OpenLogic | CentOS | 7.0 | mais recente | batch.Node.centos 7 |
| OpenLogic | CentOS | 7.1 | mais recente | batch.Node.centos 7 |
| OpenLogic | CentOS HPC | 7.1 | mais recente | batch.Node.centos 7 |
| OpenLogic | CentOS | 7.2 | mais recente | batch.Node.centos 7 |
| Oracle | Oracle-Linux | 7.0 | mais recente | batch.Node.centos 7 |
| SUSE | openSUSE | 13.2 | mais recente | batch.Node.opensuse 13.2 |
| SUSE | openSUSE-salto | 42.1 | mais recente | batch.Node.opensuse 42.1 |
| SUSE | SLES HPC | 12 | mais recente | batch.Node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | mais recente | batch.Node.opensuse 42.1 |
| Microsoft-anúncios | padrão-dados-ciência-máquina virtual | padrão-dados-ciência-máquina virtual | mais recente | batch.Node.Windows amd64 |
| Microsoft-anúncios | Linux-dados-ciência-máquina virtual | linuxdsvm | mais recente | batch.Node.centos 7 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | mais recente | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Data Center 2012 | mais recente | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012 R2-Data Center | mais recente | batch.Node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows Server-Technical Preview | mais recente | batch.Node.Windows amd64 |

## <a name="connect-to-linux-nodes"></a>Conectar-se a nós Linux

Durante o desenvolvimento ou durante a solução de problemas, talvez seja necessário entrar para nós no seu pool. Diferentemente de nós de computação do Windows, você não pode usar o protocolo de área de trabalho remota (RDP) para se conectar a nós Linux. Em vez disso, o serviço de lote permite o acesso SSH em cada nó para conexão remota.

O trecho de código Python a seguir cria um usuário em cada nó em um pool, que é necessário para conexão remota. Em seguida, ele imprime as informações de conexão SSH (secure shell) para cada nó.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Aqui está a saída de exemplo para o código anterior para um pool que contém quatro nós Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Observe que, em vez de uma senha, você pode especificar uma chave pública SSH quando você cria um usuário em um nó. No SDK Python, isso é feito usando o parâmetro **ssh_public_key** em [ComputeNodeUser][py_computenodeuser]. No .NET, isso é feito usando o [ComputeNodeUser][net_computenodeuser]. [SshPublicKey] [net_ssh_key] propriedade.

## <a name="pricing"></a>Preços

Lote Azure baseia-se na tecnologia de serviços de nuvem do Azure e máquinas virtuais do Azure. O próprio serviço de lote é oferecido sem custo, o que significa cobrado somente para os recursos de computação que suas soluções de lote consumam. Quando você escolhe a **Configuração de serviços de nuvem**, você será cobrado com base nos [preços de serviços de nuvem] [ cloud_services_pricing] estrutura. Quando você escolhe a **Configuração de máquina Virtual**, você será cobrado com base nas [máquinas virtuais preços] [ vm_pricing] estrutura.

## <a name="next-steps"></a>Próximas etapas

### <a name="batch-python-tutorial"></a>Tutorial de Python em lotes

Para um tutorial mais detalhado sobre como trabalhar com lotes usando Python, confira [Introdução ao cliente do Azure lote Python](batch-python-tutorial.md). Seu companion [amostra de código] [ github_samples_pyclient] inclui uma função de auxiliar, `get_vm_config_for_distro`, que mostra outra técnica para obter uma configuração de máquina virtual.

### <a name="batch-python-code-samples"></a>Exemplos de código do Python em lotes

Confira as outras [amostras de código do Python] [ github_samples_py] nas [amostras de lote azure] [ github_samples] repositório no GitHub para vários scripts que mostram como realizar operações de lote comuns como pool, trabalho e criação de tarefas. O [arquivo Leiame] [ github_py_readme] que acompanha o Python amostras apresenta detalhes sobre como instalar os pacotes necessários.

### <a name="batch-forum"></a>Fórum do lote

[Fórum do Azure lote] [ forum] no MSDN é um ótimo lugar para discutir lote e faça perguntas sobre o serviço. Ler postagens úteis "stickied" e envie suas perguntas conforme eles surgem enquanto você cria soluções lote.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
