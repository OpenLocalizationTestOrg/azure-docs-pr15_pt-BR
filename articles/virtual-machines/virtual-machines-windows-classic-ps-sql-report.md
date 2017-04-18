<properties 
    pageTitle="Usar o PowerShell para criar uma máquina virtual com um servidor de relatório do modo nativo | Microsoft Azure"
    description="Este tópico descreve e orienta a implantação e a configuração de um servidor de relatório de modo nativo do SQL Server Reporting Services em uma máquina Virtual do Azure. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Usar o PowerShell para criar uma máquina virtual Azure com um servidor de relatório do modo nativo

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Este tópico descreve e orienta a implantação e a configuração de um servidor de relatório de modo nativo do SQL Server Reporting Services em uma máquina Virtual do Azure. As etapas neste documento usam uma combinação de etapas manuais para criar a máquina virtual e um script do Windows PowerShell para configurar o Reporting Services na máquina virtual. O script de configuração inclui a abertura de uma porta de firewall para HTTP ou HTTPs.

>[AZURE.NOTE] Se você não precisar **HTTPS** no servidor de relatório, **ignore a etapa 2**.
>
>Depois de criar a máquina virtual na etapa 1, vá para a seção usar script para configurar o servidor de relatório e HTTP. Depois de executar o script, o servidor de relatório está pronto para uso.

## <a name="prerequisites-and-assumptions"></a>Pré-requisitos e suposições

