A maioria dos erros de autenticação de tempo resultado de configurações incorretos ou inconsistente. Aqui estão algumas sugestões específicas para verifique.

* Certifique-se de que você não perca o botão **Salvar** em qualquer lugar. Isso geralmente é fácil fazer e o resultado é que você vai ser examinando os valores corretos em uma página do portal, mas eles realmente ainda não foram salvas no ambiente Azure ou aplicativo do Azure AD.
* Para ajustar as configurações definidas na lâmina **Configurações do aplicativo** do portal do Azure, certifique-se de que o aplicativo de API correto ou web app foi selecionada quando as configurações foram inseridas.  Verifique também se as configurações foram inseridas como **configurações de aplicativo** e não **cadeias de caracteres de Conexão**, como o formato das duas seções é semelhante.
* Para autenticação um front-end JavaScript, baixe o manifesto novamente para verificar se `oauth2AllowImplicitFlow` foi alterada com êxito para `true`.
* Verifique se que você usou HTTPS sempre que você configurou URLs:

    * No código do projeto
    * Em CORS
    * Em configurações de aplicativo de ambiente Azure para cada aplicativo API e web app
    * Em configurações de aplicativo do Azure AD.
    
    Observe que se você copiar a URL de um aplicativo API do portal do, ela geralmente tem `http://` e você tiver alterá-lo manualmente `https://`.

* Certifique-se de que as alterações de código foram implantadas com êxito. Por exemplo, em uma solução de vários projetos é possível alterar o código de um projeto e escolha um dos outros acidentalmente quando você pretende implantar a alteração.
* Certifique-se de que você vai URLs HTTPS no seu navegador, não as URLs de HTTP. Por padrão, o Visual Studio cria publicar perfis com URLs HTTP e é o que é aberta no navegador após implantar um projeto.
* Autenticação para um front-end JavaScript, certifique-se de que CORS está configurado corretamente no aplicativo do API que chama o código JavaScript. Se estiver em dúvida se o problema está relacionado ao CORS, tente "*" como a URL de origem permitidos. 
* Para um JavaScript front-end, abra a guia de Console de ferramentas de desenvolvedor do seu navegador para obter mais informações de erro e examinar as solicitações HTTP na rede. No entanto, mensagens de erro de Console podem ser muito clara. Se você receber uma mensagem indicando um erro CORS, o problema real pode ser autenticação. Você pode verificar se esse for o caso, executando o aplicativo com autenticação temporariamente temporariamente desabilitada.
* Para um aplicativo de API .NET, certificar-se de que você está obtendo tantas informações em mensagens de erro possível definindo [modo customErrors como desativado](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Para um aplicativo de API .NET, inicie uma [sessão de depuração remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)e examine os valores das variáveis que são transferidos para o código que usa ADAL para adquirir um token de portador ou código que verifica declarações em relação a identificação de principal do serviço esperado. Observe que seu código pode pegar valores de configuração de várias fontes diferentes, portanto, é possível encontrar surpresas dessa forma. Por exemplo, se você digitar `ida:ClientId` como `ida:ClientID` ao definir configurações de ambiente de serviço de aplicativo do Azure, o código pode receber a `ida:ClientId` valor que está procurando do arquivo Web. config, ignorando a configuração do serviço de aplicativo do Azure. 
* Se as coisas não funcionam em uma janela do Internet Explorer normal, um log-in existente pode estar interferindo; Tente InPrivate e Chrome ou o Firefox.
