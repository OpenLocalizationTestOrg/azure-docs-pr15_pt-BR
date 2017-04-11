<properties
   pageTitle="Implementar uma arquitetura de rede híbrida com VPN Azure e local | Microsoft Azure"
   description="Como implementar uma arquitetura de rede to-site seguro que abrange uma rede virtual Azure e uma rede local conectado usando uma VPN."
   services=""
   documentationCenter="na"
   authors="RohitSharma-pnp"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="roshar"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-and-on-premises-vpn"></a>Implementar uma arquitetura de rede híbrida com o Azure e VPN de local

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve um conjunto de práticas para estender uma rede local no Azure usando uma rede privada virtual (VPN) de-to-site. O tráfego flui entre a rede local e uma rede Virtual do Azure (VNet) através de um túnel VPN IPSec. Essa arquitetura é adequada para aplicativos híbridos com as seguintes características:

- Partes do aplicativo executados local enquanto outras pessoas executam no Azure.

- O tráfego entre hardware local e a nuvem é provavelmente será luz ou é útil trade latência pouco estendida para a flexibilidade e a capacidade de processamento da nuvem.

- A rede estendida constitui um sistema fechado. Há um caminho direto da Internet para o VNet do Azure.

- Os usuários se conectar à rede local para usar os serviços hospedados no Azure. A ponte entre a rede local e os serviços em execução no Azure é transparente para os usuários.

Exemplos de cenários que correspondam a este perfil:

- Aplicativos de linha de negócios usados dentro de uma organização, onde parte da funcionalidade foi migrado para a nuvem.

- Cargas de trabalho de laboratório/de teste de desenvolvimento.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos do Azure] [ resource-manager-overview] e clássico. Este diagrama usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir destaca os componentes nesta arquitetura:

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é na "rede híbrido - VPN" página.

![[0]][0]

- **Rede local.** Esta é uma rede de computadores e dispositivos, conectados por meio de uma rede local particular executando dentro de uma organização.

- **Dispositivo de VPN.** Este é um dispositivo ou serviço que fornece conectividade externa à rede local. O dispositivo de VPN pode ser um dispositivo de hardware ou pode ser uma solução de software como o roteamento e acesso remoto (RRAS) no Windows Server 2012.

> [AZURE.NOTE] Para obter uma lista de dispositivos de VPN com suporte e informações sobre como configurar dispositivos VPN selecionados para se conectar a um Gateway de VPN do Azure, consulte as instruções para o dispositivo apropriado na [lista de dispositivos VPN suportados pelo Azure][vpn-appliance].

- **Aplicativo de nuvem fileiras.** Este é o aplicativo hospedado no Azure. Ela pode incluir vários níveis, com várias sub-redes conectadas por meio de balanceadores de carga Azure. O tráfego em cada sub-rede sujeitas regras definidas usando [Grupos de segurança de rede do Azure (NSGs)][azure-network-security-group]. Para obter mais informações, consulte [Introdução ao Microsoft Azure segurança][getting-started-with-azure-security].

> [AZURE.NOTE] Este artigo descreve o aplicativo de nuvem como uma única entidade. Consulte [executando uma arquitetura de N camadas no Azure] [ implementing-a-multi-tier-architecture-on-Azure] para obter informações detalhadas.

- **[Rede virtual (VNet)][azure-virtual-network].** O aplicativo de nuvem e os componentes do gateway de VPN Azure residem no mesmo VNet.

