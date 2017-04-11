<properties 
    pageTitle="Como delegar assinatura de produto e registro do usuário" 
    description="Aprenda a assinatura de produto e registro do usuário para um terceiro gerenciamento de API do Azure de representante." 
    services="api-management" 
    documentationCenter="" 
    authors="antonba" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="antonba"/>

# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar assinatura de produto e registro do usuário

Delegação permite que você use seu site existente para lidar com o desenvolvedor sinal-no/sign-Valorizações e assinatura para produtos em vez de usar a funcionalidade interna no portal do desenvolvedor. Isso permite que seu site possui os dados do usuário e executar a validação destas etapas de uma maneira personalizada.

## <a name="delegate-signin-up"> </a>Delegando desenvolvedor entrar e inscrição

Delegar desenvolvedor entrar e inscrição para seu site existente, que você precisará criar um ponto de extremidade de delegação especial no seu site que atua como ponto de entrada para qualquer solicitação como essa iniciadas a partir do portal do desenvolvedor do gerenciamento de API.

O fluxo de trabalho final ficará assim:

1. Cliques do desenvolvedor no link entrar ou inscrição no portal do desenvolvedor do gerenciamento de API
2. Navegador será redirecionado para o ponto de extremidade de delegação
3. Ponto de extremidade de delegação de retorno redireciona para ou apresenta solicitando usuário entrar ou inscrição de interface do usuário
4. Sucesso, o usuário é redirecionado voltar para a página de portal de desenvolvedor do gerenciamento de API que eles iniciado a partir


Para começar, vamos primeiro gerenciamento de API de configuração para rotear solicitações por meio de seu ponto de extremidade de delegação. No portal do fornecedor de gerenciamento de API, clique em **segurança** e, em seguida, clique na guia **delegação** . Clique na caixa de seleção para habilitar 'Delegate entrar & inscrição'.

![Página de delegação][api-management-delegation-signin-up]

* Decida o que a URL do seu ponto de extremidade de delegação especiais serão e insira-o no campo **URL de ponto de extremidade de delegação** . 

* Dentro do campo de **chave de autenticação de delegação** Insira um segredo que será usado para calcular uma assinatura fornecida a você para verificação garantir que a solicitação realmente é proveniente de gerenciamento de API do Azure. Você pode clicar no botão **Gerar** para API Managemnet aleatoriamente gerar uma chave para você.

Agora você precisa criar o **ponto de extremidade de delegação**. Ela tem que realizar um número de ações:

1. Receba uma solicitação da seguinte forma:

    > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= {URL da página de origem} & valor falso = {string} & sig = {string}*

    Parâmetros de consulta para o caso de entrar / inscrição:
    - **operação**: identifica qual tipo de delegação solicitá-los é - ele só é possível **entrar** neste caso
    - **returnUrl**: a URL da página onde o usuário clicou em um link de entrada ou inscrição
    - **Falso**: uma cadeia de caracteres especial do falso, usada para computação um hash de segurança
    - **SIG**: um hash de segurança computado a ser usada para comparação para o seu próprio calculado hash

2. Verifique se que a solicitação é proveniente de gerenciamento de API do Azure (opcional, mas altamente recomendado para segurança)

    * Calcule um hash SHA512 HMAC de uma cadeia de caracteres com base nos parâmetros de consulta **returnUrl** e **Falso** ([código de exemplo fornecido abaixo]):
        > HMAC (**Falso** '\n' + **returnUrl**)
         
    * Compare o hash acima calculado ao valor do parâmetro de consulta **sig** . Se os dois hashes corresponderem, ir para a próxima etapa, caso contrário, negar a solicitação.

2. Verifique se que você receba uma solicitação de entrada-no/entrada-up: o parâmetro de consulta de **operação** será definido como "**entrar**".

3. Apresentar ao usuário interface de usuário para entrar ou inscrição

4. Se o usuário estiver entrando-up você precisa criar uma conta correspondente para eles no gerenciamento de API. [Criar um usuário] com a API REST de gerenciamento. Ao fazer isso, certifique-se de que você defina a ID de usuário para o mesmo está em seu armazenamento de usuários ou uma identificação que você pode controlar as.

5. Quando o usuário for autenticado com êxito:

    * [solicitar um token logon único (SSO)] via a API REST de gerenciamento

    * Anexe um parâmetro de consulta returnUrl para a URL de SSO recebido da chamada da API acima:
        > Por exemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

    * redirecionar o usuário para o URL produzido acima

