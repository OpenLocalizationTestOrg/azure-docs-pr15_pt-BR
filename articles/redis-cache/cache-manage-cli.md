<properties 
    pageTitle="Como criar e gerenciar o Cache de relacionada de Azure usando a Interface de linha do Azure (Azure comando) | Microsoft Azure" 
    description="Saiba como instalar o CLI Azure em qualquer plataforma, como usá-lo para se conectar à sua conta do Azure e como criar e gerenciar um cache relacionada da CLI Azure." 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Como criar e gerenciar o Cache de relacionada de Azure usando a Interface de linha do Azure (Azure comando)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [CLI Azure](cache-manage-cli.md)

O Azure é uma ótima maneira de gerenciar sua infraestrutura Azure a partir de qualquer plataforma. Este artigo mostra como criar e gerenciar suas instâncias de Cache relacionada do Azure usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e gerenciar instâncias de Cache relacionada do Azure usando CLI do Azure, você deve concluir as etapas a seguir.

-   Você deve ter uma conta do Azure. Se você não tiver um, você pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns momentos.
-   [Instalar o Azure CLI](../xplat-cli-install.md).
-   Conectar-se a sua instalação do Azure CLI com uma conta pessoal do Azure ou com um trabalho ou escola conta do Azure e login da CLI do Azure usando o `azure login` comando. Para compreender as diferenças e escolher, consulte [conectar a uma assinatura do Azure a Interface do Azure de linha (Azure comando)](../xplat-cli-connect.md).
-   Antes de executar qualquer um dos seguintes comandos, alternar CLI Azure no modo do Gerenciador de recursos executando o `azure config mode arm` comando. Para obter mais informações, consulte [definir o modo do Gerenciador de recursos do Azure](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode).

## <a name="redis-cache-properties"></a>Propriedades do Cache de relacionada

As seguintes propriedades são usadas ao criar e atualizar instâncias Cache relacionada.

| Propriedade            | Alternar                                  | Descrição                                                                                                                                                                                                                                          |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nome                | -n, - nome                              | Nome do Cache relacionada.                                                                                                                                                                                                                             |
| grupo de recursos      | -g, - grupo de recursos                    | Nome do grupo de recursos.                                                                                                                                                                                                                          |
| local            | -l, - local                          | Local para criar o cache.                                                                                                                                                                                                                            |
| tamanho                | -z, - tamanho                              | Tamanho do Cache relacionada. Valores válidos: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]                                                                                                                                                                  |
| SKU                 | -x, – sku                               | Relacionada SKU. Deve ser um destes: [básico, padrão, Premium]                                                                                                                                                                                             |
| EnableNonSslPort    | -e, - enable-não--porta ssl               | Propriedade EnableNonSslPort do Cache relacionada. Adicionar este sinalizador se você deseja ativar a porta de SSL não para o cache                                                                                                                                    |
| Configuração de relacionada | -c, - configuração relacionada               | Relacionada a configuração. Insira uma cadeia JSON formatado de chaves de configuração e valores aqui. Formato: "{" ":""," ":" "}"                                                                                                                                     |
| Configuração de relacionada | -f, - relacionada--arquivo de configuração          | Relacionada a configuração. Digite o caminho de um arquivo que contém as chaves de configuração e valores aqui. Formato da entrada de arquivo: {"": "","": ""}                                                                                                                |
| Contagem de fragmentar         | -r, - contagem de fragmentar                       | Número de fragmentos de criar em um Cache de Cluster Premium com clusters.                                                                                                                                                                               |
| Rede virtual     | -v, – rede virtual                   | Quando o cache em um VNET de hospedagem, especifica a identificação do recurso BRAÇO exata da rede virtual para implantar o cache relacionada no. Exemplo de formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| tipo de chave            | -t, --tipo de tecla                          | Tipo de chave de renovação. Valores válidos: [principal, secundário]                                                                                                                                                                                             |
| StaticIP            | -p, --ip estático < ip estático >             | Quando o cache em um VNET de hospedagem, especifica um endereço IP exclusivo na sub-rede para o cache. Se não for fornecido, um será escolhido para você da sub-rede.                                                                                                |
| Sub-rede              | t, – sub-rede<subnet>                    | Quando o cache em um VNET de hospedagem, especifica o nome da sub-rede no qual deseja implantar o cache.                                                                                                                                                    |
| VirtualNetwork      | -v, – rede virtual <-rede virtual > | Quando o cache em um VNET de hospedagem, especifica a identificação do recurso BRAÇO exata da rede virtual para implantar o cache relacionada no. Exemplo de formato: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Assinatura        | -s, --assinatura                      | O identificador de assinatura.                                                                                                                                                                                                                         |

## <a name="see-all-redis-cache-commands"></a>Ver todos os comandos de Cache relacionada

Para ver todos os comandos de Cache relacionada e seus parâmetros, use o `azure rediscache -h` comando.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Criar um Cache relacionada

Para criar um Cache relacionada, use o seguinte comando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para obter mais informações sobre esse comando, execute o `azure rediscache create -h` comando.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Excluir um Cache relacionada existente

Para excluir um Cache relacionada, use o seguinte comando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para obter mais informações sobre esse comando, execute o `azure rediscache delete -h` comando.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Listar todos os Caches relacionada dentro de sua assinatura ou grupo de recursos

Para listar todos os Caches relacionada dentro de sua assinatura ou grupo de recursos, use o seguinte comando:

    azure rediscache list [options]

Para obter mais informações sobre esse comando, execute o `azure rediscache list -h` comando.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Mostrar as propriedades de um Cache de relacionada existente

Para mostrar propriedades de um Cache de relacionada existente, use o seguinte comando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Para obter mais informações sobre esse comando, execute o `azure rediscache show -h` comando.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## <a name="change-settings-of-an-existing-redis-cache"></a>Alterar as configurações de um Cache de relacionada existente

Para alterar configurações de um Cache de relacionada existente, use o seguinte comando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Para obter mais informações sobre esse comando, execute o `azure rediscache set -h` comando.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Renovar a chave de autenticação para um Cache relacionada existente

Para renovar a chave de autenticação para um Cache relacionada existente, use o seguinte comando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especificar `Primary` ou `Secondary` para `key-type`.

Para obter mais informações sobre esse comando, execute o `azure rediscache renew-key -h` comando.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Teclas de lista primário e secundário de um Cache de relacionada existente

A lista principal e secundário as teclas de um Cache de relacionada existente, use o seguinte comando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para obter mais informações sobre esse comando, execute o `azure rediscache list-keys -h` comando.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