- **[Gateway VPN Azure][azure-vpn-gateway].** O serviço de gateway VPN permite que você conectar o VNet à rede local por meio de um dispositivo de VPN. Para obter mais informações, consulte [conectar uma rede local para uma rede virtual do Microsoft Azure][connect-to-an-Azure-vnet]. O Gateway VPN consiste nos seguintes elementos:

  - **Gateway de rede virtual.** Este é um recurso que fornece um dispositivo VPN virtual para o VNet. Ele é responsável por rotear o tráfego da rede local para o VNet.

  - **Gateway de rede local.** Isso é uma abstração do aparelho VPN local. Tráfego de rede do aplicativo de nuvem à rede local é roteado por meio deste gateway.

  - **Conexão.** A conexão tem propriedades que especificam o tipo de conexão (IPSec) e a chave compartilhada com o aparelho de VPN local para criptografar o tráfego.

  - **Sub-rede do gateway.** O gateway de rede virtual é mantido em sua própria sub-rede, que está sujeito a requisitos diversos, conforme descrito na seção recomendações abaixo.

- **Balanceador de carga interno.** Tráfego de rede do Gateway VPN é roteado para o aplicativo de nuvem por meio de um balanceador de carga interno. O balanceador de carga está localizado na sub-rede front-end do aplicativo.

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência que você deve seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação.

### <a name="vnet-and-gateway-subnet"></a>Sub-rede VNet e Gateway

Crie um VNet do Azure para mantém os componentes na nuvem. Espaço de endereço do VNet Azure deve ser grande o suficiente para acomodar os endereços usados pelo VMs e sub-redes na VNet. Certifique-se de que o espaço de endereço VNet tem espaço suficiente para crescimento se VMs adicionais são provavelmente serão necessárias no futuro. Espaço de endereço da VNet não deve ser sobrepostos com a rede local. Por exemplo, o diagrama acima usa o endereço espaço 10.20.0.0/16 para o VNet.

Crie uma sub-rede denominada _GatewaySubnet_, com um intervalo de endereços do /27. Esta sub-rede é requerida pelo gateway de rede virtual e alocar 32 endereços a esta sub-rede ajudará a evitar que você está executando ao limitações de tamanho de gateway possíveis no futuro. Evite colocar essa sub-rede no meio de espaço de endereço. Uma prática recomendada é definir o espaço de endereço para a sub-rede de gateway na extremidade superior do espaço de endereço VNet. O exemplo mostrado no diagrama usa 10.20.255.224/27.  Um procedimento rápido para calcular o CIDR é da seguinte maneira:

1. Defina os bits variáveis no espaço de endereço do VNet como 1, até os bits sendo usado pelo sub-rede gateway, em seguida, defina os bits restantes como 0.

2. Converter os bits resultantes em decimal e expressá-lo como um espaço de endereço com o conjunto de comprimento de prefixo para o tamanho da sub-rede gateway.

Por exemplo, para um VNet com um intervalo de endereço IP de 10.20.0.0/16, aplicar etapa #1 acima se torna 10.20.0b11111111.0b11100000.  Convertendo que em decimal e expressá-lo como um espaço de endereço produz 10.20.255.224/27

> [AZURE.WARNING] Não implante outras máquinas virtuais ou instâncias de função à sub-rede gateway. Além disso, não atribua um NSG a esta sub-rede, como ele fará com que o gateway pare de funcionar.

### <a name="virtual-network-gateway"></a>Gateway de rede virtual

Aloca um endereço IP público para o gateway de rede virtual.

Criar o gateway de rede virtual na sub-rede Gateway e atribua-o endereço IP público alocado recentemente. Use o tipo de gateway que melhor atenda às suas necessidades e qual é habilitado por seu aparelho de VPN:

Criar um [gateway baseado em política] [ policy-based-routing] se você precisar controlar de perto como solicitações são roteadas. com base em critérios de política e prefixos de endereço. Gateways baseados em política usam roteamento estático e só funcionam com conexões to-site.

Criar um [gateway baseado em rota] [ route-based-routing] se você se conectar à rede local usando RRAS, oferecer suporte a conexões de vários locais ou região de cruz ou implementar conexões de VNet para VNet (incluindo rotas que percorrer várias VNets). Gateways baseados em rota usam roteamento dinâmico para tráfego direto entre redes. Eles podem suportar falhas no caminho de rede melhor do que rotas estáticas, porque ele podem tentar rotas alternativas. Gateways baseados em roteiro também podem reduzir a sobrecarga de gerenciamento, pois rotas não precisa ser atualizado manualmente quando altera de endereços de rede.

