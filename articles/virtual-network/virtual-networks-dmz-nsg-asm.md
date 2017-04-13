<properties
   pageTitle="Exemplo de DMZ – construir uma DMZ Simple com NSGs | Microsoft Azure"
   description="Construir um DMZ com grupos de segurança de rede (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-1--build-a-simple-dmz-with-nsgs"></a>Exemplo 1 – compilação DMZ Simple com NSGs

[Retornar para a página práticas recomendada do limite de segurança][HOME]

Este exemplo criará um DMZ simple com quatro grupos de segurança de rede e servidores windows. Ele também guiará cada um dos comandos relevantes para fornecer uma compreensão mais profunda de cada etapa. Também há uma seção de cenário de tráfego para fornecer um profundamente detalhadas de como o tráfego passa para as camadas de proteção no DMZ. Finalmente, nas referências de seção é o código completo e instruções para criar esse ambiente para testar e experimentar vários cenários. 

![Entrada DMZ com NSG][1]

## <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, há uma assinatura que contém o seguinte:

- Dois serviços em nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede Virtual, "CorpNetwork", com duas sub-redes; "FrontEnd" e "Back-end"
- Um grupo de segurança de rede que é aplicado a ambas as sub-redes
- Windows Server que representa um aplicativo de servidor de web ("IIS01")
- Servidores de duas janelas que representam o aplicativo novamente encerrar servidores ("AppVM01", "AppVM02")
- Windows server que representa um servidor DNS ("DNS01")

Na seção de referências abaixo, há um script do PowerShell que criará maioria do ambiente descrito acima. Criando as VMs e redes virtuais, embora são feitas pelo script de exemplo, não são descritos detalhadamente neste documento. 

Para criar o ambiente;

  1.    Salvar o arquivo xml de configuração de rede incluído na seção referências (atualizado com nomes, local e IP endereços para corresponder o cenário fornecido)
  2.    Atualizar as variáveis de usuário no script para corresponder ao ambiente que o script for executado contra (assinaturas, nomes de serviço, etc.)
  3.    Execute o script do PowerShell

**Observação**: A região representada no script PowerShell deve corresponder à região representada no arquivo xml de configuração da rede.

Depois que o script é executado com êxito adicional etapas opcionais podem ser tomadas, na seção referências são dois scripts para configurar o servidor web e servidor de aplicativo com um aplicativo web simples para permitir que o teste com essa configuração DMZ.

As seções a seguir fornecerá uma descrição detalhada dos grupos de segurança de rede e como eles funcionam para este exemplo, acompanhando linhas de chave do script PowerShell.

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e então carregado com seis regras. 

>[AZURE.TIP] Em geral, você deve criar suas regras de "Permitir" específico primeiro e, em seguida, as regras de "Negar" mais genéricas sobrenome. Define a prioridade atribuída que as regras são avaliadas primeiro. Quando o tráfego está localizado para aplicar a uma regra específica, não há mais regras são avaliadas. Regras NSG podem aplicar uma a direção de entrada ou de saída (a partir da perspectiva da sub-rede).

Forma declarativa, as regras a seguir estão sendo criadas para tráfego de entrada:

1.  Tráfego DNS interno (porta 53) é permitido
2.  Tráfego RDP (porta 3389) da Internet para qualquer máquina virtual é permitido
3.  Tráfego HTTP (porta 80) da Internet para o servidor de web (IIS01) é permitido
4.  Qualquer tráfego (todas as portas) de IIS01 para AppVM1 é permitido
5.  Qualquer tráfego (todas as portas) da Internet para todo o VNet (ambas as sub-redes) é negado
6.  Qualquer tráfego (todas as portas) da sub-rede Frontend à sub-rede back-end é negado

Com estas regras vinculado a cada sub-rede, se uma solicitação HTTP foi entrada da Internet para o servidor web, ambas as regras 3 (Permitir) e 5 (Negar) seria aplicado, mas como regra 3 tem uma prioridade mais alta apenas ele aplica e regra 5 prefere não entram. Portanto, a solicitação HTTP seria ter permissão para o servidor da web. Se esse mesmo tráfego estava tentando alcançar o servidor de DNS01, regra 5 (Negar) seria o primeiro a aplicar e o tráfego não deve ter permissão para passar para o servidor. Regra de 6 (Negar) bloqueia sub-rede Frontend de conversar com a sub-rede de back-end (exceto para tráfego permitido em regras 1 e 4), isso protege a rede de back-end caso um invasor comprometer o aplicativo Web da Frontend, o invasor poderia ter acesso limitado à rede back-end "protegida" (somente para recursos expostos no servidor AppVM01).

