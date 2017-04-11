<properties 
    pageTitle="Configurar uma cadeia de Conexão para o armazenamento do Azure | Microsoft Azure"
    description="Configure uma cadeia de conexão para uma conta de armazenamento do Azure. Uma cadeia de conexão inclui as informações necessárias para autenticar o acesso a uma conta de armazenamento de seu aplicativo em tempo de execução."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="configure-azure-storage-connection-strings"></a>Configurar cadeias de caracteres de Conexão de armazenamento do Azure

## <a name="overview"></a>Visão geral

Uma cadeia de conexão inclui as informações de autenticação necessárias para acessar dados em uma conta de armazenamento do Azure de seu aplicativo em tempo de execução. Você pode configurar uma cadeia de conexão para:

- Conecte ao emulador de armazenamento do Azure.
- Acesse uma conta de armazenamento do Azure.
- Acesse recursos especificados no Azure por meio de uma assinatura de acesso compartilhado (SAS).

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Armazenar a cadeia de caracteres de conexão

Seu aplicativo precisará acessar a cadeia de conexão no tempo de execução para autenticar solicitações feitas ao armazenamento do Azure. Você tem algumas opções diferentes para armazenar a cadeia de caracteres de conexão:

- Para um aplicativo é executado na área de trabalho ou em um dispositivo, você pode armazenar a cadeia de conexão em um `app.config `arquivo ou um `web.config` arquivo. Adicione a cadeia de conexão na seção **AppSettings** .
- Para um aplicativo em execução em um serviço de nuvem Azure, você pode armazenar a cadeia de conexão no [arquivo de esquema (.cscfg) de configuração do serviço Azure](https://msdn.microsoft.com/library/ee758710.aspx). Adicione a cadeia de conexão na seção **ConfigurationSettings** do arquivo de configuração do serviço.
- Você também pode usar a cadeia de conexão diretamente no seu código. Para a maioria dos cenários, no entanto, recomendamos que você armazena a cadeia de caracteres de configuração em um arquivo de configuração.

Armazenar a cadeia de caracteres de conexão dentro de um arquivo de configuração facilita atualizar a cadeia de conexão para alternar entre o emulador de armazenamento e uma conta de armazenamento do Azure na nuvem. Você só precisa editar a cadeia de conexão para apontar para seu ambiente de destino.

Você pode usar a classe de [Gerenciador de configuração do Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para acessar sua cadeia de conexão no tempo de execução, independentemente de onde o aplicativo está sendo executado.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Criar uma cadeia de conexão para o emulador de armazenamento

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md) para saber mais sobre o emulador de armazenamento.

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Criar uma cadeia de conexão para uma conta de armazenamento do Azure

Para criar uma cadeia de conexão para sua conta de armazenamento do Azure, use o formato de cadeia de conexão abaixo. Indicar se você deseja se conectar a conta de armazenamento através de HTTPS (recomendado) ou HTTP, substitua `myAccountName` com o nome de sua conta de armazenamento e substituir `myAccountKey` com a chave de acesso de conta:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Por exemplo, a cadeia de caracteres de conexão terá aparência semelhante à cadeia de caracteres de conexão exemplo seguinte:

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Armazenamento do Azure dá suporte a HTTP e HTTPS em uma cadeia de conexão; No entanto, usando HTTPS é altamente recomendável.

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Criar uma cadeia de conexão usando uma assinatura de acesso compartilhado

[AZURE.INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Criando uma cadeia de conexão para um ponto de extremidade de armazenamento explícita

Você pode especificar explicitamente os pontos de extremidade do serviço em sua cadeia de conexão em vez de usar os pontos de extremidade do padrão. Para criar uma cadeia de conexão que especifica um ponto de extremidade explícito, especifique o ponto de extremidade do serviço completa para cada serviço, incluindo a especificação de protocolo (HTTPS (recomendado) ou HTTP), no seguinte formato:

    DefaultEndpointsProtocol=[http|https];
    BlobEndpoint=myBlobEndpoint;
    QueueEndpoint=myQueueEndpoint;
    TableEndpoint=myTableEndpoint;
    FileEndpoint=myFileEndpoint;
    AccountName=myAccountName;
    AccountKey=myAccountKey

Um cenário onde você poderá especificar um ponto de extremidade explícito é se você tiver mapeado seu ponto de extremidade de armazenamento de Blob para um domínio personalizado. Nesse caso, você pode especificar seu ponto de extremidade personalizado para o armazenamento de Blob em sua cadeia de conexão e, opcionalmente, especificar os pontos de extremidade padrão para o outro serviço se seu aplicativo usa-los.

Estes são exemplos de cadeias de caracteres de conexão válida que especifica um ponto de extremidade explícito do serviço Blob:

    # Blob endpoint only
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    AccountName=storagesample;
    AccountKey=account-key

    # All service endpoints
    DefaultEndpointsProtocol=https;
    BlobEndpoint=www.mydomain.com;
    FileEndpoint=myaccount.file.core.windows.net;
    QueueEndpoint=myaccount.queue.core.windows.net;
    TableEndpoint=myaccount;
    AccountName=storagesample;
    AccountKey=account-key

O valor de ponto de extremidade que está listado na cadeia de conexão é usado para construir a solicitação URIs serviço Blob e ele determina o formulário de qualquer URIs que são retornados ao seu código.

Observe que se você optar por omitir um ponto de extremidade do serviço da cadeia de conexão, em seguida, não será capaz de usar essa cadeia de caracteres de conexão para acessar dados nesse serviço de seu código.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Criando uma cadeia de conexão com um sufixo de ponto de extremidade

Para criar uma cadeia de conexão de serviço de armazenamento em regiões ou instâncias com sufixos de ponto de extremidade diferente, como China Azure ou gestão do Azure, use o seguinte formato de cadeia de conexão. Indique se você deseja conectar-se para a conta de armazenamento por meio de HTTP ou HTTPS, substitua `myAccountName` com o nome da sua conta de armazenamento, substitua `myAccountKey` com tecla de acesso de conta e substituir `mySuffix` com o sufixo URI:


    DefaultEndpointsProtocol=[http|https];
    AccountName=myAccountName;
    AccountKey=myAccountKey;
    EndpointSuffix=mySuffix;


Por exemplo, a cadeia de conexão deve ser semelhante à seguinte cadeia de caracteres de conexão:

    DefaultEndpointsProtocol=https;
    AccountName=storagesample;
    AccountKey=<account-key>;
    EndpointSuffix=core.chinacloudapi.cn;

## <a name="parsing-a-connection-string"></a>Analisar uma cadeia de conexão

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## <a name="next-steps"></a>Próximas etapas

- [Usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md)
- [Gerenciadores de armazenamento do Azure](storage-explorers.md)
- [Usando assinaturas de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md)
