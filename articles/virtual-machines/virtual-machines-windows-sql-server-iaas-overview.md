<properties
    pageTitle="Visão geral do SQL Server em Azure máquinas virtuais | Microsoft Azure"
    description="Saiba mais sobre como executar completas edições do SQL Server nas máquinas virtuais do Azure. Obtenha links diretos para todas as imagens de máquina virtual do SQL Server e conteúdo relacionado."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/19/2016"
    ms.author="jroth"/>

# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Visão geral do SQL Server em Azure máquinas virtuais

Este tópico descreve as opções para execução do SQL Server em Azure VMs (máquinas virtuais), juntamente com [links para imagens portal](#option-1-create-a-sql-vm-with-per-minute-licensing) e obter uma visão geral das [tarefas comuns](#manage-your-sql-vm).

>[AZURE.NOTE] Se você já está familiarizado com o SQL Server e só quer ver como implantar uma máquina de virtual do SQL Server, consulte [provisionar uma máquina virtual de SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="overview"></a>Visão geral
Se você for um administrador de banco de dados ou um desenvolvedor, VMs Azure oferece uma maneira de mover suas cargas de trabalho do local do SQL Server e aplicativos para a nuvem. O vídeo a seguir fornece uma visão geral técnica do SQL Server Azure VMs.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

O vídeo abrange as seguintes áreas:

|Tempo|Área|
|---|---|
| 00:21 | O que são VMs Azure? |
| 01:45 | Segurança |
| 02:50 | Conectividade |
| 03:30 | Desempenho e confiabilidade de armazenamento |
| 05:20 | Tamanhos de máquina virtual |
| 05:54 | Alta disponibilidade e SLA |
| 07:30 | Suporte de configuração |
| 08:00 | Monitoramento |
| 08:32 | Demonstração: Criar uma máquina virtual 2016 do SQL Server |

>[AZURE.NOTE] O vídeo se concentra em SQL Server 2016, mas Azure fornece imagens de máquina virtual para várias versões do SQL Server, incluindo 2008, 2012, 2014 e 2016. 

## <a name="scenarios"></a>Cenários
Há vários motivos para isso que você pode escolher para hospedar seus dados no Azure. Se seu aplicativo foi transferido para o Azure melhora o desempenho para também mover os dados. Mas há outras vantagens. Automaticamente, você tem acesso a vários centros de dados para uma recuperação de desastres e presença global. Os dados também são altamente segura e durável.

SQL Server em execução no Azure VMs é uma opção para armazenar seus dados relacionais no Azure. Ele é boa opção para vários cenários. Por exemplo, você talvez queira configurar a máquina virtual do Azure como da mesma maneira possível para uma máquina do SQL Server local. Ou talvez você queira executar serviços e aplicativos adicionais no mesmo servidor de banco de dados. Há dois recursos principais que podem ajudá-lo a pensar ainda mais cenários e considerações:

 - [SQL Server em Azure máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/sql-server/) fornece uma visão geral dos melhores cenários para uso do SQL Server em VMs do Azure. 
 - [Escolha uma opção do SQL Server de nuvem: banco de dados do SQL Azure (PaaS) ou SQL Server no Azure VMs (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) fornece uma comparação detalhada entre o banco de dados SQL e SQL Server em execução em uma máquina virtual.

## <a name="create-a-new-sql-vm"></a>Criar uma nova VM SQL
As seguintes seções fornecem links diretos para o portal do Azure para as imagens de galeria de máquina virtual do SQL Server. Dependendo a imagem selecionada, você pode o pagamento de custos em uma base por minuto de licenciamento do SQL Server, ou você pode trazer seu próprios licença (BYOL).

Encontre orientações passo a passo para esse processo no tutorial, [provisionar uma máquina virtual de SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Além disso, revise as [práticas recomendadas de desempenho para SQL Server VMs](virtual-machines-windows-sql-performance.md), que explica como selecionar o tamanho de máquina apropriado e outros recursos disponíveis durante a configuração.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opção 1: Criar uma VM SQL com licenciamento por minuto
A tabela a seguir fornece uma matriz de imagens do SQL Server disponíveis na Galeria de máquina virtual. Clique em qualquer link para começar a criar uma nova VM SQL com sua versão especificada, edição e sistema operacional.

|Versão|Sistema Operacional|Edition|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [desenvolvimento](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL DE 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Opção 2: Criar uma VM SQL com uma licença existente
Você também pode trazer seu próprios licença (BYOL). Neste cenário, você paga apenas para a máquina virtual sem encargos adicionais para licenciamento do SQL Server. Para usar sua própria licença, use a matriz de versões do SQL Server, edições e sistemas operacionais abaixo. No portal do, esses nomes de imagem são prefixo **{BYOL}**.

|Versão|Sistema Operacional|Edition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [BYOL padrão](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [BYOL padrão](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL padrão](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Para usar imagens BYOL VM, você deve ter e Enterprise Agreement com [Mobilidade de licença por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Você também precisa uma licença válida da versão/edição do SQL Server que você deseja usar. Você deve [fornecer as informações necessárias do BYOL à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) dentro de sua máquina virtual de provisionamento do **10** dias.

## <a name="manage-your-sql-vm"></a>Gerenciar sua máquina virtual SQL
Após a configuração do seu servidor de SQL máquina virtual, há várias tarefas de gerenciamento opcional. Em vários aspectos, você configurar e gerenciar o SQL Server exatamente como você faria gerenciar uma instância do SQL Server local. No entanto, algumas tarefas são específicas do Azure. As seções a seguir destacam algumas dessas áreas com links para obter mais informações.

### <a name="connect-to-the-vm"></a>Conectar-se para a máquina virtual
Uma das etapas de gerenciamento mais básicas é para se conectar ao seu servidor de SQL máquina virtual por meio de ferramentas, como o SQL Server Management Studio (SSMS). Para obter instruções sobre como conectar à sua nova VM servidor SQL, consulte [conectar a um SQL Server computador Virtual no Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrar seus dados
Se você tiver um banco de dados existente, será deseja mover que para o VM SQL recentemente provisionado. Para obter uma lista de opções de migração e orientações, consulte [migrar um banco de dados SQL Server em uma máquina virtual do Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurar alta disponibilidade
Se você precisar alta disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve várias VMs Azure em uma rede virtual. O portal do Azure tem um modelo que define esta configuração para você. Para obter mais informações, consulte [Configurar um grupo de disponibilidade sempre ativado nas máquinas virtuais de Gerenciador de recursos do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Se você quiser configurar manualmente o seu grupo de disponibilidade e ouvinte associado, consulte [Configurar grupos de disponibilidade sempre ativado na máquina virtual do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para outras considerações de alta disponibilidade, consulte [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Fazer backup de seus dados
Azure VMs podem aproveitar [Backup automatizado](virtual-machines-windows-sql-automated-backup.md), que cria regularmente backups do seu banco de dados para o armazenamento de blob. Você também manualmente pode usar essa técnica. Para obter mais informações, consulte [Usar o armazenamento do Azure para SQL Server Backup e restauração](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obter uma visão geral de todas as opções de backup e restauração, consulte [Backup e restauração para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatizar atualizações
Azure VMs podem usar a [Aplicação automatizada de patches](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para instalar o windows importantes e SQL Server atualiza automaticamente.

### <a name="customer-experience-improvement-program-ceip"></a>Programa aperfeiçoamento da experiência (do usuário CEIP)
O programa de Aperfeiçoamento da experiência de cliente (CEIP) é ativada por padrão. Isso envia periodicamente relatórios à Microsoft para ajudar a aprimorar o SQL Server. Não há nenhuma tarefa de gerenciamento necessária com CEIP, a menos que você queira desabilitá-la após a configuração. Você pode personalizar ou desabilitar o CEIP conectando-se para a máquina virtual com área de trabalho remota. Em seguida, execute o utilitário de **erro do SQL Server e o relatório de uso** . Siga as instruções para desabilitar o relatório. 

Para obter mais informações, consulte a seção CEIP do tópico [Aceitar termos de licença](https://msdn.microsoft.com/library/ms143343.aspx) . 

## <a name="next-steps"></a>Próximas etapas
[Explorar o caminho de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em Azure máquinas virtuais.

Pergunta mais? Primeiro, veja o [SQL Server em perguntas Frequentes de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md). Mas também adicionar suas perguntas ou comentários à parte inferior de qualquer tópico SQL VM para interagir com o Microsoft e da comunidade.
