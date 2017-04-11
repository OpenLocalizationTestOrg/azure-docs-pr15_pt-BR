<properties
    pageTitle="Migrando para o armazenamento do Azure Premium | Microsoft Azure"
    description="Migre suas máquinas virtuais existentes para o armazenamento do Azure Premium. Armazenamento de Premium oferece suporte de disco de alto desempenho e baixa latência para eu/O-que requer muita cargas de trabalho em execução em máquinas virtuais do Azure."
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
    ms.date="10/21/2016"
    ms.author="yuemlu"/>


# <a name="migrating-to-azure-premium-storage"></a>Migrando para o armazenamento do Azure Premium

## <a name="overview"></a>Visão geral

Armazenamento do Azure Premium oferece suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando eu/O-que requer muita cargas de trabalho. Você pode tirar proveito da velocidade e o desempenho desses discos migrando discos de máquina virtual do seu aplicativo para o armazenamento do Azure Premium.

A finalidade deste guia é ajudar novos usuários do Azure Premium armazenamento melhor se prepara para fazer uma transição suave do seu sistema atual ao armazenamento de Premium. Guia aborda três dos componentes principais do processo: 

  - [Planejar a migração para o armazenamento de Premium](#plan-the-migration-to-premium-storage)
  - [Preparar e copiar rígido VHDs (discos virtuais) para o armazenamento de Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
  - [Criar Máquina Virtual de Azure usando o armazenamento de Premium](#create-azure-virtual-machine-using-premium-storage)

Você pode migrar VMs de outras plataformas para o armazenamento do Azure Premium ou migrar VMs existente do Azure do armazenamento padrão para o armazenamento de Premium. Este guia aborda as etapas para ambos os dois cenários. Siga as etapas especificadas na seção relevante dependendo do cenário.

>[AZURE.NOTE] Você pode encontrar uma visão de recurso e preços de armazenamento Premium em armazenamento Premium: [Armazenamento de alto desempenho para cargas de trabalho de máquina Virtual do Azure](storage-premium-storage.md). É recomendável migrar qualquer disco de máquina virtual exigindo IOPS alta para o armazenamento do Azure Premium para o melhor desempenho do aplicativo. Se seu disco não requisitar IOPS alta, você pode limitar os custos mantendo-lo no armazenamento padrão, que armazena dados de disco de máquina virtual em unidades de disco rígido (unidades de disco rígido) em vez de SSDs.

Concluir o processo de migração integralmente pode exigir ações adicionais antes e depois as etapas fornecidas neste guia. Alguns exemplos incluem configurar redes virtuais ou pontos de extremidade ou fazer alterações de código dentro do próprio aplicativo que pode exigir algum tempo de inatividade em seu aplicativo. Essas ações são exclusivas para cada aplicativo e deve ser concluída juntamente com as etapas fornecidas neste guia para fazer a transição completa para armazenamento de Premium mais contínua possível.


## <a name="plan-the-migration-to-premium-storage"></a>Planejar a migração para o armazenamento de Premium

Esta seção garante que você esteja pronto para seguir as etapas de migração neste artigo e ajuda você a tomar a decisão melhor nos tipos de máquina virtual e disco.

### <a name="prerequisites"></a>Pré-requisitos
- Você precisará de uma assinatura do Azure. Se você não tiver um, você pode criar uma assinatura de um mês [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) ou visite [Preços do Azure](https://azure.microsoft.com/pricing/) para obter mais opções.
- Para executar cmdlets do PowerShell, será necessário o módulo do PowerShell do Microsoft Azure. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instruções de instalação e ponto de instalação.
- Quando você planeja usar VMs do Azure em execução no armazenamento Premium, você precisa usar as VMs capazes de armazenamento Premium. Você pode usar discos padrão e de armazenamento de Premium com VMs capazes de armazenamento Premium. Discos de armazenamento Premium estarão disponíveis com mais tipos de máquina virtual no futuro. Para obter mais informações sobre todos os tipos de disco de máquina virtual do Azure e tamanhos disponíveis, consulte [tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md) e [tamanhos para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Considerações

Uma máquina virtual do Azure suporta anexar vários discos de armazenamento de Premium para que seus aplicativos podem ter até 64 TB de armazenamento por máquina virtual. Com o armazenamento de Premium, seus aplicativos podem alcançar 80.000 IOPS (operações de entrada/saída por segundo) por máquina virtual e 2000 MB por segunda disco taxa de transferência por máquina virtual com muito menos latências para operações de leitura. Você tem opções em uma variedade de VMs e discos. Esta seção é ajudar você a encontrar uma opção mais adequado para sua carga de trabalho.

#### <a name="vm-sizes"></a>Tamanhos de máquina virtual
As especificações de tamanho de máquina virtual do Azure são listadas em [tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md). Examine as características de desempenho de máquinas virtuais que funcionam com o armazenamento de Premium e escolha o tamanho de máquina virtual mais apropriado mais adequado para sua carga de trabalho. Certifique-se de que há largura de banda suficiente disponível em sua máquina virtual para direcionar o tráfego de disco.


#### <a name="disk-sizes"></a>Tamanhos de disco
Há três tipos de discos que podem ser usados com sua máquina virtual e cada tem IOPs específico e produtividade limites. Leve em consideração esses limites quando escolhendo o tipo de disco para sua máquina virtual com base nas necessidades de seu aplicativo em termos de capacidade, desempenho, escalabilidade e carrega pico.

|Tipo de disco de armazenamento Premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Tamanho do disco|128 GB|512 GB|1024 GB (1 TB)|
|IOPS por disco|500|2300|5000|
|Produtividade por disco|100 MB por segundo|150 MB por segundo|200 MB por segundo|

Dependendo de sua carga de trabalho, determine se discos de dados adicionais são necessários para sua máquina virtual. Você pode anexar vários discos de dados persistentes para sua máquina virtual. Se necessário, você pode distribuição entre os discos para aumentar a capacidade e desempenho do volume. (Consulte o que é a distribuição do disco [aqui](storage-premium-storage-performance.md#disk-striping).) Se você usar [Espaços de armazenamento]de discos de dados de armazenamento Premium de distribuição[4], você deve configurá-lo com uma coluna para cada disco que é usado. Caso contrário, o desempenho geral do volume distribuído pode ser menor que o esperado devido a distribuição desigual de tráfego entre os discos. Para VMs Linux, você pode usar o utilitário *mdadm* para atingir o mesmo. Consulte o artigo [Configurar o Software RAID no Linux](../virtual-machines/virtual-machines-linux-configure-raid.md) para obter detalhes.

#### <a name="storage-account-scalability-targets"></a>Destinos de escalabilidade de conta de armazenamento
Contas de armazenamento Premium tem os seguintes destinos de escalabilidade além das [metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md). Se seus requisitos de aplicativo excederem os destinos de escalabilidade de uma conta de armazenamento único, criar seu aplicativo para usar várias contas de armazenamento e seus dados de partição entre as contas de armazenamento.

|Capacidade de conta total|Largura de banda total para uma conta de armazenamento redundante localmente|
|:--|:---|
|Capacidade de disco: 35TB<br />Capacidade de instantâneo: 10 TB|Até 50 gigabits por segundo para entrada + saída|

Para obter mais informações sobre especificações de armazenamento Premium, confira [escalabilidade e metas de desempenho ao usar o armazenamento de Premium](storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

#### <a name="disk-caching-policy"></a>Diretiva de cache de disco
Por padrão, diretiva de cache de disco é *Somente leitura* para todos os dados de Premium discos e *Leitura e gravação* para o disco de sistema operacional Premium anexado a máquina virtual. Esta configuração é recomendável para atingir o desempenho ideal para IOs do seu aplicativo. Para discos de dados intenso de gravação ou somente gravação (como arquivos de log do SQL Server), desative o cache de disco para que você pode obter melhor desempenho do aplicativo. As configurações de cache para discos de dados existentes podem ser atualizadas usando o [Portal do Azure](https://portal.azure.com) ou o parâmetro *- HostCaching* do cmdlet *Set-AzureDataDisk* .

#### <a name="location"></a>Local
Escolha um local onde o armazenamento do Azure Premium está disponível. Consulte [Azure Services por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre locais disponíveis. VMs localizadas na mesma região como a conta de armazenamento que armazena os discos para a máquina virtual terá melhor desempenho que se eles estão em áreas separadas.

#### <a name="other-azure-vm-configuration-settings"></a>Outras configurações de máquina virtual do Azure
Ao criar uma máquina virtual do Azure, você será solicitado a configurar certas configurações de máquina virtual. Lembre-se de que algumas configurações corrigidas para o ciclo de vida da máquina virtual, enquanto você pode modificar ou adicionar outras pessoas posteriormente. Examine essas definições de configuração de máquina virtual do Azure e certifique-se de que eles são configurados adequadamente para corresponder às suas necessidades de carga de trabalho.

### <a name="optimization"></a>Otimização

[o armazenamento do azure Premium: Design de alto desempenho](storage-premium-storage-performance.md) fornece diretrizes para criação de aplicativos de alto desempenho usando o armazenamento do Azure Premium. Você pode seguir as diretrizes combinadas com práticas recomendadas de desempenho aplicáveis a tecnologias usadas pelo seu aplicativo.


## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparar e copiar rígido VHDs (discos virtuais) para o armazenamento de Premium

A seção a seguir fornece diretrizes para preparar VHDs de sua máquina virtual e copiando VHDs ao armazenamento do Azure.

- [Cenário 1: "eu estou migrando VMs Azure existentes para o armazenamento do Azure Premium."](#scenario1)
- [Cenário 2: "eu estou migrando VMs de outras plataformas para o armazenamento do Azure Premium."](#scenario2)

### <a name="prerequisites"></a>Pré-requisitos

Para preparar os VHDs migração, será necessário:

- Uma assinatura do Azure, uma conta de armazenamento e um contêiner nessa conta de armazenamento à qual você pode copiar seu VHD. Observe que a conta de armazenamento de destino pode ser uma conta padrão ou Premium armazenamento dependendo das suas necessidades.
- Uma ferramenta generalizar o VHD se você planeja criar várias instâncias de máquina virtual a partir dele. Por exemplo, sysprep para Windows ou virt-sysprep para Ubuntu.
- Uma ferramenta para carregar o arquivo VHD para a conta de armazenamento. Consulte [transferir dados com o utilitário de linha de comando do AzCopy](storage-use-azcopy.md) ou usar um [Explorador de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Este guia descreve copiando seu VHD usando a ferramenta de AzCopy.

> [AZURE.NOTE] Se você escolher a opção de cópia sincronizada com AzCopy, para um desempenho ideal, copie o VHD executando uma dessas ferramentas a partir de uma máquina virtual do Azure que está na mesma região como a conta de armazenamento de destino. Se você estiver copiando um VHD de uma VM Azure em uma região diferente, seu desempenho pode ser mais lento.
>
> Para copiar uma grande quantidade de dados ao longo de largura de banda limitada, considere o [uso do serviço de importação/exportação do Azure para transferir dados para o armazenamento de Blob](storage-import-export-service.md); Isso permite transferir seus dados por remessas unidades de disco rígido para um data center Azure. Você pode usar o serviço de importação/exportação do Azure para copiar dados para apenas uma conta de armazenamento padrão. Depois que os dados em sua conta de armazenamento padrão, você pode usar a [API do Blob de cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy para transferir os dados para sua conta de armazenamento premium.
>
> Observe que o Microsoft Azure suporta apenas arquivos VHD de tamanho fixo. Arquivos VHDX ou VHDs dinâmicos não são suportados. Se você tiver um VHD dinâmico, você pode convertê-la ao tamanho fixo usando o cmdlet [VHD Convert](http://technet.microsoft.com/library/hh848454.aspx) .

### <a name="scenario1"></a>Cenário 1: "eu estou migrando VMs Azure existentes para o armazenamento do Azure Premium."

Se você estiver migrando VMs existente do Azure, interromper a máquina virtual, preparar VHDs por tipo de VHD desejado e, em seguida, copie o VHD com AzCopy ou PowerShell.

A máquina virtual precisa estar completamente para baixo para migrar um estado limpo. Haverá um tempo de inatividade até que a migração é concluída.

#### <a name="step-1-prepare-vhds-for-migration"></a>Etapa 1. Preparar VHDs para a migração

Se você estiver migrando VMs existente do Azure ao armazenamento de Premium, seu VHD pode ser:

- Uma imagem generalizado sistema operacional
- Um disco de sistema operacional exclusivo
- Um disco de dados

Abaixo podemos percorrer esses 3 cenários para preparar seu VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Use um VHD generalizado do sistema operacional para criar várias instâncias de máquina virtual

Se você estiver carregando um VHD que será usado para criar várias instâncias de máquina virtual do Azure genéricas, você deve primeiro generalize VHD usando um utilitário de sysprep. Aplica-se em um VHD que está no local ou na nuvem. Sysprep remove o VHD qualquer informação específica de máquina.

>[AZURE.IMPORTANT] Tirar um instantâneo ou backup sua máquina virtual antes de generalizá-lo. Sysprep em execução interromperá e desalocar a instância de máquina virtual. Siga as etapas abaixo para sysprep um VHD do sistema operacional Windows. Observe que executando o comando Sysprep exigem que você desligue a máquina virtual. Para obter mais informações sobre Sysprep, consulte [Visão geral do Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou [Referência técnica do Sysprep](http://technet.microsoft.com/library/cc766049.aspx).

1. Abra uma janela do Prompt de comando como administrador.
2. Digite o seguinte comando para abrir Sysprep:

        %windir%\system32\sysprep\sysprep.exe

3. Na ferramenta de preparação do sistema, selecione experiência de prontos de Enter System (OOBE), marque a caixa de seleção Generalize, selecione **Desligar**e clique em **Okey**, conforme mostrado na imagem abaixo. Sysprep será generalizar o sistema operacional e desligar o sistema.

    ![][1]

Para uma VM Ubuntu, use sysprep virt para atingir o mesmo. Consulte [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para obter mais detalhes. Consulte também algumas à fonte de abrir [o software de provisionamento de servidor Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) para outros sistemas operacionais Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Use um VHD do sistema operacional exclusivo para criar uma única instância de máquina virtual

Se você tiver um aplicativo em execução na máquina virtual que requer dados específicos da máquina, não generalize VHD. Um VHD generalizado não pode ser usado para criar uma instância de máquina virtual do Azure exclusiva. Por exemplo, se você tiver o controlador de domínio em seu VHD, executar sysprep tornará ineficaz como controlador de domínio. Examine os aplicativos em execução em sua máquina virtual e o impacto da execução sysprep-las antes de generalizar o VHD.

##### <a name="register-data-disk-vhd"></a>Registrar dados disco VHD

Se você tiver discos de dados no Azure a serem migradas, você deve garantir VMs que usam esses discos de dados são encerradas.

Siga as etapas descritas abaixo para copiar VHD para o armazenamento do Azure Premium e registre-o como um disco de dados provisionado.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Etapa 2. Criar o destino para seu VHD

Crie uma conta de armazenamento para manter sua VHDs. Ao planejar onde armazenar seu VHDs, considere os seguintes pontos:

- O conta de armazenamento Premium de destino.
- O local de conta de armazenamento deve ser igual ao armazenamento de Premium capaz Azure VMs será criado na etapa final. Você pode copiar para uma nova conta de armazenamento, ou planeja usar a mesma conta de armazenamento com base nas suas necessidades.
- Copie e salve a chave da conta de armazenamento da conta de armazenamento de destino para o próximo estágio.

Para discos de dados, você pode optar por manter alguns discos de dados em uma conta de armazenamento padrão (por exemplo, discos que têm mais fria armazenamento), mas é altamente recomendável que você mover todos os dados de carga de trabalho de produção usar o armazenamento de premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Etapa 3. Copiar VHD com AzCopy ou PowerShell

Você precisará localizar sua contêiner caminho e armazenamento chave da conta para processar dessas duas opções. Chave de conta de armazenamento e o caminho de contêiner pode ser encontrada no **Portal do Azure** > **armazenamento**. A URL do contêiner será como "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opção 1: Copiar um VHD com AzCopy (cópia assíncrona)

Usando AzCopy, você pode carregar facilmente o VHD pela Internet. Dependendo do tamanho dos VHDs, isso pode demorar. Lembre-se de verificar os limites de entrada/saída de conta de armazenamento ao usar essa opção. Para obter detalhes, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md) .

1. Baixe e instale o AzCopy daqui: [versão mais recente do AzCopy](http://aka.ms/downloadazcopy)
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy está instalado.
3. Use o seguinte comando para copiar o arquivo VHD de "Fonte" para "Destino".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Exemplo:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd

    Aqui estão as descrições dos parâmetros usados no comando AzCopy:

 - * */Origem: * &lt;fonte&gt;:** * local da pasta ou URL de contêiner de armazenamento que contém o VHD.
 - * */SourceKey: * &lt;chave da conta de origem&gt;:** * chave da conta de armazenamento da conta de armazenamento de origem.
 - * */Dest: * &lt;destino&gt;:** * URL de contêiner de armazenamento para copiar o VHD para.
 - * */DestKey: * &lt;chave da conta de destino&gt;:** * chave da conta de armazenamento da conta de armazenamento de destino.
 - * */Padrão: * &lt;nome de arquivo&gt;:** * especifique o nome de arquivo do VHD para copiar.

Para obter detalhes sobre como usar a ferramenta de AzCopy, consulte [transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opção 2: Copiar um VHD com o PowerShell (sincronizados cópia)

Você também pode copiar o arquivo VHD usando o cmdlet do PowerShell Start-AzureStorageBlobCopy. Use o seguinte comando no Azure PowerShell para copiar VHD. Substitua os valores nos <> com valores correspondentes da sua conta de armazenamento de origem e destino. Para usar este comando, você deve ter um contêiner chamado vhds na sua conta de armazenamento de destino. Se o contêiner não existir, crie um antes de executar o comando.

    $sourceBlobUri = <source-vhd-uri>

    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext

Exemplo:

    C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

    C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

    C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

    C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### <a name="scenario2"></a>Cenário 2: "eu estou migrando VMs de outras plataformas para o armazenamento do Azure Premium."

Se você estiver migrando VHD de não - Azure armazenamento em nuvem para o Azure, você deve primeiro exportar o VHD em um diretório local. Ter o caminho de origem completa do diretório local onde VHD está armazenado à mão e usando AzCopy para carregá-lo ao armazenamento do Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Etapa 1. Exportar VHD em um diretório local

##### <a name="copy-a-vhd-from-aws"></a>Copiar um VHD de AWS

1. Se você estiver usando AWS, exporte a instância de EC2 para um VHD em uma classificação Amazon S3. Siga as etapas descritas na documentação Amazon exportando Amazon EC2 instâncias instalar a ferramenta de interface de linha (comando) Amazon EC2 e execute o comando de tarefa create-instância-exportação para exportar a instância de EC2 para um arquivo VHD. Certifique-se de usar o **VHD** para o disco #95; imagem & #95; Variável de formato ao executar o comando **criar a instância-exportação-tarefa** . O arquivo exportado do VHD é salvo no depósito Amazon S3 que designar durante esse processo.

        aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
        --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX

2. Baixe o arquivo VHD do Balde S3. Selecione o arquivo VHD, em seguida, **ações** > **Baixar**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copiar um VHD de outro nuvem não Azure

Se você estiver migrando VHD de não - Azure armazenamento em nuvem para o Azure, você deve primeiro exportar o VHD em um diretório local. Copie o caminho de fonte completo do diretório local onde VHD está armazenado.

##### <a name="copy-a-vhd-from-on-premise"></a>Copiar um VHD de local

Se você estiver migrando VHD de um ambiente local, você precisará o caminho de origem completa onde VHD está armazenado. O caminho de origem pode ser um compartilhamento de arquivo ou local do servidor.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Etapa 2. Criar o destino para seu VHD

Crie uma conta de armazenamento para manter sua VHDs. Ao planejar onde armazenar seu VHDs, considere os seguintes pontos:

- A conta de armazenamento de destino pode ser armazenamento padrão ou premium, dependendo das suas necessidades de aplicativo.
- A região de conta de armazenamento deve ser igual ao armazenamento de Premium capaz Azure VMs será criado na etapa final. Você pode copiar para uma nova conta de armazenamento, ou planeja usar a mesma conta de armazenamento com base nas suas necessidades.
- Copie e salve a chave da conta de armazenamento da conta de armazenamento de destino para o próximo estágio.

É altamente recomendável que você mover todos os dados de carga de trabalho de produção usar o armazenamento de premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Etapa 3. Carregue o VHD para armazenamento do Azure

Agora que você tem seu VHD no diretório local, você pode usar AzCopy ou AzurePowerShell para carregar o arquivo. vhd ao armazenamento do Azure. Ambas as opções são fornecidas aqui:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opção 1: Usando o Azure PowerShell Add-AzureVhd para carregar o arquivo. vhd

    Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>

Um exemplo <Uri> podem ser **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_**. Um exemplo <FileInfo> podem ser **_"C:\path\to\upload.vhd"_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opção 2: Usar AzCopy para carregar o arquivo. vhd

Usando AzCopy, você pode carregar facilmente o VHD pela Internet. Dependendo do tamanho dos VHDs, isso pode demorar. Lembre-se de verificar os limites de entrada/saída de conta de armazenamento ao usar essa opção. Para obter detalhes, consulte [metas de desempenho e escalabilidade de armazenamento do Azure](storage-scalability-targets.md) .

1. Baixe e instale o AzCopy daqui: [versão mais recente do AzCopy](http://aka.ms/downloadazcopy)
2. Abra o PowerShell do Azure e vá para a pasta onde o AzCopy está instalado.
3. Use o seguinte comando para copiar o arquivo VHD de "Fonte" para "Destino".

        AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

    Exemplo:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd

    Aqui estão as descrições dos parâmetros usados no comando AzCopy:

 - * */Origem: * &lt;fonte&gt;:** * local da pasta ou URL de contêiner de armazenamento que contém o VHD.
 - * */SourceKey: * &lt;chave da conta de origem&gt;:** * chave da conta de armazenamento da conta de armazenamento de origem.
 - * */Dest: * &lt;destino&gt;:** * URL de contêiner de armazenamento para copiar o VHD para.
 - * */DestKey: * &lt;chave da conta de destino&gt;:** * chave da conta de armazenamento da conta de armazenamento de destino.
 - **/BlobType: página:** Especifica que o destino é um blob de página.
 - * */Padrão: * &lt;nome de arquivo&gt;:** * especifique o nome de arquivo do VHD para copiar.

Para obter detalhes sobre como usar a ferramenta de AzCopy, consulte [transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD

Você também pode carregar um VHD à sua conta de armazenamento usando um da seguinte maneira:

- [Cópia de armazenamento do Azure Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Armazenamento do Azure Explorer Carregando Blobs](https://azurestorageexplorer.codeplex.com/)
- [Serviço de importação/exportação armazenamento referência de API REST](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] É recomendável usar o serviço de importação/exportação se estimadas carregar tempo é mais de 7 dias. Você pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo da unidade de tamanho e a transferência de dados.
>
> Importação/exportação pode ser usada para copiar para uma conta de armazenamento padrão. Você precisará copiar de armazenamento padrão para a conta de armazenamento premium usando uma ferramenta como AzCopy.


## <a name="create-azure-virtual-machine-using-premium-storage"></a>Criar VMs Azure usando o armazenamento de Premium

Após o VHD for carregado ou copiado para a conta de armazenamento desejada, siga as instruções desta seção para registrar o VHD como uma imagem de sistema operacional, ou o disco de sistema operacional dependendo do cenário e, em seguida, criar uma instância de máquina virtual a partir dele. O disco de dados VHD pode ser anexado a máquina virtual depois que ela é criada. Um exemplo de script de migração é fornecido no final desta seção. Esse script simple não coincidir com todos os cenários. Você talvez precise atualizar o script para fazer a correspondência com a sua situação específica. Para ver se esse script se aplica ao seu cenário, veja a seguir [Um exemplo de Script de migração](#a-sample-migration-script).

### <a name="checklist"></a>Lista de verificação

1.  Aguarde até que todos os discos VHD copiando seja concluída.
2.  Verifique se que o armazenamento de Premium está disponível na região que você está migrando.
3.  Decida a nova série de máquina virtual que você usará. Ele deve ser uma capaz de armazenamento Premium e o tamanho deve ser dependendo da disponibilidade na região e com base em suas necessidades.
4.  Decida o tamanho de máquina virtual exato que você vai usar. Tamanho de máquina virtual precisa ser grande o suficiente para suporte do número de discos de dados que você tem. Por exemplo Se você tiver 4 discos de dados, a máquina virtual deve ter 2 ou mais cores. Além disso, considere a potência, memória e precisa de largura de banda de rede.
5.  Crie uma conta de armazenamento Premium na região de destino. Esta é a conta que você usará para a máquina virtual novo.
6.  Ter os detalhes de máquina virtual atuais útil, incluindo a lista de discos e blobs VHD correspondentes.

Prepare seu aplicativo para o tempo de inatividade. Para executar uma migração limpa, você precisa parar todo o processamento do sistema atual. Somente depois que você pode acessar-estado consistente que você pode migrar para a nova plataforma. Duração do tempo de inatividade dependerá da quantidade de dados na discos para migrar.

>[AZURE.NOTE] Se você estiver criando um gerente de recursos VM Azure de um disco de VHD especializado, consulte [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) para implantação VM Gerenciador de recursos usando o disco existente.

### <a name="register-your-vhd"></a>Registrar seu VHD

Para criar uma máquina virtual do sistema operacional VHD ou anexar um disco de dados a uma nova VM, você deve primeiro registrá-los. Siga as etapas abaixo, dependendo do cenário do seu VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizado VHD do sistema operacional de criar várias instâncias de máquina virtual do Azure

Imagem de SO generalizada VHD uma vez carregado para a conta de armazenamento, registrá-la como uma **Imagem de máquina virtual do Azure** para que você possa criar uma ou mais instâncias de máquina virtual dele. Use os seguintes cmdlets do PowerShell para registrar seu VHD como uma imagem de SO de máquina virtual do Azure. Fornece a URL completa contêiner onde VHD foi copiado para.

    Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copie e salve o nome dessa nova imagem de máquina virtual do Azure. No exemplo acima, há *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD de sistema operacional exclusivo para criar uma única instância de máquina virtual do Azure

Após o VHD exclusivo do sistema operacional é carregado para a conta de armazenamento, registre-lo como um **Disco de sistema operacional do Azure** para que você possa criar uma instância de máquina virtual dele. Use esses cmdlets do PowerShell para registrar seu VHD como um disco de sistema operacional do Azure. Fornece a URL completa contêiner onde VHD foi copiado para.

    Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copie e salve o nome desse novo disco de sistema operacional do Azure. No exemplo acima, há *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Dados de disco VHD sejam anexados a novas instâncias de máquina virtual do Azure

O disco de dados VHD uma vez carregado a conta de armazenamento, registre-lo como um disco de dados do Azure para que ele pode ser anexado a sua nova série DS, DSv2 série ou instância de máquina virtual do GS série Azure.

Use esses cmdlets do PowerShell para registrar seu VHD como um disco de dados do Azure. Fornece a URL completa contêiner onde VHD foi copiado para.

    Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copie e salve o nome desse novo disco de dados do Azure. No exemplo acima, há *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Criar uma máquina virtual de capacidade de armazenamento de Premium

Uma vez a imagem do sistema operacional ou disco do sistema operacional são registrados, crie uma nova série de DS, DSv2 série ou máquina virtual GS série. Você usará a imagem de sistema operacional ou o nome de disco do sistema operacional que você registrou. Selecione o tipo de máquina virtual do nível de armazenamento de Premium. No exemplo abaixo, estamos usando o tamanho de máquina virtual *Standard_DS2* .

>[AZURE.NOTE] Atualize o tamanho de disco para garantir que ela corresponda sua capacidade e requisitos de desempenho e os tamanhos de disco Azure disponível.

Siga cmdlets do PowerShell passo a passo abaixo para criar a máquina virtual novo. Primeiro, defina os parâmetros comuns:

    $serviceName = "yourVM"
    $location = "location-name" (e.g., West US)
    $vmSize ="Standard_DS2"
    $adminUser = "youradmin"
    $adminPassword = "yourpassword"
    $vmName ="yourVM"
    $vmSize = "Standard_DS2"

Primeiro, crie um serviço de nuvem no qual você hospedará suas novas VMs.

    New-AzureService -ServiceName $serviceName -Location $location

Em seguida, dependendo do cenário, crie a instância de máquina virtual do Azure do disco do sistema operacional que você registrou ou imagem do sistema operacional.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizado VHD do sistema operacional de criar várias instâncias de máquina virtual do Azure

Crie a um ou mais nova máquina virtual do DS série Azure instâncias usando a **Imagem de SO do Azure** que você registrou. Especifica esse nome de imagem do sistema operacional na configuração da máquina virtual ao criar nova VM conforme mostrado abaixo.

    $OSImage = Get-AzureVMImage –ImageName "OSImageName"

    $vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

    Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

    New-AzureVM -ServiceName $serviceName -VM $vm

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD de sistema operacional exclusivo para criar uma única instância de máquina virtual do Azure

Crie uma nova instância de máquina virtual do Azure de série DS usando o **Disco de sistema operacional do Azure** que você registrou. Especifica esse nome do disco do sistema operacional na configuração da máquina virtual ao criar a máquina virtual novo conforme mostrado abaixo.

    $OSDisk = Get-AzureDisk –DiskName "OSDisk"

    $vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

    New-AzureVM -ServiceName $serviceName –VM $vm

Especifique outras informações de máquina virtual do Azure, como um serviço de nuvem, região, conta de armazenamento, conjunto de disponibilidade e política de cache. Observe que a instância de máquina virtual deve ser localizada com o sistema operacional associado ou discos de dados, portanto, a conta de armazenamento, região e serviço de nuvem selecionado deve estar no mesmo local que os VHDs subjacentes desses discos.

### <a name="attach-data-disk"></a>Anexar o disco de dados

Por fim, se você tiver registrado dados disco VHDs, anexe-os para a nova Premium armazenamento capaz Azure máquina virtual.

Usar o seguinte cmdlet do PowerShell para anexar o disco de dados para a máquina virtual novo e especifique a política de cache. No exemplo abaixo, a política de cache está definida para *somente leitura*.

    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

    Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

    Update-AzureVM  -VM $vm

>[AZURE.NOTE] Pode haver outras etapas necessárias para dar suporte a seu aplicativo que não é cobertos por este guia.

### <a name="checking-and-plan-backup"></a>Verificar e planejar backup

Depois que a máquina virtual novo estiver em execução, acessá-la usando a mesma id de logon e senha é como a máquina virtual original e verifique se tudo está funcionando como esperado. Todas as configurações, incluindo os volumes distribuídos, estaria presentes na nova VM.

A última etapa é planejar backup e agendamento de manutenção para a máquina virtual novo com base nas necessidades do aplicativo.

### <a name="a-sample-migration-script"></a>Um exemplo de script de migração

Se você tiver várias VMs para migrar, automação através de scripts do PowerShell será útil. A seguir é um exemplo de script que automatiza a migração de uma máquina virtual. Observe que abaixo script é apenas um exemplo e há algumas suposições feitas sobre os discos de máquina virtual atuais. Talvez você precise atualizar o script para fazer a correspondência com a sua situação específica.

As suposições são:

- Você está criando VMs clássico do Azure.
- Seus discos de sistema operacional de origem e fonte dados estão na mesma conta de armazenamento e mesmo contêiner. Se seu sistema operacional discos e dados não estiverem no mesmo lugar, você pode usar AzCopy ou Azure PowerShell para copiar VHDs pela contêineres e contas de armazenamento. Consulte a etapa anterior: [VHD de cópia com AzCopy ou PowerShell](#copy-vhd-with-azcopy-or-powershell). Esse script para atender seu cenário de edição é outra opção, mas recomendamos usando AzCopy ou PowerShell desde que é mais fácil e rápido.

O script de automação é fornecido abaixo. Substituir texto com suas informações e atualize o script para fazer a correspondência com a sua situação específica.

>[AZURE.NOTE] Usando o script existente não preserva a configuração de rede de sua fonte de máquina virtual. Você precisará reconfigurar as configurações de rede em seu VMs migradas.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

#### <a name="optimization"></a>Otimização

A configuração de máquina virtual atual pode ser personalizada especificamente para funcionar bem com discos padrão. Por exemplo, para aumentar o desempenho usando muitos discos em um volume distribuído. Por exemplo, em vez de usar 4 discos separadamente armazenamento Premium, você poderá otimizar o custo fazendo com que um único disco. Otimizações como esta necessidade de ser tratado em uma base caso e exigem etapas personalizadas após a migração. Além disso, observe que esse processo não pode funcionar bem para bancos de dados e aplicativos que dependem do layout de disco definido na configuração.

##### <a name="preparation"></a>Preparação

1.  Conclua a migração simples, conforme descrito na seção anterior. Otimizações serão executadas na nova VM após a migração.
2.  Defina os novos tamanhos de disco necessários para a configuração otimizada.
3.  Determine o mapeamento dos discos/volumes atuais para as novas especificações de disco.

##### <a name="execution-steps"></a>Etapas de execução

1.  Crie novos discos com os tamanhos direita na VM armazenamento Premium.
2.  Faça login a máquina virtual e copiar os dados do volume atual para o novo disco que mapeia nesse volume. Faça isso para todos os volumes atuais que precisam ser mapeados para um novo disco.
3.  Em seguida, altere as configurações do aplicativo para alternar para os novos discos e desanexar volumes antigos.

Para ajustar o aplicativo para melhorar o desempenho de disco, consulte [Otimizando o desempenho do aplicativo](storage-premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrações de aplicativos

Bancos de dados e outros aplicativos complexos podem exigir etapas especiais, conforme definido pelo provedor do aplicativo para a migração. Consulte a documentação do respectivo aplicativo. Por exemplo Normalmente os bancos de dados podem ser migrados por meio de backup e restauração.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes recursos para cenários específicos para migrar máquinas virtuais:

- [Migrar máquinas virtuais Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Criar e carregar um VHD de servidor do Windows Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md)
- [Criar e carregar um disco rígido Virtual que contém o sistema operacional Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md)
- [Migrando máquinas virtuais do Amazon AWS Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Além disso, consulte os seguintes recursos para saber mais sobre o armazenamento do Azure e máquinas virtuais do Azure:

- [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Azure máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Armazenamento de Premium: Armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
