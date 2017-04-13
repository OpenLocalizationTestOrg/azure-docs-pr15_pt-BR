<properties
    pageTitle="Configuração de armazenamento para SQL Server VMs | Microsoft Azure"
    description="Este tópico descreve como Azure configura o armazenamento para SQL Server VMs durante a configuração (modelo de implantação do Gerenciador de recursos). Ela também explica como você pode configurar o armazenamento para as VMs de servidor SQL existente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="ninarn"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/04/2016"
    ms.author="ninarn" />

# <a name="storage-configuration-for-sql-server-vms"></a>Configuração de armazenamento para VMs do SQL Server

Quando você configura uma imagem de máquina virtual do SQL Server no Azure, o Portal de ajuda para automatizar a configuração de armazenamento. Isso inclui anexando armazenamento para a máquina virtual, tornando o armazenamento acessíveis para SQL Server e configurá-lo para otimizar para suas necessidades específicas de desempenho.

Este tópico explica como Azure configura o armazenamento para as VMs do SQL Server durante a configuração e para VMs existentes. Esta configuração se baseia as [práticas recomendadas de desempenho](virtual-machines-windows-sql-performance.md) para VMs Azure executando o SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

## <a name="prerequisites"></a>Pré-requisitos
Para usar as configurações de armazenamento automatizado, sua máquina virtual requer as seguintes características:

- Provisionados com uma [imagem da Galeria do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-deploy-a-sql-vm-per-minute-licensing).
- Usa o [modelo de implantação do Gerenciador de recursos](../resource-manager-deployment-model.md).
- Usa o [armazenamento de Premium](../storage/storage-premium-storage.md).

## <a name="new-vms"></a>Novas VMs
As seções a seguir descrevem como configurar o armazenamento para novas máquinas virtuais de SQL Server.

### <a name="azure-portal"></a>Portal do Azure
Ao provisionar uma VM Azure usando uma imagem da Galeria do SQL Server, você pode optar por configurar automaticamente o armazenamento para sua nova VM. Você especificar o tamanho do armazenamento, limites de desempenho e tipo de carga de trabalho. A captura de tela a seguir mostra a lâmina de configuração de armazenamento usada durante SQL VM provisionamento.

