###<a name="server-auth"></a>Como: autenticar com um provedor (fluxo de servidor)

Para fazer com que aplicativos Mobile gerenciar o processo de autenticação em seu aplicativo, você deve registrar seu aplicativo com seu provedor de identidade. Em seguida, no seu serviço de aplicativo do Azure, você precisa configurar a ID de aplicativo e secreta fornecida pelo seu provedor.
Para obter mais informações, consulte o tutorial [adicionar autenticação para o seu aplicativo].

Depois que você registrou seu provedor de identidade, basta chame o método de .login() com o nome do seu provedor. Por exemplo, faça logon com o uso do Facebook, o código a seguir.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Se você estiver usando um provedor de identidade diferente de Facebook, altere o valor passado para o método de login acima para um dos seguintes: `microsoftaccount`, `facebook`, `twitter`, `google`, ou `aad`.

Nesse caso, o serviço de aplicativo do Azure gerencia o fluxo de autenticação de OAuth 2.0 exibindo a página de login do provedor selecionado e gerar um token de autenticação do serviço de aplicativo após login bem-sucedido com o provedor de identidade. A função de login, ao concluir, retorna um objeto JSON (usuário) que expõe a ID de usuário e o serviço de aplicativo do token de autenticação nos campos ID de usuário e authenticationToken, respectivamente. Esse token pode ser armazenados em cache e reutilizada até que ela expire.

###<a name="client-auth"></a>Como: autenticar com um provedor (fluxo de cliente)

Seu aplicativo pode também independentemente entre em contato com o provedor de identidade e fornecer o token retornado ao seu serviço de aplicativo para autenticação. Este fluxo de cliente permite que você forneça uma experiência de logon único para usuários ou para recuperar dados de usuário adicionais do provedor de identidade.

#### <a name="social-authentication-basic-example"></a>Exemplo de básico de autenticação social

Este exemplo usa o SDK cliente do Facebook para autenticação:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
Este exemplo pressupõe que o símbolo fornecido pelo provedor de respectivos SDK é armazenado na variável de token.

#### <a name="microsoft-account-example"></a>Exemplo de Account da Microsoft

O exemplo a seguir usa o SDK do Live, que suporta single sign-on para aplicativos da Windows Store usando Account da Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

Este exemplo obtém um token do Live se conectar, que é fornecido com o serviço de aplicativo chamando a função de login.

###<a name="auth-getinfo"></a>Como: obter informações sobre o usuário autenticado

As informações de autenticação para o usuário atual podem ser recuperadas do `/.auth/me` usando qualquer método AJAX do ponto de extremidade.  Certifique-se de definir o `X-ZUMO-AUTH` cabeçalho para o token de autenticação.  O símbolo de autenticação é armazenada em `client.currentUser.mobileServiceAuthenticationToken`.  Por exemplo, para usar a API de busca:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Busca está disponível como um pacote de npm ou para download do navegador do CDNJS. Você também pode usar jQuery ou outra API de AJAX para buscar as informações.  Os dados serão recebidos como um objeto JSON.
