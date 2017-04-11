<properties
    pageTitle="Usar serviços de dispositivo móvel, como o serviço de aplicativo ajudar?"
    description="Saiba quais são as vantagens serviço de aplicativo trazer para seus projetos de serviços Mobile existentes."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>Usar os serviços de celular, como o serviço de aplicativo ajudar?

##<a name="overview"></a>Visão geral
O serviço de Mobile existente é seguro e permanecerá suportado. Porém, existem número de vantagens o *Serviço de aplicativo do Azure* plataforma fornece para seu aplicativo móvel que não estão disponíveis com os serviços de celular:

- Oferta mais simples, mais fácil e mais econômica para os aplicativos que incluem web e clientes móveis
- Novos recursos de host, incluindo Web trabalhos, CNames personalizado, melhor monitoramento
- Integração completa com o Gerenciador de tráfego
- Conectividade com seus recursos no local e VPNs usando VNet além de conexões de híbrido
- Monitoramento, alertas e solução de problemas para seu aplicativo usando NewRelic ou AppInsights
- Mais sofisticado espectro de subjacente calcular recursos e preços
- Dimensionamento automático interno, balanceamento de carga e monitoramento do desempenho.
- Interno preparação, recursos de backup, reversão e testes em produção

## <a name="new-hosting-features"></a>Novos recursos de hospedagem
No *Serviço de aplicativo do Azure* o *Aplicativo móvel* do código de back-end é executado no mesmo contêiner como Web App e API App. Assim que você possa aproveitar todos os recursos nesse contêiner, incluindo alguns que não estão atualmente presentes nos serviços do Mobile:

- Adicionar lógica de back-end continuamente em execução por meio de trabalhos de Web
- Certifique-se de que seu código de back-end sempre está em execução
- Use CNames personalizado para fornecer nomes amigáveis e estáveis para seus pontos de extremidade de back-end móvel
- Localização geográfica escala de seu aplicativo com o Gerenciador de tráfego
- Inclua quaisquer bibliotecas e pacotes desejados.
- (Para .NET) Aproveitar qualquer recurso do ASP.NET, incluindo MVC
- (Para node) Aproveite qualquer biblioteca JavaScript pura do ecossistema nó, incluindo bibliotecas MVC comuns.

##<a name="access-on-premises-data-using-vnet"></a>Acessar dados de locais usando VNet
Com os serviços de celular hoje você pode já usar conexões híbrido para acessar os recursos no local. No entanto, há situações em que uma solução VPN é preferencial. Com *O serviço de aplicativo do Azure* , você pode usar VNet do Azure para seu código de back-end do aplicativo Mobile.

##<a name="use-your-favorite-backend-language"></a>Usar seu idioma favorito back-end
*Serviço de aplicativo do Azure* oferece suporte mais amplo e mais sofisticado para plataformas ASP.NET e Node, incluindo acessem os tempos de execução mais recentes.

##<a name="set-up-automatic-scale"></a>Configurar a escala automática
Com os serviços de celular, todas as instâncias do seu código de back-end estavam executando em VMs pequenas. *Serviço de aplicativo do Azure* permite que você selecione o tamanho das VMs de um conjunto mais sofisticado de opções. Você pode também rapidamente dimensionar para cima ou check-out para lidar com qualquer entrada carga de cliente, com base em várias métricas de desempenho.

##<a name="be-in-the-know"></a>Estar na "saber"
Reagir aos problemas em tempo real com monitoramento e alertas para notificar automaticamente a você e sua equipe. Integre a análise avançada de aplicativo e funcionalidade de monitoramento do novo Relíquia e AppInsights obter ainda melhores percepção como é o desempenho do seu aplicativo móvel. Com *O serviço de aplicativo do Azure* agora você pode configurar alertas com base em várias métricas de desempenho, programaticamente e através do Portal do Azure.

##<a name="keep-your-assets-safe"></a>Manter seus ativos confiáveis
Automaticamente, faça o backup de seu banco de dados e back-end. Seu código e dados é seguros contra desastres e facilmente restaurado, permitindo que você execute seus negócios com confiança.

##<a name="ready-stage-go"></a>Pronto, estágio, ir!
Com *O serviço de aplicativo do Azure* agora você pode criar vários particular de teste e ambientes para seus aplicativos móveis. Usá-los para executar testes antes de implantar. Troque a produção sem tempo de inatividade. Aplicativos Web são pré-carregados, garantindo que a melhor experiência de cliente.

Você pode obter as vantagens do *Serviço de aplicativo* para o serviço móvel existente seguindo este [tutorial](app-service-mobile-migrating-from-mobile-services.md)de início.

