<properties 
   pageTitle="Criar zonas DNS e gravar conjuntos no DNS Azure usando o SDK do .NET | Microsoft Azure" 
   description="Como criar conjuntos de registros e zonas DNS no DNS Azure usando o SDK do .NET." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/19/2016"
   ms.author="jtuliani"/>


# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Criar zonas DNS e conjuntos de registros usando o SDK do .NET

Você pode automatizar operações para criar, excluir ou atualizar zonas, conjuntos de registros e registros DNS usando DNS SDK com a biblioteca de gerenciamento de DNS do .NET. Um projeto completo do Visual Studio está disponível [aqui.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Criar uma conta de serviço principal

Normalmente, programação acesso aos recursos Azure é concedido por meio de uma conta dedicada em vez de suas próprias credenciais de usuário. Essas contas dedicadas são chamadas de contas 'serviço principal'. Para usar o projeto de amostra do SDK do Azure DNS, primeiro é necessário criar uma conta de serviço principal e atribua-as permissões corretas.

1. Siga [estas instruções](../resource-group-authenticate-service-principal.md) para criar uma conta de serviço principal (o projeto de amostra do SDK do Azure DNS pressupõe a autenticação baseada em senha.)

2. Criar um grupo de recursos ([aqui está como](../azure-portal/resource-group-portal.md)).

3. Use o Azure RBAC para conceder a conta de serviço principal permissões de 'Colaborador de zona de DNS' ao grupo de recursos ([aqui está como](../active-directory/role-based-access-control-configure.md).)

4. Se usando o projeto de amostra do SDK do Azure DNS, edite o arquivo de 'program.cs' da seguinte maneira:
    * Insira os valores corretos para tenantId, clientId (também conhecido como ID de conta), segredo (senha de conta principal de serviço) e subscriptionId como usado na etapa 1.
    * Insira o nome do grupo de recursos escolhido na etapa 2.
    * Insira um nome de zona DNS da sua escolha.

## <a name="nuget-packages-and-namespace-declarations"></a>Pacotes do NuGet e declarações de namespace

Para usar o SDK do .NET do Azure DNS, você precisa instalar o pacote de NuGet **Biblioteca de gerenciamento de DNS do Azure** e outros pacotes de Azure necessários.
 
1. No **Visual Studio**, abra um projeto ou um novo projeto. 

2. Vá para **Ferramentas** **>** **Gerenciador de pacote NuGet** **>** **Gerenciar pacotes NuGet para solução...**. 

3. Clique em **Procurar**, habilite a caixa de seleção **incluir pré-lançamento** e digite **Microsoft.Azure.Management.Dns** na caixa de pesquisa.

4. Selecione o pacote e clique em **instalar** para adicioná-lo ao seu projeto do Visual Studio.
 
5. Repita o processo acima para instalar também os seguintes pacotes: **Microsoft.Rest.ClientRuntime.Azure.Authentication** e **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Adicionar declarações de namespace

Adicionar as seguintes declarações de namespace

    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.Dns;
    using Microsoft.Azure.Management.Dns.Models;

## <a name="initialize-the-dns-management-client"></a>Inicializar o cliente de gerenciamento de DNS

A *DnsManagementClient* contém os métodos e propriedades necessárias para o gerenciamento de conjuntos de registros e zonas DNS.  O código a seguir conecta à conta principal do serviço e cria um objeto DnsManagementClient.

    // Build the service credentials and DNS management client
    var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
    var dnsClient = new DnsManagementClient(serviceCreds);
    dnsClient.SubscriptionId = subscriptionId;

## <a name="create-or-update-a-dns-zone"></a>Criar ou atualizar uma zona DNS

Para criar uma zona DNS, primeiro um objeto de "Zona" é criado para conter os parâmetros de zona DNS. Como zonas DNS não estão vinculadas a uma região específica, o local é definido como 'global'. Neste exemplo, um [Gerente de recursos do Azure 'tag'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) também é adicionado à zona.

Para realmente criar ou atualizar a zona no Azure DNS, o objeto de zona contendo os parâmetros de zona é passado para o método *DnsManagementClient.Zones.CreateOrUpdateAsyc* .

>[AZURE.NOTE] DnsManagementClient dá suporte a três modos de operação: síncrono ('CreateOrUpdate'), assíncrona ('CreateOrUpdateAsync'), ou assíncrono com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Você pode escolher qualquer um destes modos, dependendo das suas necessidades de aplicativo.

DNS Azure dá suporte à concorrência otimista, chamada [Etags](dns-getstarted-create-dnszone.md). Neste exemplo, especificando "*" para o 'If-nenhum-correspondência' Cabeçalho informa DNS do Azure para criar uma zona DNS, se ainda não existir.  A chamada falhará se uma zona com o nome fornecido já existe no grupo determinado recurso.

    // Create zone parameters
    var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"
    
    // Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
    dnsZoneParams.Tags = new Dictionary<string, string>();
    dnsZoneParams.Tags.Add("dept", "finance");
    
    // Create the actual zone.
    // Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
    // Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    // Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");

## <a name="create-dns-record-sets-and-records"></a>Criar registros e conjuntos de registros de DNS

Registros DNS são gerenciados como um conjunto de registros. Um conjunto de registros é um conjunto de registros com o mesmo nome e tipo de registro dentro de uma zona.  O nome do conjunto de registros é relativo o nome de zona, não o nome totalmente qualificado do DNS.

Para criar ou atualizar um registro definido, um "RecordSet" parâmetros objeto é criado e passado para *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Como com zonas DNS, existem três modos de operação: síncrono ('CreateOrUpdate'), assíncrona ('CreateOrUpdateAsync'), ou assíncrono com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Como com zonas DNS, operações em conjuntos de registros incluem suporte para concorrência otimista.  Neste exemplo, desde que 'If-correspondência' nem 'If-nenhum-correspondência' forem especificado, o conjunto de registros sempre é criado.  Esta chamada substitui qualquer registro existente definida com o mesmo nome e tipo de registro desta zona de DNS.

    // Create record set parameters
    var recordSetParams = new RecordSet();
    recordSetParams.TTL = 3600;

    // Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
    recordSetParams.ARecords = new List<ARecord>();
    recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

    // Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
    recordSetParams.Metadata = new Dictionary<string, string>();
    recordSetParams.Metadata.Add("user", "Mary");

    // Create the actual record set in Azure DNS
    // Note: no ETAG checks specified, will overwrite existing record set if one exists
    var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);

## <a name="get-zones-and-record-sets"></a>Obter zonas e conjuntos de registros

Os métodos *DnsManagementClient.Zones.Get* e *DnsManagementClient.RecordSets.Get* recuperem zonas individuais e conjuntos de registros, respectivamente. Conjuntos de registros são identificados por seu tipo, nome e o grupo de recursos e de zona existirem no. Zonas são identificadas por seus nomes e o grupo de recursos que eles existem no.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
    
## <a name="update-an-existing-record-set"></a>Atualizar um conjunto de registro existente

Para atualizar um conjunto de registros de DNS existente, primeiro recuperar o conjunto de registros e atualizar o conteúdo de conjunto de registros, em seguida, envie a alteração.  Neste exemplo, podemos especificar a 'Etag' do conjunto de registro recuperado no parâmetro 'If-correspondência'. A chamada falhará se uma operação de simultânea modificou o conjunto enquanto isso de registros.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

    // Add a new record to the local object.  Note that records in a record set must be unique/distinct
    recordSet.ARecords.Add(new ARecord("5.6.7.8"));

    // Update the record set in Azure DNS
    // Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
    recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);

## <a name="list-zones-and-record-sets"></a>Zonas de lista e conjuntos de registros

Zonas de lista, use os métodos *DnsManagementClient.Zones.List...* , que oferece suporte a listagem em todas as regiões em um grupo de determinado recurso ou em todas as regiões em uma assinatura do Azure determinada (entre grupos de recursos.) Para listar os conjuntos de registros, use métodos de *DnsManagementClient.RecordSets.List...* , que lista todos os conjuntos de registros em uma determinada zona ou apenas os conjuntos de registro de um tipo específico de suporte.

Observação Quando listando zonas e conjuntos de registro que resulta podem ser paginados.  O exemplo a seguir mostra como percorrer as páginas de resultados. (Um tamanho de página artificial pequeno de '2' é usado para forçar paginação; na prática esse parâmetro deve ser omitido e o tamanho de página padrão usado.)

    // Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
    // In practice, to improve performance you would use a large page size or just use the system default
    int recordSets = 0;
    var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
    recordSets += page.Count();

    while (page.NextPageLink != null)
    {
        page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
        recordSets += page.Count();
    }

## <a name="next-steps"></a>Próximas etapas

Baixe o [projeto de amostra do Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), que inclui mais exemplos de como usar o SDK do .NET de DNS do Azure, incluindo exemplos para outros tipos de registro de DNS.
