<properties 
   pageTitle="Conexão de híbrido com 2 camadas aplicativo | Microsoft Azure"
   description="Saiba como implantar dispositivos virtuais e UDR para criar um ambiente de aplicativo de várias camadas no Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/05/2016"
   ms.author="jdial" />

# <a name="virtual-appliance-scenario"></a>Cenário de dispositivo virtual

Um cenário comum entre cliente Azure maior é a necessidade de fornecer um aplicativo de duas camadas exposto na Internet, permitindo acesso no nível de volta de um data center local. Este documento apresentará um cenário usando rotas de definidas pelo usuário (UDR), um Gateway VPN e dispositivos de rede virtual para implantar um ambiente de duas camadas que atende aos seguintes requisitos:

- Aplicativo Web deve estar acessível somente pela Internet pública.
- Servidor Web que hospeda o aplicativo deve ser capaz de acessar um servidor de aplicativo de back-end.
- Todo o tráfego da Internet para o aplicativo da web deve passar por um dispositivo virtual do firewall. Este dispositivo virtual será usado para somente o tráfego de Internet.
- Todo o tráfego indo para o servidor de aplicativo deve passar por um dispositivo virtual do firewall. Este dispositivo virtual será usado para o acesso ao servidor back-end end e provenientes Rede local por meio de um Gateway VPN.
- Os administradores devem poder gerenciar os dispositivos de firewall virtual de seus computadores locais, usando um terceiro firewall usado exclusivamente para fins de gerenciamento de dispositivo virtual.

Este é um cenário de DMZ padrão com DMZ e uma rede protegida. Esse cenário pode ser construído no Azure usando NSGs, dispositivos virtuais de firewall ou uma combinação de ambos. A tabela a seguir mostra alguns dos prós e contras entre NSGs e dispositivos virtuais do firewall.

||Profissionais|Contras|
|---|---|---|
|NSG|Nenhum custo. <br/>Integrado ao Azure RBAC. <br/>Regras podem ser criadas em modelos de ARM.|Complexidade poderia variar em ambientes maiores. |
|Firewall|Controle total sobre plano de dados. <br/>Gerenciamento central por meio do console do firewall.|Custo de dispositivo de firewall. <br/>Não integrado ao Azure RBAC.|

A solução a seguir usa dispositivos virtuais de firewall para implementar um cenário de rede DMZ/protegido.

## <a name="considerations"></a>Considerações

Você pode implantar o ambiente explicado acima no Azure usando diferentes recursos disponíveis hoje, da seguinte maneira.

- **Rede Virtual (VNet)**. Um VNet Azure funciona de maneira semelhante a uma rede local e pode ser segmentada em uma ou mais sub-redes para fornecer isolamento de tráfego e separação de preocupações.
- **Dispositivo Virtual**. Vários parceiros fornecem dispositivos virtuais no Azure Marketplace que podem ser usados para os três firewalls descritos acima. 
- **Rotas (UDR) definidas pelo usuário**. Tabelas de rota podem conter UDRs usados por uma rede Azure para controlar o fluxo de pacotes dentro de uma VNet. Estas tabelas de rota podem ser aplicadas a sub-redes. Um dos recursos mais recentes do Azure é a capacidade de aplicar uma tabela de roteiro para a GatewaySubnet, fornecendo a capacidade de encaminhar todo o tráfego proveniente em VNet o Azure uma conexão híbrido para um dispositivo virtual.
- **Encaminhamento de IP**. Por padrão, o mecanismo de rede Azure encaminhar pacotes placas de interface de rede virtual (NICs) somente se o endereço IP de destino do pacote corresponde ao endereço IP da NIC. Portanto, se um UDR define que um pacote deve ser enviado para um determinado dispositivo virtual, o mecanismo de rede Azure deve cancelar esse pacote. Para garantir que o pacote foi entregue para uma máquina virtual (no caso um dispositivo virtual) que não é o destino real para o pacote, você precisa habilitar o encaminhamento de IP para o dispositivo virtual.
- **Grupos de segurança de rede (NSGs)**. O exemplo a seguir não faça uso das NSGs, mas você pode usar NSGs aplicadas ao sub-redes e/ou NICs nesta solução para filtrar ainda mais o tráfego e sair essas sub-redes e placas de rede.