Não há uma regra de saída do padrão que permite tráfego check-out para a internet. Neste exemplo, estamos permitir o tráfego de saída e não modificando as regras de saída. Para bloquear o tráfego nas duas direções, roteamento de definidos pelo usuário é necessária, isso é explorado em "Exemplo 3" abaixo.

Cada regra é discutida mais detalhadamente da seguinte maneira (**Observação**: qualquer item no abaixo lista no início com um cifrão (por exemplo: $NSGName) é uma variável definidas pelo usuário do script na seção de referência deste documento):

1. Primeiro, um grupo de segurança de rede devem ser criado para manter as regras:

        New-AzureNetworkSecurityGroup -Name $NSGName `
            -Location $DeploymentLocation `
            -Label "Security group for $VNetName subnets in $DeploymentLocation"
 
2.  A primeira regra neste exemplo permitirá tráfego DNS entre todas as redes internas para o servidor DNS na sub-rede back-end. A regra tem alguns parâmetros importantes:
  - "Digite" significa em que direção de tráfego essa regra entrarão em vigor; Isso é da perspectiva da sub-rede ou da máquina Virtual (dependendo de onde este NSG está ligado). Portanto, se tipo for "Entrada" e tráfego é inserindo a sub-rede, aplica a regra e tráfego deixando a sub-rede não é afetado por esta regra.
  - "Prioridade" define a ordem na qual o fluxo de tráfego será avaliado. Quanto menor o número maior a prioridade. Assim que uma regra se aplica a um fluxo de tráfego específico, não há mais regras são processadas. Portanto, se uma regra com prioridade 1 permite o tráfego e uma regra com prioridade 2 nega o tráfego e ambas as regras aplicadas a tráfego, o tráfego permitia a fluxo (desde que a regra 1 tinha uma prioridade mais alta levou efeito e não há mais regras foram aplicadas).
  - "Ação" significa se tráfego afetado por essa regra é bloqueado ou permitido.

            Get-AzureNetworkSecurityGroup -Name $NSGName | `
                Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
                -Type Inbound -Priority 100 -Action Allow `
                -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
                -DestinationAddressPrefix $VMIP[4] `
                -DestinationPortRange '53' `
                -Protocol *

