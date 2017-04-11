<properties
  pageTitle="Cliente e servidor controle de versão SDK em Mobile aplicativos e serviços de celular | Serviço de aplicativo do Azure"
  description="Lista de cliente SDKs e compatibilidade com versões SDK do servidor para serviços de celular e aplicativos do Azure Mobile"
  services="app-service\mobile"
  documentationCenter=""
  authors="adrianhall"
  manager="erikre"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="10/01/2016"
  ms.author="adrianha"/>

# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controle de versão de cliente e servidor em Mobile aplicativos e serviços de celular

Versão mais recente do Azure Mobile Services é o recurso de **Aplicativos móveis** do serviço de aplicativo do Azure.

SDK do cliente e servidor aplicativos Mobile originalmente é baseado naqueles nos serviços do celular, mas eles são *não* é compatível com os outros.
Isto é, você deve usar um cliente de *Aplicativos Mobile* SDK com um servidor de *Aplicativos Mobile* SDK e da mesma forma para *Serviços de celular*. Este contrato é aplicado por meio de um valor de cabeçalho especial usado pelo cliente e servidor SDKs, `ZUMO-API-VERSION`.

Observação: sempre que esse documento se refere a um back-end *Serviços Mobile* , ele não necessariamente precisa ser hospedados em serviços de celular. Agora é possível migrar um serviço móvel para executar o serviço de aplicativo sem fazer alterações de código, mas o serviço ainda seria usando versões do SDK de *Serviços de celular* .

Para saber mais sobre a migração para o aplicativo de serviço sem fazer alterações de código, consulte o artigo [migrar um serviço móvel do serviço de aplicativo do Azure].

## <a name="header-specification"></a>Especificação de cabeçalho

A chave `ZUMO-API-VERSION` pode ser especificado no cabeçalho HTTP ou a cadeia de caracteres de consulta. O valor é uma cadeia de caracteres de versão no formulário **x.y.z**.

Por exemplo:

OBTER https://service.azurewebsites.net/tables/TodoItem

CABEÇALHOS: ZUMO--VERSÃO DA API: 2.0.0

POSTAR https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Recusar a verificação de versão

Você pode deixar de verificação definindo um valor de **true** para o aplicativo de configuração **MS_SkipVersionCheck**de versão. Especifica isso na Web. config ou na seção configurações de aplicativo do portal do Azure.

> [AZURE.NOTE] Há um número de alterações de comportamento entre aplicativos de celular, especialmente nas áreas de sincronização offline, autenticação e notificações por push e serviços de celular. Você só deve deixar de verificação após o teste completo para garantir que essas alterações de comportamento não quebrar a funcionalidade do seu aplicativo de versão.

## <a name="summary-of-compatibility-for-all-versions"></a>Resumo de compatibilidade para todas as versões

O gráfico abaixo mostra a compatibilidade entre todos os tipos de cliente e servidor. Um back-end é classificado como móveis **Serviços** ou **aplicativos** com base no servidor SDK que ele usa.

|                           | **Serviços móveis** Node ou .NET | **Aplicativos móveis** Node ou .NET |
| ----------                | -----------------------             |   ----------------              |
| [Clientes de serviços de celular] | Okey                                  | Erro\*                         |
| [Clientes de aplicativos Mobile]     | Erro\*                             | Okey                              |

\*Isso pode ser controlado, especificando **MS_SkipVersionCheck**.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Serviços de celular cliente e servidor

O cliente SDKs na tabela a seguir são compatíveis com **Os serviços de celular**.

Observação: o cliente de serviços de celular SDKs *não* enviar um valor de cabeçalho para `ZUMO-API-VERSION`. Se o serviço recebe este cabeçalho ou o valor de cadeia de caracteres de consulta, um erro será retornado, a menos que você explicitamente aceitaram out conforme descrito acima.

### <a name="MobileServicesClients"></a>Cliente de *Serviços* móvel SDKs

| Plataforma de cliente                   | Versão                                                                   | Valor de cabeçalho de versão |
| -------------------               | ------------------------                                                  | -------------------  |
| Cliente gerenciado (Windows, Xamarin) | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | n/d                  |
| iOS                               | [2.2.2](http://aka.ms/gc6fex)                                             | n/d                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | n/d                  |
| HTML                              | [1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | n/d     |

### <a name="mobile-services-server-sdks"></a>Servidor de *Serviços* móvel SDKs

| Plataforma de servidor  | Versão                                                                                                        | Cabeçalho de versão aceita |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* versão 1.0. x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Sem cabeçalho de versão** |
| Node          | (em breve)                        | **Sem cabeçalho de versão** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportamento de back-ends serviços Mobile

| VERSÃO DA API DE ZUMO | Valor da MS_SkipVersionCheck | Resposta |
| ---------------- | ---------------------------- | -------- |
| Não especificado    | Qualquer                          | 200 - Okey |
| Qualquer valor        | True                         | 200 - Okey |
| Qualquer valor        | FALSO/não especificado          | 400 - Solicitação inválida |

## <a name="2.0.0"></a>Azure Mobile aplicativos cliente e servidor

### <a name="MobileAppsClients"></a>Clientes de *aplicativos* móveis SDKs

Verificação de versão foi introduzida começando com as seguintes versões do cliente SDK para **Aplicativos do Azure móvel**:

| Plataforma de cliente                   | Versão                   | Valor de cabeçalho de versão |
| -------------------               | ------------------------  | -----------------    |
| Cliente gerenciado (Windows, Xamarin) | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| iOS                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Servidor de *aplicativos* móvel SDKs

Verificação de versão está incluída na seguinte versões SDK do servidor:

| Plataforma de servidor  | SDK                                                                                                        | Cabeçalho de versão aceita |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| Node          | [Azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento de back-ends aplicativos Mobile

| VERSÃO DA API DE ZUMO | Valor da MS_SkipVersionCheck | Resposta |
| ---------------- | ---------------------------- | -------- |
| x.y.z ou nulo    | True                         | 200 - Okey |
| Nulo             | FALSO/não especificado          | 400 - Solicitação inválida |
| 1.x.y            | FALSO/não especificado          | 400 - Solicitação inválida |
| 2.0.0-2.x.y      | FALSO/não especificado          | 200 - Okey |
| 3.0.0-3.x.y      | FALSO/não especificado          | 400 - Solicitação inválida |


## <a name="next-steps"></a>Próximas etapas

- [Migrar um serviço móvel para o serviço de aplicativo do Azure]


[Clientes de serviços de celular]: #MobileServicesClients
[Clientes de aplicativos Mobile]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrar um serviço móvel para o serviço de aplicativo do Azure]: app-service-mobile-migrating-from-mobile-services.md

