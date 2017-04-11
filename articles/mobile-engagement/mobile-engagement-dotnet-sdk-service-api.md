<properties 
    pageTitle="Usando o SDK do .NET para acessar APIs do Azure Mobile contrato de serviço" 
    description="Descreve como usar o SDK do .NET de contrato Mobile acessem APIs do Azure Mobile contrato de serviço"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Usando o SDK do .NET para acessar APIs do Azure Mobile contrato de serviço

Contrato de celular Azure expõe um conjunto de APIs para gerenciar dispositivos, campanhas de alcance/Push etc. Para interagir com essas APIs, podemos também fornecer um [arquivo de Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que você pode usar com ferramentas para gerar SDKs para seu idioma preferencial. É recomendável usar a ferramenta de [AutoRest](https://github.com/Azure/AutoRest) para gerar o SDK do nosso arquivo Swagger. 

Nós criamos uma .NET SDK de maneira semelhante, que permite interagir com essas APIs usando uma embalagem c# e você não precisa fazer a negociação de token de autenticação e atualização por conta própria.  

Este exemplo percorre o conjunto de etapas a seguir para usar o SDK do .NET:

1. Primeiro, você precisa configurar a autenticação para suas APIs usando o Active Directory do Azure como descrito [aqui](mobile-engagement-api-authentication.md#authentication). No final destas etapas, você deve ter um **SubscriptionId**, **TenantId**, **ApplicationId** e **secreta**válida. 

2. Usaremos um aplicativo de Console do Windows simple para demonstrar a trabalhar com o SDK do .NET com o cenário de criação de uma campanha de lançamento. Portanto, abra o Visual Studio e criar um **Aplicativo de Console**.   

3. Em seguida você precisa baixar o SDK do .NET que está disponível como a **Biblioteca de gerenciamento do Microsoft Azure contrato** na Galeria Nuget [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
Se você estiver instalando o Nuget do Visual Studio, você precisa garantir que você tenha marcada a opção de **incluir pré-lançamento** durante a pesquisa para o pacote de seleção:

    ![][1]

4. No `Program.cs` de arquivo, adicione os namespaces a seguir:

        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;

5. Em seguida, você precisa definir as seguintes constantes que serão usadas para autenticação e interagindo com o aplicativo de contrato Mobile no qual você está criando a campanha de lançamento:

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. Definir o `EngagementManagementClient` variável que usaremos para chamar os métodos de SDK do contrato Mobile:

        static EngagementManagementClient engagementClient; 

7. Adicione o seguinte ao seu `Main` método:

        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. Definir o seguinte método que cuida da inicializar o `EngagementManagementClient` primeiro autenticar e associando próprio com o aplicativo de contrato Mobile no qual você planeja criar a campanha de lançamento:

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

    > [AZURE.IMPORTANT] Observe que você precisa usar o **Nome de recurso do aplicativo** , conforme definido no portal de gerenciamento Azure para o parâmetro AppName. 

9. Por fim, definir o método de CreateCampaign que será cuidam de usar o EngagementClient anteriormente inicializado para criar um simples **a qualquer momento** & **somente notificação** campanha com um título e a mensagem: 

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. Executar o aplicativo de console e você verá o seguinte na criação bem-sucedida da campanha:

    **Id de campanha '159' foi criado com êxito e está em estado de 'rascunho'**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
