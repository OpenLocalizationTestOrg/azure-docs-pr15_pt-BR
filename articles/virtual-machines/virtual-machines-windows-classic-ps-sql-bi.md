<properties
    pageTitle="Business Intelligence do SQL Server | Microsoft Azure"
    description="Este tópico usa recursos criados com o modelo clássico de implantação e descreve os recursos de Business Intelligence (BI) disponíveis para o SQL Server em máquinas virtuais do Azure (VMs) em execução."
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

# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Business Intelligence do SQL Server no Azure máquinas virtuais

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

A Galeria de máquina Virtual da Microsoft Azure inclui imagens que contenham instalações do SQL Server. As edições do SQL Server compatíveis compatíveis as imagens da galeria são os mesmos arquivos de instalação, que você pode instalar em computadores locais e máquinas virtuais. Este tópico resume os recursos do SQL Server Business Intelligence (BI) instalados nas imagens e etapas de configuração necessárias após uma máquina virtual está provisionada. Este tópico também descreve topologias de implantação com suporte para recursos de BI e práticas recomendadas.

## <a name="license-considerations"></a>Considerações sobre licença

Há duas maneiras de licença do SQL Server em máquinas virtuais do Microsoft Azure:

1. Benefícios de mobilidade de licença que fazem parte do Software Assurance. Para obter mais informações, consulte [Mobilidade de licença por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. Paga por taxa de hora do Azure máquinas virtuais com SQL Server instalado. Consulte a seção "SQL Server" em [Máquinas virtuais preços](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Para obter mais informações sobre as taxas de licenciamento e atuais, consulte [FAQ de licenciamento de máquinas virtuais](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server imagens disponíveis na Galeria de máquina Virtual Azure

A Galeria de máquina Virtual da Microsoft Azure inclui várias imagens que contêm o Microsoft SQL Server. O software instalado na máquina virtual imagens varia de acordo com a versão do sistema operacional e a versão do SQL Server. Na lista de imagens disponíveis na Galeria Azure máquina virtual altera com frequência.

![Imagem do SQL azure Galeria de máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) O seguinte script do PowerShell retorna a lista de imagens Azure que contêm "SQL-Server" no ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Para obter mais informações sobre edições e recursos compatíveis com SQL Server, consulte o seguinte:

- [Edições do SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Recursos compatíveis com as edições do SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Recursos de BI instalados nas imagens de galeria de máquina Virtual do SQL Server

A tabela a seguir resume os recursos de Business Intelligence instalados nas imagens de galeria de máquina Virtual da Microsoft Azure comuns para SQL Server"

- SQL Server 2016 RC3

- SQL Server 2014 SP1 Enterprise

- Padrão do SQL Server 2014 SP1

- SQL Server 2012 SP2 Enterprise

- Padrão de SP2 do SQL Server 2012

|Recurso de BI do SQL Server|Instalado na imagem da Galeria|Anotações|
|---|---|---|
|**Modo nativo do Reporting Services**|Sim|Instalado, mas exige configuração, incluindo a URL do Gerenciador de relatório. Consulte a seção [Configurar Reporting Services](#configure-reporting-services).|
|**Do modo do SharePoint Reporting Services**|Não|A imagem da Galeria de máquina Virtual da Microsoft Azure não inclui o SharePoint ou SharePoint arquivos de instalação. <sup>1</sup>|
|**Mineração de dados e multidimensionais do Analysis Services (OLAP)**|Sim|Instalado e configurado como a instância do Analysis Services padrão|
|**Tabulares do Analysis Services**|Não|Suporte no SQL Server 2012, 2014 e 2016 imagens, mas ele não está instalado por padrão. Instale outra instância do Analysis Services. Consulte a seção instalar outros serviços do SQL Server e recursos neste tópico.|
|**Analysis Services PowerPivot para SharePoint**|Não|A imagem da Galeria de máquina Virtual da Microsoft Azure não inclui o SharePoint ou SharePoint arquivos de instalação. <sup>1</sup>|

<sup>1</sup> para obter informações adicionais sobre o SharePoint e o Azure máquinas virtuais, consulte [Microsoft Azure arquiteturas do SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) e [Implantação do SharePoint em máquinas virtuais do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Execute o seguinte comando do PowerShell para obter uma lista de serviços instalados que contêm "SQL" no nome do serviço.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Recomendações gerais e práticas recomendadas

- O tamanho mínimo recomendado para uma máquina virtual é **A3** ao usar o SQL Server Enterprise Edition. O tamanho de máquina virtual **A4** é recomendado para implantações de BI do SQL Server Analysis Services e Reporting Services.

    Para obter informações sobre os tamanhos de máquina virtual atuais, consulte [Tamanhos de máquina Virtual do Azure](virtual-machines-linux-sizes.md).

- É uma prática recomendada para gerenciamento de disco para armazenar dados, log e arquivos de backup em unidades diferentes **C**: e **D**:. Por exemplo, crie discos de dados **E**: e **F**:.

    - Unidade de cache política para a unidade padrão **C**: não é ideal para trabalhar com dados.

    - **D**: unidade é uma unidade temporária que é usada principalmente para o arquivo da página. **D**: unidade não é mantida e não são salvas no armazenamento de blob. Tarefas de gerenciamento, como uma alteração para o tamanho de máquina virtual redefinir o **D**: unidade. É recomendável **não** usar o **D**: unidade para arquivos de banco de dados, incluindo tempdb.

    Para obter mais informações sobre como criar e anexar discos, consulte [como anexar um disco de dados para uma máquina Virtual](virtual-machines-windows-classic-attach-disk.md).

- Parar ou desinstalar os serviços que você não planeja usar. Por exemplo se a máquina virtual é usada somente para Reporting Services, parar ou desinstalar o Analysis Services e SQL Server Integration Services. A imagem a seguir é um exemplo dos serviços que são iniciados por padrão.

    ![Serviços do SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    >[AZURE.NOTE] Mecanismo de banco de dados do SQL Server é necessário nos cenários de BI com suporte. Em um único servidor topologia de máquina virtual, o mecanismo de banco de dados é necessário estar em execução na mesma VM.

    Para obter mais informações, consulte o seguinte: [Desinstalar o Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) e [desinstalar uma instância do Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Verificar o **Windows Update** para o novo 'atualizações importantes'. As imagens de máquina Virtual da Microsoft Azure frequentemente são atualizadas; Entretanto atualizações importantes podem se tornar disponíveis no **Windows Update** após a imagem de máquina virtual foi atualizado pela última vez.

## <a name="example-deployment-topologies"></a>Topologias de implantação de exemplo

A seguir estão as implantações de exemplo que usam máquinas virtuais do Microsoft Azure. As topologias nesses diagramas são apenas algumas das topologias possíveis que você pode usar com os recursos de BI do SQL Server e máquinas virtuais do Microsoft Azure.

### <a name="single-virtual-machine"></a>Única máquina Virtual

Analysis Services, Reporting Services, mecanismo de banco de dados do SQL Server e fontes de dados em uma única máquina virtual.

![cenário de BI iass com 1 máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Duas máquinas virtuais

- Do Analysis Services, Reporting Services e o mecanismo de banco de dados do SQL Server em uma única máquina virtual. Essa implantação inclui os bancos de dados do servidor de relatório.

- Fontes de dados em uma segunda máquina de virtual. A segunda máquina de virtual inclui o mecanismo de banco de dados do SQL Server como uma fonte de dados.

![cenário de BI iaas com 2 máquinas virtuais](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azure misto – dados no banco de dados do SQL Azure

- Do Analysis Services, Reporting Services e o mecanismo de banco de dados do SQL Server em uma única máquina virtual. Essa implantação inclui os bancos de dados do servidor de relatório.

- Fonte de dados é o banco de dados do SQL Azure.

![máquina virtual de cenários de BI iaas e AzureSQL como fonte de dados](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Híbrido – dados locais

- Na implantação de exemplo do Analysis Services, Reporting Services e o mecanismo de banco de dados do SQL Server são executados em uma única máquina virtual. Na máquina virtual hospedará os bancos de dados do servidor de relatório. A máquina virtual está associada a um domínio local através da rede Virtual do Azure ou alguns outro VPN túnel solução.

- Fonte de dados é local.

![máquina virtual de cenários de BI iaas e em fontes de dados local](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuração de modo nativo de serviços de relatório

A imagem da Galeria de máquina virtual para o SQL Server inclui modo Reporting Services nativo instalado, mas o servidor de relatório não está configurado. As etapas desta seção Configurar o servidor de relatório do Reporting Services. Para obter informações mais detalhadas sobre como configurar o modo nativo de serviços de relatório, consulte [Instalar o Reporting Services nativo modo relatório Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE] Para conteúdo semelhante que usa scripts do Windows PowerShell para configurar o servidor de relatório, consulte [Usar o PowerShell para criar um servidor do Azure máquina virtual com um nativo modo relatório](virtual-machines-windows-classic-ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Conectar-se à máquina Virtual e iniciar o Gerenciador de configuração de serviços de relatório

Há dois fluxos de trabalho comuns de conexão para uma máquina Virtual do Azure:

- Conectar-se no, clique no nome da máquina virtual e clique em **Conectar**. Uma conexão de área de trabalho remota é aberto e o nome do computador é preenchido automaticamente.

    ![conectar ao azure máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- Conectar-se à máquina virtual com Conexão de área de trabalho remota do Windows. Na interface do usuário da área de trabalho remota:

    1. Digite o **nome do serviço de nuvem** como o nome do computador.

    1. Digite dois-pontos (:) e o número da porta público que está configurado para a empresa de desktop remota TCP.

        MyService.cloudapp.NET:63133

        Para obter mais informações, consulte [o que é um serviço de nuvem?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Inicie o Gerenciador de configuração de serviços de relatório.**

1. No **Windows Server 2012**:

1. Na tela **Iniciar** , digite **Reporting Services** para ver uma lista de aplicativos.

1. **Gerenciador de configuração do Reporting Services** de atalho e clique em **Executar como administrador**.

1. No **Windows Server 2008 R2**:

1. Clique em **Iniciar**e, em seguida, clique em **Todos os programas**.

1. Clique em **Microsoft SQL Server 2016**.

1. Clique em **Ferramentas de configuração**.

1. **Gerenciador de configuração do Reporting Services** de atalho e clique em **Executar como administrador**.

Ou

1. Clique em **Iniciar**.

1. Na caixa de diálogo **Pesquisar programas e arquivos** , digite **o reporting services**. Se a máquina virtual está executando o Windows Server 2012, digite **o reporting services** na tela Iniciar do Windows Server 2012.

1. **Gerenciador de configuração do Reporting Services** de atalho e clique em **Executar como administrador**.

    ![Procure Gerenciador de configuração do ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurar o Reporting Services

**Conta de serviço e a URL do serviço da web:**

1. Verifique se que o **Nome do servidor** é o nome de servidor local e clique em **Conectar**.

1. Observe o **Nome do banco de dados de servidor de relatório**de em branco. O banco de dados é criado quando a configuração é concluída.

1. Verifique o **Status do servidor de relatório** é **iniciado**. Se você quiser verificar se o serviço no Gerenciador de servidores do Windows, o serviço é o serviço do Windows **SQL Server Reporting Services** .

1. Clique na **Conta de serviço** e altere a conta conforme necessário. Se a máquina virtual for usada em um ambiente Unido fora do domínio, a conta **ReportServer** interna é suficiente. Para obter mais informações sobre a conta de serviço, consulte [A conta de serviço](https://msdn.microsoft.com/library/ms189964.aspx).

1. No painel esquerdo, clique em **URL do serviço Web** .

1. Clique em **Aplicar** para configurar os valores padrão.

1. Observe as **URLs de serviço de Web de servidor de relatório**. Observe que a porta TCP padrão é 80 e é parte da URL. Em uma etapa posterior, você criar um ponto de extremidade de máquina Virtual do Microsoft Azure para a porta.

1. No painel de **resultados** , verifique se as ações que foi concluídas com êxito.

**Banco de dados:**

1. No painel esquerdo, clique em **banco de dados** .

1. Clique em **banco de dados de alteração**.

1. Verifique se **que criar um novo banco de dados de servidor de relatório** está selecionada e clique em Avançar.

1. Verifique se **O nome do servidor** e clique em **Testar Conexão**.

1. Se o resultado for **conexão de teste foi bem-sucedida**, clique em **Okey** e clique em **Avançar**.

1. Observe o nome do banco de dados é **ReportServer** e o **modo de servidor de relatório** é **nativo** e clique em **Avançar**.

1. Clique em **Avançar** na página de **credenciais** .

1. Clique em **Avançar** na página de **Resumo** .

1. Clique em **Avançar** na página de **progresso e acabamento** .

**Web URL de Portal, ou Gerenciador de relatórios para 2012 e de 2014:**

1. Clique em **URL de Portal da Web**ou **URL do Gerenciador de relatório** para 2014 e 2012, no painel esquerdo.

1. Clique em **Aplicar**.

1. No painel de **resultados** , verifique se as ações que foi concluídas com êxito.

1. Clique em **Sair**.

Para obter informações sobre as permissões do servidor de relatório, consulte [Concedendo permissões em um servidor de relatório do modo nativo](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Navegue para o local Gerenciador de relatório

Para verificar a configuração, navegue até o Gerenciador de relatórios sobre a máquina virtual.

1. Na VM, inicie o Internet Explorer com privilégios de administrador.

1. Navegue até http://localhost/reports sobre a máquina virtual.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Para conectar-se ao portal da web remoto ou Gerenciador de relatórios para 2014 e 2012

Se você deseja se conectar ao portal da web ou Gerenciador de relatórios de 2014 e 2012, na máquina virtual de um computador remoto, crie uma nova máquina virtual ponto de extremidade de TCP. Por padrão, o servidor de relatório ouve para solicitações HTTP na **porta 80**. Se você configurar as URLs de servidor de relatório para usar uma porta diferente, especifique esse número de porta nas instruções a seguir.

1. Crie um ponto de extremidade para máquina Virtual do porta TCP 80. Para mais informações, consulte a seção de [pontos de extremidade de máquina Virtual e portas do Firewall](#virtual-machine-endpoints-and-firewall-ports) neste documento.

1. Abra a porta 80 no firewall da máquina virtual.

1. Navegue até o portal da web ou Gerenciador de relatório, usando Azure Virtual Machine **Nome DNS** como o nome do servidor na URL. Por exemplo:

    **Servidor de relatórios**: http://uebi.cloudapp.net/reportserver  **portal da Web**: http://uebi.cloudapp.net/reports

    [Configurar um Firewall para acesso de servidor de relatório](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar relatórios na máquina Virtual Azure

A tabela a seguir resume algumas das opções disponíveis para publicar relatórios existentes de um computador local para o servidor de relatório hospedado no Microsoft Azure Virtual Machine:

- **Construtor de relatórios**: A máquina virtual inclui o clique-uma vez a versão do construtor de relatórios do Microsoft SQL Server para SQL 2014 e 2012. Para iniciar o tempo de construtor o primeiro relatório na máquina virtual com 2016 SQL:

    1. Inicie o navegador com privilégios administrativos.

    1. Navegue até o portal da web, na máquina virtual e selecione o ícone de **Download** no canto superior direito.
    
    1. Selecione **Construtor de relatórios**.

    Para obter mais informações, consulte [Iniciar o construtor de relatórios](https://msdn.microsoft.com/library/ms159221.aspx).

- **Ferramentas de dados do SQL Server**: máquina virtual: ferramentas de dados do SQL Server está instalada na máquina virtual e podem ser usadas para criar **Projetos de servidor de relatório** e relatórios na máquina virtual. Ferramentas de dados do SQL Server podem publicar os relatórios do servidor de relatórios na máquina virtual.

- **Ferramentas de dados do SQL Server: remoto**: no seu computador local, criar um projeto do Reporting Services em ferramentas de dados do SQL Server que contém os relatórios do Reporting Services. Configure o projeto para conectar-se a URL de serviço web.

    ![SSDT propriedades do projeto para o projeto SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- Criar um. Disco rígido VHD que contém relatórios e carregue e anexar a unidade.

    1. Criar um. Disco rígido de VHD no computador local que contém seus relatórios.

    1. Criar e instalar um certificado de gerenciamento.

    1. Carregar o arquivo VHD Azure usando o cmdlet Add-AzureVHD [criar e carregar um VHD de servidor do Windows para o Azure](virtual-machines-windows-classic-createupload-vhd.md).

    1. Conecte o disco na máquina virtual.

## <a name="install-other-sql-server-services-and-features"></a>Instalar outros recursos e serviços do SQL Server

Para instalar os serviços do SQL Server adicionais, como o Analysis Services no modo tabular, execute o Assistente de instalação do SQL server. Os arquivos de instalação estão no disco local da máquina virtual.

1. Clique em **Iniciar** e clique em **Todos os programas**.

1. Clique em **Microsoft SQL Server 2016**, o **Microsoft SQL Server 2014** ou o **Microsoft SQL Server 2012** e clique em **Ferramentas de configuração**.

1. Clique em **Central de instalação do SQL Server**.

Ou executar C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe ou C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] Na primeira vez que você executar a instalação do SQL Server, os arquivos de instalação mais podem ser baixados e exigem a reinicialização da máquina virtual e a reinicialização do programa de instalação do SQL Server.
>
>Se você precisa personalizar repetidamente a imagem selecionada do Microsoft Azure Virtual Machine, considere criar sua própria imagem do SQL Server. Funcionalidade de SysPrep de serviços de análise foi ativada com o SQL Server 2012 SP1 CU2. Para obter mais informações, consulte [Considerações para instalar o SQL Server usando o SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) e [Suporte a Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="to-install-analysis-services-tabular-mode"></a>Para instalar o modo Tabular do Analysis Services

As etapas nesta seção **Resumir** a instalação do modo tabular do Analysis Services. Para obter mais informações, consulte o seguinte:

- [Instalar o Analysis Services no modo Tabular](https://msdn.microsoft.com/library/hh231722.aspx)

- [Modelagem de tabular (Adventure Works Tutorial)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Para instalar o modo Tabular do Analysis Services:**

1. No Assistente de instalação do SQL Server, clique em **instalação** no painel esquerdo e, em seguida, clique em **instalação autônoma do novo SQL server ou adicionar recursos a uma instalação existente do**.

    - Se você vir a **Procurar pasta**, navegue até c:\SQLServer_13.0_full, c:\SQLServer_12.0_full ou c:\SQLServer_11.0_full e clique em **Okey**.

1. Clique em **Avançar** na página de atualizações do produto.

1. Na página **Tipo de instalação** , selecione **executar uma nova instalação do SQL Server** e clique em **Avançar**.

1. Na página **Configuração de função** , clique em **Instalação de recursos do SQL Server**.

1. Na página de **Seleção do recurso** , clique em **Analysis Services**.

1. Na página **Configuração da instância** , digite um nome descritivo, como **Tabular** nas caixas de texto **Instância nomeados** e **Id da instância** .

1. Na página de **Configuração do Analysis Services** , selecione **Modo Tabular**. Adicione o usuário atual para a lista de permissões administrativas.

1. Concluir e fechar o Assistente de instalação do SQL Server.

## <a name="analysis-services-configuration"></a>Configuração do Analysis Services

### <a name="remote-access-to-analysis-services-server"></a>Acesso remoto ao servidor do Analysis Services

Servidor do Analysis Services só oferece suporte a autenticação do windows. Para acessar o Analysis Services remotamente de aplicativos cliente como o SQL Server Management Studio ou ferramentas de dados do SQL Server, a máquina virtual deve ser adicionado ao domínio local, usando a rede Virtual do Azure. Para mais informações, consulte [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

Uma **instância padrão** do Analysis Services ouve na porta TCP **2383**. Abra a porta no firewall máquinas virtuais. Uma instância nomeada clusterizada do Analysis Services também ouve na porta **2383**.

Para uma **instância nomeada** do Analysis Services, o serviço de navegador do SQL Server é necessário para gerenciar o acesso de porta. A configuração padrão do navegador do SQL Server é porta **2382**.

No firewall máquinas virtuais, abra a porta **2382** e crie um estático Analysis Services chamado porta de instância.

1. Para verificar as portas que já estão em uso na máquina virtual e qual processo está usando as portas, execute o seguinte comando com privilégios administrativos:

        netstat /ao

1. Use o SQL Server Management Studio para criar um estático Analysis Services porta instância atualizando 'Porta' valor nomeado no tabular AS propriedades gerais de instância. Para obter mais informações, consulte o de "usar uma porta fixa para um padrão ou instância nomeada" em [Configurar o Firewall do Windows para permitir acesso do Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Reinicie a instância tabular do serviço do Analysis Services.

Para mais informações, consulte a seção de **pontos de extremidade de máquina Virtual e portas do Firewall** neste documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Pontos de extremidade de máquina virtual e portas do Firewall

Esta seção resume pontos de extremidade do Microsoft Azure Máquina Virtual para criar e portas para abrir em firewalls máquina virtual. A tabela a seguir resume as portas **TCP** para criar pontos de extremidade e as portas a serem abertas no firewall máquinas virtuais.

- Se você estiver usando uma máquina virtual única e os dois itens a seguir forem verdadeiros, você não precisa criar pontos de extremidade de máquina virtual e você não precisa abrir as portas no firewall a máquina virtual.

    - Você não se conectar remotamente para os recursos do SQL Server na máquina virtual. Estabelecer uma conexão de área de trabalho remota para a máquina virtual e acessar os recursos do SQL Server localmente na máquina virtual não é considerado uma conexão remota recursos do SQL Server.

    - Você não ingressar a máquina virtual a um domínio local por meio de rede Virtual do Azure ou outra solução de túnel VPN.

- Se a máquina virtual não está associada a um domínio mas deseja remotamente se conecte aos recursos do SQL Server na máquina virtual:

    - Abra as portas no firewall a máquina virtual.

    - Crie pontos de extremidade de máquina virtual para as portas observados (*).

- Se a máquina virtual tiver ingressada em um domínio usando um túnel VPN como rede Virtual do Azure, os pontos de extremidade não são necessários. No entanto, abra as portas no firewall a máquina virtual.

  	|Porta|Tipo|Descrição|
|---|---|---|
|**80**|TCP|Servidor de relatório acesso remoto (*).|
|**1433**|TCP|SQL Server Management Studio (*).|
|**1434**|UDP|Navegador do SQL Server. Isso é necessário quando a máquina virtual em parte de um domínio.|
|**2382**|TCP|Navegador do SQL Server.|
|**2383**|TCP|Instância do SQL Server Analysis Services padrão e instâncias nomeadas agrupadas.|
|**Definidas pelo usuário**|TCP|Crie um estático Analysis Services chamado porta de instância para um número de porta que você escolher e, em seguida, desbloquear o número da porta no firewall.|

Para obter mais informações sobre a criação de pontos de extremidade, consulte o seguinte:

- Criar pontos de extremidade:[como configurar pontos de extremidade para uma máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md).

- SQL Server: Consulte a seção "Etapas de configuração completa para se conectar à máquina virtual usando SQL Server Management Studio" de [provisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

O diagrama a seguir ilustra as portas a serem abertas no firewall máquina virtual para permitir o acesso remoto aos recursos e componentes a máquina virtual.

![portas abrir para os aplicativos de bi em VMs do Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Recursos

- Revise a política de suporte para software de servidor Microsoft usado no ambiente de máquina Virtual de Azure. O tópico a seguir resume o suporte para recursos como BitLocker, cluster de Failover e balanceamento de carga de rede. [Software de servidor Microsoft oferece suporte para máquinas virtuais do Azure](http://support.microsoft.com/kb/2721672).

- [SQL Server em Visão geral do Azure máquinas virtuais](virtual-machines-windows-sql-server-iaas-overview.md)

- [Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [Uma máquina de Virtual do SQL Server no Azure de provisionamento](virtual-machines-windows-portal-sql-server-provision.md)

- [Como anexar um disco de dados a uma máquina Virtual](virtual-machines-windows-classic-attach-disk.md)

- [Migrar um banco de dados para SQL Server em uma máquina virtual Azure](virtual-machines-windows-migrate-sql.md)

- [Determinar o modo de servidor de uma instância do Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)

- [Modelagem de multidimensional (Adventure Works Tutorial)](https://technet.microsoft.com/library/ms170208.aspx)

- [Centro de documentação do Azure](https://azure.microsoft.com/documentation/)

- [Usando o Power BI em um ambiente híbrido](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Enviar comentários e informações de contato por meio de conexão do Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Conteúdo da comunidade

- [Gerenciamento de banco de dados do SQL Azure com o PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)
