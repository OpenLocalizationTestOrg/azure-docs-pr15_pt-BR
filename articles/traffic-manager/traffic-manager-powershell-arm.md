<properties
    pageTitle="Suporte ao gerente de recursos Azure tráfego Manager | Microsoft Azure "
    description="Usando o PowerShell do Gerenciador de tráfego com o Azure Resource Manager"
    services="traffic-manager"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="azure-resource-manager-support-for-azure-traffic-manager"></a>Suporte ao gerente de recursos Azure Azure tráfego Manager

Azure Gerenciador de recursos é a interface de gerenciamento preferida para serviços no Azure. Azure perfis do Gerenciador de tráfego podem ser gerenciados usando ferramentas e APIs baseadas em Gerenciador de recursos do Azure.

## <a name="resource-model"></a>Modelo de recursos

Gerenciador de tráfego Azure é configurado usando um conjunto de configurações chamado um perfil do Gerenciador de tráfego. Esse perfil contém configurações DNS, configurações de roteamento de tráfego, configurações de monitoramento do ponto de extremidade e uma lista de pontos de extremidade do serviço para o qual o tráfego é roteado.

Cada perfil do Gerenciador de tráfego é representado por um recurso do tipo 'TrafficManagerProfiles'. No nível da API REST, o URI para cada perfil é da seguinte maneira:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="comparison-with-the-azure-traffic-manager-classic-api"></a>Comparação com a API de clássico do Gerenciador de tráfego do Azure

O suporte do Gerenciador de recursos do Azure para o Gerenciador de tráfego usa terminologia diferente que o modelo clássico de implantação. A tabela a seguir mostra as diferenças entre os termos do Gerenciador de recursos e clássico:

| Termos do Gerenciador de recursos | Termos clássico |
|-----------------------|--------------|
| Método de roteamento de tráfego | Método de balanceamento de carga |
| Método de prioridade | Método de failover |
| Método ponderado | Método de round robin |
| Método de desempenho | Método de desempenho |

Com base nos comentários do cliente, alteramos a terminologia para melhorar a clareza e reduzir a confusão comuns. Não há nenhuma diferença de funcionalidade.

## <a name="limitations"></a>Limitações

Ao fazer referência a um ponto de extremidade do tipo 'AzureEndpoints' para um aplicativo Web, pontos de extremidade do Gerenciador de tráfego podem fazer referência apenas o padrão (produção) [slot Web App](../app-service-web/web-sites-staged-publishing.md). Slots personalizadas não são suportados. Como alternativa, slots personalizadas podem ser configurados usando o tipo de 'ExternalEndpoints'.

## <a name="setting-up-azure-powershell"></a>Configuração do PowerShell do Azure

Estas instruções usam Microsoft Azure PowerShell. O seguinte artigo explica como instalar e configurar o Azure PowerShell.

- [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)

Os exemplos deste artigo presumem que você tenha um grupo de recursos existentes. Você pode criar um grupo de recursos usando o seguinte comando:

