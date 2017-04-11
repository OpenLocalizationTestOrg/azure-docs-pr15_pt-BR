## <a name="virtual-network"></a>Rede virtual
Recursos de redes (VNET) e sub-redes virtuais ajudam a definir um limite de segurança para cargas de trabalho em execução no Azure. Um VNet é caracterizada por um conjunto de espaços de endereço, definido como blocos CIDR. 

>[AZURE.NOTE] Os administradores de rede estão familiarizados com notação CIDR. Se você não estiver familiarizado com CIDR, [Saiba mais sobre ele](http://whatismyipaddress.com/cidr).

![VNet com várias sub-redes](./media/resource-groups-networking/Figure4.png)

VNets contêm as seguintes propriedades.

|Propriedade|Descrição|Valores de exemplo|
|---|---|---|
|**addressSpace**|Coleção de prefixos de endereços que constituem o VNet na notação CIDR|192.168.0.0/16|
|**sub-redes**|Coleção de sub-redes que compõem o VNet|consulte [sub-redes](#Subnets) abaixo.|
|**endereço IP**|Endereço IP atribuído a um objeto. Esta é uma propriedade de somente leitura.|104.42.233.77|

### <a name="subnets"></a>Sub-redes
Uma sub-rede é um recurso de filho de um VNet e ajuda a definir segmentos de espaços de endereço em um bloco CIDR, usando prefixos de endereço IP. NICs podem ser adicionadas a sub-redes e conectadas ao VMs, fornecendo conectividade para diversas cargas de trabalho.

Sub-redes contêm as seguintes propriedades. 

|Propriedade|Descrição|Valores de exemplo|
|---|---|---|
|**addressPrefix**|Prefixo único endereço que compõem a sub-rede na notação CIDR|192.168.1.0/24|
|**networkSecurityGroup**|NSG aplicado à sub-rede|consulte [NSGs](#Network-Security-Group)|
|**routeTable**|Tabela de rota aplicada à sub-rede|consulte [UDR](#Route-table)|
|**ipConfigurations**|Conjunto de objetos de configuração IP usados pelo NICs conectados à sub-rede|consulte [UDR](#Route-table)|


VNet de amostra no formato JSON:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Recursos adicionais

- Obter mais informações sobre [VNet](../articles/virtual-network/virtual-networks-overview.md).
- Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) para VNets.
- Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) para sub-redes.