Além da operação de **entrar** , você também pode executar o gerenciamento de conta seguindo as etapas anteriores e usando uma das seguintes operações.

-   **Alteração de senha**
-   **ChangeProfile**
-   **CloseAccount**

Você deve passar os seguintes parâmetros de consulta para operações de gerenciamento de conta.

-   **operação**: identifica qual tipo de solicitação de delegação é (ChangePassword, ChangeProfile ou CloseAccount)
-   **ID do usuário**: a id de usuário da conta para gerenciar
-   **Falso**: uma cadeia de caracteres especial do falso, usada para computação um hash de segurança
-   **SIG**: um hash de segurança computado a ser usada para comparação para o seu próprio calculado hash

## <a name="delegate-product-subscription"> </a>Delegando a assinatura do produto

Delegando a assinatura do produto funciona da mesma forma a delegação de usuário entrar/para cima. O fluxo de trabalho final seria assim:

1. Desenvolvedor seleciona um produto no portal do desenvolvedor do gerenciamento de API e clica no botão assinar
2. Navegador será redirecionado para o ponto de extremidade de delegação
3. Ponto de extremidade de delegação executa etapas de inscrição de produtos necessário - isso é até você e pode envolver redirecionar para outra página para solicitar informações de cobrança, fazendo perguntas adicionais, ou simplesmente armazenar as informações e não exigir qualquer ação do usuário


Para habilitar a funcionalidade, na página **delegação** , clique em **assinatura de produto do representante**.

Verifique se que o ponto de extremidade de delegação executa as seguintes ações:


1. Receba uma solicitação da seguinte forma:

    > *http://www.yourwebsite.com/apimdelegation?operation= {operação} & productId = {produto assine} & ID = {usuário fazendo solicitação} & falso = {string} & sig = {string}*

    Parâmetros de consulta para o caso de assinatura do produto:
    - **operação**: identifica o tipo de solicitação de delegação que ele está. Assinatura de produto solicitações das opções válidas são:
        - "Assinar": uma solicitação para assinar o usuário para um determinado produto com fornecido ID (veja abaixo)
        - "Cancelar assinatura": uma solicitação para cancelar a assinatura de um usuário de um produto
        - "Renovação": uma requst para renovar uma assinatura (por exemplo, que pode ser expirar)
    - **ID do produto**: a ID do produto o usuário solicitado para inscrever-se
    - **ID do usuário**: a ID do usuário para o qual a solicitação é feita
    - **Falso**: uma cadeia de caracteres especial do falso, usada para computação um hash de segurança
    - **SIG**: um hash de segurança computado a ser usada para comparação para o seu próprio calculado hash


2. Verifique se que a solicitação é proveniente de gerenciamento de API do Azure (opcional, mas altamente recomendado para segurança)

    * Calcule um HMAC-SHA512 de uma cadeia de caracteres com base nos parâmetros de consulta do **productId**, **ID de usuário** e **Falso** :
        > HMAC (**Falso** + '\n' + **productId** + '\n' + **ID de usuário**)
         
    * Compare o hash acima calculado ao valor do parâmetro de consulta **sig** . Se os dois hashes corresponderem, ir para a próxima etapa, caso contrário, negar a solicitação.
    
3. Execute qualquer processamento de assinatura do produto com base no tipo de operação solicitada em **operação** - por exemplo, cobrança, outras perguntas, etc.

4. Sobre a inscrição com êxito o usuário ao produto de seu lado, assine o usuário para o produto de gerenciamento de API chamando [A API REST para assinatura do produto].

## <a name="delegate-example-code"></a> Código de exemplo ##

Esses exemplos de código mostram como aproveitar a *chave de validação de delegação*, que é definida na tela delegação do portal do publisher, para criar um HMAC que é usado para validar a assinatura, provando a validade da returnUrl passado. O mesmo código funciona para a ID do produto e a ID de usuário com leve modificação.

**Código c# para gerar hash de returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**Código de NodeJS para gerar o hash do returnUrl**

    var crypto = require('crypto');
    
    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';
    
    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
    
    var signature = digest.toString('base64');

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre delegação, consulte o vídeo a seguir.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicitar um token logon único (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[criar um usuário]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[chamando a API REST para assinatura do produto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 