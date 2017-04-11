<properties
    pageTitle="Quais são os aplicativos Mobile"
    description="Saiba quais são as vantagens serviço aplicativo trazer seus aplicativos móveis do enterprise."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>o que é aplicativos Mobile?

Serviço de aplicativo do Azure é uma totalmente gerenciado [plataforma como um serviço](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) oferecendo para desenvolvedores profissionais que traz um conjunto sofisticado de recursos para web, móvel e cenários de integração. *Aplicativos móveis* no *Serviço de aplicativo do Azure* oferecem uma plataforma de desenvolvimento de aplicativos móveis altamente escaláveis, globalmente disponível para os desenvolvedores corporativos e integradores que traz um conjunto sofisticado de recursos para desenvolvedores móveis.

![Aplicativos móveis](./media/app-service-mobile-value-prop/overview.png)

##<a name="why-mobile-apps"></a>Por que aplicativos móveis?
*Aplicativos móveis* no *Serviço de aplicativo do Azure* oferece uma plataforma de desenvolvimento de aplicativos móveis altamente escaláveis, globalmente disponível para os desenvolvedores corporativos e integradores que traz um conjunto sofisticado de recursos para desenvolvedores móveis. Com aplicativos Mobile, você pode:

- **Construir nativas e entre aplicativos de plataforma** - se você está construindo nativo iOS, Android e Windows aplicativos ou Xamarin ou Cordova (Phonegap) entre plataformas aplicativos, pode aproveitar do serviço de aplicativo usando SDKs nativos.
- **Conectar-se aos sistemas corporativos** - aos aplicativos Mobile pode adicionar entrada corporativa em minutos e conectar-se a sua empresa local ou recursos de nuvem.
- **Construir aplicativos pronto para offline com a sincronização de dados** - faça sua força de trabalho móvel produtivo por aplicativos de criação que trabalhar offline e usar aplicativos Mobile para dados de sincronização em segundo plano quando houver conectividade com qualquer uma das suas fontes de dados corporativos ou SaaS APIs.
- **Notificações por push para milhões em segundos** - envolva os clientes com as notificações por push instantânea em qualquer dispositivo, personalizados às suas necessidades, enviadas quando o tempo está correto.

## <a name="mobile-app-features"></a>Recursos de aplicativo móvel
Os recursos a seguir são importantes para desenvolvimento móvel habilitado para a nuvem:

- **Autenticação e autorização** - selecione uma lista crescente de provedores de identidade, incluindo o Azure Active Directory para a autenticação de empresa, além de provedores sociais como o Facebook, Google, Twitter e Account da Microsoft.  Os aplicativos do Azure Mobile fornece um serviço de OAuth 2.0 para cada provedor.  Você também pode integrar o SDK para o provedor de identidade para a funcionalidade específica do provedor.

  Descubra mais sobre nossos [recursos de autenticação].

- **Acesso a dados** - aplicativos do Azure Mobile fornece uma fonte de dados do celular amigável OData v3 vinculada ao SQL Azure ou um SQL Server local.  Esse serviço pode ser baseado em estrutura de entidades, permitindo que você integrar facilmente com outro NoSQL e provedores de dados do SQL, incluindo provedores de [Armazenamento de tabela do Azure], MongoDB, [DocumentDB] e API de SaaS como o Office 365 e Salesforce.com.
- **Sincronização offline** - nossa SDKs de cliente tornou fácil para você criar aplicativos robustos e ágil móvel que funcionam com um dados off-line conjunto podem ser sincronizadas automaticamente com os dados de back-end, incluindo o suporte de resolução de conflitos.

  Descubra mais sobre nossos [recursos de dados].

- **Notificações por push** - nossa SDKS de cliente integrar perfeitamente com os recursos de registro do Hubs de notificação do Azure, permitindo que você envie notificações por push para milhões de usuários simultaneamente.

  Descubra mais sobre nosso [push recursos de notificação].

