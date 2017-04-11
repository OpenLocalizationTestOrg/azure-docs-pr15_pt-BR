<properties
   pageTitle="Referência de arquitetura Azure - IaaS: Criando uma floresta de recursos do Active Directory no Azure | Microsoft Azure"
   description="Como criar um domínio confiável do Active Directory no Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="creating-a-active-directory-directory-services-adds-resource-forest-in-azure"></a>Criando uma floresta de recurso de serviços de diretório do Active Directory (ADICIONA) no Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve como criar um domínio Active Directory no Azure que é separado do, mas confiável, domínios da floresta local.

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Uma organização que executa o Active Directory (AD) local pode ter uma floresta que consiste em vários domínios diferentes. Por exemplo, você pode criar domínios individuais por vários departamentos ou suborganizations ou novos domínios talvez tenha sido adicionados como resultado de aquisição ou fusão de outras organizações. Você pode usar domínios para oferecer isolamento entre áreas funcionais que devem ser mantidos separados, possivelmente por razões de segurança, mas você pode compartilhar informações entre domínios estabelecendo relações de confiança.

Uma organização que utiliza domínios separados pode tirar proveito do Azure por meio da realocação de um ou mais desses domínios na nuvem. Como alternativa, uma organização talvez queira manter todos os recursos de nuvem logicamente distinto desses mantido local e armazenar informações sobre recursos de nuvem em seu próprio diretório, em um domínio também mantido na nuvem.

Você pode implementar o Active Directory no Azure de várias maneiras diferentes, conforme descrito nos artigos [Estendendo Active Directory do Azure] [ extending-ad-to-azure] e [Implementando o Azure Active Directory][implementing-aad]. Este documento se concentra em um cenário específico: Criando um domínio na nuvem que é diferente de qualquer domínio mantido no local, mas que podem ter uma relação de confiança com domínios de local. 

Casos de uso típico para essa arquitetura incluem:

- Manter separação de segurança para objetos e identidades mantidas na nuvem.

