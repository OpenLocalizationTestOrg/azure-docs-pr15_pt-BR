## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Preparar para autenticar solicitações do Gerenciador de recursos do Windows Azure

Todas as operações que podem ser executadas nos recursos usando o [Gerenciador de recursos do Windows Azure] devem ser autenticados[ lnk-authenticate-arm] com o Windows Azure AD (Active Directory). A maneira mais fácil, essa configuração é usar o PowerShell ou CLI do Azure.

Você deve instalar o [Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior para continuar.

As etapas a seguir mostram como configurar a autenticação de senha para um aplicativo da AD usando o PowerShell. Você pode executar esses comandos em uma sessão do PowerShell padrão.

1. Faça logon em sua assinatura do Windows Azure usando o seguinte comando:

    ```
    Login-AzureRmAccount
    ```

2. Tome nota das **TenantId** e **SubscriptionId**. Você precisará-las mais tarde.

3. Crie um novo aplicativo do Windows Azure Active Directory usando o seguinte comando, substituindo os espaços reservados:

    - **{Nome para exibição}:** um nome de exibição para o seu aplicativo, como **MySampleApp**
    - **{URL da Home page}:** a URL da home page do seu aplicativo, como **http://mysampleapp/home**. Essa URL não precisa apontar para um aplicativo real.
    - **{Identificador de aplicativo}:** Um identificador exclusivo, como **http://mysampleapp**. Essa URL não precisa apontar para um aplicativo real.
    - **{Senha}:** Uma senha que você usará para autenticar com seu aplicativo.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Anote **ApplicationId** do aplicativo que você criou. Você precisará isso mais tarde.

5. Crie um novo serviço principal usando o seguinte comando, substituindo **{MyApplicationId}** por **ApplicationId** da etapa anterior:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Configurar uma atribuição de função usando o seguinte comando, substituindo **{MyApplicationId}** com sua **ApplicationId**.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
Você concluiu agora criando o aplicativo do Azure AD que habilitará a autenticação do seu aplicativo personalizado do c#. Você precisará os seguintes valores posteriormente neste tutorial:

- TenantId
- SubscriptionId
- ApplicationId
- Senha

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../articles/powershell-install-configure.md
