<properties
    pageTitle="Gerenciamento de recursos com .NET do lote de gerenciamento de conta | Microsoft Azure"
    description="Criar, excluir e modificar recursos de conta do Azure lote com a biblioteca de lote gerenciamento .NET."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/19/2016"
    ms.author="marsma"/>

# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>Gerenciar contas de lote do Azure e cotas com .NET de gerenciamento de lote

> [AZURE.SELECTOR]
- [Portal do Azure](batch-account-create-portal.md)
- [Gerenciamento de lote .NET](batch-management-dotnet.md)

Você pode diminuir manutenção sobrecarga em seus aplicativos do Azure lote usando o [Lote gerenciamento .NET] [ api_mgmt_net] biblioteca para automatizar a criação de conta do lote, exclusão, gerenciamento de chaves e descoberta de cota.

- **Criar e excluir contas de lote** dentro de qualquer região. Se, por exemplo, como um software independente de distribuidores você fornecer um serviço para seus clientes em que cada um é atribuída uma conta de lote separada para fins de faturamento, você pode adicionar recursos de criação e a exclusão de conta seu portal de clientes.
- **Recuperar e conta gerar chaves** programaticamente para qualquer uma das suas contas do lote. Isso pode ajudá-lo a atender às políticas de segurança que impõem sobreposição periódica ou expiração das chaves de conta. Quando você tiver várias contas de lote em diversas regiões Azure, automação desse processo de sobreposição aumenta a eficiência da sua solução.
- **Verifique as cotas de conta** e fazer as suposições de avaliação e erro fora determinar quais contas de lote têm quais limites. Verificando suas cotas de conta antes de iniciar trabalhos, criando pools ou adicionando nós de computação, você pode ajustar proativamente onde ou quando esses calcular recursos são criados. Você pode determinar quais contas exigem cota aumenta antes de alocação de recursos adicionais as contas.
- **Combinar os recursos de outros serviços do Azure** para uma experiência de gerenciamento completos – usando .NET de gerenciamento de lote, [Active Directory do Azure][aad_about]e o [Gerenciador de recursos do Azure] [ resman_overview] juntos no mesmo aplicativo. Usando esses recursos e suas APIs, você pode fornecer uma experiência de autenticação irrestrito, a capacidade de criar e excluir grupos de recursos e os recursos descritos acima para uma solução de gerenciamento de ponta a ponta.

> [AZURE.NOTE] Embora este artigo aborda o gerenciamento de programação de suas contas lote, chaves e cotas, você pode executar muitas dessas atividades usando o [portal do Azure][azure_portal]. Para obter mais informações, consulte [criar uma conta de lote do Azure usando o portal do Azure](batch-account-create-portal.md) e [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md).

## <a name="create-and-delete-batch-accounts"></a>Criar e excluir contas em lotes

Conforme mencionado, um dos principais recursos da API de gerenciamento do lote é criar e excluir contas de lote em uma região Azure. Para fazer isso, use [BatchManagementClient.Account.CreateAsync] [ net_create] e [DeleteAsync][net_delete], ou seus correspondentes de síncronos.

