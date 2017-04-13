<properties 
    pageTitle="Ambiente de teste de aplicativo de LOB | Microsoft Azure" 
    description="Saiba como criar uma linha baseado na web, de aplicativo de negócios em um ambiente de nuvem híbrido para IT pro ou testes de desenvolvimento." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Configurar um aplicativo de LOB baseado na web em uma nuvem híbrida para teste

Este tópico orienta a criação de um ambiente de nuvem híbrida simulada para testar uma linha baseada na web de aplicativo de negócios (LOB) hospedado no Microsoft Azure. Aqui está a configuração resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Essa configuração consiste em:

- Uma rede local simulada hospedada no Azure (o TestLab VNet).
- Uma rede virtual do local entre hospedado no Azure (TestVNET).
- Uma conexão VPN de VNet para VNet.
- Um servidor LOB baseados na web, o SQL server e o controlador de domínio secundário na rede virtual TestVNET.

Essa configuração fornece uma base e o ponto de partida comum da qual você pode:

- Desenvolva e teste aplicativos LOB hospedados em serviços de informações da Internet (IIS) em um banco de dados do SQL Server 2014 back-end no Azure.
- Execute os testes dessa híbrido simulada baseado em nuvem IT carga de trabalho.

Há três fases principais para configurar esse ambiente de teste de nuvem híbrida:

1.  Configure o ambiente de nuvem híbrida simulada.
2.  Configure o computador do SQL server (SQL1).
3.  Configure o servidor LOB (LOB1).

