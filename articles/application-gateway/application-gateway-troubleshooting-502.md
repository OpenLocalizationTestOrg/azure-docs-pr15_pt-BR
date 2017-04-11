<properties
   pageTitle="Solucionar problemas de erros de Gateway incorreto do aplicativo Gateway (502) | Microsoft Azure"
   description="Saiba como solucionar problemas de erros do aplicativo Gateway 502"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Solucionando erros de gateway incorreto no aplicativo Gateway

## <a name="overview"></a>Visão geral

Depois de configurar um Gateway de aplicativo do Azure, um dos erros que os usuários podem encontrar é "Erro do servidor: 502 - o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy". Isso pode ocorrer devido às seguintes motivos principais:

- Pool de back-end do Azure aplicativo Gateway não está configurado ou vazio.
- Nenhuma das VMs ou instâncias no conjunto de escala de máquina virtual são íntegra.
- VMs ou instâncias do conjunto de escala de máquina virtual de back-end não estão respondendo ao teste de integridade do padrão.
- Configuração inválida ou inadequada de testes de integridade personalizado.
- Solicite tempo limite ou problemas de conectividade com solicitações de usuário.

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazia

### <a name="cause"></a>Causa

Se o Gateway de aplicativo não tiver VMs ou conjunto de escala de máquina virtual configurado no pool de endereços de back-end, ele não pode rotear qualquer solicitação de cliente e gera um erro de gateway incorreto.

### <a name="solution"></a>Solução

Certifique-se de que o pool de endereços de back-end não está vazio. Isso pode ser feito por meio do PowerShell, CLI ou portal.

    Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