- **Cliente SDKs** - fornecemos um conjunto completo de SDKs de cliente que abrangem desenvolvimento nativo ([iOS], [Android] e [Windows]), desenvolvimento de plataforma cruzada ([Xamarin para iOS e Android], [Formulários de Xamarin]) e desenvolvimento de aplicativos de híbrido ([Apache Cordova]).  Cada cliente SDK está disponível com uma licença do MIT e é aberto.

## <a name="azure-app-service-features"></a>Recursos de serviço de aplicativo Azure.
Os seguintes recursos de plataforma são geralmente úteis para sites de produção móvel.

- **Dimensionamento de autoserviço** de aplicativo permite que você rapidamente escala-up ou out para lidar com qualquer entrada carga de cliente. Selecionar o número e tamanho das VMs manualmente ou configurar o dimensionamento automático para dimensionar seu aplicativo móvel back-end com base em carga ou cronograma.

  Descubra mais sobre [automática de dimensionamento].

- **Ambientes de teste** - serviço de aplicativo pode executar várias versões do seu site, permitindo que você execute A / B teste, teste em produção como parte de um plano de DevOps maior e in-loco teste de um novo back-end.

  Descubra mais sobre [ambientes temporários].

- **Implantação contínuo** - serviço de aplicativo pode integrar com sistemas SCM comuns, permitindo que você implantar automaticamente uma nova versão do seu back-end enviando para uma ramificação do seu sistema de SCM.

  Descubra mais sobre [Opções de implantação].

- **Rede virtual** - serviço de aplicativo pode se conectar aos recursos de locais usando conexões de rede, rota expressa ou híbrido virtuais.

  Descubra [mais sobre híbrido conexões], [redes virtuais]e a [rota expressa].

- **Isolados / dedicado ambientes** -serviço de aplicativo pode ser executado em um ambiente totalmente isolado e dedicado para execução com segurança de aplicativos de serviço de aplicativo do Azure em alta escala.  Isso é ideal para cargas de trabalho de aplicativos que exigem escala muito alta, isolamento ou acesso de rede seguro.

  Descubra mais sobre [Ambientes de serviço de aplicativo].

## <a name="getting-started"></a>Guia de Introdução ##
Para começar a usar os aplicativos Mobile, siga o tutorial de [Introdução] .  Isso abordará as Noções básicas para produzir um móvel back-end e um cliente de sua escolha, em seguida, integração de autenticação, sincronização offline e notificações por push.  Você pode seguir o tutorial de [Introdução] várias vezes - uma vez para cada aplicativo cliente.

Para mais informações sobre os aplicativos do Azure Mobile, consulte nosso [mapa de aprendizagem].
Para obter mais informações sobre a plataforma de serviço de aplicativo do Azure, consulte [O serviço de aplicativo do Azure].

>[AZURE.NOTE]Se você quiser começar a usar o serviço de aplicativo do Azure antes de se inscrever para uma conta do Azure, vá para [Experimentar o serviço de aplicativo](https://tryappservice.azure.com/?appServiceName=mobile), onde você pode criar imediatamente um aplicativo da web de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Serviço de aplicativo do Azure]: ../app-service/app-service-value-prop-what-is.md
[Introdução]: app-service-mobile-ios-get-started.md
[Armazenamento de tabela do Microsoft Azure]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[recursos de autenticação]: ./app-service-mobile-auth.md
[recursos de dados]: ./app-service-mobile-offline-data-sync.md
[recursos de notificação de envio]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin para iOS e Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Formulários de Xamarin]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[auto dimensionamento]: ../app-service-web/web-sites-scale.md
[ambientes de teste]: ../app-service-web/web-sites-staged-publishing.md
[Opções de implantação]: ../app-service-web/web-sites-deploy.md
[conexões de híbrido]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[redes virtuais]: ../app-service-web/web-sites-integrate-with-vnet.md
[Rota expressa]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[Ambientes de serviço de aplicativo]: ../app-service-web/app-service-app-service-environment-intro.md
[mapa de aprendizagem]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
