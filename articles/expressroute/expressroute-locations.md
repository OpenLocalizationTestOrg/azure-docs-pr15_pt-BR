<properties
   pageTitle="Rota expressa locais | Microsoft Azure"
   description="Este artigo fornece uma visão geral detalhada dos locais onde os serviços são oferecidos e como se conectar a regiões Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>Rota expressa parceiros e aos locais

As tabelas neste artigo fornecem informações sobre a rota expressa conectividade provedores, rota expressa cobertura geográfica, serviços de nuvem da Microsoft com suporte sobre rota expressa e rota expressa SIs (integradores).

## <a name="partners"></a>Rota expressa provedores de conectividade

Rota expressa é suportada em todas as regiões Azure e locais. O mapa a seguir fornece uma lista de regiões Azure e rota expressa locais. Rota expressa locais se referir aos onde Microsoft correspondentes com vários provedores de serviço.

![Mapa de localização][0]

Você terá acesso aos serviços Azure em todas as regiões dentro de uma região geopolíticas se você estiver conectado a pelo menos um local de rota expressa dentro da região geopolíticas. A tabela a seguir fornece um mapa de regiões Azure para locais de rota expressa dentro de uma região geopolíticas.

|**Região geopolíticas**|**Regiões Azure**|**Rota expressa locais**|
|---|---|---|
|**América do Norte**|Leste EUA, oeste EUA, Leste dos EUA 2, centro dos EUA, Centro Sul dos EUA, Centro Norte dos EUA, Canadá Central, Canadá Leste|Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, Nova York, Seattle, vale do Silício, Washington DC, Montreal +, cidade de Quebec +, curitiba|
|**América do Sul**|Brasil Sul|São Paulo|
|**Europa**|Europa do Norte, Europa Ocidental, Reino Unido Oeste, Sul do Reino Unido|Amsterdam, Dublin, Londres, Newport(Wales) +, Paris|
|**Ásia**|Leste Asiático, Sudeste Asiático|Hong Kong, Cingapura|
|**Japão**|Japão Oeste Japão Leste|Osaka, Tóquio|
|**Austrália**|Austrália Sudeste, Austrália Oriental|Melbourne, Sidney|
|**Índia**|Índia Oeste, Índia Central, Índia Sul|Chennai, Mumbai|



A tabela a seguir fornece informações sobre regiões e limites geopolíticas para nuvens nacionais.

|**Região geopolíticas**|**Regiões Azure**|**Rota expressa locais**|
|---|---|---|---|
|**Nuvem do governo dos EUA**|EUA Gov Iowa, EUA Gov Virgínia|Chicago, Dallas, Nova York, Washington DC|
|**China**|China Norte, China Leste|Pequim, Shanghai|
|**Alemanha**|Alemanha Central, Alemanha Oriental|Berlim, Frankfurt|


Conectividade entre regiões geopolíticas não é suportada no SKU ExpressRoute padrão. Você precisará habilitá-lo rota expressa premium dar suporte a conectividade global. Não há suporte para a conectividade aos ambientes de nuvem nacional. Você pode trabalhar com seu provedor de conectividade se tal uma necessidade surgir.


## <a name="connectivity-provider-locations"></a>Locais de provedor de serviços de conectividade

