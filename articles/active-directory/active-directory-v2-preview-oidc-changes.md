<properties
    pageTitle="Mude para o ponto de extremidade do Azure AD v 2.0 | Microsoft Azure"
    description="Uma descrição das alterações que estão sendo feitas aos protocolos aplicativo modelo v 2.0 demonstração pública."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="important-updates-to-the-v20-authentication-protocols"></a>Atualizações importantes para os protocolos de autenticação v 2.0
Desenvolvedores de atenção! Sobre as próximas duas semanas, podemos fará algumas atualizações nossos v 2.0 protocolos de autenticação que talvez desejava alterações para qualquer aplicativos que você escreveu durante nosso período de visualização significativas.  

## <a name="who-does-this-affect"></a>Quem isso afeta?
Ponto de extremidade de autenticação, convergentes® quaisquer aplicativos que tiverem sido gravados para usar o v 2.0

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Obter mais informações sobre o ponto de extremidade de v 2.0 podem ser encontradas [aqui](active-directory-appmodel-v2-overview.md).

Se você tiver criado um aplicativo usando o ponto de extremidade de v 2.0 codificando diretamente para o protocolo v 2.0, usando qualquer um dos nossos middlewares web OpenID conectar ou OAuth ou outras bibliotecas de festa 3º executem autenticação, você deve estar preparado para testar seus projetos e faça as alterações necessárias.

## <a name="who-doesnt-this-affect"></a>Quem isso não afeta?
Quaisquer aplicativos que foram gravados contra o ponto de extremidade de autenticação de produção Azure AD

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Este protocolo é definido poderá ser alterado e não estar enfrentando qualquer alteração.

Além disso, se seu aplicativo **somente** usa nossa biblioteca ADAL para executar a autenticação, você não precisa alterar nada.  ADAL tem livre seu aplicativo das alterações.  

## <a name="what-are-the-changes"></a>Quais são as alterações?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Removendo o valor de x5t de cabeçalhos JWT
O ponto de extremidade de v 2.0 usa tokens JWT extensivamente, que contêm uma seção de cabeçalho de parâmetros com metadados relevantes sobre o token.  Se você decodificar o cabeçalho de um dos nossos JWTs atuais, você poderia encontrar algo como:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Onde propriedades de "x5t" e "criança" identificam a chave pública que deve ser usada para validar a assinatura do token, como recuperados a partir do ponto de extremidade de metadados OpenID se conectar.

A alteração que estamos fazendo aqui é remover a propriedade "x5t".  Você pode continuar a usar os mesmos mecanismos para validar tokens, mas deve depender apenas a propriedade de "criança" para recuperar a chave pública correta, conforme especificado no protocolo OpenID se conectar. 

> [AZURE.IMPORTANT] **Seu trabalho: certificar-se de que seu aplicativo não dependem da existência do valor x5t.**

### <a name="removing-profileinfo"></a>Removendo profile_info
Anteriormente, o ponto de extremidade de v 2.0 tem foi retornando um objeto JSON de codificação base64 em respostas token chamadas `profile_info`.  Ao solicitar um token de acesso do ponto de extremidade v 2.0 enviando uma solicitação para:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

