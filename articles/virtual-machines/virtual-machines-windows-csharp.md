<properties
    pageTitle="Implantar recursos Azure usando c# | Microsoft Azure"
    description="Saiba como usar c# e Gerenciador de recursos do Azure para criar recursos do Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="deploy-azure-resources-using-c"></a>Implantar recursos Azure usando C# 

Este artigo mostra como criar recursos Azure usando c#.

Você precisa primeiro para certificar-se de que você terminar estas tarefas:

- Instale o [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Verificar a instalação do [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obter um [token de autenticação](../resource-group-authenticate-service-principal.md)

Ele leva cerca de 30 minutos para siga estas etapas.

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>Etapa 1: Criar um projeto do Visual Studio e instale as bibliotecas

Pacotes do NuGet são a maneira mais fácil de instalar as bibliotecas que você precisa concluir este tutorial. Para acessar as bibliotecas que você precisa no Visual Studio, siga estas etapas:

1. Clique em **arquivo** > **nova** > **projeto**.

2. Em **modelos** > **Visual c#**, selecione **Aplicativo de Console**, insira o nome e local do projeto e clique em **Okey**.

3. Clique com botão direito no nome do projeto no Solution Explorer e clique em **Gerenciar pacotes do NuGet**.

4. Tipo *Do Active Directory* na caixa de pesquisa, clique em **instalar** para o pacote de biblioteca de autenticação do Active Directory e siga as instruções para instalar o pacote.

5. Na parte superior da página, selecione **Incluir pré-lançamento**. Digite *Microsoft.Azure.Management.Compute* na caixa de pesquisa, clique em **instalar** para as bibliotecas de .NET calcular e siga as instruções para instalar o pacote.

6. Digite *Microsoft.Azure.Management.Network* na caixa de pesquisa, clique em **instalar** para as bibliotecas do .NET de rede e siga as instruções para instalar o pacote.

7. Digite *Microsoft.Azure.Management.Storage* na caixa de pesquisa, clique em **instalar** para as bibliotecas do .NET de armazenamento e siga as instruções para instalar o pacote.

8. Digite *Microsoft.Azure.Management.ResourceManager* na caixa de pesquisa, clique em **instalar** para as bibliotecas de gerenciamento de recursos.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>Etapa 2: Criar as credenciais que são usadas para autenticar solicitações

Agora você pode formatar as informações do aplicativo que você criou anteriormente em credenciais que são usadas para autenticar solicitações ao Gerenciador de recursos do Azure.

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicioná-los usando instruções para a parte superior do arquivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Para criar o token que é necessária, adicione esse método para a classe de programa:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

    Substitua {id do cliente} o identificador do Azure Active Directory do aplicativo, {segredo de cliente} com a chave de acesso do aplicativo do AD e {locatário-id} com o identificador de locatário para sua assinatura. Você pode encontrar a identificação de locatário executando Get-AzureRmSubscription. Você pode encontrar a chave de acesso, usando o portal do Azure.

3. Para chamar o método que você adicionou anteriormente, adicione este código para o método principal no arquivo Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Salve o arquivo Program.cs.

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>Etapa 3: Registrar os provedores de recurso e criar os recursos

### <a name="register-the-providers-and-create-a-resource-group"></a>Registrar os provedores e criar um grupo de recursos

Todos os recursos devem estar contidos em um grupo de recursos. Antes de adicionar recursos a um grupo, sua assinatura deve ser registrada com os provedores de recursos.

1. Adicione variáveis para o método principal da classe programa para especificar os nomes que você deseja usar para os recursos:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Substitua todos os valores de variáveis com os nomes e o identificador que você deseja usar. Você pode encontrar o identificador de assinatura executando Get-AzureRmSubscription.

2. Para criar o grupo de recursos e registrar os provedores, adicione esse método para a classe de programa:

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
            
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Para chamar o método que você adicionou anteriormente, adicione este código para o método principal:

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma [conta de armazenamento](../storage/storage-create-storage-account.md) é necessário para armazenar o arquivo de disco rígido virtual é criado para a máquina virtual.

1. Para criar a conta de armazenamento, adicione esse método para a classe de programa:

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. Para chamar o método que você adicionou anteriormente, adicione este código para o método principal da classe de programa:

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

Um endereço IP público é necessária para se comunicar com a máquina virtual.

1. Para criar o endereço IP público da máquina virtual, adicione esse método para a classe de programa:

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. Para chamar o método que você adicionou anteriormente, adicione este código para o método principal da classe de programa:

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>Criar rede virtual

Uma máquina virtual que é criada com o modelo de implantação do Gerenciador de recursos deve estar em uma rede virtual.

1. Para criar uma sub-rede e uma rede virtual, adicione esse método para a classe de programa:

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. Para chamar o método que você adicionou anteriormente, adicione este código para o método principal da classe de programa:

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### <a name="create-the-network-interface"></a>Criar a interface de rede

Uma máquina virtual precisa de uma interface de rede para se comunicar em uma rede virtual.

1. Para criar uma interface de rede, adicione esse método para a classe de programa:

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. Para chamar o método que você adicionou anteriormente, adicione este código para o método principal da classe de programa:

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Conjuntos de disponibilidade facilitam para você gerenciar a manutenção das máquinas virtuais usados por seu aplicativo.

1. Para criar o conjunto de disponibilidade, adicione esse método para a classe de programa:

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Para chamar o método que você adicionou anteriormente, adicione este código para o método principal da classe de programa:

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

1. Para criar a máquina virtual, adicione esse método para a classe de programa:

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }

    >[AZURE.NOTE] Este tutorial cria uma máquina virtual executando uma versão do sistema operacional Windows Server. Para saber mais sobre como selecionar outras imagens, consulte [navegar e selecionar máquina virtual Azure imagens com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Para chamar o método que você adicionou anteriormente, adicione este código para o método principal:

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##<a name="step-4-delete-the-resources"></a>Etapa 4: Excluir os recursos

Porque cobrado para recursos usados no Azure, sempre é uma boa prática excluir recursos que não são mais necessárias. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que você precisa fazer é excluir o grupo de recursos.

1.  Para excluir o grupo de recursos, adicione esse método para a classe de programa:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Para chamar o método que você adicionou anteriormente, adicione este código para o método principal:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## <a name="step-5-run-the-console-application"></a>Etapa 5: Executar o aplicativo de console

1. Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entrar no Azure AD usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2. Pressione **Enter** após cada código de status é retornado para criar cada recurso. Depois que a máquina virtual for criada, faça a próxima etapa antes de pressionar Enter para excluir todos os recursos.

    Demorará cerca de cinco minutos para este aplicativo de console seja executado completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação dos recursos no portal do Azure antes de excluí-los.

3. Para ver o status dos recursos, navegue até os Logs de auditoria no portal do Azure:

    ![Procurar logs de auditoria no portal do Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## <a name="next-steps"></a>Próximas etapas

- Aproveite usando um modelo para criar uma máquina virtual usando as informações em [implantar uma máquina Virtual de Azure usando c# e um modelo do Gerenciador de recursos](virtual-machines-windows-csharp-template.md).
- Saiba como gerenciar a máquina virtual que você criou revisando [máquinas virtuais de gerenciar usando o Gerenciador de recursos do Azure e PowerShell](virtual-machines-windows-csharp-manage.md).
