<properties
    pageTitle="Metadados de aplicativos de API do serviço de aplicativo para geração de descoberta e o código de API | Microsoft Azure"
    description="Saiba como aplicativos de API do serviço de aplicativo do Azure usar Swagger metadados para facilitar a geração de descoberta e o código de API."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>Metadados de aplicativos de API do serviço de aplicativo para geração de descoberta e o código de API 

Suporte para [Swagger 2.0](http://swagger.io/) API metadados é incorporado em aplicativos de API do serviço de aplicativo. Você não precisa usar Swagger, mas se você usá-lo, você pode aproveitar os recursos de aplicativos de API que facilitam a descoberta e consumo.   

## <a name="swagger-endpoint"></a>Ponto de extremidade de swagger

Você pode especificar um ponto de extremidade que fornece Swagger 2.0 JSON metadados para um aplicativo de API em uma propriedade do aplicativo API. O ponto de extremidade pode ser relativo a URL base do aplicativo API ou uma URL absoluta. URLs absolutas podem apontar fora o aplicativo de API. 

Muitos clientes downstream (por exemplo, geração de código do Visual Studio e PowerApps "Adicionar API" fluxo), a URL deve ser acessível publicamente (não protegido por usuário ou autenticação de serviço). Isso significa que se você estiver usando a autenticação do serviço de aplicativo e deseja expor a definição de API de dentro de seu próprio aplicativo, você precisa usar a opção de autenticação que permite o tráfego anônimo alcançar sua API. Para obter mais informações, consulte [autenticação e a autorização para os aplicativos de API do serviço de aplicativo](app-service-api-authentication.md).

### <a name="portal-blade"></a>Blade Portal

No [portal do Azure](https://portal.azure.com/) a URL do ponto de extremidade pode ser vista e alterada no blade **Definição de API** .

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Propriedade Azure do Gerenciador de recursos

Você também pode configurar a URL de definição de API para um aplicativo de API usando [Resource Explorer](https://resources.azure.com/) ou [modelos do Gerenciador de recursos do Azure](../resource-group-authoring-templates.md) em ferramentas de linha de comando como [PowerShell do Azure](../powershell-install-configure.md) e o [Azure CLI](../xplat-cli-install.md). 

No **Resource Explorer**, vá para **assinaturas > {sua assinatura} > resourceGroups > {seu grupo de recursos} > provedores > Microsoft.Web > sites > {seu site} > Configuração > web**, e você verá o `apiDefinition` propriedade:

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

Para obter um exemplo de um modelo do Gerenciador de recursos do Azure que define o `apiDefinition` propriedade, abra o [arquivo de azuredeploy.json no aplicativo de exemplo de lista de tarefas pendentes](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Localize a seção do modelo que se parece com a amostra JSON mostrada acima.

### <a name="default-value"></a>Valor padrão

Quando você usa o Visual Studio para criar um aplicativo de API, o ponto de extremidade de definição de API é definido automaticamente para a URL base do aplicativo API mais `/swagger/docs/v1`. Esta é a URL padrão que o pacote [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet usa para servir gerado dinamicamente metadados de Swagger para um projeto de API Web do ASP.NET. 

## <a name="code-generation"></a>Geração de código

Um dos benefícios de integrar Swagger aplicativos de API do Azure é geração automática de código. Cliente gerado classes facilitam escrever código que chama um aplicativo de API.

Você pode gerar o código do cliente para um aplicativo de API usando Visual Studio ou da linha de comando. Para obter informações sobre como gerar classes cliente no Visual Studio para um projeto de API Web do ASP.NET, consulte [Introdução aos aplicativos de API e ASP.NET](app-service-api-dotnet-get-started.md#codegen). Para obter informações sobre como fazer isso na linha de comando para todos os idiomas com suporte, consulte o arquivo Leiame do repositório do [Azure/autorest](https://github.com/azure/autorest) em GitHub.com.
 
## <a name="next-steps"></a>Próximas etapas

Para um tutorial passo a passo que orienta você durante a criação, implantação e consumindo um aplicativo de API, consulte [Introdução aos aplicativos de API do serviço de aplicativo do Azure](app-service-api-dotnet-get-started.md).

Se você usa o gerenciamento de API do Azure com os aplicativos de API, você pode usar Swagger metadados para importar sua API para gerenciamento de API. Para obter mais informações, consulte [como importar a definição de uma API com operações de gerenciamento de API do Azure](../api-management/api-management-howto-import-api.md). 