Para obter uma lista de dispositivos de VPN com suporte, consulte [dispositivos de sobre VPN para conexões do Gateway VPN - to-Site][vpn-appliances].

> [AZURE.NOTE] Depois que o gateway foi criado, você não pode alterar entre os tipos de gateway sem excluir e recriar o gateway.

Selecione o SKU de Gateway do Azure VPN que melhor atenda às suas necessidades de produtividade. Azure Gateway VPN está disponível em três SKUs mostrados na tabela a seguir. Cada SKU fornece produtividade diferente, [encargos são arrecadados] [ azure-gateway-charges] com base na quantidade de tempo que o gateway está provisionado e disponível.

| SKU | Transferência VPN | Encapsulamentos max IPSec|
|-----|----------------|------------------|
| Básico | 100 Mbps | 10 |
| Padrão | 100 Mbps | 10 |
| Alto desempenho | 200 Mbps | 30 |

> [AZURE.NOTE] O SKU básico não é compatível com rota expressa do Azure. Você pode [alterar o SKU] [ changing-SKUs] após o gateway foi criado.

Crie regras de roteamento para a sub-rede de gateway que tráfego de aplicativo de entrada direta do gateway balanceador de carga interno em vez permitindo solicitações passar diretamente VMs que implementam o aplicativo.

### <a name="on-premises-network-connection"></a>Conexão de rede local

Crie um gateway de rede local. Especifique o endereço IP público do aparelho de VPN local e o espaço de endereço da rede local. Observe que o aparelho de VPN local deve ter um endereço IP público que possa ser acessado pelo gateway de rede local no Azure VPN Gateway. O dispositivo VPN não pode ser localizado atrás de um dispositivo NAT.

Crie uma conexão de-to-site para o gateway de rede virtual e o gateway de rede local. Selecione o tipo de conexão-to-site (IPSec) e especifique a chave compartilhada. Criptografia de-to-site com o Gateway de VPN Azure baseia-se no protocolo IPSec, usando chaves pré-compartilhadas para autenticação. Você especificar a chave ao criar o Gateway de VPN do Azure. Você deve configurar o dispositivo de VPN executando o local com a mesma chave. Outros mecanismos de autenticação não são suportados no momento.

Certifique-se de que a infraestrutura de roteamento está configurada para encaminhar solicitações destinadas para endereços na VNet do Azure para o dispositivo VPN no local.

Abra qualquer portas exigidas pelo aplicativo nuvem na rede local.

Teste a conexão para verificar se:

- O dispositivo de VPN local corretamente rotear o tráfego para o aplicativo de nuvem por meio do Gateway de VPN do Azure.

- O VNet corretamente rotear o tráfego para a rede local.

- Tráfego proibido em ambas as direções é bloqueado corretamente.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Você pode obter escalabilidade vertical limitada movendo a partir da Basic ou SKUs de Gateway VPN padrão para o SKU de VPN alto desempenho.

Para VNets que espera um grande volume de tráfego VPN, considere distribuir as cargas de trabalho diferentes em separado VNets menores e configurando um Gateway VPN para cada um deles.

Você pode partição a VNet horizontalmente ou verticalmente. Para partição horizontalmente, mova algumas instâncias de máquina virtual de cada camada em sub-redes de VNet o novo. O resultado é que cada VNet tem a mesma estrutura e funcionalidade. Para partição verticalmente, reformular cada camada para dividir a funcionalidade em diferentes áreas lógicas (como a manipulação de pedidos, faturamento, gerenciamento de conta de cliente e assim por diante). Cada área funcional, em seguida, pode ser colocada em sua própria VNet.