![Configuração de armazenamento de máquina virtual do SQL Server durante a configuração](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Com base em suas escolhas, Azure executa as seguintes tarefas de configuração de armazenamento depois de criar a máquina virtual:

- Cria e anexa discos de dados de armazenamento de premium na máquina virtual.
- Configura os discos de dados para ser acessível para o SQL Server.
- Configura os discos de dados em um pool de armazenamento com base em tamanho e desempenho (IOPS e produtividade) requisitos especificados.
- Associa a uma nova unidade na máquina virtual do pool de armazenamento.
- Otimiza esta unidade novo com base em seu tipo de carga de trabalho especificada (Data warehouse, transacional processamento ou geral).

Para mais detalhes sobre como o Azure configura as configurações de armazenamento, consulte a [seção de configuração de armazenamento](#storage-configuration). Para uma explicação completa de como criar uma máquina de virtual do SQL Server no Portal do Azure, consulte [o tutorial de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Gerenciar modelos de recurso
Se você usar os modelos de Gerenciador de recursos a seguir, dois discos de dados premium são anexados por padrão, sem nenhuma configuração de pool de armazenamento. No entanto, você pode personalizar estes modelos para alterar o número de discos de dados premium anexados à máquina virtual.

- [Criar máquina virtual com Backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
- [Criar máquina virtual com patch automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
- [Criar máquina virtual com integração de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>VMs existentes
Para VMs do servidor SQL existente, você pode modificar algumas configurações de armazenamento no portal do Azure. Selecione sua máquina virtual, vá para a área de configurações e selecione configuração do SQL Server. A lâmina de configuração do SQL Server mostra o uso atual do armazenamento da sua máquina virtual. Todas as unidades que existe em sua máquina virtual são exibidas neste gráfico. Para cada unidade, o espaço de armazenamento exibe em quatro seções:

- Dados do SQL
- Log do SQL
- Outros (armazenamento não-SQL)
- Disponível

![Configurar o armazenamento para SQL Server máquina virtual existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Para configurar o armazenamento para adicionar uma nova unidade ou estender uma unidade existente, clique no link Editar acima do gráfico.

As opções de configuração que você vê varia dependendo se você usou este recurso antes. Ao usar pela primeira vez, você pode especificar os requisitos de armazenamento para uma nova unidade. Se você tiver usado anteriormente esse recurso para criar uma unidade, você pode optar por estender armazenamento da unidade.

### <a name="use-for-the-first-time"></a>Usar pela primeira vez
Se for a primeira vez que usar esse recurso, você pode especificar os limites de tamanho e desempenho de armazenamento para uma nova unidade. Esta experiência é semelhante ao que você vê em vez de provisionamento. A principal diferença é que você não tem permissão para especificar o tipo de carga de trabalho. Essa restrição impede interromper quaisquer configurações existentes do SQL Server na máquina virtual.

![Configurar os controles deslizantes de armazenamento do SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure cria uma nova unidade com base nas suas especificações. Neste cenário, o Azure executa as seguintes tarefas de configuração de armazenamento:

- Cria e anexa discos de dados de armazenamento de premium na máquina virtual.
- Configura os discos de dados para ser acessível para o SQL Server.
- Configura os discos de dados em um pool de armazenamento com base em tamanho e desempenho (IOPS e produtividade) requisitos especificados.
- Associa a uma nova unidade na máquina virtual do pool de armazenamento.

Para mais detalhes sobre como o Azure configura as configurações de armazenamento, consulte a [seção de configuração de armazenamento](#storage-configuration).

### <a name="add-a-new-drive"></a>Adicionar uma nova unidade
Se você já configurou o armazenamento em sua máquina de virtual do SQL Server, armazenamento expansão traz duas novas opções. A primeira opção é adicionar uma nova unidade, que pode aumentar o nível de desempenho de sua máquina virtual.

![Adicionar uma nova unidade a uma VM SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

No entanto, depois de adicionar a unidade, você deve executar alguma configuração manual extra para atingir o aumento de desempenho.

### <a name="extend-the-drive"></a>Estender a unidade
A outra opção de armazenamento de expansão é estender a unidade existente. Essa opção aumenta o armazenamento disponível para sua unidade, mas ela não aumentar o desempenho. Com pools de armazenamento, você não pode alterar o número de colunas após o pool de armazenamento é criado. O número de colunas determina o número de gravações paralelos, que podem ser distribuídos pelos discos de dados. Portanto, qualquer disco de dados adicionados não pode aumentar o desempenho. Eles só podem fornecer mais armazenamento para os dados que estão sendo gravados. Essa limitação também significa que, ao estender a unidade, o número de colunas determina o número mínimo de discos de dados que você pode adicionar. Se você criar um pool de armazenamento com discos de quatro dados, o número de colunas é também quatro. Qualquer momento que você estender o armazenamento, você deve adicionar discos de dados pelo menos quatro.

![Estender uma unidade de uma VM SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuração de armazenamento
Esta seção fornece uma referência para as alterações de configuração de armazenamento que Azure executa automaticamente durante a configuração no Portal do Azure ou SQL VM provisionamento.

- Se você tiver selecionado menos de dois TB de armazenamento para sua máquina virtual, o Azure não criar um pool de armazenamento.
- Se você tiver selecionado pelo menos duas TB de armazenamento para sua máquina virtual, o Azure configura um pool de armazenamento. A próxima seção deste tópico fornece detalhes de configuração do pool de armazenamento.
- Configuração de armazenamento automático sempre usa o [armazenamento de premium](../storage/storage-premium-storage.md) P30 discos de dados. Consequentemente, há um mapeamento 1:1 entre seu número de Terabytes selecionado e o número de discos de dados anexados à sua máquina virtual.

Para informações sobre preços, consulte a página de [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage) na guia **Armazenamento de disco** .

### <a name="creation-of-the-storage-pool"></a>Criação de pool de armazenamento
Azure usa as configurações a seguir para criar o pool de armazenamento em VMs do SQL Server.

| Configuração | Valor |
|-----|-----|
| Tamanho de distribuição  | 256 KB (Data warehouse); 64 KB (transações) |
| Tamanhos de disco | 1 TB |
| Cache | Leitura |
| Tamanho de alocação | Tamanho de unidade de alocação de NTFS de 64 KB |
| Inicialização de arquivo instantânea | Habilitado |
| Bloquear páginas na memória | Habilitado |
| Recuperação | Recuperação simples (sem resiliência) |
| Número de colunas | Número de discos de dados<sup>1</sup> |
| Local de TempDB | Armazenado em discos de dados<sup>2</sup> |

<sup>1</sup> após o pool de armazenamento é criado, você não pode alterar o número de colunas no pool de armazenamento.

<sup>2</sup> esta configuração se aplica somente a primeira unidade em que você cria usando o recurso de configuração de armazenamento.

## <a name="workload-optimization-settings"></a>Configurações de otimização de carga de trabalho
A tabela a seguir descreve as opções de tipo de carga de trabalho de três disponíveis e seus correspondentes otimizações:

| Tipo de carga de trabalho | Descrição | Otimizações |
|-----|-----|-----|
| **Geral** | Configuração padrão que ofereça suporte a maioria das cargas de trabalho | Nenhum |
| **Processamento de transações** | Otimiza o armazenamento para cargas de trabalho OLTP de banco de dados tradicional | Sinalizador de rastreamento 1117<br/>Sinalizador de rastreamento 1118 |
| **Data warehouse** | Otimiza o armazenamento para cargas de trabalho analíticos e relatórios | Sinalizador de rastreamento 610<br/>Sinalizador de rastreamento 1117 |

>[AZURE.NOTE] Você só pode especificar o tipo de carga de trabalho quando você provisionar uma máquina virtual SQL selecionando-a etapa de configuração de armazenamento.

## <a name="next-steps"></a>Próximas etapas
Para outros tópicos relacionados a execução do SQL Server no Azure VMs, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