Essa carga de trabalho exige uma assinatura do Azure. Se você tiver uma assinatura do MSDN ou Visual Studio, consulte [crédito Azure mensal para os assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Para obter um exemplo de um aplicativo de LOB hospedado no Azure de produção, consulte o projeto de arquitetura de **linha de negócios** em [diagramas de arquitetura de Software Microsoft e plantas](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Fase 1: Configurar o ambiente de nuvem híbrida simulado

Crie o [simulado ambiente de teste de nuvem híbrida](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Porque esse ambiente de teste não exige a presença do servidor APP1 na sub-rede rede corporativa, você pode encerrar agora.

Esta é sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)
 
## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Fase 2: Configurar o computador do SQL server (SQL1)

Azure no portal do, inicie o computador de DC2, se necessário.

Em seguida, crie uma máquina virtual para SQL1 com esses comandos em um prompt de comando do PowerShell do Azure em seu computador local. Antes de executar esses comandos, preencher os valores de variáveis e remover os caracteres < e >.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Usar o portal do Azure para se conectar ao SQL1 usando a conta de administrador local do SQL1.

Em seguida, configure regras de Firewall do Windows para permitir o tráfego do SQL Server e testes de conectividade básica. A partir de um prompt de comando do Windows PowerShell nível de administrador em SQL1, execute esses comandos.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedida do endereço IP 192.168.0.4.

Em seguida, adicione o disco de dados extras no SQL1 como um novo volume com a letra f.

1.  No painel esquerdo do Gerenciador de servidor, clique em **arquivo e serviços de armazenamento**e, em seguida, clique em **discos**.
2.  No painel de conteúdo, no grupo de **discos** , clique em **disco 2** (com a **partição** definida como **desconhecido**).
3.  Clique em **tarefas**e, em seguida, clique em **Novo Volume**.
4.  No antes que você comece a página do Assistente de novo Volume, clique em **Avançar**.
5.  Na página Selecionar o servidor e disco, clique em **2 de disco**e clique em **Avançar**. Quando solicitado, clique em **Okey**.
6.  Em especificar o tamanho da página de volume, clique em **Avançar**.
7.  Em atribuir a uma página de carta ou uma pasta de unidade, clique em **Avançar**.
8.  Na página de configurações do sistema selecione arquivo, clique em **Avançar**.
9.  Na página Confirmar seleções, clique em **criar**.
10. Ao concluir, clique em **Fechar**.

Execute esses comandos no prompt de comando do Windows PowerShell em SQL1:

    md f:\Data
    md f:\Log
    md f:\Backup

Em seguida, ingresse SQL1 no domínio CORP Windows Server Active Directory com esses comandos no prompt do Windows PowerShell no SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Use a conta de CORP\User1 quando for solicitado a fornecer credenciais de conta de domínio para o comando **Adicionar computador** .

Depois de reiniciar, use o portal do Azure para se conectar ao SQL1 *com a conta de administrador local do SQL1*.

Em seguida, configure 2014 do SQL Server para usar a unidade de f para novos bancos de dados e permissões de conta de usuário.

1.  Na tela Iniciar, digite **Gerenciamento do SQL Server**e, em seguida, clique em **SQL Server 2014 Management Studio**.
2.  Em **conectar ao servidor**, clique em **Conectar**.
3.  No painel de árvore Object Explorer, clique com botão direito **SQL1**e clique em **Propriedades**.
4.  Na janela de **Propriedades do servidor** , clique em **Configurações de banco de dados**.
5.  Localize os **locais do banco de dados padrão** e definir esses valores: 
    - **Dados**, digite o caminho **f:\Data**.
    - **Log**, digite o caminho **f:\Log**.
    - Para **Backup**, digite o caminho **f:\Backup**.
    - Observação: Somente novos bancos de dados usam estes locais.
6.  Clique **Okey** para fechar a janela.
7.  No painel de árvore **Object Explorer** , abra **segurança**.
8.  **Logon** de atalho e, em seguida, clique em **Novo Login**.
9.  Em **nome de Login**, digite **CORP\User1**.
10. Na página de **Funções de servidor** , clique em **administrador do sistema**e clique em **Okey**.
11. Feche o Microsoft SQL Server Management Studio.

Esta é sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## <a name="phase-3-configure-the-lob-server-lob1"></a>Fase 3: Configurar o servidor LOB (LOB1)

Primeiro, crie uma máquina virtual para LOB1 com esses comandos no prompt de comando do PowerShell do Azure em seu computador local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, use o portal do Azure para se conectar à LOB1 com as credenciais da conta administrador local do LOB1.

Em seguida, configure uma regra de Firewall do Windows para permitir o tráfego de teste de conectividade básica. A partir de um prompt de comando do Windows PowerShell nível de administrador em LOB1, execute esses comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedida do endereço IP 192.168.0.4.

Em seguida, ingresse LOB1 no domínio do Active Directory CORP com esses comandos no prompt do Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Use a conta de CORP\User1 quando for solicitado a fornecer credenciais de conta de domínio para o comando **Adicionar computador** .

Depois de reiniciar, use o portal do Azure para se conectar à LOB1 com a conta de CORP\User1 e a senha.

Em seguida, configure LOB1 para IIS e testar o acesso de CLIENT1.

1.  Clique em Gerenciador de servidores, **recursos e adicionar funções**.
2.  Na página **antes de começar** , clique em **Avançar**.
3.  Na página **Selecionar tipo de instalação** , clique em **Avançar**.
4.  Na página **Selecionar o servidor de destino** , clique em **Avançar**.
5.  Na página de **funções de servidor** , clique em **Servidor de Web (IIS)** na lista de **funções**.
6.  Quando solicitado, clique em **Adicionar recursos**e clique em **Avançar**.
7.  Na página **Selecionar recursos** , clique em **Avançar**.
8.  Na página do **Servidor Web (IIS)** , clique em **Avançar**.
9.  Na página **Selecionar Serviços de função** , selecione ou desmarque as caixas de seleção para os serviços que você precisa para testar seu aplicativo de LOB e, em seguida, clique em **Avançar**.
10. Na página **Confirmar seleções de instalação** , clique em **instalar**.
11. Aguarde até a conclusão da instalação dos componentes e, em seguida, clique em **Fechar**.
12. Azure no portal do, conectar ao computador CLIENT1 com as credenciais de conta CORP\User1 e inicie o Internet Explorer.
13. Na barra de endereços, digite **http://lob1/** e pressione ENTER. Você verá a página da web IIS 8 padrão.

Esta é sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Esse ambiente está pronto para você implantar seu aplicativo baseado na web em LOB1 e testar a funcionalidade de CLIENT1 na sub-rede rede corporativa.

## <a name="next-step"></a>Próxima etapa

- Adicione uma nova máquina virtual usando o [portal do Azure](virtual-machines-windows-hero-tutorial.md).
