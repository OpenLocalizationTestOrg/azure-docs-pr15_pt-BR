<properties 
   pageTitle="Sobre dispositivos VPN para conexões-to-Site VPN Gateway para redes virtuais Azure | Microsoft Azure"
   description="Este artigo discute dispositivos VPN e parâmetros de IPsec para conexões do Gateway de VPN S2S e contém links para instruções de configuração e exemplos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Sobre dispositivos VPN para conexões do Gateway VPN-to-Site

Um dispositivo VPN é necessária para configurar uma conexão de VPN to-Site (S2S). Conexões de-to-Site podem ser usadas para criar uma solução híbrida ou sempre que desejar uma conexão segura entre sua rede local e sua rede virtual. Este artigo discute dispositivos VPN compatíveis e parâmetros de configuração. 

>[AZURE.NOTE] Ao configurar uma conexão de-to-Site, um endereço de IP IPv4 público é necessário para o seu dispositivo VPN.                                                                                                                                                                               

Se seu dispositivo não aparecer na tabela [dispositivos VPN validada](#devicetable) , consulte a seção de [dispositivos VPN não validada](#additionaldevices) deste artigo. É possível que o seu dispositivo ainda pode funcionar com o Azure. Para obter suporte de dispositivo VPN, entre em contato com o fabricante do seu dispositivo.

**Itens nota ao exibir as tabelas:**

- Houve uma alteração de terminologia para roteamento estático e dinâmico. Você provavelmente executará em ambos os termos. Não há nenhuma alteração de funcionalidade, somente os nomes estão sendo alterado.
    - Roteamento estático = PolicyBased
    - Roteamento dinâmico = RouteBased
- Especificações do gateway de alto desempenho VPN e gateway RouteBased VPN são os mesmos, a menos que seja observado o contrário. Por exemplo, os dispositivos VPN validados que são compatíveis com gateways RouteBased VPN também são compatíveis com o gateway do Azure alto desempenho VPN. 


## <a name="devicetable"></a>Dispositivos VPN validados 

Podemos ter validados um conjunto de dispositivos VPN padrão em parceria com fornecedores de dispositivo. Todos os dispositivos nas famílias dispositivo contidos na lista a seguir devem trabalhar com gateways VPN do Azure. Consulte [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) para verificar o tipo de gateway que você precisa criar para a solução que você deseja configurar. 

Para ajudar a configurar seu dispositivo VPN, consulte os links que correspondem a família de dispositivo apropriado. Para obter suporte de dispositivo VPN, entre em contato com o fabricante do seu dispositivo.



| **Fornecedor**                      | **Família de dispositivo**                                        | **Versão mínima do sistema operacional**                             | **PolicyBased**                                                                                                                                                                                                             | **RouteBased**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis                  | Roteadores VPN de série de AR                                    | 2.9.2                                              | Em breve                                                                                                                                                                                                                                          | Não é compatível                                                                                                                                                                                               |
| Barracuda redes, Inc.        | Série Barracuda NextGen Firewall F             | PolicyBased: 5.4.3, RouteBased: 6.2.0  | [Instruções de configuração](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Instruções de configuração](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda redes, Inc.        |  Série Barracuda NextGen Firewall X                 | Barracuda Firewall 6.5 | [Barracuda Firewall](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Não é compatível                                                                                                                                                                                               |
| Brocado                         | VRouter Vyatta 5400                                      | Roteador virtual 6.6R3 GA                            | [Instruções de configuração](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | Não é compatível                                                                                                                                                                                               |
| Ponto de verificação                     | Gateway de segurança                                         | R75.40, R75.40VS                                     | [Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Instruções de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Exemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | Não é compatível                                                                                                                                                                                               |
| Cisco                           | RECUPERAÇÃO AUTOMATIZADA DO SISTEMA                                                      | IOS 15.1 (PolicyBased), IOS 15.2 (RouteBased)                | [Exemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Exemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (PolicyBased), IOS 15.1 (RouteBased *)               | [Exemplos de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Exemplos de Cisco *](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | NetScaler MPX, SDX, VPX      |10.1 e acima                                           | [Instruções de integração](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | Não é compatível                                                                                                                                                                                               |
| Dell SonicWALL                  | Série TZ, NSA, série SuperMassive, classe E NSA série | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6. x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Instruções - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instruções - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Instruções - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instruções - SonicOS 5,9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | Série de IP grande                                 |           12.0                                            | [Instruções de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | [Instruções de configuração](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling)                                                                                                                                                                                         |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Instruções de configuração](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Instruções de configuração](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Japão iniciativa de Internet (IIJ) | Série SEIL                                              | SEIL / X 4.60, SEIL/B1 4.60, SEIL/x86 3,20            | [Instruções de configuração](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | Não é compatível                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (PolicyBased), JunOS 11.4 (RouteBased)            | [Exemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Exemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | Série de J                                                 | JunOS 10.4r9 (PolicyBased), JunOS 11.4 (RouteBased)          | [Exemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Exemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 (PolicyBased e RouteBased)                  | [Exemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Exemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 (PolicyBased e RouteBased)                  | [Exemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Exemplos de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Serviço de acesso remoto e de roteamento                        | Windows Server 2012                                | Não é compatível                                                                                                                                                                                                                                       | [Amostras da Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| Abrir Systems AG | Gateway de segurança de controle de missão | N/D | [Guia de instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Guia de instalação](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Em breve)                                                                                                                                                                                                                                        | Não é compatível                                                                                                                                                                                               |
| Redes de Palo Alto              | Todos os dispositivos executando o sistema operacional o PANORÂMICA     | PANORÂMICA-OS 6.1.5 ou posterior (PolicyBased), PANORÂMICA-OS 7.0.5 ou posterior (RouteBased)       | [Instruções de configuração]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Instruções de configuração](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| WatchGuard                      | Todos os                                                      | Fireware XTM v11.x                                 | [Instruções de configuração](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | Não é compatível                                                                                                                                                                                               |

(*) Roteadores ISR 7200 série só oferece suporte a VPNs PolicyBased.

## <a name="additionaldevices"></a>Dispositivos VPN não validada

Se você não vir seu dispositivo listado na tabela dispositivos VPN validada, ele ainda pode trabalhar com uma conexão to-Site. Verifique se seu dispositivo VPN atende aos requisitos mínimos descritos na seção requisitos de Gateway do artigo [Sobre Gateways de VPN](vpn-gateway-about-vpngateways.md#gateway-requirements) . Dispositivos os requisitos mínimos de reunião também devem funcionar bem com gateways VPN. Contate o fabricante do dispositivo para obter instruções adicionais de configuração e suporte.


## <a name="editing-device-configuration-samples"></a>Exemplos de configuração de dispositivo de edição

Depois de baixar o exemplo de configuração de dispositivo VPN fornecidos, você precisará substituir alguns dos valores para refletir as configurações para seu ambiente. 

**Para editar uma amostra:**

1. Abra o exemplo usando o bloco de notas. 
1. Pesquisar e substituir todas as sequências de <*texto*> com os valores que pertencem aos seu ambiente. Certifique-se de incluir < e >. Quando um nome é especificado, o nome que você selecionar deve ser exclusivo. Se um comando não funcionar, consulte a documentação do fabricante de dispositivo.

| **Texto de exemplo**                | **Alterar para**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | Seu nome escolhido para esse objeto. Exemplo: myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | Seu nome escolhido para esse objeto. Exemplo: myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | Seu nome escolhido para esse objeto. Exemplo: myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | Seu nome escolhido para esse objeto. Exemplo: myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | Seu nome escolhido para esse objeto. Exemplo: myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Especifique o intervalo. Exemplo: 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Especifique a máscara de sub-rede. Exemplo: 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Especifique o intervalo de local. Exemplo: 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Especifique a máscara de sub-rede local. Exemplo: 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Essas informações específicas à sua rede virtual e está localizado no Portal de gerenciamento como **endereço IP do Gateway**. |
| &lt;SP_PresharedKey&gt;                | Essas informações específicas à sua rede virtual e estão localizadas no Portal de gerenciamento como gerenciar chave.          |



## <a name="ipsec-parameters"></a>Parâmetros IPsec

>[AZURE.NOTE] Embora os valores listados na tabela a seguir são suportados pelo Gateway VPN Azure, atualmente não há nenhuma maneira de especificar ou selecione uma combinação específica do Gateway VPN Azure. Você deve especificar as restrições do dispositivo VPN local. Além disso, você deve grampo MSS em 1350.

### <a name="ike-phase-1-setup"></a>Configuração de IKE fase 1

| **Propriedade**                                       | **PolicyBased** | **Gateway RouteBased e padrão ou alto desempenho VPN** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Versão de IKE                                        | IKEv1                          | IKEv2                                                            |
| Grupo Diffie-Hellman                               | Grupo 2 (1024 bits)             | Grupo 2 (1024 bits)                                               |
| Método de autenticação                              | Chave pré-compartilhada                 | Chave pré-compartilhada                                                   |
| Algoritmos de criptografia                              | AES256 AES128 3DES             | AES256 3DES                                                      |
| Algoritmo de hash                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| Fase 1 de segurança associação (SA) vida útil (hora) | 28.800 segundos                 | 10,800 segundos                                                   |


### <a name="ike-phase-2-setup"></a>Configuração de fase 2 da IKE

| **Propriedade**                                                             | **PolicyBased**                 | **Gateway RouteBased e padrão ou alto desempenho VPN**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Versão de IKE                                                              | IKEv1                                          | IKEv2                                                              |
| Algoritmo de hash                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| Fase 2 segurança associação (SA) vida útil (hora)                        | 3.600 segundos                                  | 3.600 segundos                                                                  |
| Fase 2 segurança associação (SA) vida útil (taxa de transferência)                  | 102,400,000 KB                                 | -                                                                  |
| Criptografia de SA IPsec & autenticação oferece (na ordem de preferência) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/D | Consulte a *associação de segurança (SA) do Gateway RouteBased IPsec oferece* (abaixo) |
| Sigilo)                                            | Não                                             | Nenhum (*)|
| Detecção de ponto inativo                                                      | Sem suporte                                  | Com suporte                                                          |

(*) Gateway Azure como respondedor IKE pode aceitar sigilo DH grupo 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Associação de segurança (SA) do Gateway RouteBased IPsec oferece

A tabela a seguir lista IPsec SA criptografia e autenticação oferece. Ofertas estão listadas na ordem de preferência que a oferta é apresentada ou aceito.

| **Criptografia de SA IPsec e ofertas de autenticação** | **Gateway Azure como iniciador**                               | **Gateway Azure como respondedor**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | AH SHA1 com ESP AES_128 com HMAC nulo                      |
| 5                                                 | AH SHA1 com ESP AES_256 com HMAC nulo                      | AH SHA1 com ESP 3_DES com HMAC nulo                        |
| 6                                                 | AH SHA1 com ESP AES_128 com HMAC nulo                      | AH MD5 com ESP 3_DES com HMAC nulo, nenhuma vida útil proposto |
| 7                                                 | AH SHA1 com ESP 3_DES com HMAC nulo                        | AH SHA1 com ESP 3_DES SHA1, nenhuma vida útil                    |
| 8                                                 | AH MD5 com ESP 3_DES com HMAC nulo, nenhuma vida útil proposto | AH MD5 com ESP 3_DES MD5, nenhuma vida útil                     |
| 9                                                 | AH SHA1 com ESP 3_DES SHA1, nenhuma vida útil                    | ESP DES MD5                                                  |
| 10                                                | AH MD5 com ESP 3_DES MD5, nenhuma vida útil                     | ESP DES SHA1, nenhuma vida útil                                   |
| 11                                                | ESP DES MD5                                                  | AH SHA1 com ESP DES nulo HMAC, sem tempos de vida proposto        |
| 12                                                | ESP DES SHA1, nenhuma vida útil                                   | AH MD5 com ESP DES nulo HMAC, sem tempos de vida proposto        |
| 13                                                | AH SHA1 com ESP DES nulo HMAC, sem tempos de vida proposto        | AH SHA1 com ESP DES SHA1, nenhuma vida útil                      |
| 14                                                | AH MD5 com ESP DES nulo HMAC, sem tempos de vida proposto        | AH MD5 com ESP DES MD5, nenhuma vida útil                       |
| 15                                                | AH SHA1 com ESP DES SHA1, nenhuma vida útil                      | ESP SHA, nenhuma vida útil                                        |
| 16                                                | AH MD5 com ESP DES MD5, nenhuma vida útil                       | ESP MD5, nenhuma vida útil                                        |
| 17                                                | -                                                            | AH SHA, nenhuma vida útil                                         |
| 18                                                | -                                                            | AH MD5, nenhuma vida útil                                        |


- Você pode especificar criptografia IPsec ESP nulo com RouteBased e alto desempenho VPN gateways. Criptografia com base em nulo não oferece proteção para dados em trânsito e só deve ser usada ao máximo latência de produtividade e mínimo é necessária.  Os clientes podem optar por usar isso em cenários de comunicação de VNet para VNet, ou quando a criptografia está sendo aplicada em outro lugar na solução.

- Para conectividade entre locais através da Internet, use as configurações de gateway padrão Azure VPN com criptografia e algoritmos de hash listados nas tabelas acima para garantir a segurança da comunicação crítica.





