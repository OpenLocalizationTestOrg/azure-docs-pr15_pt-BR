<properties 
    pageTitle="Introdução ao API REST do Azure pesquisa gerenciamento | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
    description="Administrar sua nuvem hospedado usando uma API REST de gerenciamento de serviço de pesquisa de Azure" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="get-started-with-azure-search-management-rest-api"></a>Introdução ao Azure API de REST de gerenciamento de pesquisa
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

A API de gerenciamento do Azure REST de pesquisa é uma programação para executar tarefas administrativas no portal. Operações de gerenciamento de serviço incluem criando ou excluindo o serviço, o serviço de dimensionamento e gerenciamento de chaves. Este tutorial vem com um aplicativo de cliente de exemplo que demonstra a API de gerenciamento do serviço. Ele também inclui etapas de configuração necessárias para executar o exemplo no seu ambiente de desenvolvimento local.

Para concluir este tutorial, você precisará:

- O Visual Studio 2012 ou 2013
- o download de aplicativo do cliente de exemplo

Durante a concluir o tutorial, dois serviços serão configurados: pesquisa do Azure e Azure Active Directory (AD). Além disso, você irá criar um aplicativo do AD que estabelece confiança entre o seu aplicativo cliente e o ponto de extremidade do Gerenciador de recurso no Azure.

Você precisará de uma conta do Azure para concluir este tutorial.


##<a name="download-the-sample-application"></a>Baixe o aplicativo de amostra

Este tutorial baseia-se em um aplicativo de console do Windows escrito em c#, que você pode editar e executar no Visual Studio 2012 ou 2013