```powershell
    New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

>[AZURE.NOTE] Gerenciador de recursos de Azure requer que todos os grupos de recurso tenham um local. Esse local é usado como o padrão para recursos criados desse grupo de recursos. No entanto, como os recursos de perfil do Gerenciador de tráfego são global, não regionais, a opção de local do grupo de recursos não tem impacto sobre o Gerenciador de tráfego do Azure.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de tráfego

Para criar um perfil do Gerenciador de tráfego, use o cmdlet New-AzureRmTrafficManagerProfile:

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A tabela a seguir descreve os parâmetros:

| Parâmetro | Descrição |
|-----------|-------------|
| Nome | O nome do recurso para o recurso de perfil do Gerenciador de tráfego. Perfis no mesmo grupo de recursos devem ter nomes exclusivos. Esse nome é separado do nome DNS usado para consultas DNS.|
| ResourceGroupName | O nome do grupo de recursos que contém o recurso de perfil.|
| TrafficRoutingMethod | Especifica o método de roteamento de tráfego usado para determinar qual ponto de extremidade é retornado em resposta uma consulta DNS. Valores possíveis são 'Desempenho', 'Ponderado' ou 'Priority'.|
| RelativeDnsName | Especifica a parte do nome do host do nome DNS fornecida por este perfil do Gerenciador de tráfego. Esse valor é combinado com o nome de domínio DNS usado pelo Gerenciador de tráfego do Azure para formar o nome de domínio totalmente qualificado (FQDN) do perfil. Por exemplo, configurando o valor de 'contoso' fica 'contoso.trafficmanager.net'.|
| TTL | Especifica o DNS Time-to-Live (TTL), em segundos. Este TTL informa o resolvedores DNS Local e quanto respostas DNS de cache para este perfil de tráfego Gerenciador de clientes do DNS.|
| MonitorProtocol | Especifica o protocolo usar para monitorar a saúde do ponto de extremidade. Valores possíveis são 'HTTP' e 'HTTPS'.|
| MonitorPort | Especifica a porta TCP usada para monitorar a saúde do ponto de extremidade.|
| MonitorPath | Especifica o caminho relativo o nome de domínio de ponto de extremidade usado para teste para integridade do ponto de extremidade.|

O cmdlet cria um perfil do Gerenciador de tráfego no Azure e retorna um objeto de perfil correspondente ao PowerShell. Neste ponto, o perfil não contém quaisquer pontos de extremidade. Para obter mais informações sobre a adição de pontos de extremidade para um perfil do Gerenciador de tráfego, consulte [Adicionar pontos de extremidade do Gerenciador de tráfego](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Obter um perfil do Gerenciador de tráfego

Para recuperar um objeto de perfil do Gerenciador de tráfego existente, use o cmdlet Get-AzureRmTrafficManagerProfle:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Esse cmdlet retorna um objeto de perfil do Gerenciador de tráfego.

## <a name="update-a-traffic-manager-profile"></a>Atualizar um perfil do Gerenciador de tráfego

Modificar perfis do Gerenciador de tráfego segue um processo de 3 etapas:

1. Recuperar o perfil usando Get-AzureRmTrafficManagerProfile ou usar o perfil retornado por AzureRmTrafficManagerProfile de novo.
2. Modificar o perfil. Você pode adicionar e remover pontos de extremidade ou alterar parâmetros de ponto de extremidade ou perfil. Essas alterações são operações off-line. Você está alterando apenas o objeto local na memória que representa o perfil.
3. Confirme as alterações usando o cmdlet Set-AzureRmTrafficManagerProfile.

Todas as propriedades de perfil podem ser alteradas, exceto RelativeDnsName do perfil. Para alterar o RelativeDnsName, você deve excluir o perfil e um novo perfil com um novo nome.

O exemplo a seguir demonstra como alterar TTL do perfil:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    $profile.Ttl = 300
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Há três tipos de pontos de extremidade do Gerenciador de tráfego:

1. **Pontos de extremidade Azure** são serviços hospedados no Azure
2. **Pontos de extremidade externos** são serviços hospedados fora do Azure
3. **Pontos de extremidade de aninhado** são usados para construir aninhadas hierarquias de perfis do Gerenciador de tráfego. Pontos de extremidade aninhados Habilitar configurações avançadas de roteamento de tráfego para aplicativos complexos.

Em todos os três casos, os pontos de extremidade podem ser adicionados de duas maneiras:

1. Usando um processo de 3 etapas descrito anteriormente. A vantagem desse método é que podem ser feitas várias alterações de ponto de extremidade em uma única atualização.
2. Usando o cmdlet New-AzureRmTrafficManagerEndpoint. Esse cmdlet adiciona um ponto de extremidade a um perfil de Gerenciador de tráfego existente em uma única operação.

## <a name="adding-azure-endpoints"></a>Adicionando pontos de extremidade Azure

Pontos de extremidade Azure referência aos serviços hospedados no Azure. Três tipos de pontos de extremidade Azure são suportados:

1. Aplicativos Web do Azure
2. 'Clássico' Serviços (que podem conter um serviço de PaaS ou máquinas virtuais de IaaS) em nuvem
3. Recursos PublicIpAddress Azure (que podem ser anexados a um balanceador de carga ou uma máquina virtual NIC). O PublicIpAddress deve ter um nome DNS atribuído a ser usado no Gerenciador de tráfego.

Em cada caso:

- O serviço é especificado usando o parâmetro 'targetResourceId' de adicionar AzureRmTrafficManagerEndpointConfig ou AzureRmTrafficManagerEndpoint de novo.
- A 'Target' e 'EndpointLocation' estão implícitas pela TargetResourceId.
- Especificar a 'espessura' é opcional. Pesos são usados somente se o perfil está configurado para usar o método de roteamento de tráfego de 'Ponderado'. Caso contrário, elas são ignoradas. Se especificado, o valor deve ser um número entre 1 e 1000. O valor padrão é '1'.
- Especificar a 'prioridade' é opcional. Prioridades são usadas somente se o perfil está configurado para usar o método de roteamento de tráfego de 'Priority'. Caso contrário, elas são ignoradas. Valores válidos são de 1 a 1000 com valores inferiores indicando uma prioridade mais alta. Se especificado para um ponto de extremidade, deve ser especificados para todos os pontos de extremidade. Se omitido, os valores padrão a partir de '1' são aplicados na ordem em que os pontos de extremidade estão listados.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Exemplo 1: Adicionar pontos de extremidade do Web App usando Adicionar AzureRmTrafficManagerEndpointConfig

Neste exemplo, podemos criar um perfil do Gerenciador de tráfego e adicionar dois pontos de extremidade do Web App usando o cmdlet Add-AzureRmTrafficManagerEndpointConfig.

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $webapp1 = Get-AzureRMWebApp -Name webapp1
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
    $webapp2 = Get-AzureRMWebApp -Name webapp2
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-a-classic-cloud-service-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionar um ponto de extremidade do serviço de nuvem 'clássico' usando New-AzureRmTrafficManagerEndpoint

Neste exemplo, um ponto de extremidade do serviço de nuvem 'clássico' é adicionado a um perfil de Gerenciador de tráfego. Neste exemplo, podemos especificado no perfil usando os nomes de grupo de recursos e de perfil, em vez de passar um objeto de perfil. As duas abordagens são suportadas.

    $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    New-AzureRmTrafficManagerEndpoint -Name MyCloudServiceEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $cloudService.Id -EndpointStatus Enabled

### <a name="example-3-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 3: Adicionando um ponto de extremidade de publicIpAddress usando New-AzureRmTrafficManagerEndpoint

Neste exemplo, um recurso de endereço IP público é adicionado para o perfil do Gerenciador de tráfego. O endereço IP público deve ter um nome DNS configurado e pode ser vinculado a NIC de uma máquina virtual ou um balanceador de carga.

    $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled

## <a name="adding-external-endpoints"></a>Adicionando pontos de extremidade externos

Gerenciador de tráfego usa pontos de extremidade externos para direcionar o tráfego para serviços hospedado fora do Azure. Como com pontos de extremidade Azure, pontos de extremidade externos podem ser adicionados usando Add-AzureRmTrafficManagerEndpointConfig seguido Set-AzureRmTrafficManagerProfile ou AzureRMTrafficManagerEndpoint de novo.

Ao especificar pontos de extremidade externos:

- O nome de domínio do ponto de extremidade deve ser especificado usando o parâmetro 'Target'
- Se for usado o método de roteamento de tráfego de 'Desempenho', 'EndpointLocation' é necessário. Caso contrário, é opcional. O valor deve ser um [nome válido de região Azure](https://azure.microsoft.com/regions/).
- A 'Espessura' e 'Priority' são opcionais.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Adicionando pontos de extremidade externos usando Adicionar AzureRmTrafficManagerEndpointConfig e Set-AzureRmTrafficManagerProfile

Neste exemplo, podemos criar um perfil do Gerenciador de tráfego, adicionar dois pontos finais externos e confirmar as alterações.

    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionar empresas externas usando New-AzureRmTrafficManagerEndpoint

Neste exemplo, vamos adicionar um ponto de extremidade externo a um perfil existente. O perfil é especificado usando os nomes de grupo de recursos e de perfil.

    New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled

## <a name="adding-nested-endpoints"></a>Adicionando pontos de extremidade de 'Aninhado'

Cada perfil do Gerenciador de tráfego Especifica um único método de roteamento de tráfego. No entanto, há situações que exigem mais sofisticadas roteamento de tráfego que o roteamento fornecida por um único perfil do Gerenciador de tráfego. Você pode aninhar perfis do Gerenciador de tráfego para combinar as vantagens de mais de um método de roteamento de tráfego. Perfis aninhados permitem substituir o comportamento de tráfego gerenciador padrão de suporte maior e mais complexas implantações de aplicativos. Para obter exemplos mais detalhados, consulte [perfis do Gerenciador de tráfego aninhada](traffic-manager-nested-profiles.md).

Pontos de extremidade aninhados são configurados em perfil pai, usando um tipo de ponto de extremidade específico, 'NestedEndpoints'. Ao especificar pontos de extremidade aninhados:

- O ponto de extremidade deve ser especificado usando o parâmetro 'targetResourceId'
- Se for usado o método de roteamento de tráfego de 'Desempenho', 'EndpointLocation' é necessário. Caso contrário, é opcional. O valor deve ser um [nome válido de região Azure](http://azure.microsoft.com/regions/).
- A 'Espessura' e 'Priority' são opcionais, como pontos de extremidade Azure.
- O parâmetro 'MinChildEndpoints' é opcional. O valor padrão é '1'. Se o número de pontos de extremidade disponíveis ficar abaixo esse limite, o perfil pai considera o perfil filho 'degradado' e desvia o tráfego para os outros pontos de extremidade no perfil pai.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Adicionando pontos de extremidade aninhados usando Adicionar AzureRmTrafficManagerEndpointConfig e Set-AzureRmTrafficManagerProfile

Neste exemplo, podemos criar novo Gerenciador de tráfego filho e pai perfis, adicione o filho como um ponto de extremidade aninhado ao pai e confirmar as alterações.

    $child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

Para abreviar neste exemplo, não adicionamos quaisquer outros pontos de extremidade para os perfis filho ou pai.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionando pontos de extremidade aninhados usando New-AzureRmTrafficManagerEndpoint

Neste exemplo, vamos adicionar um perfil de filho existente como um ponto de extremidade aninhado a um perfil existente do pai. O perfil é especificado usando os nomes de grupo de recursos e de perfil.

    $child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2

## <a name="update-a-traffic-manager-endpoint"></a>Atualizar um ponto de extremidade do Gerenciador de tráfego

Há duas maneiras de atualizar um ponto de extremidade do Gerenciador de tráfego existente:

1. Obtenha o perfil do Gerenciador de tráfego usando Get-AzureRmTrafficManagerProfile, atualizar as propriedades de ponto de extremidade dentro do perfil e confirmar as alterações usando Set-AzureRmTrafficManagerProfile. Esse método tem a vantagem de ser capaz de atualizar mais de uma empresa em uma única operação.
2. Obter o ponto de extremidade do Gerenciador de tráfego usando Get-AzureRmTrafficManagerEndpoint, atualizar as propriedades de ponto de extremidade e confirmar as alterações usando Set-AzureRmTrafficManagerEndpoint. Este método é mais simples, desde que ela não exige indexação na matriz de pontos de extremidade no perfil.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Atualizando usando Get-AzureRmTrafficManagerProfile e AzureRmTrafficManagerProfile do conjunto de pontos de extremidade

Neste exemplo, podemos modificar a prioridade em dois pontos de extremidade dentro de um perfil existente.

    $profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
    $profile.Endpoints[0].Priority = 2
    $profile.Endpoints[1].Priority = 1
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Exemplo 2: Atualizando um ponto de extremidade usando Get-AzureRmTrafficManagerEndpoint e Set-AzureRmTrafficManagerEndpoint

Neste exemplo, podemos modificar a espessura de uma única empresa em um perfil existente.

    $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
    $endpoint.Weight = 20
    Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Ativando e desativando os pontos de extremidade e perfis

Gerenciador de tráfego permite pontos de extremidade individuais ser habilitado e desabilitado, bem como permitindo ativando e desativando dos perfis inteiros.
Essas alterações podem ser feitas definindo obtendo/atualizando/os recursos de ponto de extremidade ou perfil. Simplifique essas operações comuns, eles também são suportados pelo cmdlets dedicado.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemplo 1: Ativando e desativando um perfil do Gerenciador de tráfego

Para habilitar um perfil do Gerenciador de tráfego, use Enable-AzureRmTrafficManagerProfile. O perfil pode ser especificado usando um objeto de perfil. O objeto de perfil pode ser passado por meio do pipeline de ou usando o '-TrafficManagerProfile' parâmetro. Neste exemplo, podemos especificar o perfil pelo nome do grupo de recursos e de perfil.

```powershell
    Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Para desativar um perfil do Gerenciador de tráfego:

