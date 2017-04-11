<properties 
    pageTitle="Licenciamento Microsoft® suave Streaming cliente portabilidade Kit" 
    description="Saiba mais sobre como o Microsoft® suave Streaming cliente portabilidade Kit de licenciamento." 
    services="media-services" 
    documentationCenter="" 
    authors="xpouyat,vsood" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016"  
    ms.author="xpouyat"/>

#<a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licenciamento Microsoft® suave Streaming cliente portabilidade Kit

##<a name="overview"></a>Visão geral

Microsoft suave Streaming cliente portabilidade Kit (**SSPK** abreviada) é uma implementação de cliente Streaming suave que é otimizada para ajudar a fabricantes de dispositivo incorporado, cabo e operadores móveis, provedores de serviço de conteúdo, fabricantes de fone, fornecedores de software independentes (ISVs) e provedores de soluções para criar produtos e serviços para streaming adaptativa streaming de conteúdo em formato de Streaming suave. SSPK é uma implementação independente de dispositivo e plataforma do cliente Streaming suave que pode ser transferido pelo Licenciado para qualquer dispositivo e a plataforma. 

Incluída abaixo é uma arquitetura de alto nível e caixa de IIS suave Streaming portabilidade Kit é a implementação do cliente de Streaming suave fornecida pela Microsoft e inclui toda a lógica de núcleo para reprodução de conteúdo de Streaming suave. Isto é, em seguida, transferido por parceiros para um dispositivo específico ou plataforma implementando interfaces apropriadas. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

##<a name="description"></a>Descrição

SSPK é licenciado em termos que oferecem valor comercial excelente. Licença SSPK fornece do setor com:

- Fonte de Streaming Kit portabilidade suave em C++ 
  - implementa funcionalidade de cliente Streaming suave
  - Adiciona formato análise, heurística, buffer lógica, etc.
- APIs de aplicativos do Player 
  - interfaces de programação para interação com um aplicativo de player de mídia
- Interface de camada (PAL) de abstração de plataforma 
  - interfaces de programação para interação com o sistema operacional (threads, sockets)
- Interface de camada (HAL) de abstração de hardware 
  - interfaces de programação para interação com hardware R / decodificadores V (decodificar, renderização)