Duplicar um controlador de domínio do Active Directory local na VNet e implementando o DNS no VNet, podem ajudar a reduzir alguns do tráfego de segurança e administrativo fluindo de locais na nuvem.

## <a name="availability-considerations"></a>Considerações de disponibilidade

Se você precisa garantir que a rede local permanecerão disponível para o Gateway de VPN do Azure, implemente um cluster de failover para o Gateway de VPN local.

Se sua organização tiver vários sites locais, criar [conexões de vários locais] [ vpn-gateway-multi-site] para um ou mais VNets do Azure. Essa abordagem requer roteamento dinâmico (baseado em rota), portanto, certifique-se de que o Gateway de VPN local oferece suporte a esse recurso.

Consulte [SLA para Gateway VPN] [ sla-for-vpn-gateway] para obter os detalhes sobre o SLA de Gateway VPN.

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

Monitorar as informações de diagnóstico de dispositivos de VPN de local. Esse processo depende os recursos fornecidos pelo dispositivo VPN. Por exemplo, se você estiver usando o roteamento e o serviço de acesso remoto no Windows Server 2012, você deve ativar [o registro em log RRAS][rras-logging].

Usar o [Diagnóstico de Gateway do Azure VPN] [ gateway-diagnostic-logs] para capturar informações sobre problemas de conectividade. Esses logs podem ser usados para controlar informações como a fonte e os destinos de conexão solicitações, qual protocolo foi usado e como a conexão foi estabelecida (ou por que a tentativa falhou).

Monitore os logs operacionais do Gateway VPN Azure usando os logs de auditoria disponíveis no portal do Azure. Logs separados estão disponíveis para o gateway de rede local, o gateway de rede Azure e a conexão. Essas informações podem ser usadas para controlar as alterações feitas para o gateway e podem ser útil se um gateway anteriormente funcionando parar de funcionar por algum motivo.

![[2]][2]

Monitorar a conectividade e controlar eventos de falha de conectividade. Você pode usar um pacote de monitoramento como [Nagios] [ nagios] para capturar e relatar essas informações.

## <a name="security-considerations"></a>Considerações de segurança

Gere uma chave compartilhada diferente para cada gateway VPN. Use uma chave compartilhada forte para ajudar a resistir a ataques de força bruta.

> [AZURE.NOTE] Atualmente, você não pode usar o Azure chave cofre ao gateway do Azure VPN chaves pré-compartilhadas.

Certifique-se de que o aparelho de VPN local usa um método de criptografia que seja [compatível com o Gateway de VPN Azure][vpn-appliance-ipsec]. Para roteamento baseado em política, o Gateway de VPN Azure suporta os algoritmos de criptografia AES256, AES128 e 3DES. Gateways baseados em rota suportam AES256 e 3DES.

Se seu aparelho de VPN local estiver em uma rede de perímetro que possui um firewall entre a rede de perímetro e a Internet, talvez você precise configurar [regras de firewall adicionais] [ additional-firewall-rules] para permitir a conexão de VPN-to-site.

Se o aplicativo na VNet enviar dados com a Internet, considere a [implementação túnel forçada] [ forced-tunneling] para direcionar todo o tráfego de Internet ligadas através da rede local. Essa abordagem permite solicitações de saída feitas pelo aplicativo de infraestrutura do local de auditoria.

> [AZURE.NOTE] Túnel forçada pode afetar a conectividade com o Azure services (o serviço de armazenamento, por exemplo) e o Gerenciador de licença do Windows.

## <a name="troubleshooting-considerations"></a>Considerações de solução de problemas

> [AZURE.NOTE] Visite o Blog de acesso remoto e o roteamento para obter informações gerais sobre [como solucionar erros comuns relacionadas a VPN][troubleshooting-vpn-errors].

Se o tráfego conseguir desviar a conexão VPN, verifique o seguinte:

