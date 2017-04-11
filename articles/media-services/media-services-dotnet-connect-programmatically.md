<properties 
    pageTitle="Conectar-se à conta de serviços de mídia usando .NET" 
    description="Este tópico demonstra como conectar ao usar os serviços de mídia .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-sdk-for-net"></a>Conectar-se à conta de serviços de mídia usando o SDK de serviços de mídia para .NET

> [AZURE.SELECTOR]
- [RESTANTE](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect-programmatically.md)


Este tópico descreve como obter uma conexão programação aos serviços de mídia do Microsoft Azure quando você estiver programando com o SDK de serviços de mídia para .NET.


## <a name="connecting-to-media-services"></a>Conectar-se aos serviços de mídia

Para se conectar aos serviços de mídia programaticamente, você deve ter anteriormente configurar uma conta do Azure, configurado serviços de mídia nessa conta e configurar um projeto do Visual Studio para o desenvolvimento com o SDK de serviços de mídia para .NET. Para obter mais informações, consulte instalação para desenvolvimento com o SDK de serviços de mídia para .NET.

No final do processo de configuração de conta de serviços de mídia, você obteve os seguintes valores de conexão necessários. Use estes fazer programação conexões com serviços de mídia.

- Nome de sua conta de serviços de mídia.

- Sua chave da conta de serviços de mídia.

Para localizar esses valores, acesse o Portal de gerenciamento do Azure, selecione sua conta de serviço de mídia e clique no ícone "**Gerenciar chaves**" na parte inferior da janela do portal. Clicando no ícone ao lado de cada caixa de texto, o valor é copiado para a área de transferência do sistema.


## <a name="creating-a-cloudmediacontext-instance"></a>Criando uma instância de CloudMediaContext

Para iniciar a programação com os serviços de mídia, você precisa criar uma instância de **CloudMediaContext** que representa o contexto de servidor. O **CloudMediaContext** inclui referências aos conjuntos importantes, incluindo trabalhos, ativos, arquivos, políticas de acesso e locators.

>[AZURE.NOTE] A classe de **CloudMediaContext** não é thread-safe. Você deve criar um novo CloudMediaContext por thread ou por conjunto de operações.


CloudMediaContext tem cinco sobrecargas de construtor. É recomendável usar construtores que utilizam **MediaServicesCredentials** como um parâmetro. Para obter mais informações, consulte os **Reutilizando Tokens de serviço de controle de acesso** que segue. 

O exemplo a seguir usa o construtor de CloudMediaContext(MediaServicesCredentials credentials) público:

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## <a name="reusing-access-control-service-tokens"></a>Reutilizando Tokens de serviço de controle de acesso

Esta seção mostra como reutilizar tokens de serviço de controle de acesso usando construtores de CloudMediaContext que utilizam MediaServicesCredentials como um parâmetro.


