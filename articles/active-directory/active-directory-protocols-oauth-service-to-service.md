<properties
    pageTitle="Azure AD serviços Auth usando OAuth2.0 | Microsoft Azure"
    description="Este artigo descreve como usar mensagens HTTP para implementar autenticação de serviços usando o fluxo de conceder OAuth2.0 cliente credenciais."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>

# <a name="service-to-service-calls-using-client-credentials"></a>Serviço para chamadas de serviço usando as credenciais do cliente

OAuth 2.0 cliente credenciais conceder fluxo de permite que um serviço da web (um *cliente confidencial*) para usar seus próprio credenciais para autenticar ao chamar outro serviço da web, em vez de representando um usuário. Neste cenário, o cliente normalmente é um serviço web intermediário, um serviço daemon ou site da web.

## <a name="client-credentials-grant-flow-diagram"></a>Diagrama de fluxo de conceder as credenciais do cliente

O diagrama a seguir explica como as credenciais do cliente conceder fluxo funciona no Azure Active Directory (AD Azure).

![Fluxo de concessão de credenciais de cliente OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. O aplicativo cliente autentica para o ponto de extremidade de emissão de token do Azure AD e solicita um token de acesso.
2. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
3. O token de acesso é usado para autenticar o recurso protegido.
4. Dados do recurso protegido são retornados ao aplicativo web.

## <a name="register-the-services-in-azure-ad"></a>Registrar serviços no Azure AD

Registre o serviço de chamada e o serviço receptor do Azure Active Directory (AD Azure). Para obter instruções detalhadas, consulte [adicionando, atualizando e removendo um aplicativo](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>Solicitar um Token de acesso

Para solicitar um token de acesso, use um HTTP POST para específicos do locatário do ponto de extremidade do Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Solicitação de token de acesso ao serviço

Uma solicitação de token de acesso de serviço para contém os seguintes parâmetros.

| Parâmetro | | Descrição |
|-----------|------|------------|
| response_type | Necessário | Especifica o tipo de resposta solicitada. Em um fluxo de concessão de credenciais do cliente, o valor deve ser **client_credentials**.|
| client_id | Necessário | Especifica a identificação de cliente do Azure AD do serviço da web chamada. Para localizar ID do cliente do aplicativo de chamada, no Portal de gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e clique em **Configurar**.|
| client_secret | Necessário |  Insira uma chave registrada para o serviço web chamada no Azure AD. Para criar uma chave, no Portal de gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e clique em **Configurar**. |
| recurso | Necessário | Insira o URI de ID de aplicativo do serviço da web recebimento. Para localizar o URI de ID do aplicativo, no Portal de gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e clique em **Configurar**. |

## <a name="example"></a>Exemplo

A seguinte POSTAGEM HTTP solicita um token de acesso do serviço da web https://service.contoso.com/. O `client_id` identifica o serviço da web que solicita o token de acesso.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>Resposta de Token de acesso de serviço para

Uma resposta de sucesso contém uma resposta JSON OAuth 2.0 com os seguintes parâmetros.

| Parâmetro   | Descrição |
|-------------|-------------|
|access_token |O token de acesso solicitado. O serviço web chamada pode usar esse token para autenticar para o serviço web de recebimento. |
|access_type  | Indica o valor de tipo de token. O único tipo que ofereça suporte Azure AD é **portador**. Para obter mais informações sobre tokens de portador, consulte o [OAuth 2.0 autorização Framework: uso de Token de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).
|expires_in   | Por quanto tempo o token de acesso é válido (em segundos).|
|expires_on   |O tempo quando o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até o tempo de expiração. Esse valor é usado para determinar o ciclo de vida de tokens em cache. |
|recurso     | O URI de ID de aplicativo do serviço da web recebimento. |

## <a name="example"></a>Exemplo

O exemplo a seguir mostra uma resposta de sucesso a uma solicitação de um token de acesso a um serviço web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Consulte também

* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md)
