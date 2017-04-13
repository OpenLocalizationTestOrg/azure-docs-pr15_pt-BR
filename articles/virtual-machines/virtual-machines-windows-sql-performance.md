<properties
    pageTitle="Práticas recomendadas de desempenho para o SQL Server | Microsoft Azure"
    description="Fornece as práticas recomendadas para otimizar o desempenho do SQL Server no Microsoft Azure VMs."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/07/2016"
    ms.author="jroth" />

# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Práticas recomendadas de desempenho para o SQL Server em máquinas virtuais do Azure

## <a name="overview"></a>Visão geral

Este tópico fornece as práticas recomendadas para otimizar o desempenho do SQL Server na máquina Virtual da Microsoft Azure. Enquanto executa o SQL Server em máquinas virtuais do Azure, recomendamos que você continue usando o mesmas opções que são aplicáveis ao SQL Server no ambiente de servidor local de ajuste de desempenho de banco de dados. No entanto, o desempenho do banco de dados relacional em uma nuvem pública depende de vários fatores como o tamanho de uma máquina virtual e a configuração dos discos dados.

Ao criar imagens do SQL Server, [considere a possibilidade de provisionamento suas VMs no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). SQL Server VMs provisionados no Portal com o Gerenciador de recursos implementar todas estas práticas recomendadas, incluindo a configuração de armazenamento.

Este artigo destina-se em obter o *melhor* desempenho para o SQL Server em VMs do Azure. Se sua carga de trabalho for requer menos, você talvez não exijam cada otimização listada abaixo. Considere suas necessidades de desempenho e padrões de carga de trabalho como você avaliar essas recomendações.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Lista de verificação rápida

A seguir está uma lista de verificação rápida para um desempenho ideal do SQL Server em máquinas virtuais do Azure:

|Área|Otimizações|
|---|---|
|[Tamanho de máquina virtual](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) ou superior para SQL Enterprise edition.<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) ou superior para edições do SQL Standard e da Web.|
|[Armazenamento](#storage-guidance)|Use o [armazenamento de Premium](../storage/storage-premium-storage.md). Armazenamento padrão só é recomendado para desenvolvimento/teste.<br/><br/>Manter a [conta de armazenamento](../storage/storage-create-storage-account.md) e máquina virtual do SQL Server na mesma região.<br/><br/>Desabilite o Azure [armazenamento geográfica redundante](../storage/storage-redundancy.md) (replicação geográfica) na conta de armazenamento.|
|[Discos](#disks-guidance)|Use um mínimo de 2 [discos P30](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) (1 para arquivos de log; 1 para arquivos de dados e TempDB).<br/><br/>Evite usar o sistema operacional ou discos temporários para armazenamento de banco de dados ou log.<br/><br/>Habilitar o cache na s os arquivos de dados e TempDB de hospedagem de leitura.<br/><br/>Não habilite o cache em s hospeda o arquivo de log.<br/><br/>Importante: Interrompa o serviço SQL Server ao alterar as configurações de cache para um disco de máquina virtual do Azure.<br/><br/>Distribuição vários discos de dados do Azure para obter maior produtividade IO.<br/><br/>Formatar com tamanhos de alocação documentadas.|
|[E/S](#io-guidance)|Ativar a compactação de página do banco de dados.<br/><br/>Habilite a inicialização instantânea de arquivo para arquivos de dados.<br/><br/>Limite ou desabilitar o crescimento automático no banco de dados.<br/><br/>Desabilite autoshrink no banco de dados.<br/><br/>Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema.<br/><br/>Mova diretórios de arquivo do SQL Server erro log e rastreamento discos de dados.<br/><br/>Configure locais de arquivos de backup e banco de dados padrão.<br/><br/>Habilite páginas bloqueadas.<br/><br/>Aplica correções de desempenho do SQL Server.|
|[Recurso específico](#feature-specific-guidance)|Faça backup diretamente ao armazenamento de blob.|

Para obter mais informações sobre *como* e *por que* fazer essas otimizações, examine os detalhes e a orientação fornecida nas seções seguintes.

## <a name="vm-size-guidance"></a>Orientação de tamanho de máquina virtual

Para aplicativos confidenciais de desempenho, é recomendável que você use os seguintes [tamanhos de máquinas virtuais](virtual-machines-windows-sizes.md):

- **SQL Server Enterprise Edition**: DS3 ou superior

- **SQL Server Standard e edições de Web**: DS2 ou superior


## <a name="storage-guidance"></a>Orientação de armazenamento

Suporte a VMs série DS (juntamente com DSv2 séries e GS-) [Armazenamento Premium](../storage/storage-premium-storage.md). Armazenamento de Premium é recomendado para todas as cargas de trabalho de produção.

> [AZURE.WARNING] Armazenamento padrão tem largura de banda e latências variáveis e só é recomendado para cargas de trabalho de desenvolvimento/teste. Cargas de trabalho de produção devem usar o armazenamento de Premium.

Além disso, é recomendável que você crie sua conta de armazenamento do Azure no mesmo data center como suas máquinas virtuais de SQL Server para reduzir os atrasos de transferência. Ao criar uma conta de armazenamento, desabilite a replicação de localização geográfica como não há garantia de ordem de gravação consistente em vários discos. Em vez disso, considere configurar uma tecnologia de recuperação de desastres do SQL Server entre dois Azure data centers. Para obter mais informações, consulte [alta disponibilidade e recuperação de dados para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Orientação de discos

Há três tipos de disco principal em uma máquina virtual do Azure:

- **O disco de sistema operacional**: quando você cria uma máquina Virtual de Azure, a plataforma irá anexar pelo menos um disco (rotulado como a unidade **C** ) para a máquina virtual para o disco de sistema operacional. Esse disco é um VHD armazenado como um blob de página em armazenamento.
- **Disco temporário**: máquinas virtuais do Azure contêm outro disco chamado disco temporário (rotulada como **D**: unidade). Este é um disco no nó que pode ser usado para espaço de rascunho.
- **Discos de dados**: você também pode anexar discos adicionais à sua máquina virtual como discos de dados e eles serão armazenados no armazenamento como blobs de página.

As seções a seguir descrevem recomendações para usar estes discos diferentes.

### <a name="operating-system-disk"></a>Disco do sistema operacional

Um disco de sistema operacional é um VHD que você pode inicializar e montar como uma versão de um sistema operacional em execução e está rotulado como unidade **C** .

Cache de política do disco do sistema operacional padrão é **Leitura/gravação**. Para aplicativos confidenciais de desempenho, recomendamos que você use discos de dados em vez do disco do sistema operacional. Consulte a seção sobre discos de dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, rotulada como **D**: unidade, não é mantido para armazenamento de blob do Microsoft Azure. Não armazene seus arquivos de banco de dados do usuário ou arquivos de log de transação do usuário em **D**: unidade.

Para série D, série Dv2 e VMs série G, a unidade temporária nessas VMs é baseado em SSD. Se sua carga de trabalho faz uso intenso de TempDB (por exemplo, para objetos temporários ou relações complexas), armazenar TempDB na unidade **D** pode resultar em maior produtividade TempDB e menor latência de TempDB.

Para VMs que oferecem suporte ao armazenamento de Premium (série DS, DSv2-séries e GS), recomendamos armazenar TempDB e/ou extensões do Pool de Buffer em um disco que suporta o armazenamento de Premium com o cache de leitura ativado. Há uma exceção a essa recomendação; Se o uso de TempDB for que requer muita gravação, você pode obter melhor desempenho armazenando TempDB na unidade local **D** , que também é baseado em SSD nesses tamanhos de máquina.

### <a name="data-disks"></a>Discos de dados

- **Use discos de dados para arquivos de dados e log**: no mínimo, use 2 Premium armazenamento [P30 discos](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage) onde um disco contém os arquivos de log e o outro contém os arquivos de dados e TempDB.

- **Distribuição do disco**: para produtividade mais, você pode adicionar discos de dados adicionais e usar a distribuição do disco. Para determinar o número de discos de dados, você precisa analisar o número de IOPS disponíveis para seu s de dados e de log. Para informações, consulte as tabelas em IOPS por tamanho de [tamanho de máquina virtual](virtual-machines-windows-sizes.md) e disco no seguinte artigo: [Usando o armazenamento de Premium para discos](../storage/storage-premium-storage.md). Use o seguinte estas diretrizes:

    - Para Windows 8/Windows Server 2012 ou posterior, use [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx). Definir o tamanho de distribuição a 64 KB para cargas de trabalho OLTP e 256 KB warehouse cargas de trabalho de dados para evitar o impacto no desempenho devido a alinhamento incorreto de partição. Além disso, defina o número de colunas = número de discos físicos. Para configurar um espaço de armazenamento com mais de 8 discos você deve usar o PowerShell (não Server Manager UI) explicitamente definir o número de colunas para coincidir com o número de discos. Para obter mais informações sobre como configurar [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx), consulte [Cmdlets de espaços de armazenamento no Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)

    - Para o Windows 2008 R2 ou anterior, você pode usar discos dinâmicos (volumes de sistema operacional distribuídos) e o tamanho de faixa é sempre 64 KB. Observe que essa opção está obsoleto no Windows 8/Windows Server 2012. Para obter informações, consulte a política de suporte em [serviço de disco Virtual está em transição para API de gerenciamento de armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

    - Se sua carga de trabalho não está conectado intenso e não precisa IOPs dedicadas, você pode configurar apenas um pool de armazenamento. Caso contrário, crie dois pools de armazenamento, um para os arquivos de log e outro pool de armazenamento para os arquivos de dados e TempDB. Determine o número de discos associados a cada pool de armazenamento com base em suas expectativas de carga. Tenha em mente que tamanhos diferentes de máquina virtual permitem que diferentes números de discos de dados anexada. Para obter mais informações, consulte [tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md).

    - Se você não estiver usando o armazenamento de Premium (cenários de desenvolvimento/teste), a recomendação é adicionar o número máximo de discos de dados compatíveis com o [tamanho da máquina virtual](virtual-machines-windows-sizes.md) e usar a distribuição do disco.

- **Diretiva de cache**: discos de dados para o armazenamento de Premium, ativar o cache os discos de dados que hospeda seus arquivos de dados e TempDB somente leitura. Se você não estiver usando o armazenamento de Premium, não habilite qualquer cache em qualquer disco de dados. Para obter instruções sobre como configurar o cache de disco, consulte os tópicos a seguir: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

    >[AZURE.WARNING] Pare o serviço SQL Server quando a alteração da configuração de cache de discos de máquina virtual do Azure para evitar a possibilidade de qualquer corrupção de banco de dados.

- **Tamanho de unidade de alocação NTFS**: ao formatar o disco de dados, é recomendável que você use um tamanho de unidade de alocação de 64 KB para arquivos de log e de dados, bem como TempDB.

- **Práticas recomendadas de gerenciamento de disco**: quando removendo um disco de dados ou alterar seu tipo de cache, interromper o serviço SQL Server durante a alteração. Quando as configurações de cache são alteradas no disco SO, Azure interrompe a máquina virtual, altera o tipo de cache e reinicia a máquina virtual. Quando as configurações de cache de um disco de dados forem alteradas, a máquina virtual não for interrompida, mas o disco de dados é desanexado da máquina virtual durante a alteração e, em seguida, reconectado.

    >[AZURE.WARNING] Falha ao interromper o serviço SQL Server durante essas operações pode causar corrupção de banco de dados.

## <a name="io-guidance"></a>Orientação de e/s

- Os melhores resultados com armazenamento Premium são obtidos quando você paralelo seu aplicativo e solicitações. Armazenamento de Premium destina-se a cenários em que o comprimento da fila IO é maior que 1, para que você verá pouco ou nenhum ganhos de desempenho para solicitações de seriais único encadeamento (mesmo se eles são armazenamento intenso). Por exemplo, isso poderá afetar os resultados de teste thread único de ferramentas de análise de desempenho, como SQLIO.

- Considere usar [compactação de página do banco de dados](https://msdn.microsoft.com/library/cc280449.aspx) como ele pode ajudar a melhorar o desempenho das cargas de trabalho intenso e/s. No entanto, a compactação de dados pode aumentar o consumo de CPU no servidor de banco de dados.

- Considere a habilitação de inicialização de arquivo instantânea reduzir o tempo necessário para alocação de arquivo inicial. Para tirar proveito da inicialização de arquivo instantânea, você pode concede a conta de serviço do SQL Server (MSSQLSERVER) com SE_MANAGE_VOLUME_NAME e adicioná-lo à política de segurança de **Executar tarefas de manutenção de Volume** . Se você estiver usando uma imagem de plataforma do servidor do SQL Azure, a conta de serviço de padrão (NT Service\MSSQLSERVER) não será adicionada à política de segurança de **Executar tarefas de manutenção de Volume** . Em outras palavras, inicialização de arquivo instantânea não está habilitada em uma imagem de plataforma do SQL Server Azure. Depois de adicionar a conta de serviço do SQL Server à política de segurança de **Executar tarefas de manutenção de Volume** , reinicie o serviço SQL Server. Pode haver considerações de segurança para usar esse recurso. Para obter mais informações, consulte [Inicialização de arquivo de banco de dados](https://msdn.microsoft.com/library/ms175935.aspx).

- **crescimento automático** é considerado simplesmente contingência para crescimento inesperado. Não gerencie o crescimento de dados e log diariamente com crescimento automático. Se for usado crescimento automático, previamente crescer o arquivo usando a opção de tamanho.

- Certifique-se de **autoshrink** está desabilitado para evitar sobrecarga desnecessária que pode afetar negativamente o desempenho.

- Mova todos os bancos de dados para discos de dados, incluindo bancos de dados do sistema. Para obter mais informações, consulte [Mover bancos de dados do sistema](https://msdn.microsoft.com/library/ms345408.aspx).

- Mova diretórios de arquivo do SQL Server erro log e rastreamento discos de dados. Isso pode ser feito no Gerenciador de configuração do SQL Server clicando instância do SQL Server e selecionando Propriedades. As configurações de arquivo de log e rastreamento de erros podem ser alteradas na guia **Parâmetros de inicialização** . O diretório de despejar especificado na guia **Avançado** . A captura de tela a seguir mostra onde procurar o parâmetro de inicialização do log de erro.

    ![Captura de tela de log de erros SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- Configure locais de arquivos de backup e banco de dados padrão. Use as recomendações neste tópico e faça as alterações na janela de propriedades do servidor. Para obter instruções, consulte [Exibir ou alterar os locais padrão para dados e arquivos de Log (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). A captura de tela a seguir demonstra onde deseja fazer essas alterações.

    ![Arquivos de Log de dados do SQL e Backup](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- Habilite páginas bloqueadas reduzir IO e quaisquer atividades de paginação. Para obter mais informações, consulte [Ativar o bloquear páginas na opção de memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Se você estiver executando o SQL Server 2012, instale o Service Pack 1 cumulativa atualização 10. Essa atualização contém a correção para um desempenho ruim e/s quando você executar select na instrução de tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo da Base de dados de Conhecimento](http://support.microsoft.com/kb/2958012).

- Considere compactar quaisquer arquivos de dados ao transferir dentro/fora do Azure.

## <a name="feature-specific-guidance"></a>Orientações específicas do recurso

Algumas implantações podem obter benefícios de desempenho adicional usando técnicas mais avançadas de configuração. A lista a seguir destaca alguns recursos do SQL Server que podem ajudar você a obter melhor desempenho:

- **Backup em armazenamento do Azure**: ao executar backups para o SQL Server em execução no Azure máquinas virtuais, você pode usar o [Backup do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx). Este recurso está disponível começando com o SQL Server 2012 SP1 CU2 e recomendados para backup até os discos de dados anexada. Quando você backup e restauração de/para o armazenamento do Azure, siga as recomendações fornecidas em [Backup do SQL Server para práticas recomendadas de URL e solução de problemas e restaurando Backups armazenados em armazenamento do Azure](https://msdn.microsoft.com/library/jj919149.aspx). Você também pode automatizar esses backups usando [Backup automatizado para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-classic-sql-automated-backup.md).

    Anteriores ao SQL Server 2012, você pode usar o [Backup do SQL Server para ferramenta do Azure](https://www.microsoft.com/download/details.aspx?id=40740). Esta ferramenta pode ajudar a aumentar a produtividade backup usando vários destinos de backup de distribuição.

- **Arquivos de dados do SQL Server no Azure**: este novo recurso, [Arquivos de dados do SQL Server no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível começando com SQL Server 2014. Executando o SQL Server com arquivos de dados no Azure demonstra características de desempenho comparável como usando discos de dados do Azure.

## <a name="next-steps"></a>Próximas etapas

Se você estiver interessado em explorar o SQL Server e armazenamento de Premium em mais detalhes, consulte o artigo [Use Azure Premium armazenamento com o SQL Server em máquinas virtuais](virtual-machines-windows-classic-sql-server-premium-storage.md).

Para práticas recomendadas de segurança, consulte [Considerações de segurança para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-security.md).

Examine outros tópicos de máquina de Virtual do SQL Server no [SQL Server em Visão geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