- Migrando domínios individuais do local na nuvem.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir destaca os componentes importantes nesta arquitetura (*clique para ampliar*). Para obter mais informações sobre os elementos de acinzentada, leia [implementar uma arquitetura de rede de seguro híbrido no Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementar uma arquitetura de rede de seguro híbrida com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Rede local.** A rede local contém sua própria floresta do AD e domínios.

- **Servidores de anúncio.** Estas são controladores de domínio a implementação de serviços de diretório (AD DS) executando como VMs na nuvem. Esses servidores hospedarem uma floresta que contém um ou mais domínios, separados desses localizado no local.

- **Relação de confiança unidirecional.** O exemplo no diagrama mostra uma relação de confiança unidirecional do domínio na nuvem para o domínio local. Este relacionamento permite que usuários locais acessar recursos no domínio em nuvem, mas não o contrário. Este é um caso comum. No entanto, você pode criar uma relação de confiança bidirecional se os usuários de nuvem também exigem acesso aos recursos locais.

- **Sub-rede do Active Directory.** Os servidores de AD DS são hospedados em uma sub-rede separada. Regras NSG proteger os servidores de AD DS e podem fornecer um firewall contra tráfego de fontes inesperadas.

- **Sub-rede de nível da Web**, **sub-rede de nível de negócios**e **sub-rede de camada de dados**. Essas sub-redes hospedam os servidores e componentes que executam aplicativos na nuvem. Para obter mais informações, consulte [Executando VMs para uma arquitetura de N camadas no Azure][running-VMs-for-an-N-tier-architecture-on-Azure]. Os recursos e VMs nesta sub-rede estão contidas no domínio de nuvem.

- **Gateway azure**. O gateway Azure oferece uma conexão entre a rede local e o VNet do Azure. Isso pode ser uma [conexão VPN] [ azure-vpn-gateway] ou [Rota expressa do Azure][azure-expressroute]. Para obter mais informações, consulte [Implementando uma arquitetura de rede de seguro híbrido no Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recomendações

Esta seção fornece uma lista de recomendações com base nos componentes essenciais necessários para implementar a arquitetura básica. Essas recomendações abrangem:

- ADICIONA configurações, e

- Configuração de relação de confiança.

Você pode ter requisitos adicionais ou diferentes daqueles descritos aqui. Você pode usar os itens desta seção como ponto de partida para considerando como personalizar a arquitetura para seu próprio sistema.

### <a name="adds-recommendations"></a>ADICIONA recomendações

Para obter recomendações específicas sobre a implementação do Active Directory na nuvem, consulte o documento [Estendendo Active Directory do Azure][extending-ad-to-azure]. O artigo [diretrizes para implantação do Active Directory do Windows Server em máquinas virtuais do Azure] [ ad-azure-guidelines] contém informações detalhadas adicionais.

### <a name="trust-recommendations"></a>Confiar recomendações

Os domínios locais estão contidos em uma floresta diferente dos domínios na nuvem. Para habilitar a autenticação de usuários locais na nuvem, os domínios na nuvem devem confiar no domínio de logon na floresta local. Da mesma forma, se a nuvem fornece um domínio de logon para usuários externos, talvez seja necessário para a floresta local confiar no domínio de nuvem.

Você pode estabelecer relações de confiança do nível de floresta criando [relações de confiança de floresta][creating-forest-trusts], ou no nível do domínio, [Criando relações de confiança externas][creating-external-trusts]. Uma relação de confiança de nível de floresta cria uma relação entre todos os domínios de duas florestas. Uma relação de confiança de nível de domínio externo só cria uma relação entre dois domínios especificados. Você só deve criar relações de confiança de nível de domínio externo entre domínios em diferentes florestas.

Relações de confiança podem ser unidirecionais (unidirecional) ou bidirecional (bidirecional):

- Uma relação de confiança unidirecional permite que os usuários em um domínio ou floresta (conhecido como *entrada* de domínio ou floresta) para acessar os recursos contidos em outro (a *saída* de domínio ou floresta). 

- Uma relação de confiança bidirecional permite que os usuários no domínio ou floresta acessar recursos contidos em outro.

A tabela a seguir resume as configurações de confiança para alguns cenários simples:

| Cenário | Locais confiáveis | Relação de confiança de nuvem |
|----------|-------------------|-------------|
| Local usuários requerem acesso aos recursos na nuvem, mas não vice-versa | Unidirecional de entrada | Unidirecional de saída |
| Os usuários na nuvem requerem acesso aos recursos localizados no local, mas não vice-versa | Unidirecional de saída | Unidirecional de entrada |
| Os usuários na nuvem e o local requer acesso aos recursos contidos em nuvem e local | Bidirecional, recebidos e enviados | Bidirecional, recebidos e enviados |

## <a name="security-considerations"></a>Considerações de segurança

Relações de confiança de níveis são transitivas. Se você estabelecer uma relação de confiança de nível de floresta entre uma floresta local e uma floresta na nuvem, essa relação de confiança está estendida para outros novos domínios criados em qualquer floresta. Se você usar domínios para fornecer separação para fins de segurança, considere a criação de relações de confiança no nível do domínio somente. Relações de nível de domínio não são transitivas.

Para considerações de segurança do AD específicas, consulte a seção *Considerações de segurança* no [Estendendo Active Directory para Azure][extending-ad-to-azure].

## <a name="availability-considerations"></a>Considerações de disponibilidade

Implemente pelo menos dois controladores de domínio para cada domínio. Isso permite replicação automática entre servidores. Crie uma disponibilidade definida para VMs atuando como servidores de AD manipulação de cada domínio. Certifique-se de que há pelo menos dois servidores do conjunto. 

Além disso, considere designar um ou mais servidores em cada domínio como [mestres de operações em espera] [ standby-operations-masters] em caso de falha de conectividade com um servidor atuando como uma função FSMO.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

AD é automaticamente scalable controladores de domínio que fazem parte do mesmo domínio. Solicitações são distribuídas entre todos os controladores em um domínio. Você pode adicionar outro controlador de domínio e ele sincroniza automaticamente com o domínio. Não configure um balanceador de carga separada para direcionar o tráfego para controladores dentro do domínio. Certifique-se de que todos os controladores de domínio têm recursos de armazenamento e memória suficientes para lidar com o banco de dados do domínio. Verifique todas as VMs de controlador de domínio do mesmo tamanho.

## <a name="management-and-monitoring-considerations"></a>Considerações de monitoramento e gerenciamento

Para obter informações sobre as considerações de monitoramento e gerenciamento, consulte as seções equivalentes [Estendendo Active Directory do Azure][extending-ad-to-azure]. 

Para obter informações adicionais, consulte [Monitoring Active Directory][monitoring_ad]. Você pode instalar ferramentas como o [Microsoft Systems Center] [ microsoft_systems_center] em um servidor de monitoramento na sub-rede gerenciamento para ajudar a realizar essas tarefas. 

## <a name="solution-components"></a>Componentes de solução

Um script de solução de amostra, [Implantar-ReferenceArchitecture.ps1][solution-script], está disponível que você pode usar para implementar a arquitetura que segue as recomendações descritas neste artigo. Esse script utiliza modelos do Gerenciador de recursos do Azure. Os modelos estão disponíveis como um conjunto de blocos de construção fundamentais, cada uma das quais executa uma ação específica como criar um VNet ou configurar um NSG. A finalidade do script é coordenar implantação do modelo.

Os modelos são parametrizados, com os parâmetros contidos em arquivos JSON separados. Você pode modificar os parâmetros nesses arquivos para configurar a implantação para atender às suas próprias necessidades. Você não precisa modificar os modelos em si. Você não deve alterar os esquemas dos objetos em arquivos de parâmetro.

Quando você edita os modelos, criar objetos que seguem as convenções de nomenclatura descritas em [Recomendados convenções de nomenclatura para recursos do Azure][naming-conventions].

A solução de exemplo cria e configura um ambiente na nuvem que implementa um domínio chamado *treyresearch.com*. Esse ambiente abrange o gateway VPN sub-rede e servidores, DMZ, camada da web, camada de negócios e componentes de nível de acesso de dados, ADICIONA e nível de gerenciamento. A solução de exemplo também inclui uma configuração opcional para criar um ambiente local simulado com seu próprio domínio, *contoso.com*. A solução inclui scripts que estabelecer uma relação de confiança entre esses domínios que permite aos usuários locais acessar objetos no domínio *treyresearch.com* na nuvem.

As seções a seguir descrevem os elementos as locais e na nuvem configurações.

### <a name="on-premises-components"></a>Componentes de local

>[AZURE.NOTE] Esses componentes não são o foco principal da arquitetura descrito neste documento e são fornecidos simplesmente para dar a você uma oportunidade para testar o ambiente de nuvem com segurança, em vez de usar um ambiente de produção real. Por esse motivo, esta seção resume apenas os arquivos de parâmetro chave. Você pode modificar configurações como os endereços IP ou os tamanhos das VMs, mas é aconselhável deixar muitos dos outros parâmetros inalterados.

Esse ambiente consiste em uma floresta do AD domínio *contoso.com* . O domínio contém dois servidores ADICIONA com endereços IP 192.168.0.4 e 192.168.0.5. Esses dois servidores também executam o serviço DNS. A conta de administrador local em ambas as VMs é chamada `testuser` com senha `AweS0me@PW`. Além disso, a configuração configura um gateway VPN para conexão com a VNet na nuvem. Você pode modificar a configuração editando os seguintes arquivos JSON localizados nos [**parâmetros/onpremise**] [ on-premises-folder] pasta:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Esse arquivo define o espaço de endereço de rede para o ambiente local.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Este arquivo contém a configuração para VMs local ADICIONA serviços de hospedagem. Por padrão, duas VMs *padrão-DS3-v2* são criadas.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** e ** [connection.parameters.json][on-premises-connection-parameters]**. Esses arquivos mantenha as configurações para a conexão VPN ao gateway VPN Azure na nuvem, incluindo a chave compartilhada a ser usado para proteger o tráfego percorrer o gateway.

Os arquivos restantes na pasta contêm as informações de configuração usadas para criar o domínio de local (*contoso.com*) usando este infraestrutura. Você usá-los para instalar ADICIONA, configuração de DNS e crie a floresta local.

A solução também usa o script a seguir, chamado [entrada-trust.ps1][incoming-trust], que é executado em uma máquina no domínio local:

```Powershell
# Run the following powershell script in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)

$TrustedDomainName = "contoso.com"
#$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

$TrustingDomainName = "treyresearch.com"
$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustingDomainDnsIpAddresses
$ForwardDomainName = $TrustingDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Esse script adiciona os endereços IP dos servidores do AD DS na nuvem (consulte a próxima seção) para o serviço DNS local e usa o [netdom] [ netdom] comando para criar uma relação de confiança unidirecional de entrada do domínio na nuvem (*treyresearch.com*).

### <a name="cloud-components"></a>Componentes de nuvem

Esses componentes formam o núcleo dessa arquitetura. Eles a infraestrutura do domínio *treyresearch.com* de instalação e criar as relações de confiança com o domínio de *contoso.com* local. Os [**parâmetros/azure**] [ azure-folder] pasta contém os seguintes arquivos de parâmetro para configurar esses componentes:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Esse arquivo define a estrutura do VNet para VMs e outros componentes na nuvem. Ele inclui configurações, como o nome, espaço de endereço, sub-redes e os endereços de todos os servidores DNS necessários. Os endereços DNS mostrado nesta referência de exemplo os endereços IP dos servidores DNS locais e também o servidor DNS Azure padrão. Modificar esses endereços para fazer referência a sua própria configuração DNS se você não estiver usando o ambiente do local de exemplo:
    
    ```json
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg",
        "addressPrefixes": [
          "10.0.0.0/16"
        ],
        "subnets": [
          {
            "name": "dmz-private-in",
            "addressPrefix": "10.0.0.0/27"
          },
          {
            "name": "dmz-private-out",
            "addressPrefix": "10.0.0.32/27"
          },
          {
            "name": "dmz-public-in",
            "addressPrefix": "10.0.0.64/27"
          },
          {
            "name": "dmz-public-out",
            "addressPrefix": "10.0.0.96/27"
          },
          {
            "name": "mgmt",
            "addressPrefix": "10.0.0.128/25"
          },
          {
            "name": "GatewaySubnet",
            "addressPrefix": "10.0.255.224/27"
          },
          {
            "name": "web",
            "addressPrefix": "10.0.1.0/24"
          },
          {
            "name": "biz",
            "addressPrefix": "10.0.2.0/24"
          },
          {
            "name": "data",
            "addressPrefix": "10.0.3.0/24"
          },
          {
            "name": "adds",
            "addressPrefix": "10.0.4.0/27"
          }
        ],
        "dnsServers": [
          "10.0.4.4",
          "10.0.4.5",
          "168.63.129.16"
        ]
      }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. Este arquivo configura as VMs executadas ADICIONA na nuvem. A configuração consiste em duas VMs. Alterar o nome de usuário de administrador e a senha na `virtualMachineSettings` seção e, opcionalmente, pode modificar o tamanho de máquina virtual para corresponder aos requisitos do domínio:

    Para obter mais informações, consulte [Estendendo Active Directory do Azure][extending-ad-to-azure].
    
    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-ad",
        "computerNamePrefix": "aad",
        "size": "Standard_DS3_v2",
        "osType": "Windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "adds",
            "privateIPAllocationMethod": "Static",
            "startingIPAddress": "10.0.4.4",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 127,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [
        ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-as"
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ```

- ** [Adicionar-adiciona-domínio-controller.parameters.json][add-adds-domain-controller-parameters]**. Este arquivo contém as configurações para criar o domínio *treyresearch.com* abrangendo os servidores ADICIONA. Ele usa extensões personalizadas que estabelecer o domínio e adicionar os servidores de ADICIONA a ele. A menos que você crie servidores ADICIONA adicionais (caso em que você deve adicioná-los para o `vms` matriz), alterar seus nomes padrão ou deseja criar um domínio com um nome diferente, você não precisa modificar esse arquivo.

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Este arquivo contém as configurações usadas para criar o gateway VPN Azure na nuvem usada para se conectar à rede local. Você deve modificar o `sharedKey` valor o `connectionsSettings` seção que coincida com o dispositivo VPN local. Para obter mais informações, consulte [Implementando uma arquitetura de rede híbrida com o Azure e local VPN][hybrid-azure-on-prem-vpn].

- ** [dmz-private.parameters.json] [ dmz-private-parameters] ** e ** [dmz-public.parameters.json ] [ dmz-public-parameters] **. Esses arquivos configuram (público) entrado e saídos (privados) lados das VMs que compõem DMZ, protegendo os servidores na nuvem. Para obter mais informações sobre esses elementos e sua configuração, consulte [Implementando DMZ entre Azure e a Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer-web.parameters.json][loadBalancer-web-parameters]**, ** [loadBalancer-biz.parameters.json][loadBalancer-biz-parameters]**, e ** [loadBalancer-data.parameters.json][loadBalancer-data-parameters]**. Esses arquivos de parâmetros contêm as especificações de máquina virtual para os níveis de acesso da web, negócios e dados e configurar balanceadores de carga para cada nível. Estas são as VMs que hospedam a web apps e bancos de dados e executam as cargas de trabalho de negócios para a organização. As VMs na camada da web são adicionadas ao domínio na nuvem usando as configurações do ** [web-máquina virtual-domínio-join.parameters.json] [ web-vm-domain-join-parameters] ** arquivo.

    Cada arquivo contém dois conjuntos de parâmetros de configuração. O `virtualMachineSettings` seção define as VMs que hospedam o serviço ADFS na nuvem. Por padrão, o script cria dois desses VMs no mesmo conjunto de disponibilidade. Os fragmentos a seguir mostram as partes relevantes do arquivo *loadBalancer-web.parameters.json* :

    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-web",
        "computerNamePrefix": "web",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "web",
            "privateIPAllocationMethod": "Dynamic",
            "isPrimary": "true",
            "enableIPForwarding": false,
            "dnsServers": [ ]
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-web-vm-as"
        }
      }
    },
    ...
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ````
    Você pode modificar os tamanhos e o número de VMs em cada camada de acordo com suas necessidades.

    O `loadBalancerSettings` seção fornece a descrição de Balanceador de carga para esses VMs. O balanceador de carga passa tráfego que aparece na porta 80 (HTTP) e a porta 443 (HTTPS) a uma ou outra das VMs. 

    >[AZURE.NOTE] A regra para porta 80 usa uma conexão de TCP em vez de HTTP. Isso ocorre porque a instalação do IIS na camada da web está configurada para oferecer suporte somente a autenticação do Windows. Autenticação anônima é desativada. Tentando *ping* porta 80 ao longo de uma conexão de HTTP falha com um erro 401 (não autorizado), enquanto usar uma conexão de TCP apenas detecta se a porta está ativa:

    ```json
    "loadBalancerSettings": {
      "value": {
        "name": "ra-adtrust-web-lb",
        "frontendIPConfigurations": [
          {
            "name": "ra-adtrust-web-lb-fe",
            "loadBalancerType": "internal",
            "internalLoadBalancerSettings": {
              "privateIPAddress": "10.0.1.254",
              "subnetName": "web"
            }
          }
        ],
        "backendPools": [
          {
            "name": "ra-adtrust-web-lb-bep",
            "nicIndex": 0
          }
        ],
        "loadBalancingRules": [
          {
            "name": "http-rule",
            "frontendPort": 80,
            "backendPort": 80,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "http-probe",
            "enableFloatingIP": false
          },
          {
            "name": "https-rule",
            "frontendPort": 443,
            "backendPort": 443,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "https-probe",
            "enableFloatingIP": false
          }
        ],
        "probes": [
          {
            "name": "http-probe",
            "port": 80,
            "protocol": "Tcp",
            "requestPath": null
          },
          {
            "name": "https-probe",
            "port": 443,
            "protocol": "Tcp",
            "requestPath": null
          }
        ],
        "inboundNatRules": [ ]
      }
    }
    ```

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Este arquivo contém a configuração para o salto caixa/gerenciamento VMs. Só é possível obter logon e acesso administrativo às VMs na web, business e camadas de dados da caixa de salto. Por padrão, o script cria uma única *Standard_DS1_v2* máquina virtual, mas você pode modificar esse arquivo para criar VMs aumentar ou adicionais se o gerenciamento de carga de trabalho é provável que seja significativo.

A configuração também usa o [trust.ps1 de saída] [ outgoing-trust] script mostrado abaixo para criar uma relação de confiança unidirecional de saída com o domínio *contoso.com* :

```powershell
# prerequiste: 
# You need to first run incoming-trust.ps1 in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)
# Then,
# Run the following powershell script in ra-adtrust-ad-vm1 (ip 10.0.4.4)

$TrustedDomainName = "contoso.com"
$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

#$TrustingDomainName = "treyresearch.com"
#$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustedDomainDnsIpAddresses
$ForwardDomainName = $TrustedDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

#netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Esse script é semelhante ao script de *entrada-trust.ps1* descrito anteriormente. Adiciona os endereços IP do local servidores de AD DS para o serviço DNS local e usa o [netdom] [ netdom] comando para criar a relação de confiança de saída.

## <a name="solution-deployment"></a>Implantação da solução

A solução pressupõe os seguintes pré-requisitos:

- Você tem uma assinatura existente do Azure na qual você pode criar grupos de recursos.

- Baixar e instalar a versão mais recente do Azure Powershell. Veja [aqui] [ azure-powershell-download] para obter instruções.

Para executar o script que implanta a solução:

1. Mover para uma pasta conveniente em seu computador local e crie as seguintes subpastas:

    - Scripts

    - Scripts/parâmetros

    - Parâmetros/scripts/Onpremise

    - Parâmetros/scripts/Azure

2. Baixar o [Implantar-ReferenceArchitecture.ps1] [ solution-script] arquivo para a pasta de Scripts

3. Baixar o conteúdo dos [parâmetros/onpremise] [ on-premises-folder] para a pasta de parâmetros/Scripts/Onpremise:

4. Baixar o conteúdo dos [parâmetros/azure] [ azure-folder] para a pasta de parâmetros/Scripts/Azure.

5. Editar o arquivo ReferenceArchitecture.ps1 implantar na pasta Scripts e altere as seguintes linhas para especificar os grupos de recursos que devem ser criados ou usados para manter os recursos criados pelo script:

    ```powershell
    # Azure Onpremise Deployments
    $onpremiseNetworkResourceGroupName = "ra-adtrust-onpremise-rg"

    # Azure ADDS Deployments
    $azureNetworkResourceGroupName = "ra-adtrust-network-rg"
    $workloadResourceGroupName = "ra-adtrust-workload-rg"
    $securityResourceGroupName = "ra-adtrust-security-rg"
    $addsResourceGroupName = "ra-adtrust-adds-rg"
    ```

6. Edite os arquivos de parâmetro nas pastas de parâmetros/Scripts/Azure e parâmetros/Scripts/Onpremise. Atualize as referências de grupo de recursos nesses arquivos para corresponder os nomes dos grupos de recursos atribuídos às variáveis no arquivo ReferenceArchitecture.ps1 implantar. A tabela a seguir mostra quais arquivos de parâmetro referência qual grupo de recursos. Os grupos de recursos *ar-adfs-carga-rg*, *ar-adfs-segurança-rg*, *ar-adfs-adiciona-rg*, *ar-adfs adfs rg*e *ar-adfs-proxy-rg* são usados somente no script PowerShell e não ocorrem em arquivos de parâmetro.

  	|Grupo de recursos|Arquivos de parâmetro|
  	|--------------|--------------|
  	|ar-adtrust-onpremise-rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|ar-adtrust-rede-rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-Public.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*duas ocorrências*)

    Além disso, defina a configuração para os locais e na nuvem componentes, conforme descrito nos [Componentes da solução] [ solution-components] seção.

7. Abra uma janela do PowerShell do Azure, mover para a pasta de Scripts e execute o seguinte comando:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Substituir `<subscription id>` com sua ID do Azure assinatura.

    Para `<location>`, especifique uma região Azure, tais como `eastus` ou `westus`.

    O `<mode>` parâmetro pode ter um dos seguintes valores:

    - `Onpremise`, para criar o ambiente local simulada.

    - `Infrastructure`, para criar a infraestrutura de VNet e saltar caixa na nuvem.

    - `CreateVpn`, para criar o gateway de rede virtual Azure e conecte-o à rede local.

    - `AzureADDS`, para construir VMs atuando como ADICIONA servidores, implantar o Active Directory para essas VMs e criar o domínio na nuvem.

    - `WebTier`, que cria web nível VMs e balanceador de carga.

    - `Prepare`, que executa todas as tarefas anteriores. **Esta é a opção recomendada se você estiver criando uma implantação inteiramente nova e você não tiver uma infraestrutura local existente.**

    - `Workload`para criar a camada de dados comerciais e VMs e balanceadores de carga. Essas VMs não são incluídos como parte do `Prepare` opção.

    >[AZURE.NOTE] Se você usar o `Prepare` opção, o script leva várias horas para ser concluída.

8.  Se você estiver usando a configuração de local do exemplo:

    1. Conectar-se à caixa de salto (*ar-adtrust-gerenciamento-vm1* no grupo de recursos de *ar-adtrust-segurança-rg* ). Faça logon como *testuser* com senha *AweS0me@PW*.

    2.  Na caixa de salto abrir uma sessão de RDP na primeira VM no domínio *contoso.com* (o domínio local). Esta máquina virtual tem o endereço IP 192.168.0.4. O nome de usuário é *contoso\testuser* com senha *AweS0me@PW*.

    3. Baixe a [entrada trust.ps1] [ incoming-trust] script e executá-lo para criar a relação de confiança de entrada do domínio *treyresearch.com* .

9. Se você estiver usando seu próprio infraestrutura local:

    1. Baixe a [entrada trust.ps1] [ incoming-trust] script.

    2. Edite o script e substitua o valor da `$TrustedDomainName` variável com o nome do seu próprio domínio.

    3. Execute o script.

10. Na caixa de salto, conectar-se para a máquina virtual primeira no domínio *treyresearch.com* (o domínio na nuvem). Esta máquina virtual tem o endereço IP 10.0.4.4. O nome de usuário é *treyresearch\testuser* com senha *AweS0me@PW*.

11. Baixe o [trust.ps1 de saída] [ outgoing-trust] script e executá-lo para criar a relação de confiança de entrada do domínio *treyresearch.com* . Se você estiver usando seus próprio máquinas locais, edite o script primeiro. Definir o `$TrustedDomainName` variável para o nome do seu domínio local e especifique os endereços IP dos servidores para este domínio AD DS o `$TrustedDomainDnsIpAddresses` variável.

12. Em um computador local, execute as etapas descritas no artigo [Verificar uma confiança] [ verify-a-trust] para determinar se a relação de confiança foi configurada corretamente entre os domínios *contoso.com* e *treyresearch.com* . Você talvez precise esperar alguns minutos após concluir as etapas anteriores antes da confiança possa ser validada.

As etapas opcionais restantes mostram como determinar se a relação de confiança do domínio está funcionando conforme esperado. Estas etapas exigem que você tenha acesso a um computador de desenvolvimento com o Visual Studio instalado.

1.  Na janela Azure PowerShell, execute o seguinte comando para criar a camada da web, se você não configurou-anteriormente (usando o `Prepare` opção):

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> WebTier
    ```

    Esse comando cria a camada da web e adiciona as VMs ao domínio *treyresearch.com* .

2. Usando o Visual Studio no computador de desenvolvimento, crie um aplicativo Web ASP.NET chamado *TreyResearchWebApp*. Use o .NET Framework 4.5.2.

3. Selecione o modelo *MVC* e altere a autenticação para *Autenticação do Windows*. Não crie um serviço de aplicativo na nuvem.

3. Criar e executar o aplicativo para testar a autenticação. Verifique se seu nome de usuário do Windows atual aparece na barra de menus na parte superior da página, em direção à direita.

4. Feche o Internet Explorer.

5. Na janela *Solution Explorer* , clique com botão direito no projeto TreyResearchWebApp, clique em *Publicar*.

6. Na janela *Publicar Web* , clique em *personalizado*. Crie um perfil personalizado chamado *TreyResearchWebApp*.

7. Na página *Conexão* , defina o *método de publicar* sistema de *Arquivos* e especifique uma pasta chamada *TreyResearchWebApp*, localizado em um local conveniente em seu computador de desenvolvimento.

8. Na página *configurações* , defina a *configuração* para *lançamento*.

9. Na página de *visualização* , clique em *Publicar*.

10. Conectar-se a cada máquina virtual na camada da web alternadamente (por meio da caixa de salto) e execute as seguintes tarefas. Os endereços IP da web camada VMs são 10.0.1.4 e 10.0.1.5. O nome de usuário para ambas as VMs é *treyresearch\testuser* com senha *AweS0me@PW*:

    1. Copie a pasta *TreyResearchWebApp* e seu conteúdo de computador de desenvolvimento para a pasta *C:\inetpub* .

    2. Usando o console do Gerenciador de serviços de informações da Internet (IIS), navegue até o *Site da Web Web\Site* no computador.

    3. No painel de *ações* , clique em *Configurações básicas*e altere o caminho físico do site para *%SystemDrive%\inetpub\TreyResearchWebApp*.

    4. No painel de *Exibição de recursos* , clique duas vezes em *autenticação*. Verifique se a *Autenticação do Windows* está habilitado e *Autenticação anônima* é desativada.

11. de um computador local, abra o Internet Explorer e navegue até o site em http://10.0.1.254 (esse é o endereço do balanceador de carga de camada web).

12. Na caixa de diálogo *Segurança do Windows* , insira as credenciais de um usuário no domínio local. Especifique um nome de usuário que não existe no domínio *treyresearch* . Se você estiver usando o ambiente local simulada crie um usuário no domínio *contoso* primeiro e especifique as credenciais do usuário.

13. Quando aparecer a home page, verifique se o domínio correto e o nome de usuário aparecem na barra de menus na parte superior da página, em direção à direita.

## <a name="next-steps"></a>Próximas etapas

- Aprenda as práticas recomendadas para [estender seu domínio local ADICIONA Azure][adds-extend-domain]

- Aprenda as práticas recomendadas para a [criação de uma infraestrutura de ADFS] [ adfs] no Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[implementing-aad]: ./guidance-identity-aad.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[running-VMs-for-an-N-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[naming-conventions]: ./guidance-naming-conventions.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[netdom]: https://technet.microsoft.com/library/cc835085.aspx
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adds-trust/parameters/onpremise/connection.parameters.json
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/incoming-trust.ps1
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/outgoing-trust.ps1
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-adds.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/add-adds-domain-controller.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-public.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-mgmt.parameters.json
[web-vm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/web-vm-domain-join.parameters.json
[solution-components]: [#solution_components]
[0]: ./media/guidance-identity-aad-resource-forest/figure1.png "Arquitetura de rede híbrida com domínios separados do AD protegida"