<properties 
    pageTitle="Inserção de anúncios no lado do cliente | Microsoft Azure" 
    description="Este tópico mostra como inserir anúncios no lado do cliente." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="inserting-ads-on-the-client-side"></a>Inserção de anúncios no lado do cliente

Este tópico contém informações sobre como inserir vários tipos de anúncios no lado do cliente.

Para obter informações sobre o suporte de legendas e ad fechada no Live streaming vídeos, consulte [suporte legenda codificada e padrões de inserção do Ad](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

>[AZURE.NOTE] Atualmente não há suporte para anúncios no Azure Media Player.

##<a id="insert_ads_into_media"></a>Inserção de anúncios sua mídia

Serviços de mídia do Azure oferece suporte para inserção de anúncios na plataforma de mídia do Windows: estruturas de Player. Estruturas de Player com suporte do ad estão disponíveis para dispositivos iOS, Silverlight, Windows Phone 8 e Windows 8. Cada estrutura de player contém código de exemplo que mostra como implementar um aplicativo de player. Há três tipos diferentes de anúncios, que você pode inserir em sua lista de mídia:.

- **Linear** – anúncios de quadro completo que pausar o vídeo principal.
- **Nonlinear** – anúncios de sobreposição que são exibidos como o vídeo principal estiver em execução, geralmente um logotipo ou outra imagem estática posicionado dentro do player.
- **Companion** – anúncios que são exibidas fora do player.

Anúncios podem ser inseridos em qualquer ponto na linha do tempo do vídeo principal. Você deve saber o player quando tocar o anúncio e quais anúncios para reproduzir. Isso é feito usando um conjunto de arquivos padrão baseado em XML: vídeo Ad serviço modelo (VAST), Digital vídeo vários Ad reprodução (VMAP), modelo de ordenar abstratos mídia (BELA) e Digital vídeo Player Ad Interface definição (VPAID). Arquivos grandes especificam quais anúncios para exibir. Arquivos VMAP especificam quando reproduzir diversos anúncios e conter XML grande. Arquivos de BELA são outra maneira de anúncios de sequência que também podem conter XML grande. Arquivos VPAID definem uma interface entre o player de vídeo e o anúncio ou o servidor do ad.

Cada estrutura de player funciona de forma diferente e cada será abordada no seu próprio tópico. Este tópico descreve os mecanismos básicos usados para inserir anúncios. Aplicativos do player de vídeo solicitar anúncios de um servidor do ad. O servidor do ad pode responder em um número de formas:

- Retornar um arquivo grande
- Retornar um arquivo VMAP (com VAST incorporado)
- Retornar um arquivo BELA (com VAST incorporado)
- Retornar um arquivo grande com anúncios VPAID

 
###<a name="using-a-video-ad-service-template-vast-file"></a>Usando um arquivo de modelo (VAST) do serviço de anúncio de vídeo

Um arquivo grande Especifica quais ad ou anúncios para exibir. O XML a seguir é um exemplo de um arquivo grande para um anúncio linear:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
    
O anúncio linear é descrito pela **<Linear>** elemento. Especifica a duração do anúncio, eventos de controle, clique em por meio de, clique em acompanhamento e um número de **<MediaFile>** elementos. Eventos de controle são especificados no **<TrackingEvents>** elemento e permitir que um servidor do ad controlar vários eventos que ocorrem durante a exibição do ad. Nesse caso, iniciar, médio, concluído e expandir eventos são controlados. O evento de inicialização ocorre quando o anúncio é exibido. O evento de ponto médio ocorre quando pelo menos 50% da linha do tempo do ad foi exibido. O evento complete ocorre quando o anúncio foi executado até o final. O evento de expansão ocorre quando o usuário expande o player de vídeo em tela cheia. Clickthroughs são especificados com um **<ClickThrough>** elemento dentro de uma **<VideoClicks>** elemento e especifica um URI para um recurso para exibir quando o usuário clica no anúncio. ClickTracking é especificado em uma **<ClickTracking>** elemento, também dentro do **<VideoClicks>** elemento e especifica um recurso de acompanhamento para o player solicitar quando o usuário clica no anúncio. O **<MediaFile>** elementos especificam informações sobre uma codificação específica de um anúncio. Quando houver mais de um **<MediaFile>** elemento, o player de vídeo pode escolher a codificação recomendadas para a plataforma. 

Anúncios lineares podem ser exibidos em uma ordem especificada. Para fazer isso, adicione adicionais <Ad> elementos para a VAST de arquivo e especificar a ordem usando o atributo de sequência. O exemplo a seguir ilustra isso:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
    
Anúncios não lineares são especificados em um <Creative> elemento também. A exemplo a seguir mostra uma <Creative> elemento que descreve um anúncio não linear.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>

 
O **<NonLinearAds>** elemento pode conter um ou mais **<NonLinear>** elementos, cada um dos quais pode descrever um anúncio não linear. O **<NonLinear>** elemento Especifica o recurso para o ad não linear. O recurso pode ser um **<StaticResouce>**, um **<IFrameResource>**, ou um **<HTMLResouce>**.**<StaticResource>** Descreve um recurso não-HTML e define um atributo creativeType que especifica como o recurso é exibido:

Imagem/gif, imagem/jpeg, imagem/png – o recurso é exibido em um HTML **<img>** marca.

Aplicativo/x-javascript – o recurso é exibido em uma marca HTML <**script**>.

Aplicativo/x-shockwave-flash – o recurso é exibido em um Flash player.

**<IFrameResource>**Descreve um recurso HTML que pode ser exibido em um IFrame. **<HTMLResource>**Descreve um pedaço de código HTML que pode ser inserido em uma página da web. **<TrackingEvents>**Especifique o URI a solicitação quando o evento ocorre e eventos de controle. Neste exemplo, os eventos acceptInvitation e recolher são rastreados. Para obter mais informações sobre o **<NonLinearAds>** elemento e seus filhos, consulte IAB.NET/VAST. Observe que o **<TrackingEvents>** elemento está localizado dentro do** <NonLinearAds> ** elemento em vez do **<NonLinear>** elemento.

Anúncios de Companion são definidos dentro de uma <CompanionAds> elemento. O <CompanionAds> elemento pode conter um ou mais <Companion> elementos. Cada <Companion> elemento descreve um anúncio companion e pode conter um <StaticResource>, <IFrameResource>, ou <HTMLResource> que são especificadas da mesma maneira como um anúncio não linear. Um arquivo grande pode conter vários anúncios companion e o aplicativo de player poderá escolher o anúncio mais apropriado para exibir. Para obter mais informações sobre VAST, consulte [3.0 grande](http://www.iab.net/media/file/VASTv3.0.pdf).

###<a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Usando um vídeo Digital vários arquivos de lista de reprodução (VMAP) do Ad

Um arquivo VMAP permite especificar quando ocorrem quebras de ad, quanto tempo dura de cada quebra, quantos anúncios podem ser exibidos dentro de uma quebra e que tipos de anúncios podem ser exibidas durante uma quebra. O seguinte em um arquivo VMAP de exemplo que define uma quebra de ad único:
    
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
     
Um arquivo VMAP começa com uma <VMAP> elemento que contém um ou mais <AdBreak> elementos, cada um definindo uma quebra de ad. Cada quebra de ad Especifica um tipo de quebra, quebra ID e deslocamento de tempo. O atributo breakType Especifica o tipo de anúncio que pode ser executado durante a quebra: linear, não linear, ou exibir. Exibir o mapa de anúncios anúncios companion grande. Mais de um tipo de ad pode ser especificado em uma lista separada por vírgulas (sem espaços). O breakID é um identificador opcional para o ad. O timeOffset Especifica quando o anúncio deve ser exibido. Ele pode ser especificado em uma das seguintes maneiras:

1. Hora – no formato hh ou mmm onde .mmm é milissegundos. O valor do atributo especifica o tempo desde o começo da linha do tempo vídeo até o início da quebra de ad.
1. Porcentagem – em formato % n onde n é a porcentagem da linha do tempo vídeo para reprodução antes da execução do ad
1. Inicial/final – Especifica que um anúncio deve ser exibido antes ou depois que o vídeo foi exibido
1. Posicione – Especifica a ordem dos ad quebras quando o intervalo das quebras de ad é desconhecido, como os de streaming ao vivo. A ordem de cada quebra ad é especificada no formato #n onde n é um inteiro 1 ou maior. 1 significa que o anúncio deve ser reproduzido na primeira oportunidade, 2 significa ad deve ser reproduzido na segunda oportunidade e assim por diante.

Dentro do elemento <**AdBreak**> pode haver um elemento <**AdSource**>. O elemento <**AdSource**> contém os seguintes atributos:

1. ID – Especifica um identificador para a fonte do ad
1. allowMultipleAds – um valor booliano que especifica se vários anúncios podem ser exibidos durante a quebra de ad
1. followRedirects – um valor booliano opcional que especifica se o player de vídeo deve aceitar redireciona dentro de uma resposta do ad

O elemento <**AdSource**> fornece o player uma resposta de ad embutida ou uma referência a uma resposta do ad. Ele pode conter um dos seguintes elementos:

- <VASTAdData>indica que uma resposta de ad grande é incorporada dentro do arquivo VMAP
- <AdTagURI>um URI que faz referência a uma resposta de anúncio de outro sistema
- <CustomAdData>-um aleatório de cadeia de caracteres que respresents uma resposta não grande

Neste exemplo, uma resposta ad em linha é especificada com um <VASTAdData> elemento que contém uma resposta do ad grande. Para obter mais informações sobre os outros elementos, consulte [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

O elemento <**AdBreak**> também pode conter um elemento de <**TrackingEvents**>. O elemento <**TrackingEvents**> permite controlar o início ou fim de uma quebra de ad ou se ocorreu um erro durante a quebra de ad. O elemento <**TrackingEvents**> contém um ou mais <**acompanhamento**> elementos, cada um deles especifica um evento de rastreamento e um rastreamento URI. Os eventos de controle possíveis são:

1. breakStart – controla o início de uma quebra de ad
1. breakEnd – controlar a conclusão de uma quebra de ad
1. Erro – controla o erro que ocorreu durante a quebra de ad

O exemplo a seguir mostra um arquivo VMAP que especifica os eventos de rastreamento

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Para obter mais informações sobre o elemento <**TrackingEvents**> e seus filhos, consulte http://iab.org/VMAP.pdf

###<a name="using-a-media-abstract-sequencing-template-mast-file"></a>Usando um resumo de mídia ordenar arquivo de modelo (mestre)

Um arquivo de BELA permite especificar disparadores que definem quando um anúncio é exibido. O que vem a seguir é um exemplo de arquivo de BELA que contém disparadores para um anúncio de rolo pre, um anúncio de rolo intermediário e um anúncio de rolo POST.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>

 

Um arquivo de BELA começa com uma **<MAST>** elemento que contém um **<triggers>** elemento. O <triggers> elemento contém um ou mais **<trigger>** elementos que definem quando um anúncio deve ser reproduzido. 

O **<trigger>** elemento contém um **<startConditions>** elemento que especificam quando um anúncio deve começar a tocar. O **<startConditions>** elemento contém um ou mais <condition> elementos. Quando cada <condition> for avaliada como verdadeira um disparador é iniciado ou revogado dependendo se o <condition> está contida em uma **< startConditions**> ou **<endConditions>** elemento respectivamente. Quando vários <condition> elementos estão presentes, eles são tratados como ou implícito, qualquer condição avaliar como true fará com que o disparador iniciar. <condition>elementos podem ser aninhados. Quando filho <condition> elementos são predefinidos, eles são tratados como um e implícita, todas as condições devem ser avaliados como verdadeiro para o disparador iniciar. O <condition> elemento contém os seguintes atributos que definem a condição: 

1. **tipo** – Especifica o tipo de condição, evento ou propriedade
1. **nome** – o nome da propriedade ou evento a ser usado durante a avaliação
1. **valor** – o valor que uma propriedade será avaliada contra
1. **operador** – a operação a ser usada durante a avaliação: EQ (igual), NEQ (não igual), GTR (maior), GEQ (maior ou igual), LT (menor que), LEQ (menor ou igual), MOD (módulo)

**<endConditions>**também contêm <condition> elementos. Quando uma condição for avaliada como true, o disparador é redefinida. O <trigger> elemento também contém um <sources> elemento que contém um ou mais <source> elementos. O <source> elementos definem o URI para a resposta ao anúncio e o tipo de resposta do ad. Neste exemplo, um URI é fornecido a resposta grande. 


    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
 

###<a name="using-video-player-ad-interface-definition-vpaid"></a>Usando a definição de Interface do Player de vídeo-Ad (VPAID)

VPAID é uma API para habilitar unidades ad executável para se comunicar com um player de vídeo. Isso permite experiências ad altamente interativo. O usuário pode interagir com o ad e o anúncio pode responder a ações tomadas pelo visualizador. Por exemplo, um anúncio pode exibir botões que permitem que o usuário exibir mais informações ou uma versão mais longa do anúncio. O player de vídeo deve suportar a API VPAID e o anúncio executável deve implementar a API. Quando um player solicita que um anúncio de um servidor do ad do servidor pode responder com uma resposta grande que contém um anúncio VPAID.

Um anúncio executável é criado no código que deve ser executado em um ambiente de runtime como Adobe Flash™ ou JavaScript que pode ser executada em um navegador da web. Quando um servidor ad retorna uma resposta de grande contendo um anúncio VPAID, o valor da apiFramework atributo na <MediaFile> elemento deve ser "VPAID". Esse atributo especifica que o anúncio contido é um anúncio executável VPAID. O atributo de tipo deve ser definido para o tipo MIME do executável, como "aplicativo/x-shockwave-flash" ou "aplicativo/x-javascript". O trecho XML a seguir mostra o <MediaFile> elemento de uma resposta de grande contendo um anúncio executável VPAID. 

    
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
 

Um anúncio executável pode ser inicializado usando o <AdParameters> elemento dentro do <Linear> ou <NonLinear> elementos em uma resposta grande. Para obter mais informações sobre o <AdParameters> elemento, consulte [3.0 grande](http://www.iab.net/media/file/VASTv3.0.pdf). Para obter mais informações sobre a API VPAID, consulte [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

##<a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementar um Windows ou o Windows Phone 8 Player com suporte do Ad

A plataforma do Microsoft Media: Player Framework para Windows 8 e Windows Phone 8 contém uma coleção de aplicativos de exemplo que mostram como implementar um aplicativo de player de vídeo usando o framework. Você pode baixar a estrutura de Player e as amostras de [estrutura de Player para o Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Quando você abre a solução de Microsoft.PlayerFramework.Xaml.Samples você verá um número de pastas dentro do projeto. A pasta de anúncios contém o código de exemplo relevante para a criação de um player de vídeo com suporte do ad. Dentro o anúncios a pasta é um número de arquivos XAML/cs para cada dos quais mostrar como inserir anúncios de maneira diferente. A lista a seguir descreve cada:

- AdPodPage.xaml mostra como exibir um pod ad.
- AdSchedulingPage.xaml mostra como agendar anúncios.
- FreeWheelPage.xaml mostra como usar o plug-in FreeWheel para agendar anúncios.
- MastPage.xaml mostra como agendar anúncios com um arquivo de BELA.
- ProgrammaticAdPage.xaml mostra como programaticamente agendar anúncios em vídeo.
- ScheduleClipPage.xaml mostra como agendar um anúncio sem um arquivo grande.
- VastLinearCompanionPage.xaml mostra como inserir um linear e ad companion.
- VastNonLinearPage.xaml mostra como inserir um anúncio não lineares.
- VmapPage.xaml mostra como especificar anúncios com um arquivo VMAP.

Cada um desses exemplos usa a classe de MediaPlayer definida pela estrutura do player. A maioria dos exemplos usam Plug-ins que adicionar suporte para vários formatos de resposta do ad. O exemplo ProgrammaticAdPage programaticamente interage com uma instância de MediaPlayer.

###<a name="adpodpage-sample"></a>Exemplo de AdPodPage

Este exemplo usa a AdSchedulerPlugin para definir quando exibir um anúncio. Neste exemplo, um anúncio de rolo intermediário está agendado para ser reproduzido depois de 5 segundos. O pod ad (um grupo de anúncios para exibir na ordem) é especificado em um arquivo grande retornado de um servidor do ad. O URI para o arquivo grande especificado no <RemoteAdSource> elemento.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
    
        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>
    
                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>
    
                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Para obter mais informações sobre o AdSchedulerPlugin, consulte [anúncios no Framework Player no Windows 8 e Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

###<a name="adschedulingpage"></a>AdSchedulingPage

Este exemplo também usa o AdSchedulerPlugin. Ele agenda três anúncios, um anúncio de pré-lançamento, um anúncio de rolo intermediário e um anúncio de rolo POST. O URI para o VAST para cada anúncio é especificado em uma <RemoteAdSource> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>
    
                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


###<a name="freewheelpage"></a>FreeWheelPage

Este exemplo utiliza a FreeWheelPlugin que especifica um atributo de origem que especifica um URI que aponta para um arquivo de SmartXML que especifica ad conteúdo bem como ad informações de agendamento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="mastpage"></a>MastPage

Este exemplo utiliza a MastSchedulerPlugin que permite que você use um arquivo de BELA. O atributo de origem Especifica o local do arquivo mestre.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="programmaticadpage"></a>ProgrammaticAdPage

Este exemplo programaticamente interage com o MediaPlayer. O arquivo de ProgrammaticAdPage.xaml cria o MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

O arquivo de ProgrammaticAdPage.xaml.cs cria um AdHandlerPlugin, adiciona um TimelineMarker para especificar quando um anúncio deve ser exibido e, em seguida, adiciona um manipulador para o evento MarkerReached que carrega uma RemoteAdSource Especifica um URI para um arquivo grande e, em seguida, reproduz ad.
    
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;
    
            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }
    
            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

###<a name="scheduleclippage"></a>ScheduleClipPage

Este exemplo usa a AdSchedulerPlugin para agendar um anúncio de rolo intermediário especificando um arquivo. wmv que contém o anúncio.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearcompanionpage"></a>VastLinearCompanionPage

Este exemplo ilustra como usar o AdSchedulerPlugin para agendar um anúncio linear rolo intermediário com um anúncio companion. O <RemoteAdSource> elemento Especifica o local do arquivo grande.
    
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage

Este exemplo utiliza a AdSchedulerPlugin agendar linear e um anúncio não lineares. O local do arquivo grande é especificado com o <RemoteAdSource> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
                            
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vmappage"></a>VMAPPage

Este amostras usa o VmapSchedulerPlugin para agendar anúncios usando um arquivo VMAP. O URI para o arquivo VMAP especificado no atributo de origem do <VmapSchedulerPlugin> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

##<a name="implementing-an-ios-video-player-with-ad-support"></a>Implementar um Player de vídeo com suporte do Ad do iOS


A plataforma do Microsoft Media: Estrutura de Player para iOS contém uma coleção de aplicativos de exemplo que mostram como implementar um aplicativo de player de vídeo usando o framework. Você pode baixar a estrutura de Player e as amostras de [Estrutura de Player de mídia do Azure](https://github.com/Azure/azure-media-player-framework). A página de github tem um link para um Wiki que contém informações adicionais sobre a estrutura de player e introdutório para a amostra de player: [Wiki de Player de mídia do Azure](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).


###<a name="scheduling-ads-with-vmap"></a>Anúncios com VMAP de agendamento

O exemplo a seguir mostra como agendar anúncios usando um arquivo VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest
    
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

###<a name="scheduling-ads-with-vast"></a>Anúncios com VAST de agendamento

O exemplo a seguir mostra como agendar um anúncio de grande ligação atrasadas.
    
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
         
   O exemplo a seguir mostra como agendar um anúncio de grande de vinculação antecipado.
Exemplo: 4 agenda um antecipado ligação ad grande //Download o VAST arquivo se (! [ framework.adResolver downloadManifest: & manifesto withURL: [NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[auto logFrameworkError];} else {adLinearTime.startTime = 7; adLinearTime.duration = 0;
        
        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

O exemplo a seguir mostra como inserir um anúncio usando aproximada Recortar edição (RCE)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um pod ad.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;
    
    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um anúncio de rolo intermediário não conjunta. Um anúncio não aderência é reproduzido apenas uma vez, independentemente de quaisquer buscando executa o visualizador.
    
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um anúncio de rolo intermediário conjunta. Um anúncio de aderência será exibido sempre que é alcançado o ponto especificado na linha do tempo vídeo.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


O exemplo a seguir mostra como agendar um anúncio de rolo POST.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um anúncio de pré-lançamento.
    
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

O exemplo a seguir mostra como agendar um anúncio de sobreposição de rolo intermediário.
    
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
##<a name="see-also"></a>Consulte também

[Desenvolver aplicativos do player de vídeo](media-services-develop-video-players.md)