A saída do cmdlet anterior deve conter o pool de endereços de back-end não vazio. A seguir é um exemplo onde dois pools são retornados que estejam configurados com endereços IP ou FQDN de back-end VMs. O estado de provisionamento do BackendAddressPool deve ser 'bem-sucedida'.
    
    BackendAddressPoolsText: 
            [{
                "BackendAddresses": [{
                    "ipAddress": "10.0.0.10",
                    "ipAddress": "10.0.0.11"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool01",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
            }, {
                "BackendAddresses": [{
                    "Fqdn": "xyx.cloudapp.net",
                    "Fqdn": "abc.cloudapp.net"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool02",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
            }]


## <a name="unhealthy-instances-in-backendaddresspool"></a>Instâncias não íntegras em BackendAddressPool

### <a name="cause"></a>Causa

Se todas as instâncias do BackendAddressPool não íntegras, Application Gateway não teria qualquer back-end para solicitação de usuário de rota para. Isso também poderia ser o caso quando instâncias de back-end são eficaz, mas não tiver o aplicativo necessário implantado.

### <a name="solution"></a>Solução

Certifique-se de que as instâncias são eficaz, e o aplicativo está configurado corretamente. Verifique se as instâncias de back-end são capazes de responder a um ping de outra máquina virtual no mesmo VNet. Se configurado com um ponto de extremidade público, certifique-se de que uma solicitação de navegador para o aplicativo da web é facilidade de manutenção.

## <a name="problems-with-default-health-probe"></a>Problemas com o teste de integridade de padrão

### <a name="cause"></a>Causa

502 erros também podem ser indicadores frequentes que o teste de integridade de padrão não é capaz de alcançar VMs de back-end. Quando uma instância de aplicativo Gateway está provisionada, ele configura automaticamente um teste de integridade de padrão para cada BackendAddressPool usando as propriedades do BackendHttpSetting. Nenhuma entrada de usuário é necessário configurar esse teste. Especificamente, quando uma regra balanceamento de carga estiver configurada, uma associação é feita entre um BackendHttpSetting e BackendAddressPool. Um teste padrão é configurado para cada um dessas associações e Application Gateway inicia uma conexão de verificação de integridade periódicos para cada instância no BackendAddressPool na porta especificada no elemento BackendHttpSetting. Tabela a seguir lista os valores associados com o teste de integridade do padrão.


|Propriedade de teste | Valor | Descrição|
|---|---|---|
| URL de teste| http://127.0.0.1/ | Caminho de URL |
| Intervalo | 30 | Intervalo de sondagem em segundos |
| Tempo limite  | 30 | Tempo limite de teste em segundos |
| Limite não íntegra | 3 | Teste a contagem de repetição. O servidor back-end está marcado para baixo após a contagem de falha de teste consecutivas atinge o limite não íntegro. |

### <a name="solution"></a>Solução

- Certifique-se de que um site padrão está configurado e está atendendo em 127.0.0.1.
- Se BackendHttpSetting Especifica uma porta diferente de 80, o site padrão deve ser configurado para escutar na porta.
- A chamada para http://127.0.0.1:port deve retornar um código de resultado HTTP de 200. Isso deve ser retornado dentro do período de tempo limite de 30 segundos.
- Certifique-se de que porta configurada é aberta e que não existem regras de firewall ou grupos de segurança de rede Azure, que bloquear tráfego de entrada ou de saída na porta configurado.
- Se o Azure VMs clássicas ou serviço de nuvem é usado com FQDN ou IP públicas, certifique-se de que o [ponto de extremidade](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) de correspondente é aberto.
- Se a máquina virtual está configurada por meio do Gerenciador de recursos do Azure e está fora do VNet onde Application Gateway está implantado, [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) deverá ser configurado para permitir acesso na porta desejada.


## <a name="problems-with-custom-health-probe"></a>Problemas com o teste de integridade personalizado

### <a name="cause"></a>Causa

Testes de integridade personalizado permitem flexibilidade adicional para o padrão de comportamento de sondagem. Ao usar testes personalizados, os usuários podem configurar o intervalo de sondagem, a URL e caminho para testar e quantas respostas com falha para aceitar antes de marcar a instância de pool de back-end como não íntegra. As seguintes propriedades adicionais são adicionadas.

|Propriedade de teste| Descrição|
|---|---|
| Nome | Nome do teste. Esse nome é usado para se referir ao teste nas configurações de HTTP de back-end. |
| Protocolo | Protocolo usado para enviar o teste. O teste usará o protocolo definido nas configurações de HTTP de back-end |
| Host |  Nome de host para enviar o teste. Aplicável somente quando vários locais é configurado no Gateway do aplicativo. Isso é diferente do nome do host de máquina virtual.  |
| Caminho | Caminho relativo do teste. O caminho válido começa da '/'. O teste é enviado à \<protocolo\>://\<host\>:\<porta\>\<caminho\> |
| Intervalo | Teste o intervalo em segundos. Este é o intervalo de tempo entre dois testes consecutivas.|
| Tempo limite | Teste o tempo limite em segundos. O teste é marcado como falha se uma resposta válida não for recebida dentro desse período de tempo limite. |
| Limite não íntegra | Teste a contagem de repetição. O servidor back-end está marcado para baixo após a contagem de falha de teste consecutivas atinge o limite não íntegro. |


### <a name="solution"></a>Solução

Valide se o teste de integridade personalizado está configurado corretamente como a tabela anterior. Além das etapas de solução de problemas a anterior, também Verifique o seguinte:

- Certifique-se de que o teste está corretamente especificado de acordo com o [guia](application-gateway-create-probe-ps.md).
- Se o Gateway de aplicativo é configurado para um único local, por padrão o Host nome deve ser especificado como '127.0.0.1', a menos que o contrário configurada no teste personalizado.
- Certifique-se de que uma chamada para http://\<host\>:\<porta\>\<caminho\> retorna um código de resultado HTTP de 200.
- Certifique-se de que o intervalo, tempo limite e UnhealtyThreshold estão dentro dos intervalos aceitáveis.

## <a name="request-time-out"></a>Solicitação de tempo limite

### <a name="cause"></a>Causa

Quando uma solicitação de usuário for recebida, Application Gateway aplica as regras de configurado para a solicitação e a encaminha para uma instância de pool de back-end. Ele espera por um intervalo de tempo para uma resposta da instância de back-end configurável. Por padrão, esse intervalo é **30 segundos**. Se Application Gateway não recebe uma resposta do aplicativo de back-end nesse intervalo, a solicitação de usuário verá um erro 502.

### <a name="solution"></a>Solução

Gateway de aplicativo permite aos usuários definir esta configuração via BackendHttpSetting, que pode ser aplicado a pools diferentes. Diferentes pools de back-end podem ter BackendHttpSetting diferente e, portanto, diferentes solicitação tempo limite configurado.

    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).
