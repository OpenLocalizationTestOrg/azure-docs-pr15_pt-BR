<properties
    pageTitle="Usar instantâneos incrementais para backup e recuperação do Azure máquinas virtuais | Microsoft Azure"
    description="Crie uma solução personalizada para backup e recuperação de discos Azure máquina virtual usando instantâneos incrementais."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>


# <a name="back-up-azure-virtual-machine-disks-with-incremental-snapshots"></a>Fazer backup de discos Azure máquina virtual com instantâneos incrementais

## <a name="overview"></a>Visão geral

Armazenamento do Azure fornece a capacidade de obter instantâneos de blobs. Instantâneos capturam o estado de blob nesse momento no tempo. Neste artigo, vamos descreverá um cenário de como você pode manter backups dos discos de máquina virtual utilizando instantâneos. Você pode usar essa metodologia quando você optar por não usar o serviço de recuperação e Backup do Azure e deseja criar uma estratégia de backup personalizada para seus discos de máquina virtual.

Azure máquina virtual discos são armazenados como blobs de página no armazenamento do Azure. Já que estamos falando estratégia de backup para discos de máquina virtual neste artigo, vamos fará referência ao instantâneos no contexto de blobs de página. Para saber mais sobre instantâneos, consulte [criar um instantâneo de um Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## <a name="what-is-a-snapshot"></a>O que é um instantâneo?

Um instantâneo de blob é uma versão somente leitura de um blob capturados em um ponto no tempo. Depois que um instantâneo tiver sido criado, ele pode ser ler, copiado, ou excluído, mas não modificado. Instantâneos oferecem uma maneira de fazer backup de um blob como aparece em um momento específico. Até restante versão 2015-04-05 você tinha a capacidade de copiar instantâneos completos. Com o resto versão 2015-07-08 e posteriores, você também pode copiar instantâneos incrementais.

## <a name="full-snapshot-copy"></a>Cópia de instantâneo completo

Instantâneos podem ser copiados para outra conta de armazenamento como um blob para manter os backups do blob base. Você também pode copiar um instantâneo sobre sua blob base, que é como restaurando o blob para uma versão anterior. Quando um instantâneo é copiado de uma conta de armazenamento para outro, ele ocupam o mesmo espaço que o blob de página base. Portanto, copiando instantâneos inteiros de uma conta de armazenamento para outro serão lentos e também irá consumir muito espaço da conta de armazenamento de destino.

>[AZURE.NOTE] Se você copiar o blob base para outro destino, os instantâneos do blob não são copiados junto com ele. Da mesma forma, se você substituir um blob base com uma cópia, instantâneos associados com o blob base não são afetados e permaneçam intactos em nome do blob base.

### <a name="back-up-disks-using-snapshots"></a>Fazer backup de discos usando instantâneos

Como uma estratégia de backup para seus discos de máquina virtual, você pode tirar instantâneos periódicos do blob disco ou de uma página e copiá-las para outra conta de armazenamento usando ferramentas como operação de [Cópia Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou [AzCopy](storage-use-azcopy.md). Você pode copiar um instantâneo para um blob de página de destino com um nome diferente. O blob de página de destino resultante é um blob de página gravável e não um instantâneo. Neste artigo, vamos descreverá etapas para fazer backups dos discos de máquina virtual utilizando instantâneos.

### <a name="restore-disks-using-snapshots"></a>Restaurar discos usando instantâneos

Quando for tempo para restaurar o disco para uma versão anterior de estável capturada em uma dos instantâneos backup, você pode copiar um instantâneo sobre o blob de página base. Depois que o instantâneo for promovido para a página de base blob, permanece instantâneo, mas sua fonte é substituída com uma cópia que pode ser tanto leitura e escrita. Neste artigo, vamos descreverá as etapas para restaurar uma versão anterior do disco do seu instantâneo.

### <a name="implementing-full-snapshot-copy"></a>Implementação de cópia de instantâneo completo

Você pode implementar uma cópia de instantâneo completo fazendo o seguinte,

-   Primeiro, crie um instantâneo do blob base usando a operação de [Instantâneo Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) .
-   Em seguida, copie o instantâneo para uma conta de armazenamento de destino usando [Blob de cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Repita esse processo para manter cópias de backup de seu blob base.

## <a name="incremental-snapshot-copy"></a>Cópia de instantâneo incremental

O novo recurso no [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API fornece uma maneira muito melhor para fazer backup os instantâneos dos seus blobs de página ou discos. A API retorna a lista de alterações entre o blob base e os instantâneos. Isso reduz a quantidade de espaço de armazenamento usado na conta de backup. O API suporta blobs de página em armazenamento Premium, bem como armazenamento padrão. Com essa API, agora você pode criar soluções de backup mais rápidas e eficientes para VMs do Azure. Isso estará disponível com a versão do resto 2015-07-08 e superior.

Cópia de instantâneo incremental permite que você copiar de uma conta de armazenamento para outro a diferença entre,

-   Blob base e seu instantâneo ou
-   Qualquer dois instantâneos do blob base

Desde que as seguintes condições sejam atendidas,

- O blob foi criado em Jan-1-2016 ou posterior.
- O blob não foi substituído com [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) ou [Copiar Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) entre dois instantâneos.


**Observação**: esse recurso está disponível para Premium e Blobs de página padrão do Azure.

Quando você tem uma estratégia de backup personalizada que usa instantâneos, copiando os instantâneos de uma conta de armazenamento para outra pode ser muito lenta e consome muito espaço de armazenamento. Em vez de copiar o instantâneo todo a uma conta de armazenamento de backup, você pode escrever a diferença entre os instantâneos consecutivas para um blob de página backup. Dessa forma, o tempo de cópia e espaço para armazenar backups é substancialmente reduzido.

### <a name="implementing-incremental-snapshot-copy"></a>Implementação de cópia de instantâneo Incremental

Você pode implementar instantâneo incremental cópia fazendo o seguinte,

-   Tirar um instantâneo do blob base usando [Instantâneo Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Copie o instantâneo para a conta de armazenamento de backup de destino usando [Blob de cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx). Este será o blob de página backup. Tirar um instantâneo do blob página backup e armazene em backup da conta.
-   Crie outro instantâneo do blob base usando instantâneo Blob.
-   Obtenha a diferença entre a primeira e a segunda instantâneos de base blob usando [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Use o novo parâmetro **prevsnapshot** para especificar o valor de DateTime do instantâneo que você deseja obter a diferença. Quando este parâmetro estiver presente, a resposta REST incluirá apenas as páginas que foram alteradas entre instantâneo de destino e instantâneo anterior, incluindo páginas limpar.
-   Use [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para aplicar essas alterações para o blob de página backup.
-   Finalmente, tirar um instantâneo do blob página backup e armazená-la na conta de armazenamento de backup.

Na próxima seção, podemos descreverá mais detalhadamente como você pode manter backups de discos usando a cópia de instantâneo Incremental

## <a name="scenario"></a>Cenário

Nesta seção, podemos descreverá um cenário que envolve uma estratégia de backup personalizada para discos de máquina virtual usando instantâneos.

Considere uma máquina virtual do Azure de série DS com um disco de P30 de armazenamento premium anexado. O disco de P30 chamado *mypremiumdisk* é armazenado em uma conta de armazenamento premium chamada *mypremiumaccount*. Uma conta de armazenamento padrão chamada *mybackupstdaccount* será usada para armazenar o backup de *mypremiumdisk*. Podemos gostaria de manter um instantâneo do *mypremiumdisk* cada 12 horas.

Para saber mais sobre a criação de discos e conta de armazenamento, consulte [contas de armazenamento do Azure sobre](storage-create-storage-account.md).

Para saber sobre como fazer backup do Azure VMs, consulte [Planejar máquina virtual do Azure backups](../backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Etapas para manter backups de um disco usando instantâneos incrementais

As etapas descritas abaixo serão instantâneos das *mypremiumdisk* e manter os backups em *mybackupstdaccount*. O backup será um blob de página padrão chamado *mybackupstdpageblob*. O blob de página backup sempre refletirão o mesmo estado que o último instantâneo de *mypremiumdisk*.

1.  Primeiro, crie o blob de página backup para o disco de armazenamento premium. Para fazer isso, tirar um instantâneo do *mypremiumdisk* chamado *mypremiumdisk_ss1*.
2.  Copie esse Instantâneo mybackupstdaccount como um blob de página chamado *mybackupstdpageblob*.
3.  Tirar um instantâneo do *mybackupstdpageblob* chamado *mybackupstdpageblob_ss1*, usando o [Instantâneo Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) e armazená-lo em *mybackupstdaccount*.
4.  Durante a janela de backup, criar outro instantâneo do *mypremiumdisk*, diga *mypremiumdisk_ss2*e armazená-lo em *mypremiumaccount*.
5.  Obtenha as alterações incrementais entre os dois instantâneos, *mypremiumdisk_ss2* e *mypremiumdisk_ss1*, usando [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) em *mypremiumdisk_ss2* com parâmetro de **prevsnapshot** definido como o carimbo de hora do *mypremiumdisk_ss1*. Escreva essas alterações incrementais para a página backup blob *mybackupstdpageblob* em *mybackupstdaccount*. Se houver intervalos excluídos nas alterações incrementais, eles devem ser removidos do blob a página de backup. Use [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) para gravar alterações incrementais o blob de página backup.
6.  Tirar um instantâneo da página backup blob *mybackupstdpageblob*, chamado *mybackupstdpageblob_ss2*. Exclua o instantâneo anterior *mypremiumdisk_ss1* da conta de armazenamento premium.
7.  Repita as etapas 4 a 6 cada janela de backup. Dessa forma, você pode manter backups de *mypremiumdisk* em uma conta de armazenamento padrão.

![Fazer backup de disco usando instantâneos incrementais](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Etapas para restaurar um disco de instantâneos

As etapas descritas abaixo irá restaurar disco premium, *mypremiumdisk* a um instantâneo anterior da conta de armazenamento de backup *mybackupstdaccount*.

1.  Identifica o ponto no tempo que você deseja restaurar o disco premium. Digamos que é instantâneo *mybackupstdpageblob_ss2*, que é armazenado na conta de armazenamento de backup *mybackupstdaccount*.
2.  Em mybackupstdaccount, promova o instantâneo *mybackupstdpageblob_ss2* como a nova página de base backup blob *mybackupstdpageblobrestored*.
3.  Tirar um instantâneo do blob página backup restaurado, chamado *mybackupstdpageblobrestored_ss1*.
4.  Copie o blob de página restaurado *mybackupstdpageblobrestored* de *mybackupstdaccount* para *mypremiumaccount* como o novo premium disco *mypremiumdiskrestored*.
5.  Tirar um instantâneo do *mypremiumdiskrestored*, chamado *mypremiumdiskrestored_ss1* para fazer backups incrementais futuros.
6.  Série de ponto DS máquina virtual para o restaurado *mypremiumdiskrestored* de disco e desanexar o antigo *mypremiumdisk* da máquina virtual.
7.  Inicie o processo de Backup descrito na seção anterior para o disco restaurado *mypremiumdiskrestored*, usando o *mybackupstdpageblobrestored* como o blob de página backup.

![Restaurar o disco de instantâneos](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como criar instantâneos de um blob e planejamento de sua infraestrutura de backup de máquina virtual usando os links abaixo.

- [Criação de um instantâneo de um Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Planejar sua infraestrutura de Backup de máquina virtual](../backup/backup-azure-vms-introduction.md)