- **Assinatura do Azure**: Verifique se o número de cores disponíveis na sua assinatura do Azure. Se você criar o tamanho de máquina virtual recomendado de **A3**, é necessário cores disponíveis de **4** . Se você usar um tamanho de máquina virtual de **A2**, é necessário cores disponíveis de **2** .
    
    - Para verificar o limite de núcleo de sua assinatura, no portal do Azure clássico, clique em configurações no painel esquerdo e, em seguida, clique em uso no menu superior.
    
    - Para aumentar a cota de núcleo, contate o [Suporte do Azure](https://azure.microsoft.com/support/options/). Para obter informações de tamanho de máquina virtual, consulte [Tamanhos de máquina Virtual do Azure](virtual-machines-linux-sizes.md).

- **Scripts do Windows PowerShell**: O tópico pressupõe que você tenha um conhecimento básico de trabalho do Windows PowerShell. Para obter mais informações sobre como usar o Windows PowerShell, consulte o seguinte:

    - [Iniciando o Windows PowerShell no Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
    
    - [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Etapa 1: Provisionar uma máquina Virtual Azure

1. Navegue até o portal de clássico Azure.

1. No painel esquerdo, clique em **máquinas virtuais** .

    ![máquinas virtuais de Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. Clique em **novo**.

    ![novo botão](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. Clique em **Galeria**.

    ![nova máquina virtual da Galeria](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. Clique em **SQL Server 2014 RTM Standard – Windows Server 2012 R2** e clique na seta para continuar.

    ![próximo](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

    Se você precisar os dados de Reporting Services controlado pelo recurso de assinaturas, escolha **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Para obter mais informações sobre o suporte de recurso e edições do SQL Server, consulte [Recursos compatíveis com as edições do SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. Na página de **configuração de máquina Virtual** , edite os campos a seguir:
                                    
    - Se houver mais de uma **Data de versão**, selecione a versão mais recente.
    
    - **Nome de máquina virtual**: O nome da máquina também é usado na próxima página configuração como o nome de serviço de nuvem DNS padrão. O nome DNS deve ser exclusivo entre o serviço do Azure. Considere a possibilidade de configurar a máquina virtual com um nome de computador que descreva a máquina virtual para que é usada. Por exemplo, ssrsnativecloud.
    
    - **Camadas**: padrão
    
    - **Tamanho: A3** é o tamanho de máquina virtual recomendado para cargas de trabalho do SQL Server. Se uma máquina virtual só é usada como um servidor de relatório, um tamanho de máquina virtual de A2 é suficiente, a menos que o servidor de relatório enfrenta uma carga de trabalho grande. Para máquina virtual informações sobre preços, consulte [Máquinas virtuais preços](https://azure.microsoft.com/pricing/details/virtual-machines/).
    
    - **Novo nome de usuário**: o nome que você fornecer será criado como um administrador na máquina virtual.
    
    - **Nova senha** e **Confirmar**. Esta senha é usada para a nova conta de administrador e é recomendável que você use uma senha forte.
    
    - Clique em **Avançar**. ![próximo](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Na próxima página, edite os campos a seguir:

    - **Serviço de nuvem**: selecione **criar um novo serviço de nuvem**.
    
    - **Nome de DNS de serviço de nuvem**: Este é o nome DNS público do serviço de nuvem que está associado com a máquina virtual. O nome padrão é o nome que você digitou para o nome da máquina virtual. Se em etapas posteriores do tópico, você cria um certificado SSL confiável e, em seguida, o nome DNS é usado para o valor do "**emitido para**" do certificado.
    
    - **Rede de grupo/Virtual de região/afinidade**: escolha a região mais próxima aos seus usuários finais.
    
    - **Conta de armazenamento**: usar uma conta de armazenamento gerado automaticamente.
    
    - **Disponibilidade definida**: nenhum.
    
    - **Pontos de EXTREMIDADE** Manter os pontos de extremidade de **Área de trabalho remota** e **PowerShell** e adicione um HTTP ou HTTPS ponto de extremidade, dependendo do seu ambiente.

        - **HTTP**: as portas de pública e privada padrão são **80**. Observe que se você usar uma porta particular diferente de 80, modificar **$HTTPport = 80** no script http.

        - **HTTPS**: as portas de pública e privada padrão são **443**. É uma prática recomendada de segurança alterar a porta particular e configurar seu firewall e o servidor de relatório para usar a porta particular. Para obter mais informações sobre pontos de extremidade, consulte [como configurar a comunicação com uma máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md). Observe que se você usar uma porta diferente 443, altere o parâmetro **$HTTPsport = 443** no script HTTPS.
    
    - Clique em Avançar. ![próximo](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Na última página do assistente, mantenha o padrão de **instalar o agente de máquina virtual** selecionada. As etapas deste tópico não utilizam o agente de máquina virtual, mas se você planeja manter esta máquina virtual, o agente de máquina virtual e extensões permitirá aprimorar a ele CM.  Para obter mais informações sobre o agente de máquina virtual, consulte [agente de máquina virtual e extensões – parte 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Um dos ad padrão extensões instaladas em execução é a extensão de "BGINFO" que é exibido na área de trabalho de máquina virtual, informações do sistema como IP interno e de espaço livre.

1. Clique em Concluir. ![Okey](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. O **Status** da máquina virtual exibe como **inicial (provisionamento)** durante o processo de provisionar e exibe como **executando** quando a máquina virtual está provisionado e pronto para uso.

## <a name="step-2-create-a-server-certificate"></a>Etapa 2: Criar um certificado de servidor

>[AZURE.NOTE] Se você não precisar HTTPS no servidor de relatório, você pode **Ignorar a etapa 2** e vá para a seção **Usar script para configurar o servidor de relatório e HTTP**. Usar o script HTTP para configurar rapidamente o servidor de relatório e o servidor de relatório estará pronto para uso.

Para poder usar HTTPS na VM, é necessário um certificado SSL confiável. Dependendo do cenário, você pode usar um dos dois métodos a seguir:

- Um certificado SSL emitido por uma autoridade de certificação (CA) e confiável pela Microsoft. Os certificados de autoridade de certificação raiz são necessários para ser distribuído por meio do Microsoft Root Certificate Program. Para obter mais informações sobre este programa, consulte [Windows e do Windows Phone 8 SSL Root Certificate Program (autoridades de certificação de membro)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e [Introdução para o Microsoft Root Certificate Program](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Um certificado auto-assinado. Certificados autoassinados não são recomendados para ambientes de produção.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Usar um certificado criado por uma autoridade de certificação confiável (CA)

1. **Solicitar um certificado de servidor para o site de uma autoridade de certificação**. 

    Você pode usar o Assistente de certificado de servidor Web para gerar um arquivo de solicitação de certificado (certreq) que você enviar uma autoridade de certificação ou para gerar uma solicitação para uma autoridade de certificação online. Por exemplo, serviços de certificados Microsoft no Windows Server 2012. Dependendo do nível de garantia de identificação oferecida pelo seu certificado de servidor, é vários dias ou vários meses para autoridade de certificação aprovar a solicitação e enviar um arquivo de certificado. 

    Para obter mais informações sobre como solicitar uma certificados de servidor, consulte o seguinte: 
    
    - Use o [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
    
    - Ferramentas de segurança para administrar o Windows Server 2012.

    [Ferramentas de segurança para administrar o Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

    >[AZURE.NOTE] O campo **emitido para** do certificado SSL confiável deve ser o mesmo que o **Nome de DNS de serviço de nuvem** que você usou para a máquina virtual novo.

1. **Instalar o certificado de servidor no servidor Web**. Nesse caso, o servidor Web é a máquina virtual que hospeda o servidor de relatório e o site é criado em etapas posteriores quando você configura o Reporting Services. Para obter mais informações sobre como instalar o certificado de servidor no servidor Web usando o snap-in MMC do certificado, consulte [instalar um certificado de servidor](https://technet.microsoft.com/library/cc740068).
    
    Se você quiser usar o script incluído neste tópico, para configurar o servidor de relatório, o valor dos certificados **impressão digital** é necessária como um parâmetro do script. Consulte a próxima seção para obter detalhes sobre como obter a impressão digital do certificado.

1. Atribua o certificado do servidor para o servidor de relatório. A atribuição é concluída na próxima seção quando você configurar o servidor de relatório.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Para usar o certificado autoassinado de máquinas virtuais

Um certificado auto-assinado foi criado na máquina virtual quando a máquina virtual foi provisionada. O certificado tem o mesmo nome que o nome VM DNS. Para evitar erros de certificado, é necessário que o certificado é confiável na VM em si e também por todos os usuários do site.

1. Para confiar na autoridade de certificação raiz do certificado na máquina virtual Local, adicione o certificado para as **Autoridades de certificação raiz confiável**. O que vem a seguir é um resumo das etapas necessárias. Para obter etapas detalhadas sobre como confiar a autoridade de certificação, consulte [instalar um certificado de servidor](https://technet.microsoft.com/library/cc740068).

    1. Azure clássico no portal do, selecione a máquina virtual e clique em conectar. Dependendo da configuração do seu navegador, você pode ser solicitado a salvar um arquivo. rdp para conexão com a máquina virtual.
    
        ![conectar ao azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Use o nome de máquina virtual do usuário, o nome de usuário e a senha que você configurou quando você criou a máquina virtual. 
    
        Por exemplo, na imagem a seguir, o nome de máquina virtual é **ssrsnativecloud** e o nome de usuário é **testuser**.
        
        ![nome de máquina virtual do login inclui](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
    
    1. Execute mmc.exe. Para obter mais informações, consulte [como: exibir certificados com o Snap-in MMC](https://msdn.microsoft.com/library/ms788967.aspx).
    
    1. No menu **arquivo** do aplicativo de console, adicionar snap-in de **certificados** , selecione a **Conta de computador** quando solicitado e clique em **Avançar**.
    
    1. Selecione **Computador Local** para gerenciar e clique em **Concluir**.
    
    1. Clique em **Okey** e, em seguida, expanda a **certificados - pessoal** nós e, em seguida, clique em **certificados**. O certificado é denominado após o nome DNS da máquina virtual e termina com **cloudapp.net**. Clique com botão direito no nome do certificado e clique em **Copiar**.
    
    1. Expanda o nó de **Autoridades de certificação raiz confiáveis** e clique em **certificados** e, em seguida, clique em **Colar**.
    
    1. Para validar, clique duas vezes no nome do certificado em **Autoridades de certificação raiz confiável** e verifique se não existem erros e você ver seu certificado. Se você quiser usar o script HTTPS incluído neste tópico, para configurar o servidor de relatório, o valor dos certificados **impressão digital** é necessária como um parâmetro do script. **Para obter o valor de impressão digital**, conclua o seguinte. Também há uma amostra do PowerShell para recuperar a impressão digital na seção [Usar script para configurar o servidor de relatório e HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
        
        1. Clique duas vezes no nome do certificado, por exemplo ssrsnativecloud.cloudapp.net.
        
        1. Clique na guia **detalhes** .
        
        1. Clique em **impressão digital**. O valor da impressão digital é exibido no campo de detalhes, por exemplo a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c disponibilidade 2f.
        
        1. Copie a impressão digital e salvar o valor para mais tarde ou edite o script agora.
        
        1. (*) Antes de executar o script, remova os espaços entre os pares de valores. Por exemplo a impressão digital observada anteriormente agora seria a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
        
        1. Atribua o certificado do servidor para o servidor de relatório. A atribuição é concluída na próxima seção quando você configurar o servidor de relatório.

Se você estiver usando um certificado SSL autoassinado, o nome no certificado já corresponde ao nome do host da máquina virtual. Portanto, o DNS da máquina já está registrado globalmente e podem ser acessado a partir de qualquer cliente.

## <a name="step-3-configure-the-report-server"></a>Etapa 3: Configurar o servidor de relatório

Esta seção o orienta Configurando a máquina virtual como um servidor de relatório de modo nativo do Reporting Services. Você pode usar um dos seguintes métodos para configurar o servidor de relatório:

- Usar o script para configurar o servidor de relatório

- Use o Gerenciador de configuração para configurar o servidor de relatório.

Para obter etapas mais detalhadas, consulte a seção [se conectar à máquina Virtual e iniciar o Gerenciador de configuração do Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Anotação de autenticação:** Autenticação do Windows é o método de autenticação recomendado e é a autenticação do Reporting Services padrão. Somente os usuários que estão configurados na máquina virtual podem acessar o Reporting Services e atribuídas às funções do Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Usar o script para configurar o servidor de relatório e HTTP

Para usar o script do Windows PowerShell para configurar o servidor de relatório, conclua as etapas a seguir. A configuração inclui HTTP, HTTPS não:

1. Azure clássico no portal do, selecione a máquina virtual e clique em conectar. Dependendo da configuração do seu navegador, você pode ser solicitado a salvar um arquivo. rdp para conexão com a máquina virtual.

    ![conectar ao azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Use o nome de máquina virtual do usuário, o nome de usuário e a senha que você configurou quando você criou a máquina virtual. 

    Por exemplo, na imagem a seguir, o nome de máquina virtual é **ssrsnativecloud** e o nome de usuário é **testuser**.
    
    ![nome de máquina virtual do login inclui](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Na VM, abra **O Windows PowerShell ISE** com privilégios administrativos. O PowerShell ISE é instalado por padrão no Windows server 2012. É recomendável que você use o ISE em vez de uma janela padrão do Windows PowerShell para que você possa cole o script ISE, modifique o script e execute o script.

1. No Windows PowerShell ISE, clique no menu **Exibir** e clique em **Mostrar painel de Script**.

1. Copie o seguinte script e cole o script do painel de script do Windows PowerShell ISE.

        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
        
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
        
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Report Server Configuration Steps
        
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
           
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
        
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Se você criou a máquina virtual com uma porta HTTP diferente de 80, modifique o parâmetro $HTTPport = 80.

1. O script está atualmente configurado para Reporting Services. Se você quiser executar o script do Reporting Services, modifique a parte de versão do caminho ao namespace para "v11", na instrução Get-WmiObject.

1. Execute o script.

**Validação**: para verificar se a funcionalidade do servidor de relatório básico está funcionando, consulte a seção [Verificar a configuração](#verify-the-configuration) mais adiante neste tópico.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Usar o script para configurar o servidor de relatório e HTTPS

Para usar o Windows PowerShell para configurar o servidor de relatório, conclua as etapas a seguir. A configuração inclui HTTPS, não HTTP.

1. Azure clássico no portal do, selecione a máquina virtual e clique em conectar. Dependendo da configuração do seu navegador, você pode ser solicitado a salvar um arquivo. rdp para conexão com a máquina virtual.

    ![conectar ao azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Use o nome de máquina virtual do usuário, o nome de usuário e a senha que você configurou quando você criou a máquina virtual. 

    Por exemplo, na imagem a seguir, o nome de máquina virtual é **ssrsnativecloud** e o nome de usuário é **testuser**.

    ![nome de máquina virtual do login inclui](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. Na VM, abra **O Windows PowerShell ISE** com privilégios administrativos. O PowerShell ISE é instalado por padrão no Windows server 2012. É recomendável que você use o ISE em vez de uma janela padrão do Windows PowerShell para que você possa cole o script ISE, modifique o script e execute o script.

1. Para habilitar scripts em execução, execute o seguinte comando do Windows PowerShell:

        Set-ExecutionPolicy RemoteSigned

    Você pode, em seguida, execute o seguinte para verificar a política:

        Get-ExecutionPolicy

1. No **Windows PowerShell ISE**, clique no menu **Exibir** e clique em **Mostrar painel de Script**.

1. Copie o seguinte script e colá-los no painel de script do Windows PowerShell ISE.

        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
        
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
        
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Reporting Services Report Server Configuration Steps
        
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
        
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
            
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## 3. Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
        
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
        
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Modificar o parâmetro **$certificatehash** no script:

    - Este é um parâmetro **obrigatório** . Se você não salvou o valor de certificado obtidos nas etapas anteriores, use um dos seguintes métodos para copiar o valor de hash de certificado da impressão digital certificados.:

        Na VM, abra o Windows PowerShell ISE e execute o seguinte comando:

            dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

        A saída terá aparência semelhante à seguinte. Se o script retornará uma linha em branco, a máquina virtual não tiver um certificado configurado por exemplo, consulte a seção [para usar o certificado autoassinado de máquinas virtuais](#to-use-the-virtual-machines-self-signed-certificate).
    
    OU
    
    - Sobre a máquina virtual executar mmc.exe e adicione o snap-in de **certificados** .
    
    - Sob o nó de **Autoridades de certificação raiz confiáveis** , clique duas vezes em seu nome de certificado. Se você estiver usando o certificado auto-assinado da máquina virtual, o certificado é denominado após o nome DNS da máquina virtual e termina com **cloudapp.net**.
    
    - Clique na guia **detalhes** .
    
    - Clique em **impressão digital**. O valor da impressão digital é exibido no campo de detalhes, por exemplo af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 4f 66 c3 31 90 48
    
    - **Antes de executar o script**, remova os espaços entre os pares de valores. Por exemplo, af1160b64b288d890a8212ff6ba9c3664f319048

1. Modificar o parâmetro **$httpsport** : 

    - Se você usou a porta 443 para o ponto de extremidade HTTPS, então você não precisa atualizar esse parâmetro no script. Caso contrário, use o valor de porta selecionado quando você configurou o ponto de extremidade HTTPS particular na máquina virtual.

1. Modificar o parâmetro **$DNSName** : 

    - O script está configurado para um certificado curinga $DNSName = "+". Se você fizer deseja configurar uma associação de certificado curinga, comente $DNSName ="+"e habilite a seguinte linha, a referência de $DNSNAme completo, # # $DNSName="$server.cloudapp.net".

        Altere o valor de $DNSName se você não quiser usar o nome DNS da máquina virtual do Reporting Services. Se você usar o parâmetro, o certificado também deve usar esse nome e registrar o nome globalmente em um servidor DNS.

1. O script está atualmente configurado para Reporting Services. Se você quiser executar o script do Reporting Services, modifique a parte de versão do caminho ao namespace para "v11", na instrução Get-WmiObject.

1. Execute o script.

**Validação**: para verificar se a funcionalidade do servidor de relatório básico está funcionando, consulte a seção [Verificar a configuração](#verify-the-connection) mais adiante neste tópico. Para verificar o certificado ligação abrir um prompt de comando com privilégios administrativos e, em seguida, execute o seguinte comando:

    netsh http show sslcert

O resultado será incluem o seguinte:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Use o Gerenciador de configuração para configurar o servidor de relatório

Se não desejar executar o script do PowerShell para configurar o servidor de relatório, siga as etapas desta seção para usar o Gerenciador de configuração de modo nativo do Reporting Services para configurar o servidor de relatório.

1. Azure clássico no portal do, selecione a máquina virtual e clique em conectar. Use o nome de usuário e a senha que você configurou quando você criou a máquina virtual.

    ![conectar ao azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. Executar o Windows update e instalar atualizações para a máquina virtual. Se a reinicialização da máquina virtual for necessária, reinicie a máquina virtual e reconectar-se para a máquina virtual do Azure portal clássico.

1. No menu Iniciar na VM, digite **Reporting Services** e abra o **Gerenciador de configuração do Reporting Services**.

1. Deixe os valores padrão para o **Nome do servidor** e **Instância do servidor de relatório**. Clique em **Conectar**.

1. No painel esquerdo, clique em **URL do serviço Web**.

1. Por padrão, RS está configurado para a porta 80 HTTP com IP "Atribuído todos". Para adicionar HTTPS:

    1. No **Certificado SSL**: selecione o certificado que você deseja usar, por exemplo [nome da máquina virtual]. cloudapp.net. Se nenhuma certificados são listados, consulte a seção **etapa 2: criar um certificado de servidor** para obter informações sobre como instalar e confiar no certificado a máquina virtual.
    
    1. Em **Porta SSL**: escolha 443. Se você configurou o ponto de extremidade HTTPS particular na VM com uma porta particular diferente, use esse valor aqui.
    
    1. Clique em **Aplicar** e aguarde a conclusão da operação.

1. No painel esquerdo, clique em **banco de dados**.

    1. Clique em **Alterar Databas**e.
    
    1. Clique em **criar um novo banco de dados de servidor de relatório** e clique em **Avançar**.
    
    1. Deixe o **Nome do servidor**padrão: como a máquina virtual nome e deixe o **Tipo de autenticação** padrão como **Usuário atual** – **Segurança integrada**. Clique em **Avançar**.
    
    1. Deixe o **Nome do banco de dados** como **ReportServer** padrão e clique em **Avançar**.
    
    1. Deixe o padrão de **Tipo de autenticação** como **Credenciais de serviço** e clique em **Avançar**.
    
    1. Clique em **Avançar** na página de **Resumo** .
    
    1. Quando a configuração for concluída, clique em **Concluir**.

1. No painel esquerdo, clique em **URL do Gerenciador de relatório**. Deixe o **Diretório Virtual** como **relatórios** padrão e clique em **Aplicar**.

1. Clique em **Sair** para fechar o Gerenciador de configuração do Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Etapa 4: Abrir porta de Firewall do Windows

>[AZURE.NOTE] Se você usou um dos scripts para configurar o servidor de relatório, você pode ignorar esta seção. O script incluído uma etapa para abrir a porta de firewall. O padrão era portas 80 para HTTP e 443 para HTTPS.

Para se conectar remotamente ao Gerenciador de relatórios ou o servidor de relatório na máquina virtual, um ponto de extremidade de TCP é necessário na máquina virtual. Ele é necessário para abrir a mesma porta no firewall da VM. O ponto de extremidade foi criado quando a máquina virtual foi provisionada.

Esta seção fornece informações básicas sobre como abrir a porta de firewall. Para obter mais informações, consulte [Configurar um Firewall para o acesso do servidor de relatório](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] Se você usou o script para configurar o servidor de relatório, você pode ignorar esta seção. O script incluído uma etapa para abrir a porta de firewall.

Se você configurou uma porta particular para HTTPS diferente 443, modifique o script a seguir adequadamente. Para abrir a porta **443** em Firewall do Windows, preencha o seguinte:

1. Abra uma janela do Windows PowerShell com privilégios administrativos.

1. Se você usou uma porta diferente 443 quando você configurou o ponto de extremidade HTTPS na máquina virtual, atualize a porta no comando a seguir e, em seguida, execute o comando:

        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Quando o comando terminar, **Okey** é exibida no prompt de comando.

Para verificar que a porta é aberta, abra uma janela do Windows PowerShell e execute o seguinte comando:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar se a funcionalidade do servidor de relatório básico agora está funcionando, abra o navegador com privilégios administrativos e navegue até o seguinte relatório servidor ad report manager URLS:

- Na VM, navegue até a URL do servidor de relatório:

        http://localhost/reportserver

- Na VM, navegue até a URL do Gerenciador de relatório:

        http://localhost/Reports

- Em seu computador local, navegue até o Gerenciador de relatório **remoto** na máquina virtual. Atualize o nome DNS no exemplo a seguir, conforme apropriado. Quando for solicitada uma senha, use as credenciais de administrador que criou quando a máquina virtual foi provisionada. O nome de usuário está em [domínio]\[nome de usuário] formato, onde o domínio é o nome do computador máquina virtual, por exemplo ssrsnativecloud\testuser. Se você não estiver usando HTTP**S**, remova o **s** na URL. Consulte a próxima seção para obter informações sobre como criar usuários adicionais na máquina virtual.

        https://ssrsnativecloud.cloudapp.net/Reports

- Em seu computador local, navegue até a URL do servidor de relatório remoto. Atualize o nome DNS no exemplo a seguir, conforme apropriado. Se você não estiver usando HTTPS, remova o s na URL.

        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Criar usuários e atribuir funções

Depois de configurar e verificar o servidor de relatório, uma tarefa administrativa comum é criar um ou mais usuários e atribuir aos usuários para funções do Reporting Services. Para obter mais informações, consulte o seguinte:

- [Criar uma conta de usuário local](https://technet.microsoft.com/library/cc770642.aspx)

- [Acesso de usuário de conceder a um servidor de relatório (Gerenciador de relatório)](https://msdn.microsoft.com/library/ms156034.aspx))

- [Criar e gerenciar atribuições de função](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar relatórios na máquina Virtual Azure

A tabela a seguir resume algumas das opções disponíveis para publicar relatórios existentes de um computador local para o servidor de relatório hospedado no Microsoft Azure Virtual Machine:

- **Script de RS.exe**: usar RS.exe script para copiar itens de relatório do e servidor de relatório existente para sua máquina Virtual Microsoft Azure. Para obter mais informações, consulte a seção "Modo nativo para o modo nativo – máquina do Microsoft Azure Virtual" na [amostra Reporting Services rs.exe Script para migrar o conteúdo entre servidores de relatórios](https://msdn.microsoft.com/library/dn531017.aspx).

- **Construtor de relatórios**: A máquina virtual inclui o clique-uma vez a versão do construtor de relatórios do Microsoft SQL Server. Para iniciar o tempo de construtor o primeiro relatório na máquina virtual:

    1. Inicie o navegador com privilégios administrativos.
    
    1. Navegue até Gerenciador de relatórios na máquina virtual e clique em **Construtor de relatórios** na faixa de opções.

    Para obter mais informações, consulte [instalar, desinstalar e suporte construtor de relatórios](https://technet.microsoft.com/library/dd207038.aspx).

- **Ferramentas de dados do SQL Server: máquina virtual**: se você criou a máquina virtual com o SQL Server 2012, ferramentas de dados do SQL Server está instalada na máquina virtual e podem ser usadas para criar **Projetos de servidor de relatório** e relatórios na máquina virtual. Ferramentas de dados do SQL Server podem publicar os relatórios do servidor de relatórios na máquina virtual.
    
    Se você criou a máquina virtual com o SQL server 2014, você pode instalar dados ferramentas - BI do SQL Server para visual Studio. Para obter mais informações, consulte o seguinte:

    - [Ferramentas de dados do Microsoft SQL Server - Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)

    - [Ferramentas de dados do Microsoft SQL Server - Business Intelligence para Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)

    - [Ferramentas de dados do SQL Server e SQL Server Business Intelligence (BI SSDT)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **Ferramentas de dados do SQL Server: remoto**: no seu computador local, criar um projeto do Reporting Services em ferramentas de dados do SQL Server que contém os relatórios do Reporting Services. Configure o projeto para conectar-se a URL de serviço web.

    ![SSDT propriedades do projeto para o projeto SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **Usar script**: Use o script para copiar o conteúdo do servidor de relatório. Para obter mais informações, consulte [amostra Reporting Services rs.exe Script para migrar o conteúdo entre servidores de relatórios](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimizar o custo se você não estiver usando a máquina virtual

>[AZURE.NOTE] Para minimizar encargos para suas máquinas virtuais do Azure quando não estiver em uso, desligue a máquina virtual do Azure portal clássico. Se você usar as opções de energia do Windows dentro de uma máquina virtual para desligar a máquina virtual, você ainda é cobrados a mesma quantidade para a máquina virtual. Para reduzir tarifas, você precisa desligar a máquina virtual no portal de clássico do Azure. Se você não precisa mais a máquina virtual, lembre-se excluir a máquina virtual e os arquivos. vhd associado para evitar encargos de armazenamento. Para obter mais informações, consulte a seção Perguntas frequentes sobre detalhes de [preços](https://azure.microsoft.com/pricing/details/virtual-machines/)de máquinas virtuais.

## <a name="more-information"></a>Mais informações

### <a name="resources"></a>Recursos

- Para conteúdo semelhante relacionado a uma implantação de servidor único de Business Intelligence do SQL Server e SharePoint 2013, consulte [Usar o Windows PowerShell para criar um Azure máquina virtual com BI do SQL Server e o SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Para conteúdo semelhante relacionado a uma implantação de vários servidor de Business Intelligence do SQL Server e SharePoint 2013, consulte [Implantar o Business Intelligence do SQL Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/dn321998.aspx).

- Para obter informações gerais relacionadas a implantações do SQL Server Business Intelligence em máquinas virtuais do Azure, consulte [SQL Server Business Intelligence em máquinas virtuais do Azure](virtual-machines-windows-classic-ps-sql-bi.md).

- Para obter mais informações sobre o custo do Azure calcular encargos, consulte o guia máquinas virtuais do [Azure Calculadora de preços](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Conteúdo da comunidade

- Para obter instruções passo a passo sobre como criar um servidor de relatório Reporting Services nativos do modo sem usar o script, consulte [Hospedando SQL Reporting Service na máquina Virtual do Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Links para outros recursos para o SQL Server em VMs do Azure

[SQL Server em Visão geral do Azure máquinas virtuais](virtual-machines-windows-sql-server-iaas-overview.md)
