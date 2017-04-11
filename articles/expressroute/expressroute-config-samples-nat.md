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

# <a name="router-configuration-samples-to-setup-and-manage-nat"></a>Exemplos de configuração de roteador para configurar e gerenciar NAT

Esta página fornece exemplos de configuração de NAT para Cisco ASA e Juniper SRX roteadores de série. Estas são destinadas a ser exemplos para obter orientações apenas e não devem ser usadas como está. Você pode trabalhar com o fornecedor surgirem com configurações apropriadas para sua rede. 

>[AZURE.IMPORTANT] Exemplos nesta página destinam-se a ser puramente para obter orientação. Você deve trabalhar com a equipe de vendas / técnico do seu fornecedor e sua equipe de rede para chegar configurações apropriadas para atender às suas necessidades. Microsoft não oferecerá suporte para problemas relacionados a configurações listadas nesta página. Você deve contatar o fornecedor de seu dispositivo para questões de suporte.

Exemplos de configuração de roteador abaixo se aplicam a peerings públicas do Azure e Microsoft. Você não deve configurar NAT para correspondência particular Azure. Examine [os requisitos de rota expressa NAT](expressroute-nat.md) e [rota expressa peerings](expressroute-circuit-peerings.md) para obter mais detalhes.

**Observação:** Você deve usar grupos de NAT IP separados para conectividade com a internet e a rota expressa. Usando o mesmo conjunto de NAT IP através da internet e a rota expressa resultará em perda de conectividade e roteamento assimétricos.

## <a name="cisco-asa-firewalls"></a>Firewalls ASA Cisco

### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Configuração de PAT para tráfego de rede de clientes para Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Configuração de PAT de tráfego da Microsoft à rede de clientes

#### <a name="interfaces-and-direction"></a>Interfaces e direção:
    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

#### <a name="configuration"></a>Configuração:
Pool NAT:

    object network outbound-PAT
        host <NAT-IP>

Servidor de destino:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Grupo de objetos para endereços IP do cliente

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>
    
    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

Comandos NAT:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Roteadores da série Juniper SRX 

### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. criar redundantes interfaces Ethernet para o cluster

    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. criar duas zonas de segurança

 - Zona de confiança para rede interna e não confiar para rede externa opostas roteadores de borda
 - Atribuir interfaces apropriadas para zonas
 - Permitir serviços nas interfaces


    segurança {zonas de confiança de zona de segurança {{host--o tráfego de entrada {-serviços do sistema {ping;                  } protocolos {bgp;                  interfaces}} {reth0.100;              }} zona de segurança não confiar {host--o tráfego de entrada {-serviços do sistema {ping;                  } protocolos {bgp;                  interfaces}} {reth1.100;              }          }      }  }


### <a name="3-create-security-policies-between-zones"></a>3. criar políticas de segurança entre regiões
 
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. Configurar políticas NAT
 - Crie dois pools NAT. Um será usado para o tráfego NAT saído para a Microsoft e outro da Microsoft para o cliente.
 - Criar regras para NAT o tráfego respectivo

        security {
            nat {
                source {
                    pool SNAT-To-ExpressRoute {
                        routing-instance {
                            External-ExpressRoute;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    pool SNAT-From-ExpressRoute {
                        routing-instance {
                            Internal;
                        }
                        address {
                            <NAT-IP-address/Subnet-mask>;
                        }
                    }
                    rule-set Outbound_NAT {
                        from routing-instance Internal;
                        to routing-instance External-ExpressRoute;
                        rule SNAT-Out {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-To-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                    rule-set Inbound-NAT {
                        from routing-instance External-ExpressRoute;
                        to routing-instance Internal;
                        rule SNAT-In {
                            match {
                                source-address 0.0.0.0/0;
                            }
                            then {
                                source-nat {
                                    pool {
                                        SNAT-From-ExpressRoute;
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }


### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Configurar BGP para anunciar prefixos seletivos em cada direção

Consulte exemplos na página de [exemplos de configuração de roteamento](expressroute-config-samples-routing.md) .

### <a name="6-create-policies"></a>6. criar políticas

    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Próximas etapas

Consulte as [Perguntas Frequentes de rota expressa](expressroute-faqs.md) para obter mais detalhes.
