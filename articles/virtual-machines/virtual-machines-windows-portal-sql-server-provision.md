<properties
    pageTitle="Provisionar uma máquina do SQL Server Virtual | Microsoft Azure"
    description="Criar e se conectar a uma máquina virtual do SQL Server Azure usando o Portal. Neste tutorial usa o modo do Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Provisionar uma máquina virtual de SQL Server no Portal do Azure

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

Este tutorial de ponta a ponta mostra como usar o Portal do Azure para provisionar uma máquina virtual executando o SQL Server.

A Galeria do Azure virtuais de máquina inclui várias imagens que contêm o Microsoft SQL Server. Com apenas alguns cliques, você pode selecionar uma das imagens SQL VM da galeria e provisioná-lo em seu ambiente do Azure.

Neste tutorial, você irá:

- [Selecione uma imagem de SQL VM da Galeria](#select-a-sql-vm-image-from-the-gallery)
- [Configurar e criar a máquina virtual](#configure-the-vm)
- [Abra a máquina virtual com área de trabalho remota](#open-the-vm-with-remote-desktop)
- [Conectar ao SQL Server remotamente](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Selecione uma imagem de SQL VM da Galeria

1. Faça logon no [portal do Azure](https://portal.azure.com) usando sua conta.

    >[AZURE.NOTE] Se você não tiver uma conta do Azure, visite a [versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

1. No portal do Azure, clique em **novo**. O portal abre a lâmina de **novo** . Os recursos de máquina virtual do SQL Server são no grupo **máquinas virtuais** do Marketplace.

1. Na lâmina **novo** , clique em **máquinas virtuais**.

1. Para ver todas as imagens disponíveis, clique em **ver todos** na lâmina **máquinas virtuais** .

    ![Azure máquinas virtuais Blade](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. Em **servidores de banco de dados**, clique em **SQL Server**. Talvez seja necessário rolar para baixo para localizar **os servidores de banco de dados**. Examine os modelos disponíveis do SQL Server.

    ![Galeria de máquina virtual SQL imagens](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. Cada modelo identifica uma versão do SQL Server e um sistema operacional. Selecione uma dessas imagens na lista. Revise a lâmina de detalhes que fornece uma descrição da imagem máquina virtual.

    >[AZURE.NOTE] Imagens de SQL VM incluem os custos de licenciamento para o SQL Server para o preço por minuto da máquina virtual que você cria. Há outra opção para trazer o-proprietário-licença (BYOL) e pagar somente para a máquina virtual. Os nomes de imagem são prefixo {BYOL}. Para obter mais informações sobre essa opção, consulte [Introdução ao SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

1. Em **Selecione um modelo de implantação**, verifique se o **Gerenciador de recursos** está selecionada. Gerenciador de recursos é o modelo de implantação recomendada para novas máquinas virtuais. Clique em **criar**.

    ![Criar com o Gerenciador de recursos de máquina virtual do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configurar a máquina virtual
Há cinco lâminas para configurar uma máquina virtual de SQL Server.

| Etapa               | Descrição                          |
|---------------------|-------------------------------|
| **Noções básicas**              | [Definir configurações básicas](#1-configure-basic-settings)      |
| **Tamanho**                | [Escolha o tamanho da máquina virtual](#2-choose-virtual-machine-size)   |
| **Configurações**            | [Configurar recursos opcionais](#3-configure-optional-features)   |
| **Configurações do SQL Server** | [Configurar definições de servidor SQL](#4-configure-sql-server-settings) |
| **Resumo**             | [Revise o resumo](#5-review-the-summary)            |

## <a name="1-configure-basic-settings"></a>1. definir configurações básicas
Na lâmina **Noções básicas** , forneça as seguintes informações:

* Insira uma máquina virtual exclusiva **nome**.
* Especifique um **nome de usuário** da conta administrador local na VM. Essa conta também é adicionada à função de servidor fixa de **administrador do sistema** do SQL Server.
* Fornece uma **senha**de forte.
* Se você tiver várias assinaturas, verifique se a assinatura está correta para a máquina virtual novo.
* Na caixa de **grupo de recursos** , digite um nome para um novo grupo de recursos. Como alternativa, para usar um recurso existente grupo clique em **Selecionar existente**. Um grupo de recursos é um conjunto de recursos relacionados no Azure (máquinas virtuais contas de armazenamento, redes virtuais, etc.).

    >[AZURE.NOTE] Usar um novo grupo de recursos é útil se você estiver apenas testando ou aprendendo sobre implantações de SQL Server no Azure. Após concluir o teste, exclua o grupo de recursos para excluir automaticamente a máquina virtual e todos os recursos associados a esse grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

* Selecione um **local** para essa implantação.
* Clique em **Okey** para salvar as configurações.

    ![Blade de Noções básicas SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Escolha o tamanho da máquina virtual
Na etapa **tamanho** , escolha um tamanho de máquina virtual na lâmina **Escolher um tamanho** . A lâmina exibe inicialmente tamanhos de máquina recomendado baseados no modelo selecionado. Ele também estima o custo mensal para executar a máquina virtual.

![Opções de tamanho de máquina virtual do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Para cargas de trabalho de produção, recomendamos selecionando um tamanho de máquina virtual que suporta o [Armazenamento de Premium](../storage/storage-premium-storage.md). Se você não precisar esse nível de desempenho, use o botão **Exibir tudo** , que mostra todas as opções de tamanho de máquina. Por exemplo, você pode usar um tamanho menor de máquina para um ambiente de teste ou desenvolvimento.

>[AZURE.NOTE] Para obter mais informações sobre máquina virtual tamanhos, consulte [tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md). Para considerações sobre tamanhos de máquina virtual do SQL Server, consulte [práticas recomendadas de desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Escolha o tamanho da sua máquina e, em seguida, clique em **Selecionar**.

## <a name="3-configure-optional-features"></a>3. configurar recursos opcionais
Na lâmina **configurações** , configure Azure armazenamento, rede e monitoramento da máquina virtual.

- Em **armazenamento**, especifique um **disco digite** de padrão ou Premium (SSD). Armazenamento de Premium é recomendado para cargas de trabalho de produção.

>[AZURE.NOTE] Se você selecionar Premium (SSD) para um tamanho de máquina que não oferece suporte para armazenamento de Premium, o tamanho da sua máquina altera automaticamente.  

- Em **conta de armazenamento**, você pode aceitar o nome da conta de armazenamento automaticamente provisionado. Você também pode clicar em **conta de armazenamento** para escolher uma conta existente e configurar o tipo de conta de armazenamento. Por padrão, o Azure cria uma nova conta de armazenamento com armazenamento redundante localmente. Para obter mais informações sobre as opções de armazenamento, consulte [replicação de armazenamento do Azure](../storage/storage-redundancy.md).

- Em **rede**, você pode aceitar os valores preenchidos automaticamente. Você também pode clicar em cada recurso para configurar manualmente a **rede Virtual**, **sub-rede**, **endereço IP público**e **Grupo de segurança de rede**. Para os fins deste tutorial, manter os valores padrão.

- Azure permite **monitoramento** por padrão com a mesma conta de armazenamento designada para a máquina virtual. Você pode alterar essas configurações aqui.

- Em **disponibilidade definida**, especifique um conjunto de disponibilidade. Para os fins deste tutorial, você pode selecionar **Nenhum**. Se você planeja configurar grupos de disponibilidade sempre ativado SQL, configure a disponibilidade para evitar a recriação da máquina virtual.  Para obter mais informações, consulte [Gerenciar a disponibilidade das máquinas virtuais](virtual-machines-windows-manage-availability.md).

Quando você terminar dessas configurações, clique em **Okey**.

## <a name="4-configure-sql-server-settings"></a>4. Configurar configurações do SQL server
Na lâmina **configurações do SQL Server** , definir configurações específicas e otimizações para SQL Server. As configurações que você pode configurar para o SQL Server incluem o seguinte.

| Configuração               |
|---------------------|
| [Conectividade](#connectivity)              |
| [Autenticação](#authentication)                |
| [Configuração de armazenamento](#storage-configuration)            |
| [Automatizada patches](#automated-patching) |
| [Backup automatizado](#automated-backup)             |
| [Integração do Azure cofre chave](#azure-key-vault-integration)             |
| [Serviços de R](#r-services) |

### <a name="connectivity"></a>Conectividade
Em **conectividade SQL**, especifique o tipo de acesso que você deseja a instância do SQL Server na máquina este virtual. Para os fins deste tutorial, selecione **pública (internet)** para permitir conexões com o SQL Server de máquinas ou serviços na internet. Com esta opção selecionada, o Azure configura automaticamente o firewall e o grupo de segurança de rede para permitir o tráfego na porta 1433.  

![Opções de conectividade do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Para se conectar ao SQL Server através da internet, você também deve habilitar autenticação do SQL Server, que é descrito na próxima seção.

>[AZURE.NOTE] É possível adicionar mais restrições para as comunicações de rede à sua máquina de virtual do SQL Server. Você pode fazer isso editando o grupo de segurança de rede após a máquina virtual é criada. Para obter mais informações, consulte [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)

Se você preferir não habilitar conexões com o mecanismo de banco de dados pela internet, escolha uma das seguintes opções:

- **Local (dentro do máquina virtual somente)** para permitir conexões com o SQL Server somente a partir dentro a máquina virtual.
- **Particular (dentro de rede Virtual)** para permitir conexões com o SQL Server com máquinas ou serviços na mesma rede virtual.

>[AZURE.NOTE] A imagem de máquina virtual do SQL Server Express edition não habilitar automaticamente o protocolo de TCP/IP. Isso é verdadeiro mesmo para as opções de conectividade pública e privada. Para edição Express, você deve usar o Gerenciador de configuração do SQL Server para [ativar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a máquina virtual.

Em geral, melhorar a segurança, escolhendo a conectividade mais restritiva que permite seu cenário. Mas todas as opções são protegíveis por meio de regras de grupo de segurança de rede e autenticação do Windows do SQL.

**Porta** padrão é 1433. Você pode especificar um número de porta diferente.
Para obter mais informações, consulte [conectar a um SQL Máquina Virtual do servidor (Resource Manager) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Autenticação
Se você solicitar a autenticação do SQL Server, clique em **Ativar** em **autenticação de SQL**.

![Autenticação do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] Se você planeja acessar o SQL Server pela internet (ou seja, a opção de conectividade a redes públicas), você deve habilitar a autenticação do SQL aqui. Acesso de público para o SQL Server requer o uso da autenticação de SQL.

Se você habilitar a autenticação do SQL Server, especifique um **nome de Login** e a **senha**. Esse nome de usuário é configurado como um logon de autenticação do SQL Server e o membro do **administrador do sistema** função de servidor fixa. Consulte [Escolher um modo de autenticação](http://msdn.microsoft.com/library/ms144284.aspx) para obter mais informações sobre modos de autenticação.

Se você não habilitar a autenticação do SQL Server, você pode usar a conta administrador local na máquina virtual para se conectar à instância do SQL Server.

### <a name="storage-configuration"></a>Configuração de armazenamento
Clique em **configuração de armazenamento** para especificar os requisitos de armazenamento.

![Configuração de armazenamento do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Se você selecionar armazenamento padrão, essa opção não está disponível. Otimização de armazenamento automático está disponível somente para armazenamento de Premium.

Você pode especificar requisitos como operações de entrada/saída por segundo (IOPs), taxa de transferência em MB/s e o tamanho total de armazenamento. Configure esses valores usando as escalas de variável. O portal calcula automaticamente o número de discos com base nesses requisitos.

Por padrão, o Azure otimiza o armazenamento para 5000 IOPs, 200 MB e 1 TB de espaço de armazenamento. Você pode alterar essas configurações de armazenamento com base na carga de trabalho. Em **armazenamento otimizado para**, selecione uma das seguintes opções:

- **Geral** é a configuração padrão e suporta a maioria das cargas de trabalho.
- Processamento **transacional** otimiza o armazenamento para cargas de trabalho OLTP de banco de dados tradicionais.
- **Data warehouse** otimiza o armazenamento para cargas de trabalho analíticos e relatórios.

>[AZURE.NOTE] Os limites superiores nos controles deslizantes variam dependendo do tamanho da sua máquina virtual selecionada.

### <a name="automated-patching"></a>Automatizada patches
**Automatizada patch** é ativada por padrão. Patches automatizada permite Azure para corrigir automaticamente o SQL Server e o sistema operacional. Especifique um dia da semana, hora e duração de uma janela de manutenção. Azure executa patches nesta janela de manutenção. O agendamento da janela de manutenção usa a localidade de máquina virtual para hora. Se você não quiser Azure para corrigir automaticamente o SQL Server e o sistema operacional, clique em **Desativar**.  

![SQL automatizado de patches](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Para obter mais informações, consulte [Automatizada patches para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Backup automatizado
Habilite backups de banco de dados automática para todos os bancos de dados em **backup automático**. Backup automático é desabilitado por padrão.

Quando você habilita backup automatizado de SQL, você pode configurar o seguinte:

- Período de retenção (dias) para backups
- Conta de armazenamento a ser usado para backups
- Opção de criptografia e senha para backups

Para criptografar o backup, clique em **Ativar**. Em seguida, especifique a **senha**. Azure cria um certificado para criptografar os backups e usa a senha especificada para proteger esse certificado.

![Backup automatizado de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Para obter mais informações, consulte [Backup automatizado para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integração do Azure Cofre de chave
Para armazenar segredos de segurança no Azure para criptografia, clique em **cofre chave Azure integration** e clique em **Ativar**.

![Integração do SQL Azure cofre chave](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

A tabela a seguir lista os parâmetros necessários para configurar a integração do Azure chave cofre.

|PARÂMETRO|DESCRIÇÃO|EXEMPLO|
|----------|----------|-------|
|**URL do cofre chave** |A localização da chave cofre.|https://contosokeyvault.Vault.Azure.NET/ |
|**Nome principal** |Azure Active Directory nome principal de serviço. Esse nome também é conhecido como a ID do cliente.  |5-4e11-af04eb07b669ccf2 fde2b411 - 33d|
| **Secreta principal**|Azure Active Directory serviço principal segredo. Esse segredo também é conhecido como o segredo de cliente. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =|
|**Nome de credencial**|**Nome de credencial**: integração de AKV cria uma credencial dentro do SQL Server, permitindo que a máquina virtual tenha acesso a chave cofre. Escolha um nome para esta credencial.| mycred1|

Para obter mais informações, consulte [Configurar integração de cofre do Azure chave para o SQL Server em VMs do Azure](virtual-machines-windows-ps-sql-keyvault.md).

Quando tiver terminado de configurações do SQL Server, clique em **Okey**.

### <a name="r-services"></a>Serviços de R
Para SQL Server 2016 Enterprise edition, você tem a opção para habilitar o [SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx). Isso permite que você use análises avançadas com o SQL Server 2016. Clique em **Ativar** na lâmina **Configurações do SQL Server** .

![Ativar os serviços do SQL Server R](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] Imagens do SQL Server que não são 2016 Enterprise edition, a opção para ativar os serviços de R é desabilitada.

## <a name="5-review-the-summary"></a>5. Analise o resumo
Na lâmina **Resumo** , revise o resumo e clique em **Okey** para criar o SQL Server, grupo de recursos e recursos especificados para esta máquina virtual.

Você pode monitorar a implantação do portal do azure. O botão de **notificações** na parte superior da tela mostra status básico da implantação.

>[AZURE.NOTE] Para fornecer uma ideia sobre os tempos de implantação, eu implantado uma VM SQL para a região Leste EUA com as configurações padrão. Essa implantação de teste levou um total de 26 minutos para ser concluída. Mas você pode enfrentar um mais rápido ou mais lento tempo de implantação com base na sua região e as configurações selecionadas.

## <a name="open-the-vm-with-remote-desktop"></a>Abra a máquina virtual com área de trabalho remota

Use as etapas a seguir para se conectar à máquina virtual com área de trabalho remota:

1. Depois que a máquina virtual do Azure é criada, o ícone para a máquina virtual aparece no painel Azure. Você também pode encontrar-navegando suas máquinas virtuais existentes. Clique em sua nova máquina virtual SQL. Uma lâmina de **Máquina Virtual** exibe os detalhes da máquina virtual.
1. Na parte superior da lâmina **Máquina Virtual** , clique em **Conectar**.
1. O navegador baixa um arquivo RDP para a máquina virtual. Abra o arquivo RDP.
    ![Área de trabalho remota para máquina virtual do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. A Conexão de área de trabalho remota notifica que o Editor desta conexão remota não pode ser identificado. Clique em **Conectar** para continuar.
1. Na caixa de diálogo **Segurança do Windows** , clique em **usar outra conta**.
1. Tipo de **nome de usuário** de ** \<nome de usuário >**, onde <user name> é o nome de usuário que você especificou quando você configurou a máquina virtual. Você precisa adicionar uma barra invertida inicial antes do nome.
1. Digite a **senha** que você configurou anteriormente para esta máquina virtual e, em seguida, clique em **Okey** para se conectar.
1. Se outra caixa de diálogo **Conexão de área de trabalho remota** perguntando se você deseja se conectar, clique em **Sim**.

Depois de se conectar à máquina virtual do SQL Server, você pode iniciar o SQL Server Management Studio e conectar-se com a autenticação do Windows usando suas credenciais de administrador local. Se você habilitou a autenticação do SQL Server, você também pode se conectar com a autenticação de SQL usando o logon do SQL e a senha que você configurou durante a configuração.

Acesso à máquina permite que você altere diretamente máquina e configurações do SQL Server com base nas suas necessidades. Por exemplo, você pode definir as configurações de firewall ou alterar definições de configuração do SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Conectar ao SQL Server remotamente

Neste tutorial, selecionamos acesso **público** para a máquina virtual e a **Autenticação do SQL Server**. Essas configurações configurado automaticamente a máquina virtual para permitir conexões do SQL Server de qualquer cliente através da internet (presumindo que tiverem login SQL correto).

>[AZURE.NOTE] Se você não selecionou público durante a configuração, outras etapas são necessárias para acessar a instância do SQL Server pela internet. Para obter mais informações, consulte [conectar a uma máquina Virtual SQL Server](virtual-machines-windows-sql-connect.md).

As seções a seguir mostram como conectar-se à sua instância do SQL Server em sua máquina virtual do outro computador pela internet.

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Próximas etapas
Para obter outras informações sobre como usar o SQL Server no Azure, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e as [Perguntas frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Para obter uma visão geral em vídeo do SQL Server em máquinas virtuais do Azure, assista a [máquina virtual do Azure é a melhor plataforma para o SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Explorar o caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em Azure máquinas virtuais.
