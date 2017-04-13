<properties
   pageTitle="O que é uma lista de controle de acesso (ACL) da rede?"
   description="Saiba mais sobre ACLs"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-is-an-endpoint-access-control-list-acls"></a>O que é um ponto de extremidade lista de controle de acesso (ACLs)?

Um ponto de extremidade lista de controle de acesso (ACL) é um aprimoramento de segurança disponível para sua implantação do Azure. Uma ACL fornece a capacidade para seletivamente permitir ou negar o tráfego para um ponto de extremidade de máquina virtual. Esse recurso de filtragem de pacote fornece uma camada adicional de segurança. Você pode especificar ACLs de rede para apenas pontos de extremidade. Você não pode especificar uma ACL para uma rede virtual ou uma sub-rede específica contidos em uma rede virtual.

> [AZURE.IMPORTANT] É recomendável usar grupos de segurança de rede (NSGs) em vez de ACLs sempre que possível. Para saber mais sobre NSGs, consulte [o que é um grupo de segurança de rede?](virtual-networks-nsg.md).

ACLs podem ser configuradas usando o PowerShell ou o Portal de gerenciamento. Para configurar uma rede ACL usando o PowerShell, consulte [Gerenciar listas de controle acesso (ACLs) para pontos de extremidade usando o PowerShell](virtual-networks-acl-powershell.md). Para configurar uma rede ACL usando o Portal de gerenciamento, veja [como configurar o pontos de extremidade para uma máquina Virtual](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

Usando ACLs da rede, você pode fazer o seguinte:

- Seletivamente permitir ou negar tráfego de entrada com base no intervalo de endereços IPv4 para um ponto de extremidade de entrada de máquina virtual de sub-rede remota.

- Endereços IP negra

- Criar várias regras por ponto de extremidade de máquina virtual

- Especificar até 50 regras ACL por ponto de extremidade de máquina virtual

- Use a ordenação da regra para garantir que o conjunto correto de regras são aplicadas em um ponto de extremidade de determinado máquina virtual (menor para o maior)

- Especifique uma ACL para uma sub-rede remota específica endereço IPv4.

## <a name="how-acls-work"></a>Como funcionam os ACLs

Uma ACL é um objeto que contém uma lista de regras. Quando você cria uma ACL e aplicá-la a um ponto de extremidade de máquina virtual, filtragem de pacotes ocorrerá no nó host da sua máquina virtual. Isso significa que o tráfego de endereços IP remotos é filtrado pelo nó host para regras de ACL correspondência em vez de em sua máquina virtual. Isso impede que sua máquina virtual gastando ciclos da CPU valiosas em filtragem de pacotes.

Quando uma máquina virtual é criada, uma ACL padrão é colocada no lugar para bloquear todo o tráfego de entrada. No entanto, se um ponto de extremidade é criado para (porta 3389), em seguida, o padrão de ACL é modificado para permitir que todo o tráfego para o ponto de extremidade. Tráfego de entrada de qualquer sub-rede remota, em seguida, é permitido a esse ponto de extremidade e nenhum provisionamento de firewall é necessária. Todas as outras portas são bloqueadas para tráfego de entrada, a menos que pontos de extremidade são criados para as portas. Tráfego de saída é permitido por padrão.

**Tabela de ACL padrão de exemplo**

| **Regra #** | **Sub-rede remota** | **Ponto de extremidade** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100    | 0.0.0.0/0     | 3389     | Permitir      |

## <a name="permit-and-deny"></a>Permitir e negar

Você pode seletivamente permitir ou negar o tráfego de rede para um ponto de extremidade de entrada de máquina virtual criando regras que especificam "Permitir" ou "Negar". É importante observar que por padrão, quando um ponto de extremidade é criado, todo o tráfego é permitido para o ponto de extremidade. Por que motivo, é importante entender como criar regras de Permitir/Negar e colocá-los na ordem correta de precedência se quiser que o controle granular sobre o tráfego de rede que você optar por permitir alcançar o ponto de extremidade de máquina virtual.

Pontos a considerar:

1. **Nenhuma ACL –** Por padrão quando um ponto de extremidade é criado, podemos permitir que todos para o ponto de extremidade.

1. **Permitir-** Quando você adiciona um ou mais "permitem" intervalos, você é rejeição de todos os outros intervalos por padrão. Somente os pacotes do intervalo IP permitido serão capazes de se comunicar com o ponto de extremidade de máquina virtual.

1. **Negar-** Quando você adiciona um ou mais "Negar" intervalos, você estará permitindo todos os outros intervalos de tráfego por padrão.

1. **Combinação de permitir e negar-** Você pode usar uma combinação de "Permitir" e "Negar" quando você quiser reservar um intervalo IP específico a ser permitido ou negado.

## <a name="rules-and-rule-precedence"></a>Regras e a precedência de regras

ACLs da rede podem ser configuradas em pontos de extremidade de máquina virtual específica. Por exemplo, você pode especificar uma rede ACL para um ponto de extremidade RDP criado em uma máquina virtual que bloqueia acesso para determinados endereços IP. A tabela a seguir mostra uma maneira para conceder acesso ao público IPs virtual (VIPs) de um determinado intervalo para permitir o acesso para RDP. Todos os outros IPs remoto são negadas. Podemos seguem uma ordem de regra *tem menor precedência* .

### <a name="multiple-rules"></a>Várias regras

No exemplo abaixo, se você quiser permitir o acesso ao ponto de extremidade RDP somente a partir de dois públicos intervalos de endereços IPv4 (65.0.0.0/8 e 159.0.0.0/8), você pode obter isso especificando duas regras *Permitir* . Nesse caso, pois RDP é criado por padrão para uma máquina virtual, talvez você queira bloquear o acesso à porta RDP com base em uma sub-rede remota. O exemplo a seguir mostra uma maneira para conceder acesso ao público IPs virtual (VIPs) de um determinado intervalo para permitir o acesso para RDP. Todos os outros IPs remoto são negadas. Isso funciona porque rede ACLs pode ser configurado para um ponto de extremidade de máquina virtual específica e acesso negado por padrão.

**Exemplo – várias regras**

| **Regra #** | **Sub-rede remota** | **Ponto de extremidade** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100    | 65.0.0.0/8    | 3389     | Permitir      |
| 200    | 159.0.0.0/8   | 3389     | Permitir      |

### <a name="rule-order"></a>Ordem de regra

Como várias regras podem ser especificadas para um ponto de extremidade, deve haver uma maneira de organizar regras para determinar qual regra tem precedência. A ordem da regra especifica a precedência. ACLs da rede seguem uma ordem de regra *tem menor precedência* . No exemplo abaixo, o ponto de extremidade na porta 80 é seletivamente acesso concedido a apenas determinados intervalos de endereços IP. Para configurar isso, temos uma regra de negação (regra \# 100) para endereços no espaço 175.1.0.1/24. Uma segunda regra é então especificada com precedência 200 que permite acesso a todos os outros endereços em 175.0.0.0/8.

**Exemplo – a precedência de regras**

| **Regra #** | **Sub-rede remota** | **Ponto de extremidade** | **Permitir/Negar** |
|--------|---------------|----------|-------------|
| 100    | 175.1.0.1/24  | 80       | Negar        |
| 200    | 175.0.0.0/8   | 80       | Permitir      |

## <a name="network-acls-and-load-balanced-sets"></a>ACLs de rede e carregar conjuntos equilibrados

ACLs da rede podem ser especificadas em um ponto de extremidade do balanceamento de carga conjunto (LB conjunto). Se uma ACL for especificada para um conjunto de LB, a ACL de rede é aplicada a todas as máquinas virtuais em desse conjunto de LB. Por exemplo, se um conjunto de LB é criado com "Porta 80" e o conjunto de LB contém 3 VMs, a ACL de rede criado no ponto de extremidade "Porta 80" de uma que máquina virtual aplica-se automaticamente às outras VMs.

![ACLs de rede e carregar conjuntos equilibrados](./media/virtual-networks-acl/IC674733.png)

## <a name="next-steps"></a>Próximas etapas

[Como gerenciar listas de controle de acesso (ACLs) para pontos de extremidade usando o PowerShell](virtual-networks-acl-powershell.md)