3.  Essa regra permitirá RDP tráfego da internet para porta RDP em qualquer servidor em qualquer sub-rede a VNET. Esta regra usa dois tipos especiais de prefixos de endereço; "VIRTUAL_NETWORK" e "INTERNET". Esta é uma maneira fácil de uma categoria maior de prefixos de endereço de endereço.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
            -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '3389' `
            -Protocol *

4.  Essa regra permite que o tráfego da internet acertar o servidor da web. Isso não altera o comportamento de roteamento; Ele permite apenas o tráfego destinado IIS01 passar. Portanto, se o tráfego da Internet tinha o servidor da web como seu destino esta regra seria permitir que ele e parar de processar mais regras. (Na regra com prioridade 140 todos os outro tráfego da internet está bloqueado). Se você estiver processando somente tráfego HTTP, essa regra poderia ser mais restrito para permitir apenas 80 de porta de destino.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
            -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] `
            -DestinationPortRange '*' `
            -Protocol *

5.  Essa regra permite o tráfego para passar do servidor IIS01 para o servidor de AppVM01, uma posterior blocos de regra a todas as outra Frontend tráfego de back-end. Para melhorar a essa regra, se a porta for conhecida que deve ser adicionado. Por exemplo, se o servidor IIS é atingir somente SQL Server em AppVM01, o intervalo de porta de destino devem ser alterado de "*" (qualquer) para 1433 (a porta SQL) permitindo uma menor superfície de ataque de entrada na AppVM01 deve o aplicativo web comprometido.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
            -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
 
6.  Esta regra nega o tráfego da internet para todos os servidores na rede. Em combinação com a regra em prioridade 110 e 120, permite somente tráfego da internet para o firewall e portas RDP para outros servidores e blocos tudo. 

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $VNetName VNet from the Internet" `
            -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '*' `
            -Protocol *
 
7.  A regra final nega tráfego da sub-rede Frontend à sub-rede back-end. Como esta é uma regra de entrada única, tráfego inverso é permitido (de back-end para o Frontend).

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
            -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix `
            -DestinationPortRange '*' `
            -Protocol * 

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-web-to-web-server"></a>(*Permitido*) Servidor Web à Web
1.  Página HTTP de solicitações de usuário de Internet do FrontEnd001.CloudApp.Net (Internet opostas serviço em nuvem)
2.  Nuvem tráfego de passagens de serviço por meio de ponto de extremidade aberto na porta 80 em direção IIS01 (o servidor web)
3.  Frontend sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não se aplicam, mover para a próxima regra
  2.    NSG regra 2 (RDP) não se aplicam, mover para a próxima regra
  3.    Aplicar NSG regra 3 (Internet para IIS01), o tráfego é o processamento de regra permitidos, interrompa
4.  Tráfego visitas endereço IP interno do servidor web IIS01 (10.0.1.5)
5.  IIS01 está aguardando tráfego da web, recebe essa solicitação e inicia o processamento da solicitação
6.  IIS01 solicita o SQL Server em AppVM01 informações
7.  Não há regras de saída em sub-rede Frontend, o tráfego é permitido
8.  A sub-rede de back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não se aplicam, mover para a próxima regra
  2.    NSG regra 2 (RDP) não se aplicam, mover para a próxima regra
  3.    NSG regra 3 (Internet ao Firewall) não se aplicam, mover para a próxima regra
  4.    NSG regra 4 (IIS01 para AppVM01) aplicar, o tráfego é permitido, parar processamento de regra
9.  AppVM01 recebe a consulta SQL e responde
10. Como não há nenhuma regra de saída da sub-rede de back-end a resposta é permitida
11. Frontend sub-rede começa processamento de regra de entrada:
  1.    Não há nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede Frontend, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema padrão permitir o tráfego entre sub-redes permitirá esse tráfego para que o tráfego é permitido.
12. O servidor IIS recebe a resposta SQL e conclui a resposta HTTP e envia ao solicitante
13. Uma vez que existem sem regras de saída na sub-rede Frontend a resposta é permitido e o usuário de Internet recebe a página da web solicitada.

#### <a name="allowed-rdp-to-backend"></a>(*Permitido*) RDP para back-end
1.  Administrador do servidor em internet solicita sessão RDP AppVM01 em BackEnd001.CloudApp.Net:xxxxx onde xxxxx é o número da porta atribuído aleatoriamente para RDP para AppVM01 (a porta atribuída pode ser encontrada no Portal do Azure ou por meio do PowerShell)
2.  Sub-rede de back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não se aplicam, mover para a próxima regra
  2.    Aplicar NSG regra 2 (RDP), o tráfego é o processamento de regra permitidos, interrompa
3.  Com sem regras de saída, aplicam regras padrão e tráfego de retorno é permitido
4.  Sessão RDP está habilitado
5.  AppVM01 solicita a senha do nome de usuário

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(*Permitido*) Pesquisa de DNS do servidor de Web no servidor DNS
1.  Servidor, IIS01, necessidades de feed de um dados em www.data.gov, mas precisa resolver o endereço da Web.
2.  A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede back-end) como o servidor DNS primário, IIS01 envia a solicitação DNS para DNS01
3.  Não há regras de saída em sub-rede Frontend, o tráfego é permitido
4.  Sub-rede de back-end começa processamento de regra de entrada:
  1.     Aplicar NSG regra 1 (DNS), o tráfego é o processamento de regra permitidos, interrompa
5.  Servidor DNS recebe a solicitação
6.  Servidor DNS não tem o endereço de cache e solicita um servidor DNS raiz na internet
7.  Não há regras de saída em sub-rede de back-end, o tráfego é permitido
8.  Servidor DNS de Internet responde, desde que esta sessão foi iniciada internamente, a resposta é permitida
9.  Servidor DNS armazenará a resposta e responde à solicitação inicial para IIS01
10. Não há regras de saída em sub-rede de back-end, o tráfego é permitido
11. Frontend sub-rede começa processamento de regra de entrada:
  1.    Não há nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede Frontend, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema padrão permitir o tráfego entre sub-redes seria permitir este tráfego para que o tráfego é permitido
12. IIS01 recebe a resposta do DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitido*) Arquivo de acesso de servidor da Web em AppVM01
1.  IIS01 solicita um arquivo em AppVM01
2.  Não há regras de saída em sub-rede Frontend, o tráfego é permitido
3.  A sub-rede de back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não se aplicam, mover para a próxima regra
  2.    NSG regra 2 (RDP) não se aplicam, mover para a próxima regra
  3.    NSG regra 3 (Internet para IIS01) não se aplicam, mover para a próxima regra
  4.    NSG regra 4 (IIS01 para AppVM01) aplicar, o tráfego é permitido, parar processamento de regra
4.  AppVM01 recebe a solicitação e responde com arquivo (presumindo que o acesso é autorizado)
5.  Como não há nenhuma regra de saída da sub-rede de back-end a resposta é permitida
6.  Frontend sub-rede começa processamento de regra de entrada:
  1.    Não há nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede Frontend, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema padrão permitir o tráfego entre sub-redes permitirá esse tráfego para que o tráfego é permitido.
7.  O servidor IIS recebe o arquivo

#### <a name="denied-web-to-backend-server"></a>(*Negado*) Web para o servidor back-end
1.  Usuário de Internet tentar acessar um arquivo em AppVM01 através do serviço de BackEnd001.CloudApp.Net
2.  Como não há nenhum ponto de extremidade aberto para compartilhamento de arquivos, isso não seria passar o serviço de nuvem e não alcançar o servidor
3.  Se os pontos de extremidade foram abertos por algum motivo, NSG regra 5 (Internet para VNet) a bloquear esse tráfego

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(*Negado*) Consulta DNS da Web no servidor DNS
1.  Usuário de Internet tentar pesquisar um registro DNS interno na DNS01 por meio do serviço de BackEnd001.CloudApp.Net
2.  Como não há nenhum ponto de extremidade aberto para DNS, isso não seria passar o serviço de nuvem e não alcançar o servidor
3.  Se os pontos de extremidade foram abertos por algum motivo, NSG regra 5 (Internet para VNet) a bloquear esse tráfego (Observação: essa regra 1 (DNS) não se aplicam por duas razões, primeiro o endereço de origem é a internet, esta regra aplica-se somente para o local VNet como fonte, também essa é uma regra de permissão, portanto, ele nunca seria negar tráfego)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Negado*) Web para acessar o SQL pelo Firewall
1.  Usuário de Internet solicita dados SQL do FrontEnd001.CloudApp.Net (Internet opostas serviço em nuvem)
2.  Como não há nenhum ponto de extremidade aberto para SQL, isso não seria passar o serviço de nuvem e não atinja o firewall
3.  Se pontos de extremidade foram abertos por algum motivo, a sub-rede Frontend começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não se aplicam, mover para a próxima regra
  2.    NSG regra 2 (RDP) não se aplicam, mover para a próxima regra
  3.    Aplicar NSG regra 3 (Internet para IIS01), o tráfego é o processamento de regra permitidos, interrompa
4.  Tráfego visitas endereço IP interno do IIS01 (10.0.1.5)
5.  IIS01 não ouvir na porta 1433, portanto, não há resposta à solicitação

## <a name="conclusion"></a>Conclusão
Esta é uma maneira relativamente simple e direta de isolar a sub-rede de back-end de tráfego de entrada.

Mais exemplos e uma visão geral dos limites de segurança de rede podem ser encontradas [aqui][HOME].

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e configuração de rede
Salve o Script completo em um arquivo de script do PowerShell. Salve a configuração de rede em um arquivo denominado "NetworkConf1.xml".
Modifique as variáveis definidas pelo usuário conforme necessário. Execute o script e, em seguida, siga as instruções de configuração de regra de Firewall contida na seção exemplo 1 acima.

#### <a name="full-script"></a>Script completo
Esse script será, com base nas variáveis definidas pelo usuário;

1.  Conectar a uma assinatura do Azure
2.  Criar uma nova conta de armazenamento
3.  Criar um novo VNet e duas sub-redes conforme definido no arquivo de configuração de rede
4.  Construir 4 windows server VMs
5.  Configure NSG incluindo:
  - Criando um NSG
  - Preenchendo-o com regras
  - Vinculando a NSG para as sub-redes apropriadas

Este script do PowerShell deve ser executado localmente na que Internet conectado PC ou servidor.

>[AZURE.IMPORTANT] Quando esse script é executado, pode haver avisos ou outras mensagens informativas que pop no PowerShell. Apenas as mensagens de erro em vermelho são causa do problema.


    <# 
     .SYNOPSIS
      Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared
      
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.
    
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.5
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
      
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 0
        #       - The AppVM1 Server must be VM 1
        #       - The DNS server must be VM 3
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 1 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 2 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 3 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #   

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                Remove-AzureEndpoint -Name "PowerShell" | `
                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
            $i++
        }
        # Add HTTP Endpoint for IIS01
        Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
            -Protocol *
        
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *
    
        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
      

#### <a name="network-config-file"></a>Arquivo de configuração de rede
Salvar o arquivo xml com local atualizado e adicionar o link para esse arquivo para a variável de $NetworkConfigFile no script acima.
    
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Exemplos de Scripts de aplicativo
Se você desejar instalar um aplicativo de amostra para esse e outros exemplos de DMZ, um foi fornecido no seguinte link: [Script do aplicativo de amostra][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Entrada DMZ com NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

