## <a name="service-chaining---transit-through-peered-vnet"></a>Serviço encadeamento - trânsito por meio de VNet peered

Embora o uso de rotas de sistema facilita o tráfego automaticamente para a sua implantação, há casos em que você deseja controlar o roteamento de pacotes por meio de um dispositivo virtual.
Nesse cenário, há dois VNets em uma assinatura, HubVNet e VNet1 conforme descrito no diagrama abaixo. Você implantar rede Virtual Appliance(NVA) em VNet HubVNet. Após estabelecer VNet correspondência entre HubVNet e VNet1, você pode configurar rotas de definidas pelo usuário e especificar o próximo salto NVA na HubVNet.

![NVA trânsito](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Para simplicidade, suponha que todos VNets aqui estão na mesma assinatura. Mas também funciona para o cenário de assinatura do cruzado.

A propriedade de chave para ativar o roteamento de trânsito é o parâmetro "Permitir o tráfego encaminhado". Isso permite aceitar e enviar tráfego de/para a NVA na VNet peered.  