Você pode encontrar o aplicativo cliente no Github em [Demonstração de API de gerenciamento do Azure pesquisa .NET](https://github.com/Azure-Samples/search-dotnet-management-api/).


##<a name="configure-the-application"></a>Configurar o aplicativo

Antes de executar o aplicativo de exemplo, você deve habilitar a autenticação para que solicitações enviadas do aplicativo cliente para o ponto de extremidade do Gerenciador de recursos podem ser aceitos. O requisito de autenticação origina-se com o [Gerenciador de recursos do Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx), que é a base para todas as operações relacionadas ao portal solicitada por meio de uma API, inclusive aqueles relacionados a gerenciamento de serviço de pesquisa. A API de gerenciamento de serviço de pesquisa do Azure é simplesmente uma extensão do Gerenciador de recursos do Azure e, portanto, herda suas dependências.  

Gerenciador de recursos de Azure requer o serviço do Active Directory do Azure como seu provedor de identidade. 

Para obter um token de acesso que permitirá solicitações alcançar o Gerenciador de recursos, o aplicativo cliente inclui um segmento de código que chama o Active Directory. O segmento de código, além das etapas necessárias para usar o segmento de código, foram emprestadas deste artigo: [solicitações de autenticação Gerenciador de recursos do Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Você pode siga as instruções no link acima ou use as etapas neste documento, se você preferir percorrer o tutorial passo a passo.

Nesta seção, você irá executar as seguintes tarefas:

1. Criar um serviço de AD
1. Criar um aplicativo do AD
1. Configurar o aplicativo de AD registrando detalhes sobre o aplicativo do cliente de exemplo que você baixou
1. Carregar o aplicativo do cliente de exemplo com valores que ele usará para obter autorização para que suas solicitações

> [AZURE.NOTE] Esses links fornecem o plano de fundo sobre como usar o Active Directory do Azure para autenticar solicitações de cliente para o Gerenciador de recursos: [Gerenciador de recursos do Azure](http://msdn.microsoft.com/library/azure/dn790568.aspx), [solicitações de autenticação Gerenciador de recursos do Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx)e [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

###<a name="create-an-active-directory-service"></a>Criar um serviço Active Directory

1. Entre [Portal do Azure](https://manage.windowsazure.com).

2. Role para baixo no painel de navegação à esquerda e clique em **Active Directory**.

4. Clique em **novo** para abrir **Os serviços de aplicativo** | **Do Active Directory**. Nesta etapa, você está criando um novo serviço do Active Directory. Esse serviço hospedará o aplicativo do AD que você vai definir algumas etapas a partir de agora. Criando um novo serviço ajuda a isolar o tutorial de outros aplicativos que você já possa estar hospedando no Azure.

5. Clique em **diretório** | **Criar personalizada**.

6. Insira um nome de serviço, domínio e localização geográfica. O domínio deve ser exclusivo. Clique na marca de seleção para criar o serviço.

     ![][5]

###<a name="create-a-new-ad-application-for-this-service"></a>Criar um novo aplicativo do AD para este serviço

1. Selecione o serviço do Active Directory "Tutorialde pesquisa" que você acabou de criar.

2. No menu superior, clique em **aplicativos**. 
 
3. Clique em **Adicionar um aplicativo**. Um aplicativo do AD armazena informações sobre os aplicativos cliente que usarão-lo como um provedor de identidade.  
 
4. Escolha **Adicionar um aplicativo minha organização está desenvolvendo**. Essa opção fornece configurações de registro para aplicativos que não são publicadas na Galeria de aplicativo. Como o aplicativo cliente não faz parte da Galeria de aplicativo, isso é a escolha certa para este tutorial.

     ![][6]
 
5. Insira um nome, como "Gerenciador de pesquisa do Azure".

6. Escolha o **aplicativo cliente nativo** para tipo de aplicativo. Isso está correto para o aplicativo de amostra; ele é um aplicativo de cliente (console) do Windows, não um aplicativo web.

     ![][7]
 
7. No URI redirecionar, insira "http://localhost/Azure-Search-Manager-App". Isso um URI ao qual Azure Active Directory redirecionará o agente de usuário em resposta a uma solicitação de autorização OAuth 2.0. O valor não precisa ser um ponto de extremidade físico, mas deve ser um URI válido. 

    Para os fins deste tutorial, o valor pode ser qualquer coisa, mas tudo o que você insira torna-se uma entrada necessária para a conexão administrativa no aplicativo de amostra. 
 
7. Clique na marca de seleção para criar o aplicativo do Active Directory. Você deve ver "Azure-pesquisa-Manager-App" no painel de navegação à esquerda.

###<a name="configure-the-ad-application"></a>Configurar o aplicativo do AD
 
9. Clique no aplicativo do AD, "Azure-pesquisa-Manager-App", que você acabou de criar. Você deve aparecer no painel de navegação à esquerda.

10. No menu superior, clique em **Configurar** .
 
11. Role para baixo até as permissões e selecione **API de gerenciamento do Azure**. Nesta etapa, você especificar a API (nesse caso, a API do Gerenciador de recursos do Azure) que o aplicativo cliente precisa acessar, juntamente com o nível de acesso que ele precisa.

12. Em permissões de delegados, clique em menu suspenso lista e selecione **gerenciamento de serviços do Access Azure (visualização**).
 
     ![][8]
 
13. Salve as alterações. 

Mantenha aberta a página de configuração do aplicativo. Na próxima etapa, você copiará valores desta página e inseri-las no aplicativo de amostra.

###<a name="load-the-sample-application-program-with-registration-and-subscription-values"></a>Carregar o programa do aplicativo de exemplo com valores de registro e assinatura

Nesta seção, você vai editar a solução no Visual Studio, substituindo valores válidos obtidos a partir do portal.
Os valores que você adicionará aparecem na parte superior da Program.cs:

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Se você ainda não tiver [baixado o aplicativo de exemplo do Github](https://github.com/Azure-Samples/search-dotnet-management-api/), você precisará dele para esta etapa.

1. Abra o **ManagementAPI.sln** no Visual Studio.

2. Abra Program.cs.

3. Fornecer `ClientId`. Do anúncio página de configuração do aplicativo deixado aberta da etapa anterior, copie a ID do cliente da página de configuração de aplicativo de AD no portal e colá-lo em Program.cs.

4. Fornecer `RedirectUrl`. Copie redirecionar URI da mesma página portal e colá-lo em Program.cs.

    ![][9]

5. Fornecer`TenantID.` 
    - Volte para o Active Directory | Tutorialde pesquisa (serviço). 
    - Clique em **aplicativos** da barra de ferramentas superior. 
    - Clique em **Pontos de extremidade do modo de exibição** na parte inferior da página. 
    - Copie o ponto de extremidade do OAUTH 2.0 autorização na parte inferior da lista. 
    - Cole o ponto de extremidade TenantID, filtrando o valor de todos os parâmetros URI exceto a ID do locatário.

    Determinado "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0", excluir tudo, exceto "55e324c7-1656-4afe-8dc3-43efcd4ffa50".

    ![][10]

6. Fornecer `SubscriptionID`.
    - Vá para a página de portal principal.
    - Clique em **configurações** na parte inferior do painel de navegação à esquerda.
    - Na guia assinaturas, copie a ID da assinatura e colá-lo em Program.cs.

7. Salvar e, em seguida, crie a solução.


##<a name="explore-the-application"></a>Explore o aplicativo

Adicione um ponto de interrupção na primeira chamada método para que você pode percorrer o programa. Pressione **F5** para executar o aplicativo e, em seguida, pressione **F11** para percorrer o código.

O aplicativo de exemplo cria um serviço gratuito de pesquisa do Azure para uma assinatura existente do Azure. Se já existe um serviço gratuito para sua assinatura, o aplicativo de exemplo falhará. Apenas um serviço gratuito da pesquisa por assinatura é permitido.

1. Abra Program.cs do Solution Explorer e vá para a função principal (string [] nulo). 
 
3. Observe que **ExecuteArmRequest** é usado para executar solicitações contra o ponto de extremidade do Gerenciador de recursos do Azure, `https://management.azure.com/subscriptions` para um determinado `subscriptionID`. Este método é usado em todo o programa para executar operações usando a API do Gerenciador de recursos do Azure ou API de gerenciamento de pesquisa.

3. Solicitações ao Gerenciador de recursos do Azure devem ser autenticadas e autorizadas. Isso é feito usando o método **GetAuthorizationHeader** , chamado pelo método **ExecuteArmRequest** , emprestado de [solicitações de autenticação Gerenciador de recursos do Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx). Observe que **GetAuthorizationHeader** chama `https://management.core.windows.net` para obter um token de acesso.

4. Você será solicitado a entrar com um nome de usuário e senha que é válida para sua assinatura.

5. Em seguida, um novo serviço de pesquisa do Azure está registrado com o provedor do Gerenciador de recursos do Azure. Novamente, esse é o método de **ExecuteArmRequest** , usado neste momento para criar o serviço de pesquisa no Azure para sua assinatura via `providers/Microsoft.Search/register`. 

6. O resto do programa usa a [API REST do Azure pesquisa gerenciamento](http://msdn.microsoft.com/library/dn832684.aspx). Observe que o `api-version` para essa API é diferente da versão de api do Gerenciador de recursos do Azure. Por exemplo, `/listAdminKeys?api-version=2014-07-31-Preview` mostra o `api-version` da API de restante de gerenciamento de pesquisa do Azure.

    A próxima série de operações recupera a definição do serviço que você acabou de criar, as teclas de api de administração, gera novamente recupera chaves, altera a réplica e partição e finalmente exclui o serviço.

    Ao alterar a contagem de réplica ou partição de serviço, esperamos que esta ação falhará se você estiver usando a edição livre. Somente a standard edition pode fazer uso de partições adicionais e réplicas.

    Excluir o serviço é a última operação.

##<a name="next-steps"></a>Próximas etapas

Após ter concluído este tutorial, você talvez queira saber mais sobre o gerenciamento de serviço ou a autenticação com o serviço do Active Directory:

- Saiba mais sobre a integração de um aplicativo cliente com o Active Directory. Consulte a [integração de aplicativos no Active Directory do Azure](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Saiba mais sobre outras operações de gerenciamento de serviço no Azure. Consulte [gerenciar seus serviços](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 
