<properties
    pageTitle="Monitorar programaticamente trabalhos em análise de fluxo | Microsoft Azure"
    description="Saiba como monitorar programaticamente trabalhos de análise de fluxo criados via APIs REST, SDK Azure ou Powershell."
    keywords=".NET monitore e monitore de trabalho, monitoramento de aplicativo"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Criar um monitor de trabalho de análise de fluxo programaticamente
 Este artigo demonstra como habilitar o monitoramento para um trabalho de análise de fluxo. Trabalhos criados via APIs REST, SDK Azure ou Powershell fazer a análise de fluxo não tiver monitoramento ativado por padrão.  Você pode habilitar manualmente isso no Portal do Azure navegando até a página de Monitor do trabalho e clicando no botão Habilitar ou você pode automatizar esse processo seguindo as etapas neste artigo. Os dados de monitoramento aparecerá na guia "Monitor" no Portal do Azure para o trabalho de análise de fluxo.

![monitor de trabalho guia trabalhos](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

- O Visual Studio 2012 ou 2013.
- Baixe e instale o [SDK do Azure .NET](https://azure.microsoft.com/downloads/).
- Um trabalho de análise de fluxo existente que precisa de monitoramento ativado.

## <a name="setup-a-project"></a>Configurar um projeto

1.  Crie um aplicativo de console do Visual Studio c# .net.
2.  No Console do Gerenciador de pacote, execute os seguintes comandos para instalar os pacotes do NuGet. O primeiro é o SDK do Azure fluxo Analytics gerenciamento .NET. O segundo é o SDK do Azure Monitor que será usado para habilitar o monitoramento. O último é o cliente do Active Directory do Azure que será usado para autenticação.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.  Adicione a seguinte seção appSettings para o arquivo App.

    ```
    <appSettings>
        <!--CSM Prod related values-->
        <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
        <add key="JobName" value="YOUR JOB NAME" />
        <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
        <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
        <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
        <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
        <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
    ```
Substituir valores para *SubscriptionId* e *ActiveDirectoryTenantId* com sua assinatura do Azure e IDs de locatários. Você pode obter esses valores, execute o seguinte cmdlet do PowerShell:

    ```
    Get-AzureAccount
    ```
4.  Adicione o seguinte usando instruções para o arquivo de origem (Program.cs) do projeto.

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.  Adicione um método de auxiliar de autenticação.

        public static string GetAuthorizationHeader()
            {
                AuthenticationResult result = null;
                var thread = new Thread(() =>
                {
                    try
                    {
                        var context = new AuthenticationContext(
                            ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                            ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                        result = context.AcquireToken(
                            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                            clientId: ConfigurationManager.AppSettings["AsaClientId"],
                            redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                            promptBehavior: PromptBehavior.Always);
                    }
                    catch (Exception threadEx)
                    {
                        Console.WriteLine(threadEx.Message);
                    }
                });

                thread.SetApartmentState(ApartmentState.STA);
                thread.Name = "AcquireTokenThread";
                thread.Start();
                thread.Join();

                if (result != null)
                {
                    return result.AccessToken;
                }

                throw new InvalidOperationException("Failed to acquire token");
        }

## <a name="create-management-clients"></a>Criar clientes de gerenciamento
O código a seguir irá configurar as variáveis necessárias e clientes de gerenciamento.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Habilitar o monitoramento para um trabalho de análise de fluxo existente

O código a seguir permitirá monitoramento para um trabalho de análise de fluxo **existente** . A primeira parte do código executa uma solicitação GET contra o serviço de análise de fluxo para recuperar informações sobre o trabalho de análise de fluxo específico. Ele usa a propriedade de "Id" (recuperada da solicitação GET) como um parâmetro para o método de colocar na segunda metade do código que envia um colocar solicitação para o serviço de ideias para habilitar o monitoramento para o trabalho de análise de fluxo.

> [AZURE.WARNING]
> Se você tiver ativado anteriormente monitoramento para um trabalho de análise de fluxo diferente, por meio do Portal do Azure ou programaticamente via o abaixo de código, **recomenda-se que você forneça o mesmo nome de conta de armazenamento que você fez quando você habilitou anteriormente monitoramento.**
>
> A conta de armazenamento está vinculada a região que você criou o trabalho de análise de fluxo em, não especificamente para o trabalho propriamente dito.
>
> Todas as análises de fluxo de trabalho (e todos os outros recursos Azure) nessa mesma região compartilham esta conta de armazenamento para armazenar dados de monitoramento. Se você fornecer uma conta de armazenamento diferente, ele pode causar efeitos indesejados do lado para o monitoramento de seus outros trabalhos de análise de fluxo e/ou outros recursos Azure.
>
> O nome da conta de armazenamento usado para substituir ```“<YOUR STORAGE ACCOUNT NAME>”``` abaixo deve ser uma conta de armazenamento que está na mesma assinatura conforme a análise de fluxo de trabalho que você está ativando o monitoramento para.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Obter suporte
Para obter assistência, tente nosso [Fórum de análise de fluxo de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
