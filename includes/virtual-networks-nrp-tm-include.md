## <a name="traffic-manager-profile"></a>Perfil do Gerenciador de tráfego

Gerenciador de tráfego e seu recurso de ponto de extremidade de filho habilitam roteamento DNS para pontos de extremidade no Azure e fora do Azure. Distribuição tal tráfego é regulamentada pelos métodos de roteamento de política. Gerenciador de tráfego também permite a integridade do ponto de extremidade monitorar e tráfego desviado adequadamente com base na integridade de um ponto de extremidade. 

| Propriedade | Descrição |
|---|---|
|**trafficRoutingMethod**| valores possíveis são *desempenho*, *ponderado*e *prioridade* | 
| **dnsConfig** | FQDN para o perfil | 
| **Protocolo** | protocolo de monitoramento, os valores possíveis são *HTTP* e *HTTPS*|
| **Porta** | porta de monitoramento |  
| **Caminho** | caminho de monitoramento |
| **Pontos de extremidade** |  contêiner para recursos de ponto de extremidade | 

### <a name="endpoint"></a>Ponto de extremidade 

Um ponto de extremidade é um recurso de filho de um perfil Gerenciador de tráfego. Ele representa um serviço ou ponto de extremidade de web à qual o usuário tráfego é distribuído com base na diretiva configurada no recurso de perfil do Gerenciador de tráfego. 

| Propriedade | Descrição | 
|---|---| 
| **Tipo** |  o tipo do ponto de extremidade, os valores possíveis são *ponto final do Azure*, *Ponto de extremidade externos*e *Aninhada empresa* | 
| **targetResourceId** |  endereço IP público de um ponto de extremidade do serviço ou da web. Isso pode ser um ponto de extremidade do Azure ou externo. | 
| **Peso** | espessura de ponto de extremidade usada no gerenciamento de tráfego. | 
| **Prioridade** | prioridade do ponto de extremidade, usada para definir uma ação de failover |

Exemplo do Gerenciador de tráfego no formato Json: 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, leia a [documentação de API REST Gerenciador de tráfego](https://msdn.microsoft.com/library/azure/mt163664.aspx) .
