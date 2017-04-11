<properties
    pageTitle="Criar autenticação não interativo .NET HDInsight applciations | Microsoft Azure"
    description="Aprenda a criar autenticação não interativo .NET HDInsight aplicativos."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Criar aplicativos de HDInsight .NET de autenticação não interativo

Você pode executar seu aplicativo .NET Azurehdinsight sob identidade do aplicativo (não-interativo) ou sob a identidade do usuário conectado-in do aplicativo (interativo). Para uma amostra do aplicativo interativo, consulte [Enviar Sqoop/porco/seção trabalhos usando o SDK do .NET HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Este artigo mostra como criar o aplicativo de .NET não interativo autenticação para se conectar ao Azurehdinsight e enviar um trabalho de seção.

Do seu aplicativo .NET, você precisará:

- sua ID do Azure assinatura locatário
- a ID do Azure Directory aplicativo cliente
- a chave secreta do aplicativo de Directory do Azure.  

O processo principal inclui as seguintes etapas:

2. Crie um aplicativo de Directory do Azure.
2. Atribua funções para o aplicativo do AD.
3. Desenvolva seu aplicativo cliente.


##<a name="prerequisites"></a>Pré-requisitos

- Cluster de HDInsight. Você pode criar uma seguindo as instruções encontradas no [tutorial de Introdução](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## <a name="create-azure-directory-application"></a>Criar aplicativo Directory do Azure 
Quando você cria um aplicativo do Active Directory, ele realmente cria o aplicativo e um serviço principal. Você pode executar o aplicativo sob a identidade do aplicativo.

Atualmente, você deve usar o portal de clássico Azure para criar um novo aplicativo do Active Directory. Essa capacidade será adicionada ao portal do Azure em uma versão posterior. Você também pode executar essas etapas através do PowerShell do Azure ou CLI do Azure. Para obter mais informações sobre como usar o PowerShell ou CLI com o serviço principal, consulte [serviço de autenticar principal com o Gerenciador de recursos do Azure](../resource-group-authenticate-service-principal.md).

**Para criar um aplicativo de Directory do Azure**

1.  Entrar no [portal clássico Azure]( https://manage.windowsazure.com/).
2.  Selecione o **Active Directory** no painel esquerdo.

    ![Clássico portal active directory do Azure](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.  Selecione a pasta que você deseja usar para criar o novo aplicativo. Ela deverá ser existente.
4.  Clique em **aplicativos** da parte superior para listar os aplicativos existentes.
5.  Clique em **Adicionar** da parte inferior para adicionar um novo aplicativo.
6.  Insira o **nome**, selecione o **aplicativo da Web e/ou Web API**e clique em **Avançar**.

    ![novo aplicativo do active directory do azure](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.  Digite o **URL de logon** e **URI de ID do aplicativo**. Para **URL de SIGN-ON**, forneça o URI a um site que descreva o seu aplicativo. A existência do site da web não é validada. Para URI de ID do aplicativo, forneça a URI que identifica seu aplicativo. E, em seguida, clique em **Concluir**.
Demora alguns momentos para criar o aplicativo.  Depois que o aplicativo é criado, o portal mostra a página Glace rápida do novo aplicativo. Não feche o portal. 

    ![novas propriedades de aplicativo do active directory do azure](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Para obter o aplicativo cliente ID e a chave secreta**

1.  Na página do aplicativo AD recém-criado, clique em **Configurar** no menu superior.
2.  Faça uma cópia da **ID do cliente**. Você precisará no seu aplicativo .NET.
3.  Em **chaves**, clique em lista suspensa **Selecione duração** e selecione **ano 1** ou **2 anos**. O valor da chave não será exibido até que você salve a configuração.
4.  Clique em **Salvar** na parte inferior da página. Quando aparece a chave secreta, faça uma cópia da chave. Você precisará no seu aplicativo .NET.

##<a name="assign-ad-application-to-role"></a>Atribuir AD aplicativo à função

Você deve atribuir o aplicativo a uma [função](../active-directory/role-based-access-built-in-roles.md) para conceder permissões para realizar ações. Você pode definir o escopo no nível da assinatura, grupo de recursos ou recurso. As permissões são herdadas de níveis inferiores de escopo (por exemplo, adicionando um aplicativo para a função de leitor para um grupo de recursos significa que ele pode ler o grupo de recursos e os recursos que ele contém). Neste tutorial, você irá definir o escopo no nível de grupo de recursos.  Como o portal de clássico Azure não dá suporte a grupos de recursos, essa parte tem que ser executado a partir do portal do Azure. 

**Para adicionar a função de proprietário para o aplicativo do AD**

1.  Entrar no [portal do Azure](https://portal.azure.com).
2.  No painel esquerdo, clique em **Grupo de recursos** .
3.  Clique no grupo de recursos que contém o cluster HDInsight onde você executarão sua consulta de seção posteriormente neste tutorial. Se houver muitos grupos de recursos, você pode usar o filtro.
4.  Clique em **acesso** da lâmina do cluster.

    ![ícone de nuvem e thunderbolt = início rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.  Clique em **Adicionar** da lâmina **usuários** .
6.  Siga as instruções para adicionar a função de **proprietário** para o aplicativo do AD que você criou no último procedimento. Quando você conclui-la com êxito, você deverá ver o aplicativo listado na lâmina usuários com a função de proprietário.


##<a name="develop-hdinsight-client-application"></a>Desenvolver o aplicativo de cliente HDInsight

Crie um aplicativo de console c# .net seguindo as instruções encontradas em [trabalhos de enviar Hadoop em HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Em seguida, substitua o método de GetTokenCloudCredentials com o seguinte:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Para recuperar a ID de locatário através do PowerShell:

    Get-AzureRmSubscription

Ou, CLI Azure:

    azure account show --json

      
## <a name="see-also"></a>Consulte também

- [Enviar trabalhos de Hadoop em HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Criar o aplicativo do Active Directory e capital de serviço usando o portal](../resource-group-create-service-principal-portal.md)
- [Autenticar principal do serviço com o Gerenciador de recursos do Azure](../resource-group-authenticate-service-principal.md)
- [Controle de acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md)
