<properties
   pageTitle="Redefinir um Gateway VPN Azure | Microsoft Azure"
   description="Este artigo o orienta redefinir o Gateway de VPN do Azure. O artigo se aplica ao gateways VPN o clássico e os modelos de implantação do Gerenciador de recursos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Redefinir um Gateway de VPN Azure usando o PowerShell


Este artigo o orienta redefinir o Gateway de VPN Azure usando cmdlets do PowerShell. Essas instruções incluem o modelo clássico de implantação e o modelo de implantação do Gerenciador de recursos.

Redefinir o gateway do Azure VPN é útil se você perder a conectividade VPN entre locais em um ou mais túneis VPN S2S. Nessa situação, seus dispositivos VPN local estão funcionando corretamente, mas não são capazes de estabelecer encapsulamentos de segurança IP com os gateways VPN do Azure. 

Cada gateway Azure VPN é composto de duas instâncias de máquina virtual em execução em uma configuração de espera ativo. Quando você usa o cmdlet do PowerShell para redefinir o gateway, ele reinicializa gateway e reaplica as configurações de local entre a ele. O gateway mantém o endereço IP público já tem. Isso significa que você não precisa atualizar a configuração do roteador VPN com um novo endereço IP público para o gateway VPN do Azure.  

Depois que o comando é emitido, instância atual ativa do gateway VPN do Azure é reiniciada imediatamente. Haverá um breve intervalo durante o failover da instância ativa (sendo reiniciado), para a instância de espera. O espaço deve ser menor que um minuto.

Se a conexão não é restaurada após a primeira reinicialização, execute o comando mesmo novamente para reiniciar a segunda instância de máquina virtual (o novo gateway ativa). Se a dois reinicialização é solicitadas após a outra, haverá um período de um pouco mais onde ambas as instâncias de máquina virtual (ativas e em espera) estão sendo reinicializadas. Isso fará com que um intervalo mais a conectividade VPN, até 2 a 4 minutos para VMs concluir a reinicialização.

Após dois reinicialização, se você ainda estiver tendo problemas de conectividade entre locais, abra uma solicitação de suporte do portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Antes de redefinir seu gateway, verifique se os principais itens listados abaixo para cada túnel VPN IPsec-to-Site (S2S). Qualquer incompatibilidade na itens resultará na desconexão de túneis de VPN S2S. Verificar e corrigir as configurações de seu local e gateways VPN Azure evita que você reinicialização desnecessárias e interrupções para as outras conexões de trabalho em gateways.

Verifique os seguintes itens antes de redefinir seu gateway:

- Os endereços de IP de Internet (VIPs) para o gateway VPN do Azure e o gateway VPN local estão configurados corretamente no Azure e as políticas VPN local.
- A chave previamente compartilhada deve ser o mesmo em gateways VPN do Azure e locais.
- Se você aplicar a configuração de IKE/IPsec específica, como criptografia, hash algoritmos e sigilo (sigilo), verifique os gateways VPN Azure tanto o local têm as mesmas configurações.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Redefinir um Gateway VPN usando o modelo de implantação do gerenciamento de recursos

O cmdlet do PowerShell Gerenciador de recursos para redefinir o gateway é `Reset-AzureRmVirtualNetworkGateway`. O exemplo a seguir redefine o gateway VPN do Azure, "VNet1GW", no grupo de recursos "TestRG1".

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Redefinir um Gateway VPN usando o modelo clássico de implantação

O cmdlet do PowerShell para redefinir o gateway Azure VPN é `Reset-AzureVNetGateway`. O exemplo a seguir redefine o gateway do Azure VPN para a rede virtual chamado "ContosoVNet".
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Resultado:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Próximas etapas
    
Consulte a [referência de cmdlets do PowerShell gerenciamento de serviço](https://msdn.microsoft.com/library/azure/mt617104.aspx) e a [referência de cmdlet do PowerShell Gerenciador de recursos](http://go.microsoft.com/fwlink/?LinkId=828732) para obter mais informações.