### <a name="on-premises-vpn-appliance"></a>Dispositivo de VPN local:

**O dispositivo de VPN local está funcionando corretamente?**

Verifique se qualquer arquivos de log gerados pelo dispositivo VPN para falhas e erros. O local dessas informações varia de acordo com o seu aparelho. Por exemplo, se você estiver usando o RRAS no Windows Server 2012, você pode usar o seguinte comando do PowerShell para exibir informações de evento de erro para o serviço RRAS:

```
Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
```

A propriedade de _mensagem_ de cada entrada fornece uma descrição do erro. Alguns exemplos comuns são:

- Impossibilidade de conectar-se, possivelmente devido a um endereço IP incorreto especificado para o Gateway de VPN Azure na configuração da interface de rede RRAS VPN:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {41, 3, 0, 0}
Index              : 14231
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: The network connection between your computer and
                     the VPN server could not be established because the remote server is not responding. This could
                     be because one of the network devices (e.g, firewalls, NAT, routers, etc) between your computer
                     and the remote server is not configured to allow VPN connections. Please contact your
                     Administrator or your service provider to determine which device may be causing the problem.
Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                     your computer and the VPN server could not be established because the remote server is not
                     responding. This could be because one of the network devices (e.g, firewalls, NAT, routers, etc)
                     between your computer and the remote server is not configured to allow VPN connections. Please
                     contact your Administrator or your service provider to determine which device may be causing the
                     problem.}
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:26:02 PM
TimeWritten        : 3/18/2016 1:26:02 PM
UserName           :
Site               :
Container          :
```

- A chave compartilhada errada sendo especificada na configuração de interface de rede do RRAS VPN:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {233, 53, 0, 0}
Index              : 14245
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: IKE authentication credentials are unacceptable

Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                     unacceptable
                     }
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:34:22 PM
TimeWritten        : 3/18/2016 1:34:22 PM
UserName           :
Site               :
Container          :
```

Você também pode obter informações de log de eventos sobre tentativas de se conectar por meio do serviço RRAS usando o seguinte comando do PowerShell:

```
Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
```

Em caso de uma falha ao se conectar, esse log irá conter erros com uma aparência semelhantes à seguinte:

```
EventID            : 20227
MachineName        : on-prem-vm
Data               : {}
Index              : 4203
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                     AzureGateway which has failed. The error code returned on failure is 809.
Source             : RasClient
ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
InstanceId         : 20227
TimeGenerated      : 3/18/2016 1:29:21 PM
TimeWritten        : 3/18/2016 1:29:21 PM
UserName           :
Site               :
Container          :
```

**É o VPN aparelho corretamente rotear o tráfego através do Gateway de VPN Azure?**

Use uma ferramenta como [PsPing] [ psping] para verificar a conectividade e o roteamento entre o gateway VPN. Por exemplo, para testar a conectividade de um computador local em um servidor web localizado na VNet, execute o seguinte comando (substituir `<<web-server-address>>` com o endereço do servidor web):

```
PsPing -t <<web-server-address>>:80
```

Se a máquina local pode direcionar o tráfego para o servidor da web, você deverá ver saída similar ao seguinte:

```
D:\PSTools>psping -t 10.20.0.5:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.0.5:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.0.5:80 (warmup): 6.21ms
Connecting to 10.20.0.5:80: 3.79ms
Connecting to 10.20.0.5:80: 3.44ms
Connecting to 10.20.0.5:80: 4.81ms

  Sent = 3, Received = 3, Lost = 0 (0% loss),
  Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
```

Se o computador local não é possível se comunicar com o destino especificado, você verá mensagens assim:

```
D:\PSTools>psping -t 10.20.1.6:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.1.6:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80:
  Sent = 3, Received = 0, Lost = 3 (100% loss),
  Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
```

**O firewall local permitir o tráfego de VPN para passar? As portas corretas foram abertas?**