O trecho de código a seguir cria uma conta, obtém a conta recém-criado do serviço do lote e, em seguida, exclui-lo. Neste trecho de código e os outros neste artigo, `batchManagementClient` é uma instância totalmente inicializada do [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Aplicativos que usam a biblioteca .NET de gerenciamento de lote e sua classe BatchManagementClient exigem acesso de **administrador de serviço** ou **coadministrator** para a assinatura que possui a conta de lote a ser gerenciado. Para obter mais informações, consulte a seção de [Active Directory do Azure](#azure-active-directory) e o [AccountManagement] [ acct_mgmt_sample] código de exemplo.

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperar e gerar chaves de conta

Obtenha chaves primárias e secundárias conta de qualquer conta lote dentro de sua assinatura usando [ListKeysAsync][net_list_keys]. Você pode gerar essas chaves usando [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [AZURE.TIP] Você pode criar um fluxo de trabalho de conexão simplificada para os aplicativos de gerenciamento. Primeiro, obter uma chave de conta para a conta de lote que você deseja gerenciar com [ListKeysAsync][net_list_keys]. Em seguida, use esta tecla ao inicializar da biblioteca .NET lote [BatchSharedKeyCredentials] [ net_sharedkeycred] classe, que é usada ao inicializar [BatchClient][net_batch_client].

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verificar a assinatura do Azure e cotas de conta de lote

Assinaturas do Azure e os serviços do Azure individuais como lote todos têm cotas padrão que limitam o número de determinadas entidades contido nelas. Para as cotas de padrão para assinaturas do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](./../azure-subscription-service-limits.md). Para as cotas de padrão do serviço do lote, consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md). Usando a biblioteca de .NET de gerenciamento de lote, você pode verificar essas cotas em seus aplicativos. Isso permite que você tome decisões de alocação antes de adicionar contas ou calcular recursos como pools e nós de computadores.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificar uma assinatura do Azure para cotas de conta de lote

Antes de criar uma conta de lote em uma região, você pode verificar sua assinatura do Azure para ver se você é capaz de adicionar uma conta na região.

Trecho de código abaixo, primeiro usamos [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] para obter uma coleção de todas as contas de lote que estão dentro de uma assinatura. Depois que obtivemos essa coleção, podemos determinar quantas contas estão na região de destino. Em seguida, podemos usar [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] para obter a cota de conta de lote e determinar quantas contas (se houver) podem ser criadas em região.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

No trecho de acima, `creds` é uma instância de [TokenCloudCredentials][azure_tokencreds]. Para ver um exemplo de criar esse objeto, consulte o [AccountManagement] [ acct_mgmt_sample] código de exemplo no GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verificar uma conta de lote para as cotas de recursos de computação

Antes de aumentar os recursos de computação na sua solução de lote, você pode verificar garantir que os recursos que você deseja alocar não excede as cotas da conta. No trecho de código abaixo, podemos imprimir as informações de cota da conta de lote chamado `mybatchaccount`. Em seu próprio aplicativo, você pode usar essas informações para determinar se a conta pode lidar com os recursos adicionais a ser criado.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Embora existam cotas de padrão para assinaturas do Azure e serviços, muitos desses limites podem ser emitidos pelo emitir uma solicitação no [portal do Azure][azure_portal]. Por exemplo, consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md) para obter instruções sobre aumentando suas cotas de conta do lote.

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Lote gerenciamento .NET, Azure AD e Gerenciador de recursos

Quando você trabalha com a biblioteca .NET de gerenciamento de lote, você normalmente também usar [Azure Active Directory] [ aad_about] (Azure AD) e o [Gerenciador de recursos do Azure][resman_overview]. O projeto de exemplo discutido abaixo usa Azure Active Directory e o Gerenciador de recursos enquanto ele demonstra a API de .NET de gerenciamento de lote.

### <a name="azure-active-directory"></a>Active Directory do Azure

Azure próprio usa Azure AD para a autenticação de seus clientes, os administradores de serviços e usuários organizacionais. No contexto do .NET de gerenciamento de lote, você pode usar Azure AD para autenticar um administrador colegas ou administrador de assinatura. Isso permite que a biblioteca de gerenciamento consultar o serviço de lote e executar as operações descritas neste artigo.

No projeto de exemplo discutidos abaixo, a [Biblioteca de autenticação do Active Directory] do Azure[ aad_adal] (ADAL) é usado para solicitar que o usuário suas credenciais do Microsoft. Quando as credenciais de administrador ou coadministrator de serviço são fornecidas, o aplicativo pode consultar Azure para obter uma lista de assinaturas – e pode criar e excluir grupos de recursos e contas do lote.

### <a name="resource-manager"></a>Gerenciador de recursos

Quando você criar contas de lote com a biblioteca .NET de gerenciamento de lote, você normalmente criarão-las dentro de um [grupo de recursos][resman_overview]. Você pode criar o grupo de recursos por programação usando o [ResourceManagementClient] [ resman_client] classe no [Gerenciador de recursos .NET] [ resman_api] biblioteca. Ou você pode adicionar uma conta a um grupo existente do recurso que você criou anteriormente usando o [portal do Azure][azure_portal].