- Interface DRM (gerenciamento) de direitos digitais 
  - interfaces de programação para lidar com DRM através de camada de abstração de DRM (DAL)
  - Microsoft PlayReady portabilidade Kit fornecido separadamente, mas integra-se através desta interface. Para obter mais detalhes sobre o licenciamento da Microsoft PlayReady Device, clique [aqui](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Exemplos de implementação 
  - exemplo de implementação de PAL para Linux
  - exemplo de implementação de HAL para GStreamer

##<a name="licensing-options"></a>Opções de licenciamento

Microsoft suave Streaming cliente portabilidade Kit é disponibilizado para licenciados em dois contratos de licença distintas: uma para desenvolvimento de produtos provisórias de cliente de Streaming suave e outro para distribuir suave Streaming cliente produtos finais para usuários finais.
 
- Para fabricantes de chipset, integradores ou fornecedores de software independentes (ISVs) que precisam de um código-fonte portabilidade kit para desenvolver produtos provisório, um Microsoft suave Streaming cliente portabilidade Kit **Provisório licença de produto** deve ser executada.
- Fabricantes de dispositivo ou fabricantes que exigem direitos de distribuição para suave Streaming Final produtos cliente aos usuários finais, a Microsoft suave Streaming cliente portabilidade Kit **Licença de produto Final** deve ser executada.

###<a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft suave Streaming portabilidade Kit provisórias licença de produto do cliente

Sob esta licença, a Microsoft oferece um suave Streaming cliente portabilidade Kit e os direitos de propriedade intelectual necessários para desenvolver e distribuir produtos provisórias de cliente de Streaming suave para outros licenciados de dispositivo de suavização Streaming cliente portabilidade Kit que distribuem suave Streaming cliente Final produtos.

####<a name="fee-structure"></a>Estrutura de taxa

Uma taxa de licença única de US $50.000 fornece acesso suave Streaming cliente portabilidade Kit. 

###<a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft suave Streaming portabilidade licença de produto do Final do Kit de cliente

Sob esta licença, a Microsoft oferece todos os direitos de propriedade intelectual necessária para receber suave Streaming cliente provisórias produtos de outros licenciados suave Streaming cliente portabilidade Kit e distribuir empresa produtos da marca suave Streaming cliente Final para os usuários finais.

####<a name="fee-structure"></a>Estrutura de taxa

Cliente suave Streaming produto Final é oferecido em um modelo de royalty como em:

- US $0,10 por implementação do dispositivo fornecida
- O royalty está limitado a r $50.000 cada ano
- Nenhum royalty para primeiras 10.000 implementações de dispositivo cada ano 

##<a name="licensing-procedure-and-sspk-access"></a>Acessam o procedimento de licenciamento e SSPK

Envie um email [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) para todas as consultas de licenciamento.

O [portal de distribuição de SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) é acessível para registrado licenciados provisórias.

Provisório e SSPK Final licenciados podem enviar perguntas técnicas para [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##<a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft suave Streaming licenciados de contrato de produto provisório do cliente

- Soluções de negócios adroit, Inc
- SA de transmissão Digital avançado
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis tecnologias Ltd
- Alticast Corporation
- Amazon Digital serviços, Inc.
- Software para multimídia AVC co., Ltd.
- Cavium, Inc.
- EchoStar comprando Corporation
- Enseo, Inc.
- Fluendo S.A.
- HANDAN BroadInfoCom co., Ltd.
- Infomir GMBH
- Irdeto EUA Inc
- Liberdade Global Services BV
- Inc MediaTek.
- MStar Co, Ltd
- Nintendo co., Ltd.
- OpenTV, Inc.
- Saffron Digital limitado
- Sichuan Changhong elétrico co., Ltd
- SoftAtHome
- Sony Corporation
- Tatung tecnologia Inc
- TCL Technoly eletrônicos (Huizhou) Co., Ltd.
- Var Vestel Elektronik Sanayi Ticaret A.S.
- VisualOn, Inc.
- ZTE Corporation

##<a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft suave Streaming licenciados de contrato do produto Final de cliente

- SA de transmissão Digital avançado
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis tecnologias Ltd
- Amazon Digital serviços, Inc.
- AmTRAN tecnologia co., Ltd.
- Arcadyan tecnologia Corporation
- ATMACA ELEKTRONİK SAN. VAR TİC. A.Ş
- Britânico Sky difusão limitada
- CastPal tecnologia Inc. Shenzhen
- Compal eletrônicos, Inc.
- Dongguan Digital AV tecnologia corp., Ltd.
- EchoStar comprando Corporation
- Enseo, Inc.
- Filmflex filmes limitado
- Fluendo S.A.
- Gibson inovações limitado
- Haier informações Applicantion S.R.L
- HANDAN BroadInfoCom co., Ltd.
- Homecast co., Ltd
- Hon Hai precisão setor co., Ltd.
- Infomir GMBH
- Kaonmedia co., Ltd.
- KDDI Corporation
- Nintendo co., Ltd.
- Laranja SA
- Saffron Digital limitado
- Sagemcom banda larga SAS
- Shenzhen Coship eletrônicos CO., LTD
- Shenzhen Jiuzhou elétrico co., Ltd
- Shenzhen Skyworth Digital tecnologia co., Ltd
- Elétrico para Changhong Sichuan co., Ltd.
- Corp. Industrial de Skardin
- Sky Deutschland Fernsehen GmbH & KG co.
- SmarDTV S.A.
- SoftAtHome
- Sony Corporation
- Limitado TCL exterior Marketing (os comercial de Macau)
- Tecnologias de entrega de tecnicolor, SAS
- Ltd Global Tongfang
- Estilo de vida Toshiba produtos e serviços Corporation
- S.r.o. de /Slovakia/ Corporation universal de mídia
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
