## <a name="obtain-an-azure-resource-manager-token"></a>Obter um token de gerente de recursos do Windows Azure

Azure Active Directory deve autenticar todas as tarefas realizadas com recursos usando o Gerenciador de recursos do Windows Azure. O exemplo mostrado aqui usa a autenticação de senha, para outras abordagens, consulte [solicitações do Gerenciador de recursos do Windows Azure autenticando][lnk-authenticate-arm].

1. Adicione o seguinte código para o método **Main** em Module. vb para recuperar um token do Windows Azure AD usando o id do aplicativo e a senha.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Crie um objeto de **ResourceManagementClient** que usa o token adicionando o código a seguir ao final do método **Main** :

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Criar ou obter uma referência ao, o grupo de recursos que você está usando:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx