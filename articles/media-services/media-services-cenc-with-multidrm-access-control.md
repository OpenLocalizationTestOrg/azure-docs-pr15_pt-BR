<properties 
    pageTitle="CENC com Multi-DRM e controle de acesso: uma implementação no Azure e Azure e Design de referência de serviços de mídia | Microsoft Azure" 
    description="Saiba mais sobre como o Microsoft® suave Streaming cliente portabilidade Kit de licenciamento." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>

#<a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC com Multi-DRM e controle de acesso: uma implementação no Azure e Azure e Design de referência de serviços de mídia

##<a name="key-words"></a>Palavras-chave
 
Azure Active Directory, serviços de mídia do Azure, Azure Media Player, criptografia dinâmico, licença entrega, PlayReady, Widevine, FairPlay, Encryption(CENC) comum, Multi-DRM, Axinom, traço, EME, MSE, JSON Web Token (JWT), declarações, navegadores modernos, sobreposição de chave, chave simétrica, chave assimétricos, OpenID conectar, X509 certificado. 

##<a name="in-this-article"></a>Neste artigo

Os tópicos a seguir são descritos neste artigo:

- [Introdução](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [Visão geral deste artigo](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Um design de referência](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Design de mapeamento para tecnologia de implementação](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Implementação](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [Procedimentos de implementação](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [Alguns truques em implementação](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Tópicos adicionais para implementação](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP ou HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [Active Directory do Azure sobreposição chave de assinatura](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [Onde está o Token de acesso?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [E quanto ao vivo Streaming?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [O que são servidores de licença fora serviços de mídia do Azure?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [O que acontece se eu quiser usar um STS personalizado?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [O sistema concluído e teste](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [Logon do usuário](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [Usando as extensões de mídia criptografada para PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [Usando EME para Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [Usuários não qualificados](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [Executando o serviço de Token seguro personalizado](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Resumo](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>Introdução

É conhecido que é uma tarefa complexa projetar e criar um subsistema DRM para um OTT ou online streaming solução. E é uma prática comum dos operadores/online provedores vídeo terceirizados essa parte especializados provedores de serviços DRM. O objetivo deste documento é apresentar um design de referência e a implementação do subsistema DRM de ponta a ponta em OTT ou solução streaming on-line.

Os leitores alvo deste documento são engenheiros trabalhando no subsistema DRM de OTT ou soluções on-line streaming/multi-screen ou qualquer leitores interessados no subsistema DRM. Pressupõe-se que os leitores esteja familiarizados com pelo menos uma das tecnologias DRM no mercado, como PlayReady, Widevine, FairPlay ou Adobe acesso.

Por DRM, podemos também incluem CENC (criptografia comuns) com multi-DRM. Uma tendência principal no setor OTT e streaming online é usar CENC com multi-native-DRM em várias plataformas de cliente, que é uma mudança da tendência anterior da usando um único DRM e seu cliente SDK para várias plataformas de cliente. Ao usar CENC com vários native DRM, PlayReady e Widevine são criptografados pela especificação de [Criptografia comum (CENC ISO/IEC 23001-7)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

As vantagens de CENC com multi-DRM são da seguinte maneira:

1. Reduz criptografia custa como um processamento de criptografia única é usado direcionado para plataformas diferentes com seus DRMs nativas;
1. Reduz o custo do gerenciamento de ativos criptografados desde que apenas uma única cópia de ativos criptografadas é necessária;
1. Elimina DRM de licenciamento de cliente custo desde o native client DRM é geralmente gratuito na sua plataforma nativa.

Microsoft foi um Promotores ativo de traço e CENC junto com alguns players principais setores. Serviços de mídia do Microsoft Azure vem fornecendo suporte do traço e CENC. Para anúncios recentes, consulte blogs do Mingfei: [Serviços de entrega de licença anunciando Google Widevine nos serviços de mídia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)e [Serviços de mídia do Azure adiciona embalagem do Google Widevine para fornecer o fluxo de multi-DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Visão geral deste artigo

O objetivo deste artigo inclui o seguinte:

1. Fornece um design de referência de subsistema DRM usando CENC com multi-DRM;
1. Fornece uma implementação de referência na plataforma de serviços de mídia do Microsoft Azure/Azure;
1. Discute alguns tópicos de design e implementação.

O artigo, "multi-DRM" discute o seguinte:

1. Microsoft PlayReady
1. Widevine do Google
1. Apple FairPlay (ainda não é suportado pelos serviços de mídia do Azure)

A tabela a seguir resume o aplicativo nativo plataforma/nativo e navegadores compatíveis com cada DRM.

**Plataforma de cliente**|**Suporte DRM nativo**|**Navegador/aplicativo**|**Formatos de streaming**
----|------|----|----
**TVs inteligentes, o operador STBs, OTT STBs**|PlayReady principalmente, e/ou Widevine e/ou outros|Linux, Opera, WebKit, outros|Vários formatos
**Dispositivos Windows 10 (PC com Windows, Tablets com Windows, Windows Phone, Xbox)**|PlayReady|MS IE11/borda/EME<br/><br/><br/>UWP|TRAÇO (para HLS, PlayReady não é suportado)<br/><br/>TRAÇO, Streaming suave (para HLS, PlayReady não é suportado) 
**Dispositivos Android (telefone, Tablet, TV)**|Widevine|Chrome/EME|TRAÇO
**iOS (iPhone, iPad), clientes de OS X e TV da Apple**|FairPlay|Safari 8 + / EME|HLS
**Plug-in: Adobe faltava**|Acesso de faltava|Plug-in do navegador|HDS, HLS

Considerando o estado atual de implantação para cada DRM, um serviço normalmente desejará implementar DRMs 2 ou 3 para garantir que todos os tipos de pontos de extremidade de endereço da melhor maneira.

Não há uma relação entre a complexidade da lógica de serviço e a complexidade no lado do cliente alcançar um determinado nível de experiência do usuário em vários clientes.

Para tornar sua seleção, tenha em mente esses fatos:

- PlayReady nativamente é implementado em cada dispositivo com Windows, em alguns dispositivos Android e disponível por meio de software SDKs em praticamente qualquer plataforma
- Widevine nativamente implementado em cada dispositivo Android, no Chrome e em alguns outros dispositivos
- FairPlay está disponível somente no iOS e clientes do Mac OS ou através do iTunes.

Portanto, um típico multi-DRM seria:

- Opção 1: PlayReady e Widevine
- Opção 2: PlayReady, Widevine e FairPlay


## <a name="a-reference-design"></a>Um design de referência

Nesta seção, apresentaremos um design de referência que é independente para tecnologias usadas para implementar a ele.

Um subsistema DRM pode conter os seguintes componentes:

1. Gerenciamento de chaves
1. Embalagem de DRM
1. Entrega de licença do DRM
1. Verificação de qualificação
1. Autenticação/autorização
1. Player
1. Origem/CDN

O diagrama a seguir ilustra a interação de alto nível entre os componentes em um subsistema DRM.

![Subsistema DRM com CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Há três básicas "camadas" no design:

1. Camada de back-office (em preto), que não estão expostos externamente;
1. Camada de "DMZ" (azul) que contém todos os pontos de extremidade opostas público;
1. Camada da Internet pública (azul claro) contendo CDN e players com tráfego de Internet pública.
 
Deve haver uma ferramenta de gerenciamento de conteúdo para controlar a proteção de DRM, independentemente é criptografia estática ou dinâmica. As entradas para criptografia de DRM devem incluir:

1. Conteúdo de vídeo MBR;
1. Chave de conteúdo;
1. URLs de aquisição de licença.

Durante o tempo de reprodução, o fluxo de alto nível é:

1. Usuário é autenticado;
1. Token de autorização é criado para o usuário;
1. Conteúdo protegido por DRM (manifesto) é baixado para o player;
1. Player envia a solicitação de aquisição de licença para os servidores de licença junto com chave ID e a autorização token.

Antes de passar para o próximo tópico, algumas palavras sobre o projeto de gerenciamento de chaves.

**ContentKey – para ativos**|**Cenário**
------|---------------------------
1-para-1|Caso mais simples. Ele fornece o controle mais refinado. Mas isso geralmente resulta no maior custo de entrega de licença. Em uma licença mínima solicitação é necessária para cada ativo protegido.
1-para-muitos|Você pode usar a mesma chave de conteúdo para vários ativos. Por exemplo, para todos os ativos em um grupo lógico como um gênero ou um subconjunto de gênero (ou Gene de filme), você poderá usar uma única chave de conteúdo.
Muitos-para-1|Várias chaves de conteúdo são necessários para cada ativo. <br/><br/>Por exemplo, se você precisar aplicar proteção de CENC dinâmica com multi-DRM para MPEG-traço e criptografia AES de 128 dinâmico para HLS, será necessário duas separadas conteúdas chaves, cada uma com seu próprio ContentKeyType. (Para a chave de conteúdo usada para proteção de CENC dinâmica, ContentKeyType.CommonEncryption deve ser usado, enquanto para a chave de conteúdo usada para criptografia AES de 128 dinâmico, ContentKeyType.EnvelopeEncryption deve ser usada.)<br/><br/>Outro exemplo, em proteção de CENC de conteúdo de traço, teoricamente, uma chave de conteúdo pode ser usado para proteger o fluxo de vídeo e outra chave de conteúdo para proteger o fluxo de áudio. 
Muitos-para - muitos|Combinação dos dois cenários acima: um conjunto de chaves de conteúdo são usadas para cada um dos vários ativos no mesmo ativo "grupo".


Outro fator importante considerar é o uso de licenças persistentes e não persistentes.

Por que essas considerações são importantes? 

Eles têm impacto direto custo de entrega de licença se você usar nuvem pública para entrega de licença. Vamos considerar os seguintes dois casos de design diferente para ilustrar:



1. Assinatura mensal: usar licença persistente e mapeamento de chave para ativos de conteúdo de 1-para-muitos. Por exemplo para todas as crianças filmes, usamos uma única chave de conteúdo para criptografia. Neste caso: 

    Total de licenças de # solicitada para todas as crianças filmes/dispositivo = 1

1. Assinatura mensal: usar licença não persistente e mapeamento de 1-para-1 entre ativos e de chave de conteúdo. Neste caso:

    Total de licenças de # solicitada para todas as crianças filmes/dispositivo = [filmes # observadas] x [sessões #]

Como você pode ver facilmente, os dois diferentes designs resultarem em padrões de solicitação de licença muito diferente, portanto, se o serviço de entrega de licença é fornecido por uma nuvem pública como os serviços de mídia do Azure de custo de entrega de licença.

## <a name="mapping-design-to-technology-for-implementation"></a>Design de mapeamento para tecnologia de implementação

Em seguida, podemos mapear nosso design genérico para tecnologias na plataforma de serviços de mídia do Microsoft Azure/Azure, especificando qual tecnologia usar para cada bloco de construção.

A tabela a seguir mostra o mapeamento:

**Bloco de construção**|**Tecnologia**
------|-------
**Player**|[Player de mídia do Microsoft Azure](https://azure.microsoft.com/services/media-services/media-player/)
**Provedor de identidade (IDP)**|Active Directory do Azure
**Serviço de símbolo seguro (STS)**|Active Directory do Azure
**Fluxo de trabalho de proteção de DRM**|Proteção dinâmica de serviços de mídia do Azure
**Entrega de licença do DRM**|1. serviços de mídia do azure a entrega de licença (PlayReady, Widevine, FairPlay), ou <br/>2. servidor de licenças de Axinom, <br/>3. servidor de licença do PlayReady personalizado
**Origem**|Ponto de extremidade de Streaming de serviços de mídia do Azure
**Gerenciamento de chaves**|Não é necessário para implementação de referência
**Gerenciamento de conteúdo**|Um aplicativo de console c#

Em outras palavras, o provedor de identidade (IDP) e seguro Token STS (serviço) será Azure AD. Player, usaremos [API de Player de mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/). Serviços de mídia do Azure e o Azure Media Player suportam traço e CENC com multi-DRM.

O diagrama a seguir mostra a estrutura e o fluxo com o mapeamento de tecnologia acima geral.

![CENC em AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a criptografia de CENC dinâmico, a ferramenta de gerenciamento de conteúdo usará as entradas a seguir:

1. Abrir conteúdo;
1. Chave de conteúdo da chave/Gerenciamento de geração;
1. URLs de aquisição de licença.
1. Uma lista de informações do Azure AD.

A saída da ferramenta de gerenciamento de conteúdo será:

1. ContentKeyAuthorizationPolicy que contém a especificação em como entrega de licença verifica um token JWT e especificações de licença DRM;
1. AssetDeliveryPolicy que contém as especificações streaming format, proteção de DRM e URLs de aquisição de licença.

Durante a execução, o fluxo é como abaixo:

1. Sobre a autenticação de usuário, um token JWT é gerado;
1. Uma das declarações contidas no token JWT é "grupos" declarar que contém a ID do objeto de grupo de "EntitledUserGroup". Essa declaração será usada para passar "verificação de qualificação".
1. Manifesto de cliente de downloads do Player de uma CENC conteúdo protegido e "vê" o seguinte:
    1. ID, chave 
    1. o conteúdo é CENC protegida,
    1. URLs de aquisição de licença.

1. Player faz uma solicitação de aquisição de licença com base em navegador/DRM com suporte. ID de chave na solicitação de aquisição de licença, e o token JWT também será enviado. Serviço de entrega de licença verificará o token JWT e as declarações contidas antes de emitir a licença necessária.

##<a name="implementation"></a>Implementação

###<a name="implementation-procedures"></a>Procedimentos de implementação

A implementação incluirá as seguintes etapas:

1. Preparar ativos de teste: codificar/pacote um vídeo de teste para multi-taxa de bits fragmentado MP4 nos serviços de mídia do Azure. Este ativo não está protegido por DRM. Proteção de DRM será feita por proteção dinâmica mais tarde.
1. Criar chave ID e conteúdo chave (opcionalmente de propagação chave). Para nosso objetivo, sistema de gerenciamento de chaves não é necessária desde que estamos lidando com apenas um único conjunto de chave ID e a chave de conteúdo para alguns ativos de teste;
1. Use a API de AMS para configurar os serviços de entrega de licença multi-DRM para o ativo de teste. Se você estiver usando os servidores de licença personalizada pela sua empresa ou fornecedores de sua empresa em vez de serviços de licença nos serviços de mídia do Azure, você pode ignorar esta etapa e especificar URLs de aquisição de licença na etapa para configurar a entrega de licença. API de AMS é necessária para especificar que algumas detalhadas configurações, como restrição de diretiva de autorização, modelos de resposta de licença para diferentes serviços de licença do DRM, etc. No momento, o portal do Azure não ainda oferece a interface de usuário necessária para essa configuração. Você pode encontrar informações de nível de API e código no documento de Julia Kornich de exemplo: [usando PlayReady e/ou Widevine criptografia comuns dinâmico](media-services-protect-with-drm.md). 
1. Use a API de AMS para configurar política de entrega de ativos para o ativo de teste. Você pode encontrar informações de nível de API e código no documento de Julia Kornich de exemplo: [usando PlayReady e/ou Widevine criptografia comuns dinâmico](media-services-protect-with-drm.md).
1. Criar e configurar um locatário do Azure Active Directory do Azure;
1. Criar algumas contas de usuário e grupos em seu locatário do Azure Active Directory: você deve criar pelo menos "EntitledUser" Agrupar e adicionar um usuário a esse grupo. Os usuários neste grupo passará a seleção de qualificação na aquisição de licença e os usuários não neste grupo falharão passar verificação de autenticação e não será capazes de adquirir qualquer licença. Ser membro desse grupo de "EntitledUser" é uma declaração de "grupos" necessários no token de JWT emitido por Azure AD. Esse requisito de declaração deve ser especificado na configuração de etapa de serviços de entrega de licença de multi-DRM.
1. Crie um aplicativo do ASP.NET MVC que estejam hospedando seu player de vídeo. Este aplicativo ASP.NET será protegido com autenticação do usuário contra o locatário do Active Directory do Azure. Declarações PRI serão incluídas nos tokens de acesso obtidos após autenticação do usuário. OpenID conectar API é recomendado para esta etapa. Você precisa instalar os pacotes de NuGet a seguir:
    - Pacote de instalação Microsoft.Azure.ActiveDirectory.GraphClient
    - Pacote de instalação Microsoft.Owin.Security.OpenIdConnect
    - Pacote de instalação Microsoft.Owin.Security.Cookies
    - Pacote de instalação Microsoft.Owin.Host.SystemWeb
    - Pacote de instalação Microsoft.IdentityModel.Clients.ActiveDirectory
1. Crie um player usando a [API de Player de mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/). [API de ProtectionInfo do Player de mídia do Azure](http://amp.azure.net/libs/amp/latest/docs/) permite especificar qual tecnologia DRM usar na plataforma DRM diferente.
1. Matriz de teste:

**DRM**|**Navegador**|**Resultado de usuário direito**|**Resultado de usuário não qualificado**
---|---|-----|---------
**PlayReady**|Borda do MS ou IE11 no Windows 10|Bem-sucedida|Falhas
**Widevine**|Chrome no Windows 10|Bem-sucedida|Falhas
**FairPlay** |EM ABERTO||

George Trifonov da equipe de serviços de mídia do Azure escreveu um blog fornece etapas detalhadas em configurar o Active Directory do Azure para um aplicativo de player do ASP.NET MVC: [integrar Azure Media Services OWIN MVC baseado app com o Active Directory do Azure e restringir a entrega de chave conteúdo com base em declarações JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Jorge também escreveu um blog sobre [autenticação de token de JWT nos serviços de mídia do Azure e criptografia dinâmico](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). E aqui está sua [amostra sobre a integração do Azure AD com serviços de mídia do Azure chave entrega](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Para obter informações sobre Azure Active Directory:

- Você pode encontrar informações de desenvolvedor no [Guia do Azure Active Directory desenvolvedor](../active-directory/active-directory-developers-guide.md).
- Você pode encontrar informações de administrador [Administrar seu diretório do Azure AD](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Alguns truques em implementação

Há algumas "truques" a implementação. Esperamos que a seguinte lista de "truques" pode ajudar você a solução de problemas caso você encontre problemas.

1. **Emissor** URL deve terminar com **"/"**.  

    **Audiência** deve ser a identificação de cliente de aplicativos do player e você também deve adicionar **"/"** no final da URL do emissor.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    No [Decodificador de JWT](http://jwt.calebb.net/), você deve ver **aud** e **iss** como abaixo do token de JWT:
    
    ![1º de problema](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Adicione permissões ao aplicativo no AAD (na guia de configuração do aplicativo). Isso é necessário para cada aplicativo (versões locais e implantados).

    ![problema 2a](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Use o emissor correto na configuração de proteção de CENC dinâmica:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    A seguir não funcionará:
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    O GUID é o ID de locatário AAD. O GUID pode ser encontrado no pop-up de pontos de extremidade no portal do Azure.

4. Associações de grupo de concessão declarações privilégios. Certifique-se no arquivo de manifesto de aplicativo AAD, temos o seguinte

    "groupMembershipClaims": "Todos" (o valor padrão é nulo)

5. Configurando TokenType adequado ao criar requisitos de restrição.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Como adicionar suporte de JWT (AAD) além SWT (ACS), o padrão TokenType é TokenType.JWT. Se você usar SWT/ACS, você deve definir para TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Tópicos adicionais para implementação
Próximo nós será desc uss alguns tópicos adicionais em nosso projeto e implementação.

###<a name="http-or-https"></a>HTTP ou HTTPS?

O aplicativo de player de ASP.NET MVC criamos deve oferecer suporte a seguir:

1. Autenticação de usuário por meio do Azure AD que precisa ser em HTTPS;
1. Troca de token de JWT entre cliente e Azure AD que precisa ser em HTTPS;
1. Aquisição de licença DRM pelo cliente que é necessária para ser em HTTPS se entrega de licença é fornecida pelos serviços de mídia do Azure. Claro, conjunto de produtos do PlayReady não exigem HTTPS para entrega de licença. Se o servidor de licenças PlayReady estiver fora de serviços de mídia do Azure, HTTP ou HTTPS poderia ser usado.

Portanto, o aplicativo de player ASP.NET usará HTTPS como uma prática recomendada. Isso significa que o Player de mídia do Azure será uma página em HTTPS. No entanto, para streaming podemos preferem HTTP, portanto, precisamos considerar o problema de conteúdo misto.

1. Navegador não permitir conteúdo misto. Mas permitem plug-ins como Silverlight e OSMF plug-in para suave e traço. Conteúdo misto é um problema de segurança, isso é devido a ameaças da capacidade de inserir JS mal-intencionado que pode fazer com que os dados do cliente correr risco.  Navegadores bloquear esta por padrão e até a única maneira de trabalhar com ele está no lado do servidor (origem), para permitir que todos os domínios (independentemente https ou http). Isso provavelmente não é uma boa ideia ou.
1. Podemos Evite conteúdo misto: ambos usam HTTP ou ambos usam HTTPS. Ao reproduzir conteúdo misto, tech silverlightSS requer limpando um aviso de conteúdo misto. tech flashSS lida com conteúdo misto sem aviso conteúdo misto.
1. Se seu ponto de extremidade de streaming foi criado antes de agosto de 2014, ele não oferecerá suporte HTTPS. Nesse caso, crie e use um novo ponto de extremidade de streaming para HTTPS.

A implementação de referência, para conteúdo protegido por DRM, aplicativos e streaming estarão em HTTTPS. Para abrir conteúdo, o player não precisar de autenticação ou a licença, você tem a liberdade usar HTTP ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Active Directory do Azure sobreposição chave de assinatura

Este é um ponto importante levar em consideração da implementação. Se você não considerar isso na sua implementação, o sistema concluído eventualmente interromperá trabalho completamente dentro no máximo 6 semanas.

Azure AD usa padrão da indústria para estabelecer confiança entre si mesmo e aplicativos usando o Azure AD. Especificamente, o Azure AD usa uma chave de assinatura que consiste em um par de chaves público e privado. Quando o Azure AD cria um token de segurança que contém informações sobre o usuário, esse símbolo é assinado pelo Azure AD usando sua chave privada antes que ela é enviada para o aplicativo. Para verificar se o token é válida e realmente originado do Azure AD, o aplicativo deve validar a assinatura do token usando a chave pública exposta pela Azure AD contida no documento de metadados de Federação do locatário. Esta chave pública – e a chave de assinatura da qual ela deriva – são o mesmo usado para todos os locatários no Azure AD.

Informações detalhadas sobre sobreposição de chave do Azure AD podem ser encontradas no documento: [Informações importantes sobre assinatura sobreposição de chave no Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Entre o [par de chaves pública-particular](https://login.windows.net/common/discovery/keys/), 

- A chave privada é usada pelo Azure Active Directory para gerar um token JWT;
- A chave pública é usada por um aplicativo como os serviços de entrega de licença DRM no AMS para verificar o token JWT;
 
Para finalidade de segurança, o Active Directory do Azure gira este certificado periodicamente (a cada seis semanas). No caso de violações de segurança, a sobreposição de chave pode ocorrer a qualquer momento. Portanto, os serviços de entrega de licença no AMS precisam atualizar a chave pública usada como Azure AD gira o par de chaves, caso contrário, a autenticação de token no AMS falhará e nenhuma licença será emitida. 

Isso é feito definindo TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument ao configurar serviços de entrega de licença DRM.

O fluxo de token JWT é como abaixo:

1.  Azure AD irá emitir o token JWT com a chave privada atual de um usuário autenticado;
2.  Quando um player vê uma CENC com conteúdo protegido multi-DRM, primeiro localizará o token JWT emitido por Azure AD.
3.  O player envia a solicitação de aquisição de licença com o token JWT a serviços de entrega de licença no AMS;
4.  Os serviços de entrega de licença no AMS usará a chave pública atual/válida do Azure AD para verificar o token JWT, antes de emitir licenças.

Serviços de entrega de licença DRM sempre serão ser verificando a chave pública atual/válida do Azure AD. A chave pública apresentada pelo Azure AD será a chave usada para verificar um token JWT emitido por Azure AD.

O que acontece se a chave sobreposição acontece depois AAD gera um token JWT, mas antes do JWT token é enviado por jogadores aos serviços de entrega de licença DRM no AMS para verificação? 

Porque uma chave pode ser acumulada a qualquer momento, sempre há mais de uma chave pública válida disponível no documento de metadados de Federação. Azure entrega de licença de serviços de mídia pode usar qualquer uma das chaves especificadas no documento, como uma chave pode ser acumulada em breve, outro pode ser sua substituição e assim por diante.

### <a name="where-is-the-access-token"></a>Onde está o Token de acesso?

Se você observar como um aplicativo web chama um aplicativo de API em [Identidade de aplicativo com OAuth 2.0 cliente credenciais conceder](active-directory-authentication-scenarios.md#web-application-to-web-api), o fluxo de autenticação é como abaixo:

1.  Um usuário está conectado ao Azure AD no aplicativo da web (consulte o [Navegador da Web para o aplicativo da Web](active-directory-authentication-scenarios.md#web-browser-to-web-application).
2.  O ponto de extremidade de autorização do Azure AD redireciona o agente de usuário volta para o aplicativo cliente com um código de autorização. O agente de usuário retorna código de autorização para URI de redirecionamento do aplicativo cliente.
3.  O aplicativo web precisa adquirir um token de acesso para que ele possa autenticar na Web API e recuperar o recurso desejado. Ela faz uma solicitação para empresa de token do Azure AD, fornecendo a credencial, ID do cliente e URI de ID do aplicativo web da API. Ele apresenta o código de autorização para comprovar que o usuário aceitou.
4.  Azure AD autentica o aplicativo e retorna um token de acesso JWT que é usado para chamar a API da web.
5.  Em HTTPS, o aplicativo da web usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho da solicitação de autorização na Web API. Web API, em seguida, valida o token JWT e se a validação for bem-sucedida, retornará o recurso desejado.

Nesse fluxo "identidade de aplicativo", web API confia que o aplicativo da web do usuário autenticado. Por esse motivo, esse padrão é chamado um subsistema confiável. O [diagrama nesta página](http://msdn.microsoft.com/library/azure/dn645542.aspx/) descreve como o código de autorização conceder fluxo funciona.

Em aquisição de licença com restrição token, podemos estão seguindo o mesmo padrão de subsistema confiável. E o serviço de entrega de licença nos serviços de mídia do Azure web recurso de API, o "recurso de back-end" precisa de um aplicativo da web para acessar. Onde está o token de acesso?

Na verdade, podemos estão obtendo token de acesso do Azure AD. Após a autenticação de usuário bem-sucedida, será retornado o código de autorização. O código de autorização é usado, junto com a chave de ID e o aplicativo cliente, ao exchange para o token de acesso. E o token de acesso para acessar um aplicativo de "ponteiro" apontando ou que representa o serviço de entrega de licença de serviços de mídia do Azure.

Precisamos registre e configure o aplicativo "ponteiro" no Azure AD seguindo as etapas abaixo:

1.  No locatário Azure AD

    - Adicione um aplicativo (recurso) com a URL de logon: 

    https://[resource_name].azurewebsites.NET/ e 

    - URL de ID do aplicativo: 
    
    https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.  Adicionar uma nova chave para o aplicativo de recurso;
3.  Atualizar o arquivo de manifesto do aplicativo para que a propriedade groupMembershipClaims tem o seguinte valor: "groupMembershipClaims": "Todos"  
4.  No aplicativo do Azure AD apontando para o player web app, na seção "permissões para outros aplicativos", adicione o aplicativo de recurso que foi adicionado na etapa 1 acima. Em "permissões de representante" marque marca de seleção de "Acesso [resource_name]". Isso dá a permissão de aplicativo da web para criar tokens de acesso para acessar o aplicativo de recurso. Você deve fazer isso para local e implantado versão do aplicativo da web, se você está desenvolvendo com o Visual Studio e Azure web app.
    
Portanto, o token JWT emitido por Azure AD é realmente o token de acesso para acessar esse recurso de "ponteiro".

### <a name="what-about-live-streaming"></a>E quanto ao vivo Streaming?

Em acima, nossa discussão tenha sido concentrando-se no sob demanda ativos. E quanto ao vivo streaming?

A boa notícia é que você pode usar exatamente o mesmo design e implementação para proteger streaming ao vivo em serviços de mídia do Azure, tratando os ativos associados a um programa como um "ativo VOD".

Especificamente, é conhecido que para fazer ao vivo de streaming nos serviços de mídia do Azure, você precisa criar um canal e, em seguida, um programa no canal. Para criar o programa, você precisa criar um ativo que conterá o arquivamento dinâmico para o programa. Para fornecer CENC com proteção de multi-DRM do conteúdo ao vivo, tudo o que você precisa fazer, é aplicar o mesmo configuração/processamento ao ativo como se fosse um "ativo VOD" antes de iniciar o programa.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>O que são servidores de licença fora serviços de mídia do Azure?

Muitas vezes, os clientes podem têm investido em licença farm de servidores em seus próprios dados centralizar ou hospedado por provedores de serviços DRM. Felizmente, a proteção do conteúdo de serviços de mídia do Azure permite que você operar no modo híbrido: conteúdo hospedado e dinamicamente protegido nos serviços de mídia do Azure, enquanto as licenças DRM são entregues por servidores fora de serviços de mídia do Azure. Nesse caso, há as seguintes considerações de alterações:

1. O serviço de Token seguro precisa emitir tokens que são aceitáveis e podem ser verificadas pelo farm de servidores de licença. Por exemplo, os servidores de licenças de Widevine fornecidos pelo Axinom requer um token JWT específico que contém "mensagem de qualificação". Portanto, você precisa ter um STS para emitir o token tal JWT. Os autores concluiu tal implementação e você pode encontrar os detalhes no seguinte documento no [Centro de documentação do Azure](https://azure.microsoft.com/documentation/): [Usando Axinom para fornecer Widevine licenças aos serviços de mídia do Azure](media-services-axinom-integration.md). 
1. Você não precisa configurar o serviço de entrega de licença (ContentKeyAuthorizationPolicy) nos serviços de mídia do Azure. O que você precisa fazer é fornecer a licença URLs de aquisição (para PlayReady, Widevine e FairPlay) quando você configura AssetDeliveryPolicy em configurar o CENC com multi-DRM.
 
### <a name="what-if-i-want-to-use-a-custom-sts"></a>O que acontece se eu quiser usar um STS personalizado?

Pode haver algumas razões que um cliente pode optar por usar um STS personalizado (serviço Token seguro) para fornecer tokens JWT. Algumas delas são:

1.  O provedor de identidade (IDP) usados pelo cliente não suporta STS. Nesse caso, um STS personalizado pode ser uma opção.
2.  O cliente talvez precise controle mais flexível ou maior em integração STS com assinantes do cliente sistema de cobrança. Por exemplo, um operador MVPD pode oferecer vários pacotes de assinante OTT como premium, básica, esportes, etc. O operador talvez queira corresponder as declarações em um token com pacote do assinante para que apenas o conteúdo no pacote direita é disponibilizado. Nesse caso, um STS personalizado fornece flexibilidade necessária e controle.

Duas alterações precisam ser feitas ao usar um STS personalizado:

1.  Ao configurar o serviço de entrega de licença de um ativo, você precisa especificar a chave de segurança usada para verificação pelo STS personalizado (mais detalhes abaixo) em vez da chave atual do Azure Active Directory.
2.  Quando um token JTW é gerado, uma chave de segurança for especificada em vez da chave privada do X509 atual certificado no Active Directory do Azure.

Há dois tipos de chaves de segurança:

1.  Chave simétrica: a mesma chave é usada para gerar e verificar um token JWT;
2.  Chave assimétrico: um par de chaves pública-particular em um certificado é usado com chave privada para criptografar/gerar um token JWT e a chave pública para verificar o token de X509.

####<a name="tech-note"></a>Observação técnicos

Se você usar o .NET Framework / c# como sua plataforma de desenvolvimento, a X509 certificado usado para a chave de segurança assimétricos deve ter pelo menos 2048 comprimento de chave. Este é um requisito da classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey no .NET Framework. Caso contrário, a seguinte exceção será lançada:

IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para assinatura não pode ser menor que '2048' bits. 

## <a name="the-completed-system-and-test"></a>O sistema concluído e teste

Vamos percorrer alguns cenários no sistema de ponta a ponta concluído para que os leitores possam ter um basic "imagem" do comportamento antes de iniciar uma conta de logon.

O aplicativo da web de player e seu login podem ser encontradas [aqui](https://openidconnectweb.azurewebsites.net/).

Se você precisa é "não-integrados" cenário: hospedados em serviços de mídia do Azure que não são seja protegidas ou protegido por DRM mas sem autenticação de token de ativos de vídeo (emissão uma licença para quem solicitá-la), você poderá testá-lo sem login (alternando para HTTP se o streaming de vídeo sobre HTTP).

Se você precisa é o cenário de ponta a ponta integrado: ativos de vídeo está sob a proteção de DRM dinâmica nos serviços de mídia do Azure, com autenticação de token e token JWT sendo gerada pelo Azure AD, você precisa fazer logon.

### <a name="user-login"></a>Logon do usuário

Para testar o sistema DRM integrado de ponta a ponta, você precisa ter uma "conta" criados ou adicionados. 

Qual conta?

Embora Azure originalmente permitido o acesso somente pelos usuários de conta da Microsoft, ela agora permite o acesso por usuários de ambos os sistemas. Isso foi feito fazendo com que todas as propriedades Azure confiança Azure AD para autenticação, tendo o Azure AD autenticar usuários organizacionais e criando um relacionamento de Federação onde Azure AD confia o sistema de identidade do Microsoft conta consumidor para autenticar os usuários do consumidor. Como resultado, Azure AD é capaz de autenticar contas da Microsoft "convidado" bem como contas do Azure AD "nativas".

Como Azure AD confiar no domínio de conta MSA (Microsoft), você pode adicionar todas as contas de qualquer um dos seguintes domínios ao Azure AD personalizado locatário e usam a conta de logon:

**Nome de domínio**|**Domínio**
-----------|----------
**Domínio de locatário personalizado Azure AD**|someName.onmicrosoft.com
**Domínio corporativo**|Microsoft.com
**Domínio de conta MSA (Microsoft)**|Outlook.com, live.com, hotmail.com

Você pode contatar qualquer um dos autores tenho uma conta criados ou adicionados por você. 

Abaixo estão as capturas de tela de páginas de login diferentes usadas por contas de domínio diferente.

**Azure personalizado conta de domínio de locatário do AD**: nesse caso, você pode ver a página de login personalizada personalizado domínio de locatário do Azure AD.

![Conta de domínio personalizado Azure AD locatário](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio da Microsoft com cartão inteligente**: neste caso você verá a página de login personalizada pela Microsoft corporativa IT com autenticação de dois fatores.

![Conta de domínio personalizado Azure AD locatário](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Conta MSA (Microsoft)**: nesse caso, consulte a página de login da Account da Microsoft para consumidores.

![Conta de domínio personalizado Azure AD locatário](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Usando as extensões de mídia criptografada para PlayReady

Em um navegador moderno com extensões de mídia criptografado (EME) para obter suporte PlayReady, como IE 11 no Windows 8.1 e para cima e navegador Microsoft Edge no Windows 10, PlayReady será o DRM subjacente para EME.

![Usando EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A área de player escuro é devido ao fato de que a proteção de PlayReady impede um façam captura de tela de vídeo protegida. 

A tela a seguir mostra os plug-ins do player e o suporte MSE/EME.

![Usando EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME no Microsoft Edge e 11 IE no Windows 10 permite chamar do [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) em dispositivos Windows 10 que suportam esse recurso. PlayReady SL3000 desbloqueie o fluxo de conteúdo premium aprimorada (4K, cabeçalho, etc.) e novos modelos de fornecimento de conteúdo (janela antecipado para conteúdo avançado).

Concentre-se nos dispositivos Windows: PlayReady é o DRM somente no hardware disponível nos dispositivos Windows (PlayReady SL3000). Um serviço de streaming use PlayReady através de EME ou um aplicativo de UWP e oferecem uma qualidade de vídeo superior usando PlayReady SL3000 que outro DRM. Geralmente, o conteúdo de 2K fluirá pelo Chrome ou Firefox e conteúdo de 4K pelo Microsoft borda/IE11 ou um aplicativo de UWP no mesmo dispositivo (dependendo das configurações de serviço e implementação).


#### <a name="using-eme-for-widevine"></a>Usando EME para Widevine

Em um navegador com suporte no Android 4.4.4, EME/Widevine, como o Chrome 41 + no Windows 10, Windows 8.1, Mac OSX Yosemite e Chrome moderno Google Widevine é o DRM atrás EME.

![Usando EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Observe que Widevine não impede um façam captura de tela de vídeo protegida.

![Usando EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Usuários não qualificados

Se um usuário não for um membro do grupo ""intitulado usuários, o usuário não será capaz de passar "verificação de qualificação" e o serviço de licença multi-DRM irá se recusar emitir a licença solicitada, conforme mostrado abaixo. A descrição detalhada é "adquirir licença falhou", que é criado.

![Usuários não qualificados](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Executando o serviço de Token seguro personalizado

Para o cenário da execução personalizado seguro Token STS (serviço), o token JWT será emitido pelo STS personalizado usando chave simétrica ou assimétrico. 

No caso de uso chave simétrica (usando Chrome):

![Executando STS personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

No caso de uso assimétrico chave via um X509 (usando o navegador moderno da Microsoft) de certificado.

![Executando STS personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Em ambos os casos acima, a autenticação do usuário permanece o mesmo – por meio do Azure AD. A única diferença é que os tokens JWT são emitidos pelo STS personalizado em vez do Azure AD. É claro que, ao configurar proteção de CENC dinâmica, a restrição de serviço de entrega de licença Especifica o tipo de token JWT, chave simétrica ou assimétrico.

## <a name="summary"></a>Resumo

Neste documento, discutimos CENC com vários native DRM e controle de acesso via autenticação de token: seu design e sua implementação usando Azure, serviços de mídia do Azure e o Azure Media Player.

- Um design de referência é apresentado que contém todos os componentes necessários em um subsistema DRM/CENC;
- Uma implementação de referência no Azure, serviços de mídia do Azure e Azure Media Player.
- Alguns tópicos diretamente envolvidas no design e implementação também são discutidos.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Confirmações 

William Zhang, Mingfei Yan, Roland entidade legal franco, Kilroy Martins, Julia Kornich