Verifique se o dispositivo de VPN local usa um método de criptografia que seja [compatível com o Gateway de VPN Azure][vpn-appliance].

Para roteamento baseado em política, o Gateway de VPN Azure suporta os algoritmos de criptografia AES256, AES128 e 3DES. Gateways baseados em rota suportam AES256 e 3DES.

### <a name="azure-vnet-gateway"></a>Gateway do Azure VNet:

Examine [os logs de diagnóstico de Gateway do Azure VPN] [ gateway-diagnostic-logs] possíveis problemas.

**São o Gateway de VPN do Azure e o aparelho de VPN local configurado com a mesma chave de autenticação compartilhada?**

Você pode exibir a chave compartilhada armazenada pelo Gateway VPN Azure usando o seguinte comando CLI Azure:

```
azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
```

Use o comando apropriado para o seu aparelho de VPN local para mostrar a chave compartilhada configurada para esse dispositivo.

Verifique se a sub-rede _GatewaySubnet_ mantendo que o Gateway de VPN Azure não está associado um NSG.

Você pode exibir os detalhes de sub-rede usando o seguinte comando CLI Azure:

```
azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
```

Verifique se há nenhum campo de dados chamado _id do grupo de segurança de rede_. O exemplo a seguir mostra os resultados por exemplo de _GatewaySubnet_ que tem uma NSG atribuído (_Grupo de Gateway VPN_). Isso pode causar impedir que o gateway funcione corretamente, se houver quaisquer regras definidas para este NSG:

```
C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
    info:    Executing command network vnet subnet show
    + Looking up virtual network "profx-vnet"
    + Looking up the subnet "GatewaySubnet"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
    data:    Name                            : GatewaySubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.20.3.0/27
    data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
    info:    network vnet subnet show command OK
```

**Máquinas virtuais VNet o Azure estão configuradas para permitir o tráfego proveniente fora do VNet? Verifique todas as regras NSG associadas sub-redes contendo essas máquinas virtuais.**

Você pode exibir todas as regras NSG usando o seguinte comando CLI Azure:

```
azure network nsg show -g <<resource-group>> -n <<nsg-name>>
```

**O Gateway de VPN Azure foi desconectado?**

Você pode usar o seguinte comando do PowerShell do Azure para verificar o status atual da conexão VPN do Azure. O `<<connection-name>>` parâmetro é o nome da conexão do Azure VPN que vincule o gateway de rede virtual e o gateway local.

```
Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
```

Os seguintes trechos realçam a saída gerada se o gateway está conectado (o primeiro exemplo) e desconectado (o segundo exemplo):

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : Connected
EgressBytesTransferred     : 55254803
IngressBytesTransferred    : 32227221
ProvisioningState          : Succeeded
...
```

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : NotConnected
EgressBytesTransferred     : 0
IngressBytesTransferred    : 0
ProvisioningState          : Succeeded
...
```

### <a name="host-vm-configuration-network-bandwidth-utilization-and-application-performance"></a>Configuração de máquina virtual do host, utilização de largura de banda de rede e desempenho de aplicativo:

Verificar se o firewall no sistema operacional convidado em execução no Azure VMs na sub-rede estão configurados corretamente para permitir o tráfego permitido dos intervalos de IP local.

**É o volume de tráfego perto o limite da largura de banda disponível para o Gateway de VPN Azure?**

Ferramentas depende os recursos disponíveis para seu aparelho de VPN executando o local. Por exemplo, se você estiver usando o RRAS no Windows Server 2012, você pode usar o Monitor de desempenho para controlar o volume de dados sendo recebidos e transmitidos sobre a conexão de VPN; usando o objeto _RAS Total_ , selecione os contadores _Bytes recebidos/s_ e _Bytes transmitidos/s_ :

![[3]][3]

Você deve comparar os resultados com a largura de banda disponível ao gateway VPN (100 Mbps para o Basic e SKUs padrão e 200 Mbps para a alta SKU de desempenho):

