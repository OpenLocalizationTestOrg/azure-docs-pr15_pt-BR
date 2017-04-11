<properties
   pageTitle="Exemplos de configuração de roteador rota expressa do cliente | Microsoft Azure"
   description="Esta página fornece exemplos de configuração de roteador para roteadores Cisco e Juniper."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="router-configuration-samples-to-setup-and-manage-routing"></a>Exemplos de configuração de roteador para configurar e gerenciar o roteamento

Esta página fornece interface e roteamento exemplos de configuração para roteadores de série Cisco IOS-XE e Juniper MX. Estas são destinadas a ser exemplos para obter orientações apenas e não devem ser usadas como está. Você pode trabalhar com o fornecedor surgirem com configurações apropriadas para sua rede. 

>[AZURE.IMPORTANT] Exemplos nesta página destinam-se a ser puramente para obter orientação. Você deve trabalhar com a equipe de vendas / técnico do seu fornecedor e sua equipe de rede para chegar configurações apropriadas para atender às suas necessidades. Microsoft não oferecerá suporte para problemas relacionados a configurações listadas nesta página. Você deve contatar o fornecedor de seu dispositivo para questões de suporte.

Exemplos de configuração de roteador abaixo se aplicam a todas as peerings. Analisar [requisitos de roteamento de rota expressa](expressroute-routing.md) e [rota expressa peerings](expressroute-circuit-peerings.md) para obter mais detalhes sobre o roteamento.

## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE com base em roteadores

Os exemplos nesta seção se aplicam para qualquer roteador que executam a família IOS XE SO.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurando interfaces e sub

Exigirá uma interface sub por correspondência em cada roteador que você se conectar à Microsoft. Uma interface de sub pode ser identificada com uma ID de VLAN ou um par empilhado de IDs de VLAN e um endereço IP.

#### <a name="dot1q-interface-definition"></a>Definição de interface Dot1Q

Este exemplo fornece a definição de interface sub para uma interface sub com uma única ID de VLAN. Ele seja exclusivo por correspondência. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

#### <a name="qinq-interface-definition"></a>Definição de interface QinQ

Este exemplo fornece a definição de interface sub para uma interface sub com um duas IDs de VLAN. O ID de VLAN externa (s-marca), se usada permanece o mesmo em todos os peerings. O ID de VLAN interna (c-marca) é exclusivo por correspondência. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
### <a name="2-setting-up-ebgp-sessions"></a>2. Configurando sessões eBGP

Você deve configurar uma sessão BGP com a Microsoft para cada correspondência. O exemplo a seguir permite que você configurar uma sessão BGP com a Microsoft. Se o endereço IPv4 usado para a sua interface sub foi a.b.c. d, o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto de endereço de IPv4 do vizinho BGP sempre será um número par.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configurando prefixos para ser anunciado sobre a sessão BGP

Você pode configurar o roteador para anunciar prefixos selecionados à Microsoft. Você pode fazer isso usando o exemplo abaixo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. mapas de rotas

Você pode usar os mapas de rota e listas de prefixo para prefixos de filtro propagados na sua rede. Você pode usar o exemplo abaixo para realizar a tarefa. Certifique-se de que você tenha a configuração de listas de prefixo apropriado.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Roteadores da série Juniper MX 

Os exemplos nesta seção se aplicam para qualquer roteador de série Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurando interfaces e sub

#### <a name="dot1q-interface-definition"></a>Definição de interface Dot1Q

Este exemplo fornece a definição de interface sub para uma interface sub com uma única ID de VLAN. Ele seja exclusivo por correspondência. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


#### <a name="qinq-interface-definition"></a>Definição de interface QinQ

Este exemplo fornece a definição de interface sub para uma interface sub com um duas IDs de VLAN. O ID de VLAN externa (s-marca), se usada permanece o mesmo em todos os peerings. O ID de VLAN interna (c-marca) é exclusivo por correspondência. O último octeto de seu endereço IPv4 sempre será um número ímpar.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Configurando sessões eBGP

Você deve configurar uma sessão BGP com a Microsoft para cada correspondência. O exemplo a seguir permite que você configurar uma sessão BGP com a Microsoft. Se o endereço IPv4 usado para a sua interface sub foi a.b.c. d, o endereço IP do vizinho BGP (Microsoft) será a.b.c.d+1. O último octeto de endereço de IPv4 do vizinho BGP sempre será um número par.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Configurando prefixos para ser anunciado sobre a sessão BGP

Você pode configurar o roteador para anunciar prefixos selecionados à Microsoft. Você pode fazer isso usando o exemplo abaixo.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. mapas de rotas

Você pode usar os mapas de rota e listas de prefixo para prefixos de filtro propagados na sua rede. Você pode usar o exemplo abaixo para realizar a tarefa. Certifique-se de que você tenha a configuração de listas de prefixo apropriado.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Próximas etapas

Consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md) para obter mais detalhes.
