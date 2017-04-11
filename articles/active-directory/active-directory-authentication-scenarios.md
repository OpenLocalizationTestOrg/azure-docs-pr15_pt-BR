
<properties
   pageTitle="Cenários de autenticação do Azure AD | Microsoft Azure"
   description="Uma visão geral dos cinco cenários autenticação mais comuns do Azure Active Directory (AAD)"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="authentication-scenarios-for-azure-ad"></a>Cenários de autenticação do Azure AD

Azure Active Directory (AD Azure) simplifica a autenticação para desenvolvedores fornecendo identidade como uma fonte de serviço, com suporte para protocolos padrão da indústria como OAuth 2.0 e OpenID conectar, bem como abrir bibliotecas para diferentes plataformas para ajudá-lo a começar a codificação rapidamente. Este documento ajudará você a entender o suporta cenários Azure AD vários e mostrará a você como começar. Ele é dividido em seções a seguir:

- [Noções básicas de autenticação no Azure AD](#basics-of-authentication-in-azure-ad)

- [Declarações em Tokens de segurança do Azure AD](#claims-in-azure-ad-security-tokens)

- [Noções básicas de como registrar um aplicativo no Azure AD](#basics-of-registering-an-application-in-azure-ad)

- [Cenários e tipos de aplicativo](#application-types-and-scenarios)

  - [Navegador da Web para o aplicativo Web](#web-browser-to-web-application)

  - [Aplicativo de página única (SPA)](#single-page-application-spa)

  - [Aplicativo nativo Web API](#native-application-to-web-api)

  - [Aplicativo Web Web API](#web-application-to-web-api)

  - [Daemon ou aplicativo de servidor Web API](#daemon-or-server-application-to-web-api)



## <a name="basics-of-authentication-in-azure-ad"></a>Noções básicas de autenticação no Azure AD

Se você estiver familiarizado com os conceitos básicos de autenticação no Azure AD, leia esta seção. Caso contrário, talvez você queira ignorar para baixo para [tipos de aplicativos e cenários](#application-types-and-scenarios).

Vamos considerar o cenário mais básico onde é necessária uma identidade: precisa de um usuário em um navegador da web para autenticar para um aplicativo web. Este cenário é descrito mais detalhadamente na seção de [Navegador da Web para o aplicativo da Web](#web-browser-to-web-application) , mas é um ponto de partida útil para ilustrar os recursos do Azure AD e conceitualizar como funciona o cenário. Considere o diagrama a seguir para esse cenário:

![Visão geral de logon ao aplicativo web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Com o diagrama acima em mente, veja aqui o que você precisa saber sobre seus vários componentes:

- Azure AD é o provedor de identidade, responsável por verificar a identidade de usuários e aplicativos que existem no diretório da organização e finalmente emitir tokens de segurança durante a autenticação bem-sucedida desses usuários e aplicativos.


- Um aplicativo que quer terceirizado autenticação Azure AD deve ser registrado no Azure AD, que registra e identifica exclusivamente o aplicativo no diretório.


- Os desenvolvedores podem usar as bibliotecas de autenticação abrir origem Azure AD para facilitar a autenticação manipulando os detalhes de protocolo para você. Consulte [Azure Active Directory autenticação bibliotecas](active-directory-authentication-libraries.md) para obter mais informações.


• Depois que um usuário foi autenticado, o aplicativo deve validar token de segurança do usuário para garantir que a autenticação foi bem-sucedida para os participantes pretendidos. Os desenvolvedores podem usar as bibliotecas de autenticação fornecidos para lidar com a validação de qualquer token do Azure AD, incluindo JSON Web Tokens (JWT) ou SAML 2.0. Se você quiser executar validação manualmente, consulte a documentação de [Manipulador de Token de JWT](https://msdn.microsoft.com/library/dn205065.aspx) .


> [AZURE.IMPORTANT] Azure AD utiliza criptografia de chave pública para assinar tokens e verificar se eles são válidos. Consulte [Importantes informações sobre a assinatura de chave sobreposição no Azure AD](active-directory-signing-key-rollover.md) para obter mais informações sobre a lógica necessária que você deve ter em seu aplicativo para garantir que ela é sempre atualizada com as teclas mais recentes.


• O fluxo de solicitações e respostas para o processo de autenticação é determinado pelo protocolo de autenticação que foi usado, como 2.0 OAuth, OpenID conectar, Web Services Federation ou SAML 2.0. Esses protocolos são discutidos mais detalhadamente no tópico [Protocolos de autenticação do Azure Active Directory](active-directory-authentication-protocols.md) e nas seções a seguir.

> [AZURE.NOTE] Azure AD suporta o 2.0 OAuth e padrões OpenID conectar que tornam o amplo usam de tokens de portador, incluindo tokens de portador representados como JWTs. Um token de portador é um token de segurança leve que concede acesso à "portador" para um recurso protegido. Nesse sentido, "portador" é qualquer pessoa que pode apresentar o token. Embora uma festa deve primeiro autenticar com o Azure AD para receber o token de portador, se as etapas necessárias não são levadas para proteger o token de transmissão e armazenamento, podem ser interceptado e usado por uma pessoa não intencionais. Enquanto alguns tokens de segurança tem um mecanismo interno para impedir que autorizadas usá-los, tokens de portador não tem esse mecanismo e devem ser transportados em um canal seguro como segurança da camada de transporte (HTTPS). Se um token de portador é transmitido em Limpar, um homem-ataque de interceptação pode ser usado por um terceiro mal-intencionado adquirir o token e usá-lo para um acesso não autorizado a um recurso protegido. Os mesmos princípios de segurança se aplicam ao armazenar ou cache tokens de portador para uso posterior. Sempre certifique-se de que seu aplicativo transmite e armazena tokens de portador de maneira segura. Para mais considerações de segurança em tokens de portador, consulte [RFC 6750 seção 5](http://tools.ietf.org/html/rfc6750).


Agora que você tem uma visão geral dos conceitos básicos, leia as seções a seguir para compreender como funciona o provisionamento no Azure AD e os cenários comuns Azure AD oferece suporte.


## <a name="claims-in-azure-ad-security-tokens"></a>Declarações em Tokens de segurança do Azure AD

Tokens de segurança emitidos por Azure AD contenham declarações ou declarações de informações sobre o assunto que foi autenticado. Essas declarações podem ser usadas pelo aplicativo para várias tarefas. Por exemplo, eles podem ser usados para validar o token, identifique locatário de diretório do assunto, exibir informações do usuário, determinar a autorização da entidade e assim por diante. As declarações presentes em qualquer token de segurança dependem do tipo de token, o tipo de credencial usado para autenticar o usuário e a configuração do aplicativo. Uma breve descrição de cada tipo de declaração emitida por Azure AD é fornecida na tabela a seguir. Para obter mais informações, consulte [suporte Token e tipos de declaração](active-directory-token-and-claims.md).


| Declaração | Descrição |
|-------|-------------|
| ID do aplicativo | Identifica o aplicativo que está usando o token.
| Público-alvo | Identifica o recurso destinatário que o token destina. |
| Referência de classe de contexto de autenticação de aplicativo | Indica como o cliente foi autenticado (cliente público versus confidencial cliente). |
| Autenticação instantânea | Registra a data e hora quando ocorreu a autenticação. |
| Método de autenticação | Indica como o assunto do token foi autenticado (senha, certificado, etc.). |
| Nome | Fornece o nome fornecido do usuário definido no Azure AD. |
| Grupos | Contém grupos de objetos de Ids do Azure AD que o usuário é membro. |
| Provedor de identidade | O provedor de identidade que autenticado o assunto do token de registros. |
| Emitido em | A hora em que o token foi emitido, geralmente usado para atualização do token de registros. |
| Emissor | Identifica o STS emitido o token bem como o locatário do Azure AD. |
| Sobrenome | Fornece o sobrenome do usuário definido no Azure AD. |
| Nome | Fornece um valor legível humano que identifica o assunto do token. |
| Id do objeto | Contém um identificador exclusivo, imutável do assunto no Azure AD. |
| Funções | Contém nomes amigáveis Azure AD das funções do aplicativo que o usuário recebeu. |
| Escopo | Indica as permissões concedidas para o aplicativo cliente. |
| Assunto | Indica o capital sobre quais o token declarações informações. |
| Id do locatário | Contém um identificador exclusivo, imutável do locatário diretório que emitiu o token. |
| Vida útil do token | Define o intervalo de tempo em que um token é válido. |
| Nome UPN | Contém o nome de usuário principal do assunto. |
| Versão | Contém o número de versão do símbolo. |


## <a name="basics-of-registering-an-application-in-azure-ad"></a>Noções básicas de como registrar um aplicativo no Azure AD

Qualquer aplicativo que outsources autenticação Azure AD deve ser registrado em um diretório. Esta etapa envolve informando Azure AD sobre seu aplicativo, incluindo a URL onde tem localizada, a URL para enviar respostas após a autenticação, o URI para identificar seu aplicativo e muito mais. Essas informações são necessárias por alguns motivos principais:

- Azure AD necessidades coordenadas para se comunicar com o aplicativo ao manipular tokens de logon ou troca. Eles incluem o seguinte:

  - URI de ID do aplicativo: O identificador de um aplicativo. Esse valor é enviado ao Azure AD durante a autenticação para indicar qual aplicativo o chamador deseja um símbolo para. Além disso, esse valor é incluído no token de forma que o aplicativo sabe era o destino pretendido.


  - Responder a URL e redirecionar URI: no caso de um API da web ou o aplicativo da web, a URL de resposta é o local ao qual o Azure AD enviará a resposta de autenticação, incluindo um token se autenticação foi bem-sucedido. No caso de um aplicativo nativo, o URI redirecionar é um identificador exclusivo para o qual Azure AD redirecionará o agente de usuário em uma solicitação de OAuth 2.0.


  - ID do cliente: A identificação de um aplicativo, que é gerado pelo Azure AD quando o aplicativo é registrado. Ao solicitar um código de autorização ou token, a ID do cliente e a chave são enviadas ao Azure AD durante a autenticação.


  - Chave: A chave que é enviada junto com uma ID de cliente ao autenticar para Azure AD para chamar um web API.


- Azure AD precisa garantir que o aplicativo tem as permissões necessárias para acessar os dados de diretório, outros aplicativos em sua organização e assim por diante

Provisionamento fica mais claro quando você entenda que existem duas categorias de aplicativos que podem ser desenvolvidos e integrados ao Azure AD:

- Um único aplicativo do locatário: um aplicativo de locatário único destina-se ao uso em uma organização. Estes são normalmente linha de negócios (LoB) aplicativos escritos por um desenvolvedor de empresa. Um aplicativo de locatário único só precisa ser acessados por usuários em um diretório e como resultado, ele só precisa ser provisionado em um diretório. Esses aplicativos geralmente são registrados por um desenvolvedor na organização.


- Aplicativo de vários locatário: um aplicativo de vários locatário destina para uso em muitas organizações, não apenas uma organização. Estes são normalmente software-como um serviço (SaaS) aplicativos escritos por um software independentes de distribuidores. Aplicativos de vários locatários precisam ser provisionados em cada diretório onde eles serão usados, que requer o usuário ou administrador consentimento para registrá-los. Esse processo de consentimento inicia quando um aplicativo tiver sido registrado no diretório e é fornecido acesso a API do gráfico ou talvez outra web API. Quando um usuário ou administrador de uma organização diferente se inscreve usar o aplicativo, eles são apresentados com uma caixa de diálogo que exibe as permissões que o aplicativo requer. O usuário ou o administrador pode, em seguida, consentimento para o aplicativo, que fornece o acesso do aplicativo aos dados informados e finalmente registra o aplicativo em seu diretório. Para obter mais informações, consulte [Visão geral do Framework consentimento](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Algumas considerações adicionais surgem ao desenvolver um aplicativo de vários locatário em vez de um aplicativo de locatário único. Por exemplo, se você estiver criando seu aplicativo disponível para usuários em vários diretórios, você precisa de um mecanismo para determinar quais locatário onde estejam. Um aplicativo de locatário único só precisa aparência em seu próprio diretório de um usuário, enquanto um aplicativo de vários locatário precisa para identificar um usuário específico de todas as pastas no Azure AD. Para realizar esta tarefa, o Azure AD fornece um ponto de extremidade de autenticação comuns onde qualquer aplicativo do locatário vários pode direcionar solicitações de entrada, em vez de um ponto de extremidade do locatário específicas. Neste ponto de extremidade é https://login.microsoftonline.com/common para todas as pastas no Azure AD, enquanto um ponto de extremidade do locatário específicas pode ser https://login.microsoftonline.com/contoso.onmicrosoft.com. O ponto de extremidade comum é especialmente importante levar em consideração ao desenvolver seu aplicativo, pois você precisará a lógica necessária para lidar com vários locatários durante entrar, saída e a validação de token.

Se você atualmente está desenvolvendo um aplicativo de locatário único, mas deseja disponibilizar para muitas organizações, você pode facilmente fazer alterações ao aplicativo e sua configuração no Azure AD para torná-lo vários locatários capaz. Além disso, o Azure AD usa a mesma chave de assinatura para todos os símbolos de todas as pastas, se você está fornecendo autenticação em um aplicativo de vários locatário ou Locatário único.

Cada cenário listado neste documento inclui uma subseção que descreva seus requisitos de provisionamento. Para obter informações mais detalhadas sobre um aplicativo no Azure AD e as diferenças entre aplicativos único e vários locatários de provisionamento, consulte [Aplicativos de integração com o Active Directory do Azure](active-directory-integrating-applications.md) para obter mais informações. Continue lendo para entender os cenários comuns do aplicativo no Azure AD.

## <a name="application-types-and-scenarios"></a>Cenários e tipos de aplicativo

Cada um dos cenários descritos neste documento pode ser desenvolvida usando vários idiomas e plataformas. Eles são copiados por exemplos de código completo que estão disponíveis no nosso [guia de exemplos de código](active-directory-code-samples.md), ou diretamente do correspondente [Github repositórios de amostra](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory). Além disso, se seu aplicativo precisa uma parte específica ou segmento de um cenário de ponta a ponta, na maioria dos casos essa funcionalidade pode ser adicionada independentemente. Por exemplo, se você tiver um aplicativo nativo que chama um web API, você pode adicionar um aplicativo web que também chama web API facilmente. O diagrama a seguir ilustra esses cenários e tipos de aplicativos, e como os diferentes componentes podem ser adicionados:

![Tipos de aplicativos e cenários](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Estes são os cinco cenários de aplicativo principal suportados pelo Azure AD:

- [Navegador da Web para o aplicativo da Web](#web-browser-to-web-application): um usuário precisa entrar para um aplicativo web que é protegido por Azure AD.

- [Aplicativo de página única (SPA)](#single-page-application-spa): um usuário precisa entrar em um única página aplicativo protegido pelo Azure AD.

- [Aplicativo nativo para API da Web](#native-application-to-web-api): um aplicativo nativo que é executado em um telefone, tablet ou PC precisa autenticar um usuário para acessar recursos de uma web API protegido pelo Azure AD.

- [Aplicativo da Web para API da Web](#web-application-to-web-api): um aplicativo web precisa obter recursos de uma web API protegido por Azure AD.

- [Daemon ou aplicativo de servidor Web API](#daemon-or-server-application-to-web-api): um aplicativo daemon ou um aplicativo de servidor com nenhuma interface de usuário da web precisa obter recursos de uma web API protegido por Azure AD.

### <a name="web-browser-to-web-application"></a>Navegador da Web para o aplicativo Web

Esta seção descreve um aplicativo que faz a autenticação de um usuário em um navegador da web em um aplicativo web. Neste cenário, o aplicativo web direciona o navegador do usuário para entrá-los no Azure AD. Azure AD retorna uma resposta entrar pelo navegador do usuário, que contém declarações sobre o usuário em um token de segurança. Este cenário é compatível com logon usando os protocolos de Web Services Federation, SAML 2.0 e OpenID conectar.


#### <a name="diagram"></a>Diagrama
![Fluxo de autenticação de navegador para o aplicativo web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)


#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo


1. Quando um usuário visita o aplicativo e precisa entrar, eles serão redirecionados por meio de uma solicitação de entrada para o ponto de extremidade de autenticação no Azure AD.


2. O usuário entra na página de entrada.


3. Se a autenticação for bem-sucedida, o Azure AD cria um token de autenticação e retorna uma resposta entrar a URL de resposta do aplicativo que foi configurado no Portal de gerenciamento do Azure. Para um aplicativo de produção, esta URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD que são necessárias pelo aplicativo para validar o token.


4. O aplicativo valida o token usando uma assinatura de chave pública e informações de emissor disponíveis o documento de metadados de Federação do Azure AD. Quando o aplicativo valida o token, o Azure AD inicia uma nova sessão com o usuário. Esta sessão permite ao usuário acessar o aplicativo até que ela expire.


#### <a name="code-samples"></a>Exemplos de código


Consulte as amostras de código para navegador da Web para cenários de aplicativo Web. E, verifique com frequência – adicionamos novos exemplos o tempo todo. [Navegador da web para o aplicativo da Web](active-directory-code-samples.md#web-browser-to-web-application).


#### <a name="registering"></a>Registrando


- Locatário único: Se você estiver criando um aplicativo para a sua organização, ele deve ser registrado no diretório da sua empresa usando o Portal de gerenciamento do Azure.


- Vários locatários: Se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deve ser registrado no diretório da empresa, mas também deve ser registrado no diretório de cada organização que usarão o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para os clientes que permita consentimento para seu aplicativo. Quando eles se inscrever para seu aplicativo, ele serão apresentados com uma caixa de diálogo que mostra as permissões que o aplicativo requer e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador da organização outros pode ser necessário dar consentimento. Quando o usuário ou administrador dê o seu consentimento, o aplicativo é registrado no seu diretório. Para obter mais informações, consulte [Aplicativos de integração com o Active Directory do Azure](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiração do token

A sessão do usuário expira quando o ciclo de vida do token emitido por Azure AD expira. Seu aplicativo pode encurtar esse período de tempo se desejado, como sair usuários com base em um período de inatividade. Quando a sessão expira, o usuário será solicitado a entrar novamente.





### <a name="single-page-application-spa"></a>Aplicativo de página única (SPA)

Esta seção descreve autenticação para um único aplicativo de página, que usa Azure AD e a autorização implícita OAuth 2.0 concedem para proteger sua web API volta encerrar. Aplicativos de página única geralmente são estruturados como uma camada de apresentação de JavaScript (front-end) que é executado no navegador e um back-end de API da Web que é executado em um servidor e implementa lógica de negócios do aplicativo. Para saber mais sobre a autorização implícita grant e ajuda você a decidir se é adequado para seu cenário de aplicativo, consulte [Noções básicas sobre o fluxo de conceder implícito OAuth2 no Active Directory do Azure](active-directory-dev-understanding-oauth2-implicit-grant.md).

Neste cenário, quando o usuário entra no, o JavaScript front-end usa [biblioteca de autenticação do Active Directory para JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) e a concessão de autorização implícito para obter um token de ID (id_token) do Azure AD. O token é armazenada em cache e o cliente anexa-lo à solicitação de token de portador ao fazer chamadas para sua API Web back-end, que é protegida usando o middleware OWIN. 
#### <a name="diagram"></a>Diagrama

![Diagrama de aplicativo de página único](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo

1. O usuário navega para o aplicativo da web.


2. O aplicativo retornará o front-end JavaScript (camada de apresentação) para o navegador.


3. O usuário inicia entrar, por exemplo, clicando em uma link de entrada. O navegador envia um GET para o ponto de extremidade de autorização Azure AD para solicitar um token de ID. A solicitação inclui a URL de ID e a resposta do cliente nos parâmetros de consulta.


4. Azure AD valida a URL de resposta contra o URL responder registrado que foi configurado no Portal de gerenciamento do Azure.


5. O usuário entra na página de entrada.


6. Se a autenticação for bem-sucedida, o Azure AD cria um token de ID e o retorna como um fragmento de URL (#) para URL de resposta do aplicativo. Para um aplicativo de produção, esta URL de resposta deve ser HTTPS. O token retornado inclui declarações sobre o usuário e o Azure AD que são necessárias pelo aplicativo para validar o token.


7. O código do cliente JavaScript executado no navegador extrai o token da resposta para usar em Protegendo chamadas para web do aplicativo que encerrar API novamente.


8. Chamadas de navegador da web do aplicativo API volta termina com o token de acesso no cabeçalho de autorização.

#### <a name="code-samples"></a>Exemplos de código


Consulte os exemplos de código para cenários de aplicativo de página única (SPA). Certifique-se de que, verifique com frequência, que podemos adicionar novos exemplos o tempo todo. [Aplicativo de página única (SPA)](active-directory-code-samples.md#single-page-application-spa).


#### <a name="registering"></a>Registrando


- Locatário único: Se você estiver criando um aplicativo para a sua organização, ele deve ser registrado no diretório da sua empresa usando o Portal de gerenciamento do Azure.


- Vários locatários: Se você estiver criando um aplicativo que pode ser usado por usuários fora da sua organização, ele deve ser registrado no diretório da empresa, mas também deve ser registrado no diretório de cada organização que usarão o aplicativo. Para disponibilizar seu aplicativo em seu diretório, você pode incluir um processo de inscrição para os clientes que permita consentimento para seu aplicativo. Quando eles se inscrever para seu aplicativo, ele serão apresentados com uma caixa de diálogo que mostra as permissões que o aplicativo requer e, em seguida, a opção de consentimento. Dependendo das permissões necessárias, um administrador da organização outros pode ser necessário dar consentimento. Quando o usuário ou administrador dê o seu consentimento, o aplicativo é registrado no seu diretório. Para obter mais informações, consulte [Aplicativos de integração com o Active Directory do Azure](active-directory-integrating-applications.md).

Depois de registrar o aplicativo, ele deverá ser configurado para usar o protocolo de OAuth 2.0 implícito conceder. Por padrão, este protocolo está desabilitado para aplicativos. Para habilitar o protocolo OAuth2 implícito conceder para seu aplicativo, baixe seu manifesto de aplicativo do Portal de gerenciamento do Azure, defina o valor de "oauth2AllowImplicitFlow" como verdadeiro e carregue o manifesto voltar ao portal. Para obter instruções detalhadas, consulte [Habilitando OAuth 2.0 implícito conceder para aplicativos de página única](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiração do token

Quando você usa ADAL.js para gerenciar a autenticação com o Azure AD, você se beneficiar vários recursos que facilitam atualizando um token expirado, além de obter tokens para recursos de API do web adicionais que podem ser chamados pelo aplicativo. Quando o usuário autenticado com êxito com o Azure AD, é estabelecida uma sessão protegida por um cookie do usuário entre o navegador e o Azure AD. É importante observar que a sessão existe entre o usuário e o Azure AD e não entre o usuário e o aplicativo da web executado no servidor. Quando um token expira, ADAL.js usa esta sessão silenciosamente obter outro token. Ela faz isso usando um iFrame oculto para enviar e receber a solicitação usando o protocolo de conceder implícito OAuth. ADAL.js também pode usar o mesmo mecanismo silenciosamente obter tokens de acesso do Azure AD para outra web recursos de API que o aplicativo chama contanto que esses recursos de suporte (CORS), o compartilhamento de recursos entre origens é registrado no diretório do usuário, e qualquer consentimento obrigatório foi fornecido pelo usuário durante a entrada.


### <a name="native-application-to-web-api"></a>Aplicativo nativo Web API


Esta seção descreve um aplicativo nativo que chama um web API em nome de um usuário. Este cenário se baseia no tipo de OAuth 2.0 autorização código concessão com um cliente público, conforme descrito na seção 4.1 da [especificação OAuth 2.0](http://tools.ietf.org/html/rfc6749). O aplicativo nativo obtém um token de acesso do usuário usando o protocolo OAuth 2.0. Esse token de acesso é enviado na solicitação na Web API, que autoriza o usuário e retorna o recurso desejado.

#### <a name="diagram"></a>Diagrama

![Aplicativo nativo para Web API do diagrama](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Fluxo de autenticação de aplicativo nativo para API

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo


Se você estiver usando as bibliotecas de autenticação do AD, mais os detalhes do protocolo descritos a seguir são tratados para você, como o pop-up do navegador, o cache de token e manipulação de tokens de atualização.

1. Usando um navegador pop-up, que o aplicativo nativo faz uma solicitação para o ponto de extremidade de autorização no Azure AD. Esta solicitação inclui a ID do cliente e o redirecionamento URI do aplicativo nativo, conforme mostrado no Portal de gerenciamento e a ID URI do aplicativo para a web API. Se o usuário já não foi conectado, ele será solicitado a entrar novamente


2. Azure AD autentica o usuário. Se ele é um aplicativo de vários locatário e consentimento é necessário para usar o aplicativo, o usuário precisará consentimento se eles ainda não fez isso. Depois de conceder consentimento e mediante autenticação bem-sucedida, o Azure AD emite uma resposta de código de autorização ao URI de redirecionamento do aplicativo cliente.


3. Quando Azure AD emite uma resposta de código de autorização para o redirecionamento URI, o aplicativo cliente interrompe interação de navegador e extrai o código de autorização de resposta. Usando este código de autorização, o aplicativo cliente envia uma solicitação para empresa de token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do cliente e redirecionar URI) e o recurso desejado (aplicativo URI de identificação para a web API).


4. O código de autorização e informações sobre a API de aplicativos e da web do cliente são validados por Azure AD. Após a validação for bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT. Além disso, o Azure AD retorna informações básicas sobre o usuário, como sua ID de nome e locatário de exibição


5. Em HTTPS, o aplicativo cliente usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho da solicitação de autorização na Web API. Web API, em seguida, valida o token JWT e se a validação for bem-sucedida, retornará o recurso desejado.


6. Quando o token de acesso expira, o aplicativo cliente receberá um erro que indica que o usuário precisa autenticar novamente. Se o aplicativo tem um token de atualização válido, ele pode ser usado para adquirir um novo token de acesso sem avisar o usuário entrar novamente. Se o token de atualização expirar, o aplicativo precisará interativamente autenticar o usuário novamente.


> [AZURE.NOTE] O token de atualização emitido por Azure AD pode ser utilizado para acessar vários recursos. Por exemplo, se você tiver um aplicativo cliente que tenha permissão para chamar web duas APIs, o token de atualização pode ser usado para obter um acesso token na outra Web API também.


#### <a name="code-samples"></a>Exemplos de código


Consulte as amostras de código para o aplicativo nativo para cenários de API da Web. E, verifique com frequência – adicionamos novos exemplos o tempo todo. [Aplicativo nativo Web API](active-directory-code-samples.md#native-application-to-web-api).


#### <a name="registering"></a>Registrando


- Locatário único: A nativas aplicativo e web API devem ser registrados no mesmo diretório no Azure AD. Web API pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo nativo a seus recursos. O aplicativo de cliente, em seguida, seleciona as permissões desejadas no menu suspenso de "Permissões para outros aplicativos" no Portal de gerenciamento do Azure.


- Vários locatários: Primeiro, o aplicativo nativo só registrado o desenvolvedor ou pasta do Editor. Segundo, o aplicativo nativo é configurado para indicar as permissões requer ser funcional. Esta lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino fornece consentimento para o aplicativo, o que torna mais disponíveis para sua organização. Alguns aplicativos exigem permissões de nível de usuário apenas, que qualquer usuário na organização pode consentimento para. Outros aplicativos requerem permissões de nível de administrador, que um usuário na organização não consentimento para. Somente um administrador de diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador dê o seu consentimento, apenas web API está registrada no seu diretório. Para obter mais informações, consulte [Aplicativos de integração com o Active Directory do Azure](active-directory-integrating-applications.md).


#### <a name="token-expiration"></a>Expiração do token


Quando o aplicativo nativo usa seu código de autorização para obter acesso a um JWT token, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar novamente o usuário sem exigir que eles entrar novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e o token de atualização.





### <a name="web-application-to-web-api"></a>Aplicativo Web Web API


Esta seção descreve um aplicativo web que precisa obter recursos de uma web API. Nesse cenário, há dois tipos de identidade que o aplicativo da web pode usar para autenticar e chamar web API: uma identidade de aplicativo, ou uma identidade de usuário delegado.

*Identidade de aplicativo:* Esse cenário usa a concessão de credenciais de cliente OAuth 2.0 para autenticar como o aplicativo e acessar a web API. Ao usar uma identidade de aplicativo, web API pode detectar somente se o aplicativo da web está chamando, como a web API não recebe qualquer informação sobre o usuário. Se o aplicativo recebe informações sobre o usuário, ela será enviada através do protocolo de aplicativo e ele não está assinado pelo Azure AD. Web API confia que o aplicativo da web do usuário autenticado. Por esse motivo, esse padrão é chamado um subsistema confiável.

*Delegada identidade de usuário:* Este cenário pode ser feito de duas maneiras: OpenID conecte e concessão de código de autorização de OAuth 2.0 com um cliente confidencial. O aplicativo web obtém um token de acesso do usuário, o que prova na Web API que o usuário autenticado com êxito para o aplicativo da web e que o aplicativo da web foi capaz de obter uma identidade de usuário delegado para chamar a API da web. Esse token de acesso é enviado na solicitação na Web API, que autoriza o usuário e retorna o recurso desejado.

#### <a name="diagram"></a>Diagrama

![Aplicativo da Web para diagrama de API da Web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)



#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo

A identidade do aplicativo e tipos de identidade de usuário delegado são discutidos no fluxo de abaixo. A principal diferença entre elas é que a identidade do usuário delegado deve primeiro adquirir um código de autorização antes que o usuário pode entrar e obter acesso à web API.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Conceder as credenciais de identidade de aplicativo com cliente OAuth 2.0

1. Um usuário está conectado ao Azure AD no aplicativo da web (consulte o [Navegador da Web para o aplicativo Web](#web-browser-to-web-application) acima).


2. O aplicativo web precisa adquirir um token de acesso para que ele possa autenticar na Web API e recuperar o recurso desejado. Ela faz uma solicitação para empresa de token do Azure AD, fornecendo a credencial, ID do cliente e URI de ID do aplicativo web da API.


3. Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da web.


4. Em HTTPS, o aplicativo da web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho da solicitação de autorização na Web API. Web API, em seguida, valida o token JWT e se a validação for bem-sucedida, retornará o recurso desejado.

##### <a name="delegated-user-identity-with-openid-connect"></a>Conectar-se de identidade de usuário delegado com OpenID

1. Um usuário estiver conectado a um aplicativo web usando o Azure AD (consulte a seção de [Navegador da Web para o aplicativo Web](#web-browser-to-web-application) acima). Se o usuário do aplicativo da web não ainda aceitou permitindo que o aplicativo da web chamar a API da web em seu nome, o usuário precisará consentimento. O aplicativo exibirá as permissões requer e se qualquer uma dessas permissões de administrador, um usuário normal no diretório não poderão consentimento. Esse processo de consentimento se aplica somente locatários vários aplicativos, não único locatário, como o aplicativo já terá as permissões necessárias. Quando o usuário conectado, o aplicativo web recebeu um token de ID com informações sobre o usuário, bem como um código de autorização.


2. O aplicativo web usando o código de autorização emitido por Azure AD, envia uma solicitação para empresa de token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do cliente e redirecionar URI) e o recurso desejado (aplicativo URI de identificação para a web API).


3. O código de autorização e informações sobre o aplicativo web e web API são validados por Azure AD. Após a validação for bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT.


4. Em HTTPS, o aplicativo da web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho da solicitação de autorização na Web API. Web API, em seguida, valida o token JWT e se a validação for bem-sucedida, retornará o recurso desejado.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identidade de usuário delegado com concessão de código de autorização de OAuth 2.0

1. Um usuário já estiver conectado a um aplicativo web, cujo mecanismo de autenticação é independente do Azure AD.


2. O aplicativo web requer um código de autorização para adquirir um token de acesso, para que ele emite uma solicitação através do navegador para o ponto de extremidade de autorização do Azure AD, fornecendo a ID do cliente e redirecionar URI para o aplicativo web após a autenticação bem-sucedida. O usuário entra no Azure AD.


3. Se o usuário do aplicativo da web não ainda aceitou permitindo que o aplicativo da web chamar a API da web em seu nome, o usuário precisará consentimento. O aplicativo exibirá as permissões requer e se qualquer uma dessas permissões de administrador, um usuário normal no diretório não poderão consentimento. Esse processo de consentimento se aplica somente locatários vários aplicativos, não único locatário, como o aplicativo já terá as permissões necessárias.


4. Depois que o usuário aceitou, o aplicativo web recebe o código de autorização que ele precisa adquirir um token de acesso.


5. O aplicativo web usando o código de autorização emitido por Azure AD, envia uma solicitação para empresa de token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do cliente e redirecionar URI) e o recurso desejado (aplicativo URI de identificação para a web API).


6. O código de autorização e informações sobre o aplicativo web e web API são validados por Azure AD. Após a validação for bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT.


7. Em HTTPS, o aplicativo da web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho da solicitação de autorização na Web API. Web API, em seguida, valida o token JWT e se a validação for bem-sucedida, retornará o recurso desejado.

#### <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para o aplicativo da Web para cenários de API da Web. E, verifique com frequência – adicionamos novos exemplos o tempo todo. [Aplicativo Web API](active-directory-code-samples.md#web-application-to-web-api)do Web.


#### <a name="registering"></a>Registrando

- Locatário único: Para a identidade do aplicativo e casos de identidade de usuário delegado, o aplicativo da web e a API web devem ser registrados no mesmo diretório no Azure AD. Web API pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo da web a seus recursos. Se um tipo de identidade de usuário delegado estiver sendo usado, o aplicativo web precisa selecione as permissões desejadas no menu suspenso "Permissões para outros aplicativos" no Portal de gerenciamento do Azure. Esta etapa não é necessária se o tipo de identidade de aplicativo estiver sendo usado.


- Vários locatários: Primeiro, o aplicativo da web está configurado para indicar as permissões requer ser funcional. Esta lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino fornece consentimento para o aplicativo, o que torna mais disponíveis para sua organização. Alguns aplicativos exigem permissões de nível de usuário apenas, que qualquer usuário na organização pode consentimento para. Outros aplicativos requerem permissões de nível de administrador, que um usuário na organização não consentimento para. Somente um administrador de diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador dê o seu consentimento, o aplicativo web e a API web são ambos registradas no seu diretório.

#### <a name="token-expiration"></a>Expiração do token

Quando o aplicativo web usa seu código de autorização para obter acesso a um JWT token, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar novamente o usuário sem exigir que eles entrar novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e o token de atualização.


### <a name="daemon-or-server-application-to-web-api"></a>Daemon ou aplicativo de servidor Web API


Esta seção descreve um aplicativo daemon ou servidor que precisa obter recursos de uma web API. Há dois cenários sub que se aplicam a esta seção: um daemon que precisa chamar uma web API, criado em tipo de conceder as credenciais do cliente OAuth 2.0; e um aplicativo de servidor (como um web API) que precisa chamar uma API, criado na especificação de rascunho OAuth 2.0 On-Behalf-Of web.

Para o cenário quando um aplicativo daemon precisa chamar uma web API, é importante entender algumas coisas. Primeiro, a interação do usuário não é possível com um aplicativo daemon, que exige o aplicativo para que sua própria identidade. Um exemplo de um aplicativo daemon é um trabalho em lotes ou um serviço de sistema operacional executado em segundo plano. Esse tipo de aplicativo solicita um token de acesso usando sua identidade de aplicativo e apresentar seu cliente ID, credencial (senha ou certificado) e aplicativo URI de ID do Azure AD. Após a autenticação bem-sucedida, o daemon recebe um token de acesso do Azure AD, que é usado para chamar a API da web.

Para o cenário quando um aplicativo de servidor precisa chamar uma web API, é útil usar um exemplo. Imagine que um usuário autenticado em um aplicativo nativo, e este aplicativo nativo precisa chamar uma web API. Azure AD emite um token de acesso JWT para chamar a API da web. Se a API web precisa chamar outra web downstream API, ele pode usar o fluxo em nome de delegar a identidade do usuário e autenticar na Web de segundo nível API.

#### <a name="diagram"></a>Diagrama

![Daemon ou aplicativo de servidor ao diagrama de API da Web](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrição do fluxo de protocolo

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Conceder as credenciais de identidade de aplicativo com cliente OAuth 2.0

1. Primeiro, o aplicativo do servidor precisa autenticar com Azure AD como ele mesmo, sem qualquer interação humana como uma caixa de diálogo de logon interativa. Ela faz uma solicitação para empresa de token do Azure AD, fornecendo a credencial, ID do cliente e URI de ID do aplicativo.


2. Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da web.


3. Em HTTPS, o aplicativo da web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho da solicitação de autorização na Web API. Web API, em seguida, valida o token JWT e se a validação for bem-sucedida, retornará o recurso desejado.


##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identidade de usuário delegado com OAuth 2.0 em nome de rascunho especificação

O fluxo discutido abaixo pressupõe que um usuário foi autenticado em outro aplicativo (como um aplicativo nativo), e sua identidade de usuário foi usada para adquirir um token de acesso para o API da web de primeiro nível.

1. O aplicativo nativo envia o token de acesso para o API da web de primeiro nível.


2. A API da web de primeiro nível envia uma solicitação para o ponto de extremidade token do Azure AD, fornecendo seu cliente ID e credenciais, bem como token de acesso do usuário. Além disso, a solicitação é enviada com uma on_behalf_of parâmetro que indica a web API está solicitando novos tokens para chamar um web downstream API em nome de usuário original.


3. Azure AD verifica se a web de primeiro nível API tem permissões para acessar a web de segundo nível API e valida a solicitação, retornando um token de acesso JWT e uma JWT token na Web de primeiro nível API de atualização.


4. Em HTTPS, a API da web de primeiro nível, em seguida, chamadas de segundo nível web API anexando a cadeia de caracteres de token no cabeçalho da autorização na solicitação. A web de primeiro nível API pode continuar a chamada web segundo nível API desde o token de acesso e tokens de atualização são válidos.

#### <a name="code-samples"></a>Exemplos de código

Consulte as amostras de código para Daemon ou aplicativo de servidor para cenários de API da Web. E, verifique com frequência – adicionamos novos exemplos o tempo todo. [Servidor ou Daemon aplicativo Web API](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Registrando

- Locatário único: Para a identidade do aplicativo e casos de identidade de usuário delegado, o aplicativo daemon ou o servidor deve ser registrado no mesmo diretório no Azure AD. Web API pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o daemon ou o acesso do servidor aos seus recursos. Se um tipo de identidade de usuário delegado estiver sendo usado, o aplicativo do servidor precisa selecione as permissões desejadas no menu suspenso "Permissões para outros aplicativos" no Portal de gerenciamento do Azure. Esta etapa não é necessária se o tipo de identidade de aplicativo estiver sendo usado.


- Vários locatários: Primeiro, o aplicativo daemon ou servidor está configurado para indicar as permissões requer ser funcional. Esta lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino fornece consentimento para o aplicativo, o que torna mais disponíveis para sua organização. Alguns aplicativos exigem permissões de nível de usuário apenas, que qualquer usuário na organização pode consentimento para. Outros aplicativos requerem permissões de nível de administrador, que um usuário na organização não consentimento para. Somente um administrador de diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador dê o seu consentimento, ambos da web APIs registrado no seu diretório.

#### <a name="token-expiration"></a>Expiração do token

Quando o primeiro aplicativo usa seu código de autorização para obter acesso a um JWT token, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar novamente o usuário sem pedir credenciais. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e o token de atualização.

## <a name="see-also"></a>Consulte também

[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Exemplos de código do Active Directory do Azure](active-directory-code-samples.md)

[Informações importantes sobre a assinatura de chave sobreposição no Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)