> [AZURE.SELECTOR]
[Locais pelo provedor](expressroute-locations.md#connectivity-provider-locations)
[provedores por local](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Azure de produção

| **Provedor de serviços**  |**Microsoft Azure** | **CRM Online e do Office 365** | **Locais** |
|-----------------------|--------------------|----------------|---------------|
| **AARNet** | Com suporte | Com suporte | Melbourne, Sidney |
| **[Redes Aryaka]( http://www.aryaka.com/)** | Com suporte | Com suporte | Tóquio Amsterdam, vale do Silício, Cingapura, Washington DC |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Com suporte | Com suporte | Amsterdam, Chicago, Dallas, Londres, vale do Silício, Cingapura, Sidney, Washington DC |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Com suporte | Com suporte | Amsterdam, Hong Kong, Londres, vale do Silício, Cingapura, Sidney, Tóquio, Washington DC |
|**CenturyLink** | Em breve | Em breve| Vale do silício |
|**China Telecom Global** | Com suporte | Sem suporte | Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Com suporte | Em breve | Curitiba de Dallas, Montreal +, |
| **[COLT]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Com suporte | Com suporte | Amsterdam, Dublin, Londres, Tóquio |
| **Comcast** | Com suporte | Com suporte | Chicago, vale do Silício, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Com suporte | Com suporte | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Com suporte | Com suporte | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Angeles, Melbourne, Nova York, Osaka, Paris +, são Paulo, Seattle, vale do Silício, Cingapura, Sidney, Tóquio, Curitiba, Washington DC |
| **euNetworks** |  Com suporte | Com suporte | Amsterdam |
| **GÉANT** | Com suporte | Com suporte | Amsterdam |
| **[Internet iniciativa Japão Inc - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Com suporte | Com suporte | Osaka, Tóquio |
| **[InterCloud]( https://www.intercloud.com/)** | Com suporte | Com suporte | Amsterdam, Londres, Cingapura, Washington DC |
| **Conectar-se de soluções de Internet - nuvem** | Com suporte | Com suporte | Amsterdam, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | Com suporte | Com suporte | Amsterdam, Londres, Paris |
| **Jisc** | Com suporte | Com suporte | Londres | 
| **[Comunicações de nível 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Com suporte | Com suporte | Amsterdam, Chicago, Dallas, Las Vegas +, Londres, Seattle, vale do Silício, Washington DC |
| **Megaport** | Com suporte | Com suporte | Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, Nova York, Seattle, Cingapura Sidney, Washington DC |
| **PARA MOUNTAIN** | Com suporte | Com suporte | Londres |
| **Dados da próxima geração** | Em breve | Em breve | Newport(Wales) + |
| **NEXTDC** | Com suporte | Com suporte | Melbourne, Sidney |
| **Comunicações de NTT** | Com suporte | Com suporte | Londres, Los Angeles, Osaka, Tóquio |
| **[Laranja]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Com suporte | Com suporte | Amsterdam, Hong Kong, Londres, vale do Silício, Cingapura Sidney, Washington DC |
| **Limitado PCCW Global** | Com suporte | Com suporte | Hong Kong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Com suporte | Com suporte | Cingapura |
| **Softbank** | Com suporte | Com suporte | Osaka, Tóquio | 
| **[Comunicações de Tata](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Com suporte | Com suporte | Amsterdam, Madras, Hong Kong, Londres, Bombaim, vale do Silício, Cingapura Washington DC |
| **[Grupo de TeleCity]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Com suporte | Com suporte | Amsterdam, Dublin, Londres |
| **Telefonica** | Com suporte | Com suporte | São Paulo |
| **Telenor** | Com suporte | Com suporte | Amsterdam, Londres |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Com suporte | Com suporte | Melbourne, Sidney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Com suporte | Com suporte | Amsterdam, Hong Kong, Londres, vale do Silício, Cingapura, Sidney, Tóquio, Washington DC |
| **Vodafone** | Com suporte | Sem suporte | Londres | 
| **[Grupo de Zayo]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Com suporte | Com suporte | Chicago, Los Angeles, Nova York, vale do Silício, Curitiba, Washington DC |

 **+**Indica em breve

### <a name="national-cloud-environments"></a>Ambientes de nuvem nacional

#### <a name="us-government-cloud"></a>Nuvem do governo dos EUA

| **Provedor de serviços**  |**Microsoft Azure** | **Office 365** | **Locais** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Com suporte | Com suporte | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Com suporte | Com suporte | Chicago, Dallas, Nova York, Washington DC |
| **[Comunicações de nível 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Com suporte | Com suporte | Chicago, Nova York +, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Com suporte | Com suporte | Chicago, Dallas +, Nova York, Washington DC |

#### <a name="china"></a>China

| **Provedor de serviços**  |**Microsoft Azure** | **Office 365** | **Locais** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Com suporte | Sem suporte | Pequim, Shanghai|
Para saber mais, consulte [rota expressa na China](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Alemanha

| **Provedor de serviços**  |**Microsoft Azure** | **Office 365** | **Locais** |
|-----------------------|--------------------|----------------|---------------|
| **[COLT]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Com suporte | Sem suporte | Berlim +, Frankfurt|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Com suporte | Sem suporte | Frankfurt|
| **e shelter** | Com suporte | Sem suporte | Berlim|
| **Interxion** | Com suporte | Sem suporte | Frankfurt|

## <a name="nonpartners"></a>Conectividade por meio de provedores de serviço não listado

Se o seu provedor de conectividade não estiver listado nas seções anteriores, você ainda pode criar uma conexão.

- Verifique com seu provedor de conectividade para ver se eles estiverem conectados a qualquer uma das trocas na tabela acima. Você pode verificar os links a seguir para obter mais informações sobre serviços oferecidas por provedores de exchange. Vários provedores de conectividade já estão conectados a trocas de Ethernet.

    - [Nuvem de Equinix Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Ter o seu provedor de conectividade estender sua rede para o local de correspondência de preferência.
    - Certifique-se de que seu provedor de conectividade estende a conectividade de maneira altamente disponível para que sejam sem pontos únicos de falha.
- Solicite um circuito de rota expressa com o exchange como seu provedor de conectividade para se conectar à Microsoft.
    - Siga as etapas em [criar um circuito de rota expressa](expressroute-howto-circuit-classic.md) para configurar a conectividade.

|**Provedor de serviços corporativos de conectividade**|**Exchange**|**Locais**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Cingapura|
|**Comunicações de Alasca**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|Nova York, Washington DC|
|**[Comunicações de XO](http://www.xo.com/)**|Equinix|Vale do silício|


## <a name="expressroute-system-integrators"></a>Rota expressa integradores

Habilitar conectividade particular para atender às suas necessidades pode ser desafiador, com base na escala de sua rede. Você pode trabalhar com qualquer um dos integradores listados na tabela a seguir para ajudá-lo com integração a rota expressa.

|**Integrador**|**Continente**|
|---|---|
|**[Inc Avanade.](http://www.avanade.com/)**| Ásia, Europa, EUA |
|**[Soluções de dotnet](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Serviços profissionais de Equinix](http://www.equinix.com/services/consulting/)**|CONOSCO|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Ásia |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | CONOSCO |
|**[Liderança de projeto](http://www.projectleadership.net/azure)** | CONOSCO |

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a rota expressa, consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md).
- Certifique-se de que todos os pré-requisitos forem atendidos. Consulte [rota expressa pré-requisitos](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
