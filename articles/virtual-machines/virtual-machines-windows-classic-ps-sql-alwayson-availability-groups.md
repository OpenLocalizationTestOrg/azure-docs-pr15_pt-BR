<properties
    pageTitle="Configurar sempre no grupo de disponibilidade na máquina virtual do Azure com o PowerShell"
    description="Este tutorial usa recursos criados com o modelo clássico de implantação e usa o PowerShell para criar um sempre na disponibilidade de grupo no Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm-with-powershell"></a>Configurar sempre no grupo de disponibilidade na máquina virtual do Azure com o PowerShell

> [AZURE.SELECTOR]
- [Gerenciador de recursos: modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Gerenciador de recursos: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Clássico: interface do usuário](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Azure VMs (máquinas virtuais) pode ajudar os administradores de banco de dados menor implementar o custo de um sistema SQL Server de alta disponibilidade. Este tutorial mostra como implementar um grupo de disponibilidade usando o SQL Server sempre na ponta a ponta dentro de um ambiente do Azure. No fim do tutorial, sua solução de SQL Server sempre ativada no Azure consiste nos seguintes elementos:

- Uma rede virtual que contém várias sub-redes, incluindo um front-end e uma sub-rede de back-end

- Um controlador de domínio com um domínio do Active Directory (AD)

- Duas VMs implantado à sub-rede back-end e ingressar no domínio do AD do servidor SQL

- Um cluster WSFC 3 nós com o modelo de quorum maioria de nós

- Um grupo de disponibilidade com duas réplicas síncrono confirmação de um banco de dados de disponibilidade

Este cenário é escolhido para sua simplicidade, não para sua eficácia de custo ou outros fatores no Azure. Por exemplo, você pode minimizar o número de VMs para um grupo de disponibilidade de dois réplica para salvar em horas de computação no Azure usando o controlador de domínio como a testemunha de compartilhamento de arquivo de quorum em um cluster WSFC de 2 nós. Esse método reduz a contagem de máquina virtual por uma na configuração da acima.

Este tutorial destina-se para mostrar as etapas necessárias para configurar a solução descrita acima sem elaborando nos detalhes de cada etapa. Portanto, em vez de mostrando as etapas de configuração de interface gráfica, ele usa PowerShell script para levam você rapidamente através de cada etapa. Ele pressupõe o seguinte:

- Você já tem uma conta do Microsoft Azure com a assinatura de máquina virtual.

- Você instalou os [cmdlets do PowerShell do Azure](../powershell-install-configure.md).

- Você já tem uma compreensão sólida de sempre em grupos de disponibilidade para soluções no local. Para obter mais informações, consulte [Sempre em grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Conectar-se a sua assinatura do Azure e criar a rede Virtual

1. Em uma janela do PowerShell no computador local, importar o módulo Azure, baixar um arquivo de configurações de publicação para sua máquina e conectar sua sessão do PowerShell à sua assinatura do Azure importando as configurações de publicação baixadas.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    O comando **Get-AzurePublishgSettingsFile** gera automaticamente um gerenciamento certificado com o Azure downloads-lo à sua máquina. Um navegador será aberto automaticamente e você será solicitado a inserir as credenciais de conta da Microsoft para sua assinatura do Azure. O arquivo baixado **publishsettings** contém todas as informações necessárias para gerenciar sua assinatura do Azure. Depois de salvar este arquivo em um diretório local, importá-lo usando o comando **Importar AzurePublishSettingsFile** .

    >[AZURE.NOTE] O arquivo publishsettings contém suas credenciais (sem codificação) que são usados para administrar seus serviços e assinaturas do Azure. A prática recomendada de segurança para esse arquivo é armazená-lo temporariamente fora de seus diretórios de origem (por exemplo, na pasta Libraries\Documents) e, em seguida, excluí-la após a importação ter sido concluído. Um usuário mal-intencionado obter acesso ao arquivo publishsettings pode editar, criar e excluir seus serviços Azure.

1. Defina uma série de variáveis que você usará para criar sua infraestrutura de nuvem.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Observe o seguinte para garantir que seus comandos funcionará mais tarde:

    - Variáveis **$storageAccountName** e **$dcServiceName** devem ser exclusivos porque eles são usados para identificar seu armazenamento conta e nuvem servidor na nuvem, respectivamente, na internet.

    - Nomes especificados para variáveis **$affinityGroupName** e **$virtualNetworkName** são configurados no documento de configuração de rede virtual que você usará posteriormente.

    - **$sqlImageName** Especifica o nome da imagem máquina virtual que contenha o SQL Server 2012 Service Pack 1 Enterprise Edition atualizado.

    - Para simplificar, **Contoso! 000** é a mesma senha usada em todo o tutorial inteiro.

1. Crie um grupo de afinidade.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. Crie uma rede virtual importando um arquivo de configuração.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    O arquivo de configuração contém o documento XML a seguir. Em resumo, ela especifica uma rede virtual chamada **ContosoNET** no grupo de afinidade chamado **ContosoAG**e tem o espaço de endereço **10.10.0.0/16** e tem duas sub-redes, **10.10.1.0/24** e **10.10.2.0/24**, que são a sub-rede frontal e voltar sub-rede, respectivamente. A sub-rede frontal é onde você pode colocar aplicativos cliente como o Microsoft SharePoint e a sub-rede traseira é onde você colocará as VMs do SQL Server. Se você alterar as variáveis **$affinityGroupName** e **$virtualNetworkName** anteriormente, você também deve alterar os nomes correspondentes abaixo.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

1. Crie uma conta de armazenamento que está associada ao grupo de afinidade você criou e defini-lo como a conta de armazenamento atual em sua assinatura.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. Crie o servidor DC no novo conjunto de disponibilidade e serviço de nuvem.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Esta série de comandos obsoleta fazer o seguinte:

    - **Novo AzureVMConfig** cria uma configuração de máquina virtual.

    - **Adicionar AzureProvisioningConfig** fornece os parâmetros de configuração de um servidor de Windows autônomo.

    - **Adicionar AzureDataDisk** adiciona o disco de dados que você usará para armazenar dados do Active Directory, com a opção definir nenhum de cache.

    - **Novo AzureVM** cria um novo serviço de nuvem e a nova máquina de virtual do Azure no serviço de nuvem do novo.

1. Aguarde a máquina virtual novo ser totalmente provisionado e baixar o arquivo de desktop remoto para seu diretório de trabalho. Desde que a nova máquina de virtual do Azure demora muito tempo para provisionar, o tempo de loop continua a monitorar a máquina virtual novo até que ele esteja pronto para uso.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

O servidor de DC agora está provisionado com êxito. Em seguida, você irá configurar o domínio do Active Directory neste servidor DC. Deixe a janela do PowerShell aberta no computador local. Você usará-lo novamente mais tarde para criar as duas VMs de servidor SQL.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio

1. Conecte ao servidor DC abrindo o arquivo de desktop remoto. Use o nome de usuário AzureAdmin e a senha do administrador máquina **Contoso! 000**, que você especificou ao criar a máquina virtual novo.

1. Abra uma janela do PowerShell no modo de administrador.

1. Execute o seguinte **DCPROMO. EXE** comando para configurar o domínio **corp.contoso.com** , com os diretórios de dados na unidade de disco M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Quando o comando for concluída, a máquina virtual será reiniciado automaticamente.

1. Conectar-se ao servidor DC novamente abrindo o arquivo de desktop remoto. Desta vez, faça logon como **CORP\Administrator**.

1. Abra uma janela do PowerShell no modo de administrador e importar o módulo do Active Directory do PowerShell usando o seguinte comando:

        Import-Module ActiveDirectory

1. Execute os seguintes comandos para adicionar três usuários para o domínio.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** é usado para configurar tudo relacionado as instâncias de serviço do SQL Server, o cluster WSFC e o grupo de disponibilidade. **CORP\SQLSvc1** e **CORP\SQLSvc2** são usados como as contas de serviço do SQL Server para as duas VMs de servidor SQL.

1. Em seguida, execute os seguintes comandos para conceder **CORP\Install** as permissões para criar objetos de computador no domínio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    O GUID especificado acima é o GUID para o tipo de objeto do computador. A conta de **CORP\Install** precisa ter a permissão **Ler todas as propriedades** e **Criar objetos de computador** para criar os objetos Active direto para o cluster WSFC. A permissão de **Ler todas as propriedades** já é fornecida a CORP\Install por padrão, então você não precisa conceder explicitamente. Para obter mais informações sobre as permissões necessárias para criar o cluster WSFC, consulte [Guia do Failover Cluster passo a passo: Configurando contas no Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Agora que você concluiu a configuração do Active Directory e os objetos de usuário, você criará duas VMs do SQL Server e associá-los para este domínio.

## <a name="create-the-sql-server-vms"></a>Criar as VMs do SQL Server

1. Continue a usar a janela do PowerShell que está aberta no computador local. Defina as seguintes variáveis adicionais:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    O endereço IP **10.10.0.4** é geralmente atribuída para a primeira máquina virtual criar na sub-rede **10.10.0.0/16** da sua rede virtual Azure. Você deve verificar que esse é o endereço do seu servidor de DC executando **IPCONFIG**.

1. Execute os seguintes comandos obsoleta para criar a máquina virtual primeira no cluster WSFC, chamado **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Observe as seguintes informações sobre o comando acima:

    - **Novo AzureVMConfig** cria uma configuração de máquina virtual com o nome do conjunto de disponibilidade desejado. As VMs subsequentes serão criadas com o mesmo nome de conjunto de disponibilidade para que elas estão unidas para o mesmo conjunto de disponibilidade.

    - **Adicionar AzureProvisioningConfig** une a máquina virtual para o domínio do Active Directory que você criou.

    - **Definir AzureSubnet** coloca a máquina virtual na sub-rede voltar.

    - **Novo AzureVM** cria um novo serviço de nuvem e a nova máquina de virtual do Azure no serviço de nuvem do novo. O parâmetro **DnsSettings** Especifica que o servidor DNS para os servidores no serviço de nuvem novo tem o de endereço IP **10.10.0.4**, que é o endereço IP do servidor DC. Este parâmetro é necessário para habilitar as novas VMs no serviço de nuvem ingressar no domínio do Active Directory com êxito. Sem esse parâmetro, você deve definir manualmente as configurações de IPv4 em sua máquina virtual para usar o servidor de DC como o servidor DNS primário após a máquina virtual está provisionada e, em seguida, ingresse a máquina virtual no domínio do Active Directory.

1. Execute os seguintes comandos obsoleta criar VMs SQL Server, chamado **ContosoSQL1** e **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Observe as seguintes informações sobre os comandos acima:

    - **Novo AzureVMConfig** usa o mesmo nome de conjunto de disponibilidade do servidor de DC e usa a imagem do SQL Server 2012 Service Pack 1 Enterprise Edition na Galeria de máquina virtual. Ele também define o disco de sistema operacional para leitura em cache somente (sem cache de gravação). É recomendável que você migrar os arquivos de banco de dados para um disco de dados separado que você anexar a máquina virtual e configurá-lo com nenhuma leitura ou gravação em cache. No entanto, a segunda melhor coisa é remover o cache de gravação do disco do sistema operacional, desde que você não pode remover cache do disco do sistema operacional de leitura.

    - **Adicionar AzureProvisioningConfig** une a máquina virtual para o domínio do Active Directory que você criou.

    - **Definir AzureSubnet** coloca a máquina virtual na sub-rede voltar.

    - **Adicionar AzureEndpoint** adiciona pontos de extremidade de acesso para que aplicativos clientes possam acessar essas instâncias de serviços do SQL Server na internet. Portas diferentes recebem ContosoSQL1 e ContosoSQL2.

    - **Novo AzureVM** cria a nova VM servidor SQL no serviço de nuvem como ContosoQuorum. Você deve colocar as VMs no serviço de nuvem mesmo se quiser que eles sejam no mesmo conjunto de disponibilidade.

1. Aguarde até que cada máquina virtual para ser totalmente provisionado e baixe o seu arquivo de desktop remoto para seu diretório de trabalho. O loop percorre as três VMs novo e executa os comandos dentro dos colchetes curvas de nível superior para cada um deles.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    SQL Server VMs agora são provisionadas e em execução, mas eles são instalados com o SQL Server com opções padrão.

## <a name="initialize-the-wsfc-cluster-vms"></a>Inicializar WSFC Cluster VMs

Nesta seção, você precisa modificar os servidores de três que você usará no cluster WSFC e de instalação do SQL Server. Especificamente:

- (Todos os servidores) Você precisa instalar o recurso **Cluster de Failover** .

- (Todos os servidores) Você precisa adicionar **CORP\Install** como o **administrador**da máquina.

- (ContosoSQL1 e ContosoSQL2 somente) Você precisa adicionar **CORP\Install** como uma função de **administrador do sistema** do banco de dados padrão.

- (ContosoSQL1 e ContosoSQL2 somente) Você precisa adicionar **Sistema/Autoridade NT** como um logon com as seguintes permissões:

    - Alterar qualquer grupo de disponibilidade

    - Conectar SQL

    - Exibir estado do servidor

- (ContosoSQL1 e ContosoSQL2 somente) O protocolo **TCP** já está habilitado na VM SQL Server. No entanto, você ainda precisar abrir o firewall para acesso remoto do SQL Server.

Agora, você está pronto para começar. Começando com **ContosoQuorum**, siga as etapas abaixo:

1. Conecte a **ContosoQuorum** abrindo os arquivos da área de trabalho remotos. Use o nome de usuário **AzureAdmin** e a senha do administrador máquina **Contoso! 000**, que você especificou ao criar VMs.

1. Verifique se que os computadores tem sido adicionados com êxito ao **corp.contoso.com**.

1. Aguarde a instalação do SQL Server concluir a executando as tarefas de inicialização automatizada antes de continuar.

1. Abra uma janela do PowerShell no modo de administrador.

1. Instale o recurso cluster de Failover do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Adicione **CORP\Install** como administrador local.

        net localgroup administrators "CORP\Install" /Add

1. Faça logout do ContosoQuorum. Terminar com este servidor agora.

        logoff.exe

Em seguida, inicialize **ContosoSQL1** e **ContosoSQL2**. Siga as etapas a seguir, que são idênticas para ambas as VMs do SQL Server.

1. Conecte-se ao SQL Server VMs dois abrindo os arquivos da área de trabalho remotos. Use o nome de usuário **AzureAdmin** e a senha do administrador máquina **Contoso! 000**, que você especificou ao criar VMs.

1. Verifique se que os computadores tem sido adicionados com êxito ao **corp.contoso.com**.

1. Aguarde a instalação do SQL Server concluir a executando as tarefas de inicialização automatizada antes de continuar.

1. Abra uma janela do PowerShell no modo de administrador.

1. Instale o recurso cluster de Failover do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Adicionar **CORP\Install** como administrador local

        net localgroup administrators "CORP\Install" /Add

1. Importe o provedor do PowerShell do SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. Adicione **CORP\Install** como a função de administrador do sistema para a instância do SQL Server padrão.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Adicionar **Sistema/Autoridade NT** como um logon com as três permissões descrito acima.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Abra o firewall para acesso remoto do SQL Server.

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Faça logout do ambas as VMs.

        logoff.exe

Por fim, você está pronto para configurar o grupo de disponibilidade. Você usará o provedor do PowerShell do SQL Server para realizar todo o trabalho em **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurar o grupo de disponibilidade

1. Conecte a **ContosoSQL1** novamente abrindo os arquivos da área de trabalho remotos. Em vez de fazer logon usando a conta de máquina, faça logon usando **CORP\Install**.

1. Abra uma janela do PowerShell no modo de administrador.

1. Defina as seguintes variáveis:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"

1. Importar o provedor do PowerShell do SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. Altere a conta de serviço do SQL Server para ContosoSQL1 para CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Altere a conta de serviço do SQL Server para ContosoSQL2 para CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Baixe **CreateAzureFailoverCluster.ps1** de [Criar WSFC Cluster para sempre na disponibilidade de grupos na máquina virtual do Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) para o diretório de trabalho local. Você usará esse script para ajudá-lo a criar um cluster WSFC funcional. Para obter informações importantes sobre como WSFC interage com a rede Azure, consulte [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

1. Alterar para seu diretório de trabalho e crie cluster WSFC com o script baixado.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Habilite sempre em grupos de disponibilidade para as instâncias padrão do SQL Server em **ContosoSQL1** e **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Crie uma pasta de backup e conceder permissões para as contas de serviço do SQL Server. Você usará esse diretório para preparar o banco de dados de disponibilidade na réplica secundário.

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Criar um banco de dados em **ContosoSQL1** chamado **MyDB1**, faça um backup total e um backup de log e restaurá-los em **ContosoSQL2** com a opção **WITH NORECOVERY** .

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Criar a disponibilidade de pontos de extremidade de grupo nas VMs SQL Server e definir as permissões apropriadas os pontos de extremidade.

        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server1\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"
        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server2\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"

        Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
        Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. Crie as réplicas de disponibilidade.

        $primaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server1 `
            -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate
        $secondaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server2 `
            -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate

1. Finalmente, criar o grupo de disponibilidade e ingressar a réplica secundária para o grupo de disponibilidade.

        New-SqlAvailabilityGroup `
            -Name $ag `
            -Path "SQLSERVER:\SQL\$server1\Default" `
            -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
            -Database $db
        Join-SqlAvailabilityGroup `
            -Path "SQLSERVER:\SQL\$server2\Default" `
            -Name $ag
        Add-SqlAvailabilityDatabase `
            -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
            -Database $db

## <a name="next-steps"></a>Próximas etapas
Você tem agora com êxito implementado SQL Server sempre em pela criação de um grupo de disponibilidade no Azure. Para configurar um ouvinte para esse grupo de disponibilidade, consulte [Configurar um ouvinte ILB para sempre em grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Para obter outras informações sobre como usar o SQL Server no Azure, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