[Controle de acesso do Active Directory do Azure](https://msdn.microsoft.com/library/hh147631.aspx) (também conhecido como o serviço de controle de acesso ou ACS) é um serviço baseado em nuvem que oferece uma maneira fácil de autenticar e autorizar usuários acessem seus aplicativos web. Serviços de mídia do Microsoft Azure controla o acesso aos seus serviços apesar protocolo de OAuth que requer um token de ACS. Serviços de mídia recebe os tokens ACS de um servidor de autorização.

Ao desenvolver com o SDK de serviços de mídia, você pode optar por não lida com os tokens porque o SDK do código gerentes-los para você. No entanto, permitindo que o SDK gerenciar totalmente os tokens de ACS leva a solicitações de token desnecessárias. Solicitando tokens leva tempo e consome os recursos de cliente e servidor. Além disso, o servidor ACS acelera as solicitações se a taxa for muito alta. O limite é de 30 solicitações por segundo, consulte [Limitações do serviço ACS](https://msdn.microsoft.com/library/gg185909.aspx) para obter mais detalhes.

Começando com o SDK de serviços de mídia versão 3.0.0.0, você pode reutilizar os tokens de ACS. Construtores de **CloudMediaContext** que têm **MediaServicesCredentials** como um parâmetro habilitar os tokens de ACS entre vários contextos de compartilhamento. A classe MediaServicesCredentials encapsula as credenciais de serviços de mídia. Se um token de ACS está disponível e seu tempo de expiração é conhecido, você pode criar uma nova instância de MediaServicesCredentials com o token e passe para o construtor de CloudMediaContext. Observe que o SDK dos serviços de mídia atualiza automaticamente tokens sempre que ela expirem. Há duas maneiras de reutilizar tokens de ACS, conforme mostrado nos exemplos a seguir.

- Você pode armazenar em cache o objeto **MediaServicesCredentials** na memória (por exemplo, em uma variável de classe estática). Em seguida, passe o objeto em cache para o construtor de CloudMediaContext. O objeto MediaServicesCredentials contém um token de ACS que pode ser reutilizado se ele ainda estiver válido. Se o token não é válido, ele será atualizado pelo SDK de serviços de mídia usando as credenciais fornecidas ao construtor MediaServicesCredentials.

    Observe que o objeto **MediaServicesCredentials** obtém um token válido depois que o RefreshToken é chamado. O **CloudMediaContext** chama o método **RefreshToken** no construtor. Se você estiver planejando salvar os valores de token para um armazenamento externo, verifique se o valor de TokenExpiration é válido antes de salvar os dados de token. Se não for válido, chame RefreshToken antes de armazenamento em cache.

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Você também pode cache a cadeia de caracteres AccessToken e os valores de TokenExpiration. Os valores mais tarde podem ser usados para criar um novo objeto de MediaServicesCredentials com os dados armazenados em cache de token.  Isso é especialmente útil para cenários em que o token pode ser compartilhado com segurança entre vários processos ou computadores.

    Os seguintes trechos de código chamar os métodos SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage e UpdateTokenDataInExternalStorageIfNeeded que não são definidos neste exemplo. Você pode definir esses métodos para armazenar, recuperar e atualizar dados token em um armazenamento externo. 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    Use os valores de token salvos para criar MediaServicesCredentials.


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    Atualize a cópia do token caso o token foi atualizado pelo SDK de serviços de mídia. 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- Se você tiver várias contas de serviços de mídia (por exemplo, para fins ou distribuição geográfica de compartilhamento de carga) você pode armazenar em cache objetos MediaServicesCredentials usando a coleção de System.Collections.Concurrent.ConcurrentDictionary (a coleção de ConcurrentDictionary representa um conjunto de threads de pares de chave/valor que possa ser acessado por vários threads simultaneamente). Em seguida, você pode usar o método GetOrAdd para obter as credenciais armazenadas em cache. 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## <a name="connecting-to-a-media-services-account-located-in-the-north-china-region"></a>Conexão para uma conta de serviços de mídia localizada na região Norte da China

Se sua conta estiver localizada na região Norte da China, use o seguinte construtor:

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Por exemplo:


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## <a name="storing-connection-values-in-configuration"></a>Armazenar valores de Conexão na configuração

É altamente recomendável para armazenar valores de conexão, especialmente confidenciais valores, como seu nome de conta e senha, na configuração. Além disso, é uma prática recomendada para criptografar dados de configuração confidenciais. Você pode criptografar o arquivo de configuração inteira usando o Windows arquivo EFS (sistema). Para habilitá-lo em um arquivo, clique com botão direito no arquivo, selecione **Propriedades**e habilite a criptografia na guia de configurações **avançadas** . Ou você pode criar uma solução personalizada para criptografar partes selecionadas de um arquivo de configuração usando configuração protegida. Consulte [criptografia configuração informações usando configuração protegida](https://msdn.microsoft.com/library/53tyfkaw.aspx).

O arquivo de App a seguir contém os valores de conexão necessários. Os valores na <appSettings> elemento são os valores necessários obtido do processo de configuração de conta de serviços de mídia.

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


Para recuperar os valores de conexão de configuração, você pode usar a classe **ConfigurationManager** e atribuir os valores de campos no seu código:
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