```powershell
    Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

O cmdlet desativar AzureRmTrafficManagerProfile solicita confirmação. Este prompt pode ser eliminado usando a '-Force' parâmetro.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemplo 2: Ativando e desativando um ponto de extremidade do Gerenciador de tráfego

Para habilitar um ponto de extremidade do Gerenciador de tráfego, use Enable-AzureRmTrafficManagerEndpoint. Há duas maneiras para especificar o ponto de extremidade

1. Usando um objeto de TrafficManagerEndpoint passado pelo pipeline de ou a '-TrafficManagerEndpoint' parâmetro
2. Usando o nome do ponto de extremidade, tipo de ponto de extremidade, nome do perfil e nome do grupo de recursos:

```powershell
    Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Da mesma forma, para desativar um ponto de extremidade do Gerenciador de tráfego:

```powershell
     Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Como com desativar-AzureRmTrafficManagerProfile, o cmdlet desativar AzureRmTrafficManagerEndpoint solicita confirmação. Este prompt pode ser eliminado usando a '-Force' parâmetro.

## <a name="delete-a-traffic-manager-endpoint"></a>Excluir um ponto de extremidade do Gerenciador de tráfego

Para remover os pontos de extremidade individuais, use o cmdlet Remove-AzureRmTrafficManagerEndpoint:

```powershell
    Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Esse cmdlet solicita confirmação. Este prompt pode ser eliminado usando a '-Force' parâmetro.

## <a name="delete-a-traffic-manager-profile"></a>Excluir um perfil de Gerenciador de tráfego

Para excluir um perfil do Gerenciador de tráfego, use o cmdlet remover AzureRmTrafficManagerProfile, especificando os nomes de grupo de recursos e de perfil:

```powershell
    Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Esse cmdlet solicita confirmação. Este prompt pode ser eliminado usando a '-Force' parâmetro.

O perfil a ser excluído também pode ser especificado usando um objeto de perfil:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Esta sequência também pode ser usada:

```powershell
    Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Próximas etapas

[Gerenciador de tráfego monitoramento](traffic-manager-monitoring.md)

[Considerações de desempenho do Gerenciador de tráfego](traffic-manager-performance-considerations.md)