![[4]][4]

**São qualquer uma das máquinas virtuais VNet o Azure lento? São que eles sobrecarregado, são suficiente deles para lidar com a carga, há todos os balanceadores de carga configurados corretamente?**

Isso requer [capturar e analisar informações de diagnóstico][azure-vm-diagnostics]. Você pode examinar os resultados usando o portal do Azure, mas muitas ferramentas de terceiros também estão disponíveis que podem fornecer informações detalhadas sobre os dados de desempenho.

**É o aplicativo fazendo com uso eficiente de uso dos recursos de nuvem?**

Código de aplicativo de instrumentos em execução em cada máquina virtual para determinar se aplicativos estão fazendo o melhor uso de recursos. Você pode usar ferramentas como [Obtenção de informações de aplicativo] [ application-insights] para ajudar a realizar essas tarefas.

## <a name="solution-deployment"></a>Implantação da solução

Se você tiver uma infraestrutura local existente já configurada com um dispositivo de VPN, você pode implantar a arquitetura de referência seguindo estas etapas:

1. Clique com botão direito no botão abaixo e selecione um dos "Abrir link em uma nova guia" ou "Abrir link em nova janela":  
[![Implantar para o Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn%2Fazuredeploy.json)

2. Aguarde o link para abrir no portal do Azure e, em seguida, siga estas etapas: 
    - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-hybrid-vpn-rg` na caixa de texto.
    - Selecione a região na caixa suspensa **local** .
    - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
    - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
    - Clique no botão de **compra** .

3. Aguarde a implantação ser concluída.

## <a name="next-steps"></a>Próximas etapas

- [Instalando controladores de domínio adicionais para um domínio do Active Directory local usando VMs em um VNet Azure][installing-ad].

- [Diretrizes para implantar o Windows Server Active Directory no Azure VMs][deploying-ad].

- [Criando um servidor DNS em uma VNet][creating-dns].

- [Configurar e gerenciar o DNS em um VNet][configuring-dns].

- [Usando dispositivos de segurança de rede local Stormshield em uma VPN][stormshield].

- [Implementar uma arquitetura de rede híbrida com rota expressa do Azure][expressroute].

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[arm-templates]: ../resource-group-authoring-templates.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: ../virtual-network/virtual-networks-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-network-security-group]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: ../vpn-gateway/vpn-gateway-multi-site.md
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_0/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[create-azure-vnet]: ../virtual-network/virtual-networks-create-vnet-classic-cli.md
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: ../application-insights/app-insights-overview-usage.md
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[vpn-appliances]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[installing-ad]: ../active-directory/active-directory-install-replica-active-directory-domain-controller.md
[deploying-ad]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[creating-dns]: https://blogs.msdn.microsoft.com/mcsuksoldev/2014/03/04/creating-a-dns-server-in-azure-iaas/
[configuring-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[stormshield]: https://azure.microsoft.com/marketplace/partners/stormshield/stormshield-network-security-for-cloud/
[vpn-appliance-ipsec]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters
[expressroute]: ./guidance-hybrid-network-expressroute.md
[naming conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetwork.parameters.json
[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/blueprints/hybrid-network-vpn.png "Estrutura de um híbrido abrangendo o local de rede e infra-estruturas em nuvem"
[1]: ./media/guidance-hybrid-network-vpn/partitioned-vpn.png "Partição um VNet para melhorar a escalabilidade"
[2]: ./media/guidance-hybrid-network-vpn/audit-logs.png "Logs de auditoria no portal do Azure"
[3]: ./media/guidance-hybrid-network-vpn/RRAS-perf-counters.png "Contadores de desempenho para monitorar o tráfego de rede VPN"
[4]: ./media/guidance-hybrid-network-vpn/RRAS-perf-graph.png "Gráfico de desempenho de rede VPN de exemplo"