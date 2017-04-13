<properties
    pageTitle="Diretrizes de soluções de armazenamento | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de design e implementação chaves para implantar soluções de armazenamento em serviços de infraestrutura Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="storage-infrastructure-guidelines"></a>Diretrizes de infraestrutura de armazenamento

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Este artigo aborda compreensão necessidades de armazenamento e considerações de design para atingir o desempenho ideal virtuais de máquina.


## <a name="implementation-guidelines-for-storage"></a>Diretrizes de implementação de armazenamento

Decisões:

- Você precisa usar o armazenamento Standard ou Premium para sua carga de trabalho?
- Você precisa distribuição do disco para criar discos maiores que 1023 GB?
- Você precisa distribuição do disco para obter um desempenho ideal de i/o para sua carga de trabalho?
- Qual conjunto de contas de armazenamento você precisa hospedar sua carga de trabalho TI ou infraestrutura?

Tarefas:

- Examine a exigências de i/o dos aplicativos que você está implantando e planeja o número apropriado e o tipo de contas de armazenamento.
- Crie o conjunto de contas de armazenamento usando a convenção de nomenclatura. Você pode usar o PowerShell do Azure ou o portal.


## <a name="storage"></a>Armazenamento

Armazenamento do Azure é uma parte fundamental da implantação e gerenciamento de aplicativos e máquinas virtuais (VMs). Armazenamento do Azure fornece serviços para armazenar dados de arquivos, dados não estruturados e mensagens, e também é parte da infraestrutura VMs de suporte.

Há dois tipos de contas de armazenamento disponível para o suporte VMs:

- Dão de contas de armazenamento padrão acesso ao armazenamento de blob (usado para armazenar discos de máquina virtual do Azure), tabela, fila do armazenamento e armazenamento de arquivos.
- Contas de [armazenamento Premium](../storage/storage-premium-storage.md) oferecem suporte de disco de alto desempenho e baixa latência para i/o intenso cargas de trabalho, como servidores de SQL em um cluster sempre ativado. Armazenamento de Premium atualmente suporta somente a discos de máquina virtual do Azure.

Azure cria VMs com um disco de sistema operacional, um disco temporário e zero ou mais discos de dados opcionais. O disco de sistema operacional e discos de dados são blobs de página Azure, enquanto o disco temporário é armazenado localmente no nó onde reside a máquina. Tome cuidado ao criar aplicativos para usar apenas este disco temporário para dados não-persistente como a máquina virtual pode ser migrada entre hosts durante um evento de manutenção. Todos os dados armazenados no disco temporário seriam perdidos.

Alta disponibilidade e durabilidade é fornecido pelo ambiente de armazenamento do Azure subjacente para garantir que seus dados permaneçam protegidos contra falhas de hardware ou de manutenção planejadas. Ao projetar seu ambiente de armazenamento do Azure, você pode optar por replicar o armazenamento de máquina virtual:

- localmente em uma determinada data de Center Azure
- entre data centers Azure dentro de uma determinada região
- entre data centers Azure em diferentes regiões

Você pode ler [mais sobre as opções de replicação de alta disponibilidade](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Discos do sistema operacional e dados têm um tamanho máximo de 1023 GB (Gigabytes). O tamanho máximo de um blob é 1024 GB e que deve conter os metadados (rodapé) do arquivo VHD (um GB é 1024<sup>3</sup> bytes). Você pode usar espaços de armazenamento no Windows Server 2012 para ultrapasse esse limite por juntos pool discos de dados para apresentar volumes lógicos maiores que 1023GB para sua máquina virtual.

Há alguns limites de escalabilidade ao criar as implantações de armazenamento do Azure, consulte [limites de assinatura e o serviço do Microsoft Azure, cotas e restrições](azure-subscription-service-limits.md#storage-limits) para obter mais detalhes. Consulte também [destinos de desempenho e escalabilidade de armazenamento do Azure](../storage/storage-scalability-targets.md).

Para armazenamento de aplicativo, você pode armazenar dados de objeto não estruturados como documentos, imagens, backups, dados de configuração, logs, etc. usando o armazenamento de blob. Em vez de seu aplicativo gravar em um disco virtual anexado a máquina virtual, o aplicativo pode escrever diretamente ao armazenamento de blob do Microsoft Azure. Armazenamento de blob também fornece a opção de [níveis de armazenamento quente e interessantes](../storage/storage-blob-storage-tiers.md) dependendo das suas necessidades de disponibilidade e restrições de custo.


## <a name="striped-disks"></a>Discos distribuídos
Além de permitindo que você crie discos maiores que 1023 GB, em muitos casos, usem distribuição para discos de dados melhora o desempenho, permitindo que vários blobs para trás o armazenamento de um único volume. Com distribuição, i / o necessário para gravar e ler dados de um único disco lógico prossegue em paralelo.

Azure impõe limites no número de discos de dados e largura de banda disponível, dependendo do tamanho da máquina virtual. Para obter detalhes, consulte [tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md).

Se você estiver usando a distribuição do disco para discos de dados do Azure, considere as seguintes diretrizes:

- Discos de dados devem ser sempre o tamanho máximo (1023 GB).
- Anexe os discos de dados máximo permitidos para o tamanho de máquina virtual.
- Use espaços de armazenamento.
- Evite usar opções de cache de disco do Azure dados (diretiva de cache = nenhum).

Para obter mais informações, consulte [espaços de armazenamento - Projetando para desempenho](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).


## <a name="multiple-storage-accounts"></a>Várias contas de armazenamento

Ao criar seu ambiente de armazenamento do Azure, você pode usar várias contas de armazenamento como o número de VMs implantar aumenta. Isso ajuda a distribuir o i / o entre a infraestrutura de armazenamento do Azure subjacente para manter o desempenho ideal para suas VMs e aplicativos. Ao projetar os aplicativos que você está implantando, considere os requisitos de e/s que cada VM tem e saldo out essas VMs entre contas de armazenamento do Azure. Tente evitar toda a alta i / exige VMs em apenas uma ou duas contas de armazenamento de agrupamento.

Para obter mais informações sobre os recursos de i/o das diferentes opções de armazenamento do Azure e alguns recomendável máximos, consulte [destinos de desempenho e escalabilidade de armazenamento do Azure](../storage/storage-scalability-targets.md).


## <a name="next-steps"></a>Próximas etapas

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 