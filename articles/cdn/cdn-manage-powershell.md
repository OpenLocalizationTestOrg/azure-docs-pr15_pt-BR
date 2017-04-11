<properties
    pageTitle="Gerenciar Azure CDN com PowerShell | Microsoft Azure"
    description="Saiba como usar os cmdlets do PowerShell do Azure para gerenciar CDN do Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="casoper"/>


# <a name="manage-azure-cdn-with-powershell"></a>Gerenciar Azure CDN com PowerShell

PowerShell fornece um dos métodos mais flexíveis para gerenciar os perfis de CDN do Azure e pontos de extremidade.  Você pode usar o PowerShell interativamente ou escrevendo scripts para automatizar tarefas de gerenciamento.  Este tutorial demonstra várias das tarefas mais comuns que podem ser realizadas com o PowerShell para gerenciar os perfis de CDN do Azure e pontos de extremidade.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o PowerShell para gerenciar os perfis de CDN do Azure e pontos de extremidade, você deve ter o módulo do PowerShell do Azure instalado.  Para saber como instalar o PowerShell do Azure e conectar ao Azure usando o `Login-AzureRmAccount` cmdlet, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

>[AZURE.IMPORTANT] Você deve fazer logon com `Login-AzureRmAccount` antes de executar cmdlets do PowerShell do Azure.

## <a name="listing-the-azure-cdn-cmdlets"></a>Listando os cmdlets CDN do Azure

Você pode listar todos os cmdlets de CDN Azure usando o `Get-Command` cmdlet.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Obtendo ajuda

Você pode obter ajuda com qualquer um desses cmdlets usando o `Get-Help` cmdlet.  `Get-Help`fornece uso e sintaxe e opcionalmente mostra exemplos.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Perfis de CDN Azure existentes de listagem

O `Get-AzureRmCdnProfile` cmdlet sem parâmetros recupera todos os seus perfis CDN existentes.

```powershell
Get-AzureRmCdnProfile
```

Esta saída pode ser usada para cmdlets para enumeração.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Você também pode retornar um único perfil, especificando o grupo de recursos e de nome do perfil.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

>[AZURE.TIP] É possível ter vários perfis CDN com o mesmo nome, desde que eles estão em grupos de recursos diferentes.  Omitindo o `ResourceGroupName` parâmetro retorna todos os perfis com um nome correspondente.

## <a name="listing-existing-cdn-endpoints"></a>Listagem existentes pontos de extremidade CDN

`Get-AzureRmCdnEndpoint`pode recuperar um ponto de extremidade individual ou todos os pontos de extremidade em um perfil.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Criação de pontos de extremidade e perfis CDN

`New-AzureRmCdnProfile`e `New-AzureRmCdnEndpoint` são usados para criar perfis CDN e pontos de extremidade.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Verificando a disponibilidade de nome do ponto de extremidade

`Get-AzureRmCdnEndpointNameAvailability`Retorna um objeto que indica se o nome de um ponto de extremidade está disponível.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Adicionar um domínio personalizado

`New-AzureRmCdnCustomDomain`Adiciona um nome de domínio personalizado para um ponto de extremidade existente.

>[AZURE.IMPORTANT] Você deve configurar o CNAME com seu provedor DNS, conforme descrito em [como mapear domínio personalizado ao ponto de extremidade de rede de entrega de conteúdo (CDN)](./cdn-map-content-to-custom-domain.md).  Você pode testar o mapeamento antes de modificar seu ponto de extremidade usando `Test-AzureRmCdnCustomDomain`.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modificando um ponto extremo

`Set-AzureRmCdnEndpoint`modifica uma empresa existente.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Descarte/pré-loading ativos CDN

`Unpublish-AzureRmCdnEndpointContent`exclusão de cache ativos, enquanto `Publish-AzureRmCdnEndpointContent` carrega previamente ativos em pontos de extremidade com suporte.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Pontos de extremidade de CDN iniciar/interromper
`Start-AzureRmCdnEndpoint`e `Stop-AzureRmCdnEndpoint` podem ser usados para iniciar e parar de pontos de extremidade individuais ou grupos de pontos de extremidade.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Excluir recursos CDN

`Remove-AzureRmCdnProfile`e `Remove-AzureRmCdnEndpoint` pode ser usado para remover perfis e pontos de extremidade.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Próximas etapas

Saiba como automatizar Azure CDN com [.NET](./cdn-app-dev-net.md) ou [Node](./cdn-app-dev-node.md).

Para saber mais sobre os recursos CDN, consulte [Visão geral de CDN](./cdn-overview.md).


