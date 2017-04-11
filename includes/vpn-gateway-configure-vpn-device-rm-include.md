
Para configurar seu dispositivo VPN, será necessário o endereço IP público do gateway rede virtual para configurar seu dispositivo VPN local. Trabalhar com o fabricante do seu dispositivo para obter informações de configuração específicas e configure seu dispositivo. Consulte os [Dispositivos VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) para obter mais informações sobre dispositivos VPN que funcionam bem com o Azure.

Para localizar o endereço IP público do seu gateway de rede virtual usando o PowerShell, use o exemplo a seguir:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Você também pode exibir o endereço IP público para o gateway de rede virtual usando o portal do Azure. Navegue até **os gateways de rede Virtual**e, em seguida, clique no nome do seu gateway.