## <a name="sample-project-on-github"></a>Projeto de amostra no GitHub

Para ver o lote gerenciamento .NET em ação, confira o [AccountManagment] [ acct_mgmt_sample] projeto de amostra no GitHub. Este aplicativo de console mostra a criação e o uso da [BatchManagementClient] [ net_mgmt_client] e [ResourceManagementClient][resman_client]. Ele também demonstra o uso da [Biblioteca de autenticação do Active Directory] do Azure[ aad_adal] (ADAL), que é requerido pelo ambos os clientes.

Para executar o aplicativo de exemplo com êxito, você deve primeiro registrá-lo com o Azure AD usando o portal do Azure. Siga as etapas na seção [Adicionando um aplicativo](../active-directory/active-directory-integrating-applications.md#adding-an-application) [integrando aplicativos com o Active Directory do Azure] [ aad_integrate] para registrar o aplicativo de amostra dentro de sua própria conta do diretório padrão. Certifique-se de selecionar **Aplicativo nativo do cliente** para o tipo de aplicativo e você pode especificar qualquer URI válido (tais como `http://myaccountmanagementsample`) para o **URI redirecionar**– ele não precisa ser um ponto de extremidade real.

Após adicionar seu aplicativo, delegar a permissão de **Gerenciamento de serviços do Access Azure como organização** para o aplicativo de *API de gerenciamento de serviço do Windows Azure* nas configurações do aplicativo no portal:

![Permissões de aplicativo no portal do Azure][2]

> [AZURE.TIP] Se **API de gerenciamento de serviço do Windows Azure** não aparecer em *permissões para outros aplicativos*, clique em **Adicionar aplicativo**, selecione **API de gerenciamento de serviço do Windows Azure**, clique no botão de marca de seleção. Em seguida, delegar permissões conforme especificado acima.

Depois que você adicionou o aplicativo conforme descrito acima, atualizar `Program.cs` na [AccountManagment] [ acct_mgmt_sample] projeto de amostra com seu aplicativo redirecionar URI e ID do cliente. Você pode encontrar esses valores na guia **Configurar** do seu aplicativo:

![Configuração do aplicativo no portal do Azure][3]

O [AccountManagment] [ acct_mgmt_sample] aplicativo de exemplo demonstra as seguintes operações:

1. Adquirir um token de segurança do Azure AD usando [ADAL][aad_adal]. Se o usuário não estiver conectado já, ele será solicitado suas credenciais Azure.
2. Usando o token de segurança obtido do Azure AD, criar uma [SubscriptionClient] [ resman_subclient] a consulta do Azure para obter uma lista de assinaturas associados à conta. Isso permite a seleção de uma assinatura se vários forem encontrados.
3. Crie um objeto de credenciais associado a assinatura selecionada.
4. Criar [ResourceManagementClient] [ resman_client] usando as credenciais.
5. Use [ResourceManagementClient] [ resman_client] para criar um grupo de recursos.
6. Use [BatchManagementClient] [ net_mgmt_client] para executar diversas operações de conta de lote:
  - Crie uma conta de lote no novo grupo de recursos.
  - Obter a conta recém-criado serviço do lote.
  - Imprima as teclas de conta para a nova conta.
  - Gerar uma nova chave primária da conta.
  - Imprima as informações de cota da conta.
  - Imprima as informações de cota para a assinatura.
  - Imprima todas as contas na assinatura.
  - Exclua conta recém-criado.
7. Exclua o grupo de recursos.

Antes de excluir o grupo de recursos e de conta de lote recém-criado, você pode inspecionar ambos no [portal do Azure][azure_portal]:

![Portal Azure exibindo o grupo de recursos e a conta de lote][1]
<br />
*Portal Azure exibindo novo grupo de recursos e conta em lotes*

[aad_about]: ../active-directory/active-directory-whatis.md "O que é Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integração de aplicativos com o Active Directory do Azure"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
