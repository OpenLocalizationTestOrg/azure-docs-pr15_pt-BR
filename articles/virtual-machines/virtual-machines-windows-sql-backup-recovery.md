<properties
    pageTitle="Backup e restauração para o SQL Server | Microsoft Azure"
    description="Descreve as considerações de backup e restauração para bancos de dados do SQL Server em máquinas virtuais do Azure em execução."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Backup e restauração para SQL Server no Azure máquinas virtuais

## <a name="overview"></a>Visão geral

Fazer backup de dados em bancos de dados do SQL Server é uma parte importante da estratégia de proteção contra perda de dados devido a erros de aplicativo ou usuário. Isso é igualmente verdadeiro para o SQL Server em máquinas virtuais do Azure (VMs) em execução.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Para o SQL Server em execução no Azure VMs, você pode usar nativo backup e restaurar técnicas usando discos anexados para o destino dos arquivos de backup. No entanto, há um limite quanto ao número de discos que você pode anexar a uma máquina virtual Azure, com base no [tamanho da máquina virtual](virtual-machines-linux-sizes.md). Há também a sobrecarga de gerenciamento de disco a considerar.

Começando com o SQL Server 2014, você pode fazer backup e restaurar ao armazenamento de Blob do Microsoft Azure. SQL Server 2016 também fornece aprimoramentos para essa opção. Além disso, para arquivos de banco de dados armazenados em armazenamento de Blob do Microsoft Azure, SQL Server 2016 fornece uma opção para backups quase instantâneos e restaura rápida usando instantâneos Azure. Este artigo fornece uma visão geral dessas opções e informações adicionais podem ser encontradas no [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

>[AZURE.NOTE] Para uma discussão das opções para fazer backup de bancos de dados muito grandes, consulte [Estratégias de Backup do banco de dados do servidor em SQL vários terabytes para máquinas virtuais do Azure](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

As seções a seguir incluem informações específicas sobre as diferentes versões do SQL Server compatíveis com uma máquina virtual Azure.

## <a name="sql-server-virtual-machines"></a>Máquinas de virtuais do SQL Server

Quando a instância do SQL Server está em execução em uma máquina Virtual do Azure, seus arquivos de banco de dados será já residem em discos de dados no Azure. Esses discos ao vivo no armazenamento de Blob do Azure. Os motivos para fazer backup de seu banco de dados e as abordagens que você reserve alteração ligeiramente. Considere o seguinte. 

- Você não precisa executar backups de banco de dados para fornecer proteção contra falha de hardware ou mídia porque Microsoft Azure fornece essa proteção como parte do serviço do Microsoft Azure.

- Ainda é necessário realizar backups de banco de dados para fornecer proteção contra erros de usuário ou, para fins de arquivamento, motivos regulamentares ou fins administrativos.

- Você pode armazenar o arquivo de backup diretamente no Azure. Para obter mais informações, consulte as seções a seguintes oferecem orientação para as diferentes versões do SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016

Microsoft SQL Server 2016 dá suporte a recursos de [backup e restauração com blobs Azure](https://msdn.microsoft.com/library/jj919148.aspx) encontrados no SQL Server 2014. Mas também inclui as seguintes melhorias:

| Aprimoramento de 2016               | Detalhes                          |
|---------------------|-------------------------------|
| **Distribuição**              | Ao fazer backup ao armazenamento de blob do Microsoft Azure, SQL Server 2016 oferece suporte ao backup blobs vários para habilitar o backup dos bancos de dados grandes, até no máximo 12,8 TB.      |
| **Backup de instantâneo**                | Por meio do uso instantâneos Azure, o Backup de instantâneo de arquivo do SQL Server fornece backups quase instantâneos e restaura rápida para arquivos de banco de dados armazenados usando o serviço de armazenamento de Blob do Azure. Esse recurso permite simplificar o backup e restaurar diretivas. Instantâneo de arquivos de backup também suporta ponto no tempo restauração. Para obter mais informações, consulte [Backups instantâneo para arquivos de banco de dados no Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).   |
| **Gerenciado a programação de Backup**            | Backup de gerenciadas do SQL Server para o Azure agora suporta agendas personalizadas. Para obter mais informações, consulte [Backup de gerenciadas do SQL Server para Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).   |

Para um tutorial dos recursos do SQL Server 2016 ao usar o armazenamento de Blob do Azure, consulte [Tutorial: usando o serviço de armazenamento de Blob do Microsoft Azure com bancos de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014

SQL Server 2014 inclui as seguintes melhorias:

1. **Backup e restauração para Azure**:

 - Agora o *Backup do SQL Server para URL* tem suporte no SQL Server Management Studio. A opção para fazer backup em Azure agora está disponível quando as tarefas de Backup ou restauração ou o Assistente de plano de manutenção no SQL Server Management Studio. Para obter mais informações, consulte [Backup do SQL Server para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *SQL Server gerenciado Backup para Azure* tem novas funcionalidades que permitem o gerenciamento de backup automatizado. Isto é especialmente útil para automatizar o gerenciamento de backup para instâncias de 2014 do SQL Server em execução em um computador do Azure. Para obter mais informações, consulte [Backup de gerenciadas do SQL Server para Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Backup automatizado* fornece automação adicional para ativar automaticamente o *Backup de gerenciadas do SQL Server para o Azure* em todos os bancos de dados novos e existentes para um servidor de SQL máquina virtual no Azure. Para obter mais informações, consulte [Backup automatizado para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).
 - Para obter uma visão geral de todas as opções para Backup de 2014 do SQL Server para o Azure, consulte [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Criptografia**: SQL Server 2014 oferece suporte a criptografia de dados quando cria um backup. Ele dá suporte a vários algoritmos de criptografia e o uso osf um certificado ou chave assimétrico. Para obter mais informações, consulte [Criptografia de Backup](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012

Para obter informações detalhadas sobre o SQL Server Backup e restauração no SQL Server 2012, consulte [Backup e restauração do SQL Server bancos de dados (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

Iniciando no SQL Server 2012 SP1 cumulativa atualização 2, você pode fazer o backup e restauração do serviço de armazenamento de Blob do Azure. Esse aprimoramento pode ser usado para fazer backup de bancos de dados do SQL Server em um SQL Server em execução em uma máquina Virtual de Azure ou uma instância de local. Para obter mais informações, consulte [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Alguns dos benefícios de usar o serviço de armazenamento de Blob do Azure incluem a capacidade para ignorar o limite de 16 de disco para discos anexados, facilidade de gerenciamento, a disponibilidade direta do arquivo de backup a outra instância da instância do SQL Server em execução em uma máquina virtual Azure ou uma instância de locais para fins de recuperação de desastres ou migração. Para obter uma lista completa dos benefícios usando um serviço de armazenamento de blob do Microsoft Azure backups do SQL Server, consulte a seção de *benefícios* em [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Recomendações de prática recomendada e informações de solução de problemas, consulte [Fazer Backup e restaurar as práticas recomendadas (serviço de armazenamento de Blob do Azure)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008

Para SQL Server Backup e restauração no SQL Server 2008 R2, consulte [Fazendo backup e restauração de bancos de dados no SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Para SQL Server Backup e restauração no SQL Server 2008, consulte [Fazendo backup e restauração de bancos de dados no SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Próximas etapas

Se você estiver planejando sua implantação do SQL Server em uma máquina virtual do Azure, você pode encontrar orientações provisionamento o seguinte tutorial: [provisionamento uma máquina de Virtual do SQL Server no Azure com o Gerenciador de recursos do Azure](virtual-machines-windows-portal-sql-server-provision.md).

Embora backup e restauração podem ser usados para migrar seus dados, há potencialmente mais fácil caminhos de migração de dados para SQL Server em uma máquina virtual do Azure. Para uma discussão completa das opções de migração e recomendações, consulte [migrar um banco de dados SQL Server em uma máquina virtual do Azure](virtual-machines-windows-migrate-sql.md).

Analise os outros [recursos para executando o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