A resposta ficaria o objeto JSON a seguir:
```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

O `profile_info` informações de valor contido sobre o usuário que conectado no aplicativo - seu nome para exibição, nome, sobrenome, endereço de email, identificador e assim por diante.  Essencialmente, o `profile_info` foi usado para o cache de token e fins de exibição.

Estamos agora removendo o `profile_info` valor – mas não se preocupe, estamos ainda fornecendo essas informações para os desenvolvedores em um local ligeiramente diferentes.  Em vez de `profile_info`, o ponto de extremidade de v 2.0 agora retornará um `id_token` em cada resposta token:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Você pode decodificar e analisar o id_token para recuperar as mesmas informações que você recebeu do profile_info.  O id_token é um JSON Web Token (JWT), com conteúdo conforme especificado pelo OpenID se conectar.  O código para fazer então deve ser muito semelhante – basta extrair o segmento intermediário (o corpo) a id_token e na Base 64 decodificá-lo para acessar o objeto JSON dentro.

Sobre as próximas duas semanas, você deve codificar seu aplicativo para recuperar as informações do usuário em um a `id_token` ou `profile_info`; o que for presente.  Dessa forma, quando a alteração é feita, o seu aplicativo perfeitamente pode lidar com a transição do `profile_info` para `id_token` sem interrupção.

> [AZURE.IMPORTANT] **Seu trabalho: certificar-se de que seu aplicativo não dependem da existência do `profile_info` valor.**

### <a name="removing-idtokenexpiresin"></a>Removendo id_token_expires_in
Semelhante a `profile_info`, estamos também removendo o `id_token_expires_in` parâmetro das respostas.  Anteriormente, o ponto de extremidade de v 2.0 retornará um valor para `id_token_expires_in` juntamente com cada resposta de id_token, por exemplo em uma resposta de autorizar:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Ou em uma token resposta:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

O `id_token_expires_in` valor indica o número de segundos a id_token permanecerá válido para.  Agora, estamos removendo a `id_token_expires_in` valor completamente.  Em vez disso, você pode usar o padrão de OpenID conectar `nbf` e `exp` declarações para examinar a validade de um id_token.  Consulte a [referência de token de v 2.0](active-directory-v2-tokens.md) para obter mais informações sobre essas declarações.

> [AZURE.IMPORTANT] **Seu trabalho: certificar-se de que seu aplicativo não dependem da existência do `id_token_expires_in` valor.**


### <a name="changing-the-claims-returned-by-scopeopenid"></a>Alterando as declarações retornadas pelo scope = openid
Essa alteração será o mais significativo – na verdade, ela afetará quase todos os aplicativos que usa o ponto de extremidade de v 2.0.  Muitos aplicativos enviam solicitações para o ponto de extremidade de v 2.0 usando o `openid` escopo, como:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Hoje, quando o usuário concede consentimento para a `openid` escopo, seu aplicativo recebe uma grande variedade de informações sobre o usuário na id_token resultante.  Essas declarações podem incluir o nome dele, preferencial de nome de usuário, endereço de email, ID do objeto e muito mais.

Nesta atualização, estamos alterando as informações que o `openid` escopo dá acesso ao aplicativo para melhor comform com a especificação de OpenID conectar.  O `openid` escopo serão só permitir que o seu aplicativo entre o usuário na e recebe um identificador de aplicativo específico do usuário no `sub` reivindicar da id_token.  As declarações em um id_token com apenas o `openid` escopo concedido será sem qualquer informação de identificação pessoal.  Exemplo id_token declarações são:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Se desejar obter informações de identificação pessoal (IIP) sobre o usuário em seu aplicativo, seu aplicativo precisará solicitar permissões adicionais do usuário.  Estamos introduzindo suporte para dois novos escopos da especificação de OpenID conectar – o `email` e `profile` escopos – que permitem fazer isso.

- O `email` escopo é bastante simples – ele permite o acesso de aplicativo ao endereço de email principal do usuário por meio do `email` reivindicar na id_token.  Observe que o `email` declaração nem sempre será presente no id_tokens – só será incluído se disponíveis no perfil do usuário.
- O `profile` escopo concedem o acesso do aplicativo para todas as outras informações básicas sobre o usuário – seu nome, nome de usuário preferencial, a ID de objeto e assim por diante.

Isso permite que você o código de seu aplicativo de maneira mínimo divulgação – você pode solicitar o usuário apenas o conjunto de informações que o seu aplicativo precisa para fazer seu trabalho.  Se desejar continuar recebendo o conjunto completo de informações do usuário que seu aplicativo está recebendo, você deve incluir todos os três escopos em suas solicitações de autorização:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Seu aplicativo pode começar a enviar o `email` e `profile` escopos imediatamente e o ponto de extremidade de v 2.0 aceitará esses dois escopos e começar solicitando permissões de usuários conforme necessário.  No entanto, a alteração na interpretação a `openid` escopo não terá efeito para algumas semanas.

> [AZURE.IMPORTANT] **Seu trabalho: adicionar o `profile` e `email` escopos se seu aplicativo requer informações sobre o usuário.**  Observe que ADAL incluirá ambas as permissões em solicitações por padrão. 

### <a name="removing-the-issuer-trailing-slash"></a>Removendo o emissor barra à direita.
Anteriormente, o valor de emissor que aparece em tokens do ponto de extremidade v 2.0 levou o formulário

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Onde o guid foi o tenantId do locatário Azure AD que emitiu o token.  Com essas alterações, o valor de emissor torna-se

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

em ambos os tokens e no documento discovery OpenID se conectar.

> [AZURE.IMPORTANT] **Seu trabalho: certificar-se de que seu aplicativo aceita o valor de emissor com e sem uma barra invertida durante a validação de emissor.**

## <a name="why-change"></a>Por que mudar?
Os principais motivos para introduzir essas alterações é compatível com a especificação de padrão OpenID se conectar.  Sendo OpenID conectar compatíveis, esperamos minimizar diferenças entre integração com serviços de identidade da Microsoft e com outros serviços de identidade do setor.  Queremos permitem aos desenvolvedores usar suas bibliotecas de autenticação favorito Abrir origem sem ter de alterar as bibliotecas para acomodar diferenças da Microsoft.

## <a name="what-can-you-do"></a>O que você pode fazer?
Até hoje, você pode começar a fazer todas as alterações descritas acima.  Você deve imediatamente:

1.  **Remova quaisquer dependências na `x5t` parâmetro de cabeçalho.**
2.  **Lidar com a transição do `profile_info` para `id_token` em respostas token.**
3.  **Remova quaisquer dependências na `id_token_expires_in` parâmetro de resposta.**
3.  **Adicionar o `profile` e `email` escopos para o seu aplicativo se seu aplicativo precisar de informações do usuário básica.**
4.  **Aceite os valores de emissor em tokens de com e sem uma barra invertida.**

Nossa [documentação de protocolo v 2.0](active-directory-v2-protocols.md) já foi atualizado para refletir essas alterações, para que você pode usá-lo como referência ajudando a atualizar seu código.

Se você tiver mais dúvidas sobre o escopo das alterações, fique à vontade falar no Twitter em @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Com que frequência ocorrerá alterações de protocolo?
Não podemos prever qualquer posterior à ruptura muda para os protocolos de autenticação.  Podemos intencionalmente agrupará essas alterações em uma versão para que você não tem que passar por esse tipo de processo de atualização novamente a qualquer momento em breve.  Claro, podemos vão continuar a adicionar recursos ao serviço de autenticação v 2.0 convergentes que você possa aproveitar, mas essas alterações devem ser aditivos e não quebra existentes código.

Por fim, podemos gostaria de dizer obrigado por experimentar coisas durante o período de visualização.  A obtenção de informações e experiências de nossos usuários iniciais foram muito útil até o momento, e esperamos que você continue compartilhar suas opiniões e ideias.

Codificação feliz!

A divisão de identidade da Microsoft