![Conectividade IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Neste exemplo, há uma assinatura que contém o seguinte:

- 2 grupos de recursos não é mostrados no diagrama. 
    - **ONPREMRG**. Contém todos os recursos necessários para simular uma rede local.
    - **AZURERG**. Contém todos os recursos necessários para o ambiente de rede virtual Azure. 
- Um VNet denominado **onpremvnet** usado para simular uma análise de local segmentado conforme listado abaixo.
    - **onpremsn1**. Sub-rede que contém uma máquina virtual (VM) executando Ubuntu para simular um servidor local.
    - **onpremsn2**. Sub-rede que contém uma máquina virtual executando Ubuntu para simular um computador local usado por um administrador.
- Não há um dispositivo de firewall virtual chamado **OPFW** em **onpremvnet** usado para manter um túnel para **azurevnet**.
- Um VNet chamado **azurevnet** segmentado conforme listado abaixo.
    - **azsn1**. Sub-rede de firewall externo usado exclusivamente para o firewall externo. Todo o tráfego de Internet será entrar nesta sub-rede. Esta sub-rede contém apenas uma NIC vinculada ao firewall externo.
    - **azsn2**. Sub-rede de front-end hospedando uma máquina virtual executando como um servidor web que será acessado da Internet.
    - **azsn3**. Sub-rede de back-end hospedando uma máquina virtual executando um servidor de aplicativo de back-end serem acessados pelo servidor web front-end.
    - **azsn4**. Sub-rede de gerenciamento usado exclusivamente para fornecer acesso de gerenciamento para todos os dispositivos virtuais de firewall. Esta sub-rede contém apenas uma NIC para cada dispositivo virtual do firewall usada na solução.
    - **GatewaySubnet**. Sub-rede de conexão do Azure híbrido necessário para rota expressa e Gateway VPN para fornecer conectividade entre VNets do Azure e outras redes. 
- Existem 3 dispositivos virtuais do firewall na rede **azurevnet** . 
    - **AZF1**. Firewall externo exposto à Internet pública, usando um recurso de endereço IP público do Azure. Você precisa ter um modelo do Marketplace, ou diretamente do fornecedor de seu dispositivo, que disposições um dispositivo virtual 3-NIC.
    - **AZF2**. Firewall interno usado para controlar o tráfego entre **azsn2** e **azsn3**. Isso também é um dispositivo virtual 3-NIC.
    - **AZF3**. Firewall de gerenciamento acessível aos administradores do data center local e conectado a uma sub-rede de gerenciamento usada para gerenciar todos os dispositivos de firewall. Você pode encontrar 2-NIC modelos de dispositivo virtual no mercado ou solicitar uma diretamente do fornecedor de seu dispositivo.

## <a name="user-defined-routing-udr"></a>Roteamento (UDR) definidas pelo usuário

Cada sub-rede no Azure pode ser vinculado a uma tabela UDR usada para definir como o tráfego iniciadas sub-rede é roteada. Se nenhuma UDRs estiverem definidos, o Azure usa rotas padrão para permitir o tráfego flua de uma sub-rede para outra. Para entender melhor UDRs, visite [o que são rotas de definidas pelo usuário e encaminhamento de IP](./virtual-networks-udr-overview.md#ip-forwarding).

Para garantir a comunicação é feita através do aparelho de firewall correto, com base no último requisito acima, você precisa criar a seguinte tabela de rota contendo UDRs em **azurevnet**.

### <a name="azgwudr"></a>azgwudr

Neste cenário, o único tráfego fluindo de locais para Azure será usado para gerenciar os firewalls conectando-se ao **AZF3**e que o tráfego deve percorrer o firewall interno, **AZF2**. Portanto, somente uma rota é necessária na **GatewaySubnet** conforme mostrado abaixo.

|Destino|Próximo salto|Explicação|
|---|---|---|
|10.0.4.0/24|10.0.3.11|Permite que o tráfego local atinja firewall gerenciamento **AZF3**|

### <a name="azsn2udr"></a>azsn2udr

|Destino|Próximo salto|Explicação|
|---|---|---|
|10.0.3.0/24|10.0.2.11|Permite que o tráfego da sub-rede de back-end que hospeda o servidor de aplicativo por meio de **AZF2**|
|0.0.0.0/0|10.0.2.10|Permite que todo o tráfego ser roteada pelos **AZF1**|

### <a name="azsn3udr"></a>azsn3udr

|Destino|Próximo salto|Explicação|
|---|---|---|
|10.0.2.0/24|10.0.3.10|Permite que o tráfego **azsn2** flua de servidor de aplicativo para o servidor de Web por meio de **AZF2**|

Você também precisa criar tabelas de rotas para as sub-redes no **onpremvnet** para simular data center local.

### <a name="onpremsn1udr"></a>onpremsn1udr

|Destino|Próximo salto|Explicação|
|---|---|---|
|192.168.2.0/24|192.168.1.4|Permite que o tráfego **onpremsn2** por meio de **OPFW**|

### <a name="onpremsn2udr"></a>onpremsn2udr

|Destino|Próximo salto|Explicação|
|---|---|---|
|10.0.3.0/24|192.168.2.4|Permite que o tráfego da sub-rede foi feito no Azure por meio de **OPFW**|
|192.168.1.0/24|192.168.2.4|Permite que o tráfego **onpremsn1** por meio de **OPFW**|

## <a name="ip-forwarding"></a>Encaminhamento de IP 

UDR e encaminhamento de IP são recursos que você pode usar em combinação para permitir que dispositivos virtuais a ser usado para controlar o fluxo de tráfego em um VNet do Azure.  Um dispositivo virtual é nada mais do que uma máquina virtual que executa um aplicativo usado para lidar com o tráfego de rede de alguma forma, como um firewall ou um dispositivo NAT.

Este dispositivo virtual máquina virtual deve ser capaz de receber tráfego de entrada não endereçada a mesmo. Para permitir que uma máquina virtual para receber tráfego endereçado para outros destinos, você deve habilitar o encaminhamento de IP para a máquina virtual. Este é um Azure definindo, não uma configuração no sistema operacional convidado. Seu aparelho virtual ainda precisa executar algum tipo de aplicativo para lidar com o tráfego de entrada e encaminhá-lo adequadamente.

Para saber mais sobre o encaminhamento de IP, visite [o que são rotas de definidas pelo usuário e encaminhamento de IP](./virtual-networks-udr-overview.md#ip-forwarding).

Como exemplo, imagine que você tenha a seguinte configuração em um vnet Azure:

- Sub-rede **onpremsn1** contém uma máquina virtual chamada **onpremvm1**.
- Sub-rede **onpremsn2** contém uma máquina virtual chamada **onpremvm2**.
- Um dispositivo virtual chamado **OPFW** está conectado à **onpremsn1** e **onpremsn2**.
- Uma rota de definidas pelo usuário vinculada à **onpremsn1** Especifica que todo o tráfego para **onpremsn2** deve ser enviado ao **OPFW**.

Neste ponto, se **onpremvm1** tenta estabelecer uma conexão com **onpremvm2**, o UDR será usado e tráfego será enviado para **OPFW** como o salto seguinte. Tenha em mente que o destino do pacote real não está sendo alterado, ele ainda diz **onpremvm2** é o destino. 

Sem IP Forwarding habilitado para **OPFW**, a lógica de rede virtual Azure será descarta os pacotes, desde que ele permita somente pacotes sejam enviadas para uma máquina virtual se o endereço IP da VM é o destino para o pacote.

Com o encaminhamento de IP, a lógica de rede virtual Azure encaminhará os pacotes para OPFW, sem alterar seu endereço de destino original. **OPFW** deve lidar com os pacotes e determinar o que fazer com eles.

Para o cenário acima para trabalhar, você deve habilitar encaminhamento de IP nas NICs para **OPFW**, **AZF1**, **AZF2**e **AZF3** que são usadas para roteamento de (todas as NICs exceto aqueles vinculado à sub-rede gerenciamento). 

## <a name="firewall-rules"></a>Regras de firewall

Conforme descrito acima, encaminhamento de IP somente garante pacotes são enviados para os dispositivos virtuais. Seu aparelho ainda precisa decidir o que fazer com os pacotes. No cenário acima, você precisará criar as seguintes regras em seus dispositivos:

### <a name="opfw"></a>OPFW

OPFW representa um dispositivo local que contém as seguintes regras:

- **Roteiro**: todo o tráfego para 10.0.0.0/16 (**azurevnet**) deve ser enviado através de túnel **ONPREMAZURE**.
- **Diretiva**: permitir todo o tráfego de bidirecional entre **port2** e **ONPREMAZURE**.
 
### <a name="azf1"></a>AZF1

AZF1 representa um dispositivo virtual Azure contendo as seguintes regras:

- **Diretiva**: permitir todo o tráfego de bidirecional entre **port1** e **port2**.

### <a name="azf2"></a>AZF2

AZF2 representa um dispositivo virtual Azure contendo as seguintes regras:

- **Roteiro**: todo o tráfego para 10.0.0.0/16 (**onpremvnet**) deve ser enviado para o endereço IP de gateway Azure (isto é 10.0.0.1) por meio de **port1**.
- **Diretiva**: permitir todo o tráfego de bidirecional entre **port1** e **port2**.

## <a name="network-security-groups-nsgs"></a>Grupos de segurança de rede (NSGs)

Neste cenário, NSGs não estão sendo usados. No entanto, você pode aplicar NSGs a cada sub-rede para restringir o tráfego de entrada e de saída. Por exemplo, você pode aplicar as seguintes regras NSG à sub-rede FW externa.

**Entrada**

- Permitir todo o tráfego TCP da Internet para a porta 80 em qualquer máquina virtual na sub-rede.
- Nega todo o tráfego da Internet.

**Saída**
- Nega todo o tráfego de Internet.

## <a name="high-level-steps"></a>Etapas de nível altas

Para implantar esse cenário, siga as etapas de alto níveis abaixo.

1.  Faça login à sua assinatura do Azure.
2.  Se você quiser implantar um VNet para simular a rede local, provisione os recursos que fazem parte de **ONPREMRG**.
3.  Provisione os recursos que fazem parte de **AZURERG**.
4.  Provisione o túnel de **onpremvnet** para **azurevnet**.
5.  Depois que todos os recursos são provisionados, faça logon no **onpremvm2** e ping 10.0.3.101 para testar a conectividade entre **onpremsn2** e **azsn3**.
