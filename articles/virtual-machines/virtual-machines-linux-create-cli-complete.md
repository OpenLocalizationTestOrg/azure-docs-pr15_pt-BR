
<properties
   pageTitle="Criar um ambiente Linux completo utilizando a CLI Azure | Microsoft Azure"
   description="Crie armazenamento, uma VM Linux, uma rede virtual e sub-rede, um balanceador de carga, uma NIC, um IP público e um grupo de segurança de rede, tudo a partir do zero, usando a CLI do Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-complete-linux-environment-by-using-the-azure-cli"></a>Criar um ambiente Linux completo usando a CLI do Azure

Neste artigo, criamos uma rede simples com um balanceador de carga e um par de VMs que são úteis para desenvolvimento e computação simples. Podemos percorrer o processo comando pelo comando, até que você tenha duas VMs Linux funcionando e seguro para que você possa se conectar em qualquer lugar na Internet. Em seguida, você pode mover para redes e ambientes mais complexos.

Durante o percurso, você aprenderá a hierarquia de dependência que oferece a você o modelo de implantação do Gerenciador de recursos e sobre a quantidade de energia ele oferece. Depois de ver como o sistema é criado, você poderá recriá-lo muito mais rapidamente usando [modelos do Gerenciador de recursos do Azure](../resource-group-authoring-templates.md). Além disso, após você aprender como as partes do seu ambiente se encaixam, criação de modelos para automatizá-las se torna mais fácil.

O ambiente contém:

- Duas VMs dentro de um conjunto de disponibilidade.
- Um balanceador de carga com uma regra de balanceamento de carga na porta 80.
- Regras de segurança de grupo (NSG) proteger sua máquina virtual indesejado tráfego de rede.

![Visão geral do ambiente básicas](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Para criar esse ambiente personalizado, você precisa a mais recente [Do Azure CLI](../xplat-cli-install.md) no modo do Gerenciador de recursos (`azure config mode arm`). Você também precisa de uma ferramenta de análise de JSON. Este exemplo usa [jq](https://stedolan.github.io/jq/).

## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar a tarefa, os seguintes detalhes de seção rapidamente a base comandos para carregar uma máquina virtual no Azure. Mais informações detalhadas e contexto para cada etapa pode ser encontrada no restante do documento, inicial [aqui](#detailed-walkthrough).

Certifique-se de que você tenha [A CLI Azure](../xplat-cli-install.md) conectado e usando o modo do Gerenciador de recursos:

```bash
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Incluem nomes de parâmetro de exemplo `myResourceGroup`, `mystorageaccount`, e `myVM`.

Crie o grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no `westeurope` local:

```bash
azure group create -n myResourceGroup -l westeurope
```

Verifique se o grupo de recursos usando o analisador JSON:

```bash
azure group show myResourceGroup --json | jq '.'
```

Crie a conta de armazenamento. O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount` (o nome da conta de armazenamento deve ser exclusivo, portanto forneça seu próprio nome exclusivo):

```bash
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Verificar a conta de armazenamento usando o analisador JSON:

```bash
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Crie a rede virtual. O exemplo a seguir cria uma rede virtual chamada `myVnet`:

```bash
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Crie uma sub-rede. O exemplo a seguir cria uma sub-rede denominada `mySubnet`"

```bash
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Verificar a rede virtual e sub-rede usando o analisador JSON:


```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Crie um IP público. O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns` (o nome DNS deve ser exclusivo, portanto forneça seu próprio nome exclusivo):

```bash
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Crie o balanceador de carga. O exemplo a seguir cria um balanceador de carga denominado `myLoadBalancer`:

```bash
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Criar um pool IP front-end para o balanceador de carga e associar o IP público. O exemplo a seguir cria um pool IP front-end denominado `mySubnetPool`:

```bash
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool 
```

Crie o pool IP de back-end para o balanceador de carga. O exemplo a seguir cria um pool IP de back-end denominado `myBackEndPool`:

```bash
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Crie regras de conversão (NAT) de endereço para o balanceador de carga de rede de entrada SSH. O exemplo a seguir cria duas regras de Balanceador de carga, `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```bash
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Criar web regras NAT para o balanceador de carga de entrada. O exemplo a seguir cria uma regra de Balanceador de carga chamada`myLoadBalancerRuleWeb`

```bash
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Crie o teste de integridade de Balanceador de carga. O exemplo a seguir cria um teste TCP chamado `myHealthProbe`:

```bash
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Verificar o balanceador de carga, pools de IP e regras NAT usando o analisador JSON:

```bash
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Crie o primeiro cartão de interface de rede (NIC). Substituir o `#####-###-###` seções com sua própria identificação de assinatura Azure. Sua assinatura ID é indicado na saída do `jq` ao examinar os recursos que você está criando. Você também pode ver sua ID de assinatura com `azure account list`. 

O exemplo a seguir cria uma NIC denominada `myNic1`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Criar a segunda NIC. O exemplo a seguir cria uma NIC denominada `myNic2`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Verificar as duas NICs usando o analisador JSON:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Crie o grupo de segurança de rede. O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```bash
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Adicione duas regras de entrada para o grupo de segurança de rede. O exemplo a seguir cria duas regras, `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```bash
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verifique se o grupo de segurança de rede e as regras de entrada usando o analisador JSON:

```bash
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Vincule o grupo de segurança de rede a NICs dois:

```bash
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Crie o conjunto de disponibilidade. O exemplo a seguir cria uma conjunto nomeada de disponibilidade `myAvailabilitySet`:

```bash
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Crie a primeira VM Linux. O exemplo a seguir cria uma máquina virtual chamada `myVM1`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Crie a segunda VM Linux. O exemplo a seguir cria uma máquina virtual chamada `myVM2`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Use o analisador JSON para verificar se tudo o que foi criado:

```bash
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exporte seu novo ambiente a um modelo para recriar rapidamente novas instâncias:

```bash
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Explicação passo a passo detalhada
As etapas detalhadas seguir explicam o que cada comando está fazendo à medida que seu ambiente. Esses conceitos são úteis quando você cria suas própria ambientes personalizados para desenvolvimento ou produção.

Certifique-se de que você tenha [A CLI Azure](../xplat-cli-install.md) conectado e usando o modo do Gerenciador de recursos:

```bash
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Incluem nomes de parâmetro de exemplo `myResourceGroup`, `mystorageaccount`, e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Criar grupos de recursos e escolha locais de implantação

Grupos de recursos Azure são entidades de implantação lógica que contêm informações de configuração e metadados para habilitar o gerenciamento de lógico de implantações de recurso. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no `westeurope` local:

```bash
azure group create --name myResourceGroup --location westeurope
```

Saída:

```bash                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Você precisará contas de armazenamento para seus discos de máquina virtual e para qualquer disco de dados adicionais que você deseja adicionar. Você criar contas de armazenamento quase imediatamente depois que você cria grupos de recursos.

Aqui, nós usamos o `azure storage account create` comando, passando o local da conta, grupo de recursos que controla-lo e o tipo de suporte de armazenamento desejada. O exemplo a seguir cria uma conta de armazenamento denominada `mystorageaccount`:

```bash
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Saída:

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Para examinar nosso grupo de recursos usando o `azure group show` comando, vamos usar a ferramenta de [jq](https://stedolan.github.io/jq/) junto com o `--json` opção CLI do Azure. (Você pode usar **jsawk** ou qualquer biblioteca de idioma que você preferir para analisar o JSON.)

```bash
azure group show myResourceGroup --json | jq '.'
```

Saída:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Para investigar a conta de armazenamento, usando a CLI, primeiro é necessário definir as teclas e nomes de conta. Substitua o nome da conta de armazenamento no exemplo a seguir com um nome que você escolher:

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Em seguida, você pode ver suas informações de armazenamento facilmente:

```bash
azure storage container list
```

Saída:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e sub-rede

Em seguida você vai precisar criar uma rede virtual em execução no Azure e uma sub-rede na qual você pode criar suas VMs. O exemplo a seguir cria uma rede virtual chamada `myVnet` com a `192.168.0.0/16` prefixo de endereço:

```bash
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16 
```

Saída:

```bash
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Novamente, vamos usar a opção – json de `azure group show` e **jq** para ver como que estamos construindo nossos recursos. Agora temos um `storageAccounts` recurso e um `virtualNetworks` recurso.  

```bash
azure group show myResourceGroup --json | jq '.'
```

Saída:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Agora vamos criar uma sub-rede no `myVnet` rede virtual no qual as VMs são implantadas. Usamos o `azure network vnet subnet create` comando, juntamente com os recursos que já criamos: o `myResourceGroup` grupo de recursos e o `myVnet` rede virtual. No exemplo a seguir, vamos adicionar sub-rede denominada `mySubnet` com o prefixo de endereço de sub-rede `192.168.1.0/24`:

```bash
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Saída:

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Como a sub-rede é logicamente dentro da rede virtual, podemos procure as informações da sub-rede com um comando ligeiramente diferentes. O comando usamos é `azure network vnet show`, mas podemos continuar examinar a saída JSON usando **jq**.

```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Saída:

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address-pip"></a>Criar um endereço IP público (PIP)

Agora vamos criar o endereço IP público (PIP) que podemos atribuir a seu balanceador de carga. Ele permite que você se conectar à sua VMs da Internet usando o `azure network public-ip create` comando. Como o endereço padrão é dinâmico, podemos criar uma entrada de DNS nomeada no domínio **cloudapp.azure.com** usando o `--domain-name-label` opção. O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns`. Como o nome DNS deve ser exclusivo, forneça seu próprio nome de DNS exclusivo:

```bash
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Saída:

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

O endereço IP público também é um recurso de nível superior, para que você possa vê-lo com `azure group show`.

```bash
azure group show myResourceGroup --json | jq '.'
```

Saída:

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Você pode investigar mais detalhes do recurso, incluindo o nome de domínio totalmente qualificado (FQDN) do subdomínio, usando o completo `azure network public-ip show` comando. O recurso de endereço IP público tenha sido alocado logicamente, mas um endereço específico ainda não foi atribuído. Para obter um endereço IP, você vai precisar de um balanceador de carga, nós ainda não tiver criado.

```bash
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Saída:

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Criar um balanceador de carga e pools de IP
Quando você cria um balanceador de carga, ele permite distribuir o tráfego entre várias VMs. Ele também fornece redundância para seu aplicativo executando várias VMs que respondem a solicitações de usuário em caso de manutenção ou sobrecarga. O exemplo a seguir cria um balanceador de carga denominado `myLoadBalancer`:

```bash
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Saída:

```bash
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Nossa balanceador de carga é bastante vazio, portanto, vamos criar alguns pools de IP. Queremos criar dois pools IP para nosso balanceador de carga - um para o front-end e outro para back-end. Pool de front-end IP está visível publicamente. Também é o local para o qual podemos atribuir o PIP que criamos anteriormente. Em seguida, usamos o pool de back-end como um local para nossos VMs para se conectar ao. Dessa forma, o tráfego pode fluir pelo Balanceador de carga às VMs.

Primeiro, vamos criar nossa pool de front-end de IP. O exemplo a seguir cria um pool de front-end denominado `myFrontEndPool`:

```bash
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool 
```

Saída:

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Observe como usamos os `--public-ip-name` alternar para passar o `myPublicIP` que criamos anteriormente. Atribuir o endereço IP público ao balanceador de carga permite que você atinja suas VMs através da Internet.

Em seguida, vamos criar nossa segundo pool de IP, desta vez para nosso tráfego de back-end. O exemplo a seguir cria um pool de back-end denominado `myBackEndPool`:

```bash
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Saída:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Podemos ver como o nosso balanceador de carga está fazendo olhando com `azure network lb show` e examinando a saída JSON:

```bash
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Saída:

```bash
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Criar regras NAT de Balanceador de carga
Para obter o tráfego fluir pelo nosso balanceador de carga, precisamos criar regras de conversão (NAT) que especificam ações de entrada ou saídas de endereço de rede. Você pode especificar o protocolo usar e mapear portas externas para portas internas conforme desejado. Para nosso ambiente, vamos criar algumas regras que permitem SSH por meio de nosso balanceador de carga em nossas VMs. Podemos configurar as portas TCP 4222 e 4223 para direcionar a porta TCP 22 em nossas VMs (que criamos mais tarde). O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleSSH1` para mapear porta TCP 4222 porta 22:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Saída:

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Repita o procedimento para a segunda regra NAT para o SSH. O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleSSH2` para mapear porta TCP 4223 porta 22:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Vamos também ir adiante e criar uma regra NAT para a porta TCP 80 para tráfego da web, interceptação a regra para cima até o nossos pools de IP. Se nós ligar a regra de pool IP, em vez de conectar a regra de nossas VMs individualmente, podemos pode adicionar ou remover VMs de pool de IP. O balanceador de carga ajusta automaticamente o fluxo de tráfego. O exemplo a seguir cria uma regra chamada `myLoadBalancerRuleWeb` para mapear a porta TCP 80 para porta 80:

```bash
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Saída:

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Criar um teste de integridade de Balanceador de carga

Um integridade periodicamente teste verificações nas VMs atrás nosso balanceador de carga para garantir que eles são operacionais e responder às solicitações conforme definido. Caso contrário, forem removidos da operação para garantir que os usuários não estão sendo direcionados a eles. Você pode definir verificações personalizadas para o teste de integridade, juntamente com intervalos e valores de tempo limite. Para obter mais informações sobre testes de integridade, consulte [testes de Balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). O exemplo a seguir cria um TCP integridade analisada nomeado `myHealthProbe`:

```bash
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Saída:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Aqui, estamos especificado um intervalo de 15 segundos para nossas verificações de integridade. Nós pode perder um máximo de quatro testes (um minuto) antes de Balanceador de carga considera que o host não está mais funcionando.

## <a name="verify-the-load-balancer"></a>Verifique se o balanceador de carga
Agora a configuração de Balanceador de carga é feita. Aqui estão as etapas usadas:

1. Primeiro você criou um balanceador de carga.
2. Em seguida, você criou um pool de front-end de IP e atribuído um IP público.
3. Em seguida, você criou um pool IP de back-end que VMs podem se conectar a.
4. Depois disso, você criou regras NAT que permitem SSH às VMs para gerenciamento, juntamente com uma regra que permite a porta TCP 80 para nosso aplicativo web.
5. Por fim, você adicionou um teste de integridade para verificar periodicamente as VMs. Este teste de integridade garante que os usuários não tentam acessar uma máquina virtual que já não está funcionando ou servir conteúdo.

Vamos revisar a aparência de seu balanceador de carga agora:

```bash
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Saída:

```bash
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Criar uma NIC para usar com o Linux VM

NICs estão disponíveis programaticamente porque você pode aplicar regras para seu uso. Você também pode ter mais de um. No seguinte `azure network nic create` comando, você ligar a NIC ao pool IP de back-end de carga e associá-lo com a regra NAT para permitir o tráfego SSH.
 
Substituir o `#####-###-###` seções com sua própria identificação de assinatura Azure. Sua assinatura ID é indicado na saída do `jq` ao examinar os recursos que você está criando. Você também pode ver sua ID de assinatura com `azure account list`. 

O exemplo a seguir cria uma NIC denominada `myNic1`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Saída:

```bash
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Você pode ver os detalhes examinando o recurso diretamente. Examinar o recurso usando o `azure network nic show` comando:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Saída:

```bash
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Agora podemos criar a segunda NIC, interceptação no nosso pool IP de back-end novamente. Essa regra NAT tempo a segunda permite o tráfego SSH. O exemplo a seguir cria uma NIC denominada `myNic2`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Criar um grupo de segurança de rede e regras

Agora podemos criar um grupo de segurança de rede e as regras de entrada que regem o acesso à NIC. Um grupo de segurança de rede podem ser aplicado a uma NIC ou sub-rede. Você define regras para controlar o fluxo de tráfego e sair de suas VMs. O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Vamos adicionar a regra de entrada para o NSG permitir conexões de entrada na porta 22 (para oferecer suporte SSH). O exemplo a seguir cria uma regra chamada `myNetworkSecurityGroupRuleSSH` para permitir que a porta TCP 22:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Agora vamos adicionar a regra de entrada para o NSG permitir conexões de entrada na porta 80 (para suportar tráfego da web). O exemplo a seguir cria uma regra chamada `myNetworkSecurityGroupRuleHTTP` para permitir que a porta TCP 80:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [AZURE.NOTE] A regra de entrada é um filtro para conexões de rede de entrada. Neste exemplo, podemos vincular a NSG para a NIC virtual VMs, o que significa que qualquer solicitação de porta 22 é passada para a NIC na nossa máquina virtual. Essa regra de entrada é sobre uma conexão de rede e não sobre um ponto de extremidade, que é o que seria sobre nas implantações clássicas. Para abrir uma porta, você deve deixar o `--source-port-range` definida como '\*' (o valor padrão) para aceitar solicitações de entrada de **qualquer** solicitando porta. Portas são normalmente dinâmicas.

## <a name="bind-to-the-nic"></a>Vincular a NIC

Vincule a NSG a NICs. Precisamos conectar nossas NICs com nosso grupo de segurança de rede. Execute os dois comandos, para ligar ambas nossas NICs:

```bash
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```bash
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Disponibilidade define ajuda disseminação suas VMs em domínios de falha e domínios de atualização. Vamos criar uma disponibilidade definida para suas VMs. O exemplo a seguir cria uma conjunto nomeada de disponibilidade `myAvailabilitySet`:

```bash
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domínios de falha definem um agrupamento de máquinas virtuais que compartilham uma opção de origem e de rede de energia comuns. Por padrão, as máquinas virtuais que são configuradas em seu conjunto de disponibilidade são separadas em até três domínios de falha. A ideia é que um problema de hardware em um desses domínios de falhas não afeta a cada máquina virtual que esteja executando o aplicativo. Azure distribui automaticamente VMs entre os domínios de falha ao colocá-los em um conjunto de disponibilidade.

Domínios atualização indicam grupos de máquinas virtuais e hardware físico subjacente que pode ser reiniciado ao mesmo tempo. A ordem na qual atualização domínios são reinicializados pode não estar sequencial durante a manutenção planejada, mas somente uma atualização é reinicializada uma vez. Novamente, Azure distribui automaticamente suas VMs em domínios atualização quando colocando-as em um site de disponibilidade.

Leia mais sobre como [Gerenciar a disponibilidade de VMs](./virtual-machines-linux-manage-availability.md).

## <a name="create-the-linux-vms"></a>Criar VMs Linux

Você criou os recursos de rede e de armazenamento para oferecer suporte a VMs acessível pela Internet. Agora vamos criar esses VMs e protegê-las com uma chave SSH que não tem uma senha. Nesse caso, vamos criar um Ubuntu máquina virtual com base na LTS mais recente. Vamos localizar informações imagem usando `azure vm image list`, conforme descrito em [encontrar imagens de máquina virtual do Azure](virtual-machines-linux-cli-ps-findimage.md).

Selecionamos uma imagem usando o comando `azure vm image list westeurope canonical | grep LTS`. Nesse caso, usamos `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Para o último campo, podemos passar `latest` para que no futuro estamos sempre chegar a compilação mais recente. (A cadeia de caracteres que usamos é `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

A próxima etapa é familiar para qualquer pessoa que já tenha criado um ssh chave pública e privada de rsa emparelhar no Linux ou Mac usando **ssh keygen-t rsa -b 2048**. Se você não tem quaisquer pares de chave de certificado no seu `~/.ssh` diretório, você pode criá-los:

- Automaticamente, usando o `azure vm create --generate-ssh-keys` opção.
- Manualmente, usando [as instruções de criá-las](virtual-machines-linux-mac-create-ssh-keys.md).

Como alternativa, você pode usar o `--admin-password` método para autenticar suas conexões SSH após a máquina virtual é criada. Esse método é geralmente menos seguro.

Podemos criar a máquina virtual colocando todos os nossos recursos e informações junto com o `azure vm create` comando:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Saída:

```bash
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Você pode se conectar à sua máquina virtual imediatamente usando suas chaves SSH padrão. Certifique-se de que você especifique a porta apropriada desde que estamos passando por meio de Balanceador de carga. (Para nossa máquina virtual primeiro, vamos configurar a regra NAT para encaminhar porta 4222 para nossa máquina virtual):

```bash
 ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Saída:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Ir adiante e crie sua máquina virtual segundo da mesma maneira:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

E agora você pode usar o `azure vm show myResourceGroup myVM1` comando para examinar o que você criou. Neste ponto, você está executando suas VMs Ubuntu atrás de um balanceador de carga no Azure que você possa entrar somente com seu par de chaves SSH (porque as senhas estão desativadas). Você pode instalar nginx ou httpd, implantar um aplicativo web e ver o tráfego fluxo por meio de Balanceador de carga para ambas as VMs.

```bash
azure vm show --resource-group myResourceGroup --name myVM1
```

Saída:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportar o ambiente como um modelo
Agora que você criou esse ambiente, e se você deseja criar um ambiente de desenvolvimento adicional com os mesmos parâmetros ou um ambiente de produção que corresponde a ele? Gerenciador de recursos usa modelos JSON que definem todos os parâmetros para seu ambiente. Criar ambientes inteira referenciando este modelo JSON. Você pode [criar modelos JSON manualmente](../resource-group-authoring-templates.md) ou exportar um ambiente existente para criar o modelo JSON para você:

```bash
azure group export --name myResourceGroup
```

Esse comando cria o `myResourceGroup.json` arquivo no seu diretório de trabalho atual. Quando você cria um ambiente com este modelo, você será solicitado para todos os nomes de recursos, incluindo os nomes para o balanceador de carga, interfaces de rede ou VMs. Você pode preencher esses nomes no seu arquivo de modelo adicionando o `-p` ou `--includeParameterDefaultValue` parâmetro para o `azure group export` comando que foi mostrado anteriormente. Edite seu modelo JSON para especificar os nomes dos recursos ou [criar um arquivo de parameters.json](../resource-group-authoring-templates.md#parameters) que especifica os nomes dos recursos.

Para criar um ambiente de seu modelo:

```bash
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Talvez você queira ler [mais sobre como implantar a partir de modelos](../resource-group-template-deploy-cli.md). Saiba mais sobre como atualizar ambientes, usar o arquivo de parâmetros e acessar os modelos de um local de armazenamento única forma incremental.

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para começar a trabalhar com vários componentes de rede e VMs. Você pode usar esse ambiente de amostra construir seu aplicativo usando os componentes principais do introduzido aqui.
