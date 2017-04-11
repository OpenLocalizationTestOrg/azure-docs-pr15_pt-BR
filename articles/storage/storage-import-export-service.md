<properties
    pageTitle="Usando importação/exportação para transferir dados para armazenamento de Blob | Microsoft Azure"
    description="Aprenda a criar importar e exportar trabalhos no portal do Azure clássico para transferir dados para armazenamento de blob."
    authors="renashahmsft"
    manager="aungoo"
    editor="tysonn"
    services="storage"
    documentationCenter=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="renash"/>


# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-blob-storage"></a>Usar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de Blob

## <a name="overview"></a>Visão geral

Serviço de importação/exportação Azure permite transferir grandes quantidades de dados com segurança ao Azure blob storage por remessa unidades de disco rígido a um centro de dados do Azure. Você também pode usar esse serviço para transferir dados de armazenamento de blob do Microsoft Azure para unidades de disco rígido e enviar para o seu site local. Esse serviço é adequado em situações em que você deseja transferir várias TB de dados ou do Azure, mas carregando ou baixando pela rede não é viável devido a largura de banda limitada ou custos de rede alto.

O serviço requer que unidades de disco rígido deve ser Bloqueador de bit criptografada para a segurança dos seus dados. O serviço suporta contas de armazenamento clássico presentes em todas as regiões do Azure público. Você deve fornecer unidades de disco rígido para um dos locais com suporte especificados mais adiante neste artigo.

Neste artigo, você aprenderá mais sobre o serviço de importação/exportação do Azure e como enviar unidades para copiar os dados para e do armazenamento de Blob do Azure.

> [AZURE.IMPORTANT] Você pode criar e gerenciar a importação e exportação trabalhos para armazenamento clássico usando o portal de clássico ou o [serviço de importação/exportação APIs REST](http://go.microsoft.com/fwlink/?LinkID=329099). Contas de armazenamento do Gerenciador de recursos não são suportadas no momento.

## <a name="when-should-i-use-the-azure-importexport-service"></a>Quando devo usar o serviço de importação/exportação do Azure?

Você pode considerar usando o serviço de importação/exportação do Azure ao carregar ou baixar dados através da rede estiver lenta ou obter largura de banda de rede adicionais é dispendioso.

Você pode usar esse serviço em cenários como:

- Migração de dados para a nuvem: mover grandes quantidades de dados no Azure rapidamente e econômica.
- Distribuição de conteúdo: enviar dados rapidamente aos seus sites de cliente.
- Backup: Fazer backups dos seus dados no local para armazenar em armazenamento de blob do Microsoft Azure.
- Recuperação de dados: recuperar grande quantidade de dados armazenados em armazenamento de blob e que seja entregue em seu local.

## <a name="pre-requisites"></a>Pré-requisitos

Nesta seção, podemos tenha listado os pré-requisitos necessários para usar esse serviço. Examine-las cuidadosamente antes de entregar as unidades.

### <a name="storage-account"></a>Conta de armazenamento

Você deve ter uma assinatura existente do Azure e uma ou mais contas de armazenamento **clássico** para usar o serviço de importação/exportação. Cada trabalho pode ser usado para transferir dados de ou para apenas uma conta de armazenamento clássico. Em outras palavras, um trabalho de importação/exportação único não pode abranger em várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, veja [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

### <a name="blob-types"></a>Tipos de blob

Você pode usar o serviço de importação/exportação do Azure para copiar dados para blobs de **bloco** ou blobs de **página** . Por outro lado, você só pode exportar **bloco** blobs, blobs de **página** ou **Acrescentar** blobs de armazenamento do Azure usando esse serviço.

### <a name="job"></a>Trabalho

Para começar o processo de importação para ou exportando do armazenamento de Blob, você primeiro criar um trabalho. Um trabalho pode ser um trabalho de importação ou um trabalho de exportação:

- Crie um trabalho de importação quando você deseja transferir dados locais para blobs em sua conta de armazenamento do Azure.
- Crie um trabalho de exportação quando você deseja transferir dados atualmente armazenados como blobs em sua conta de armazenamento para unidades de disco rígido que são fornecidos a você.

Quando você cria um trabalho, notifique o serviço de importação/exportação que você irá enviando uma ou mais unidades de disco rígido a um centro de dados do Azure.

- Para um trabalho de importação, você irá entrega unidades de disco rígido que contenha seus dados.
- Para um trabalho de exportação, você irá entrega unidades de disco rígido vazias.
- Você pode fornecer até 10 unidades de disco rígido por trabalho.

Você pode criar uma importação ou exportação de trabalho usando o [portal de clássico](https://manage.windowsazure.com/) ou a [API REST do Azure armazenamento importar/exportar](http://go.microsoft.com/fwlink/?LinkID=329099).

### <a name="client-tool"></a>Ferramenta de cliente

A primeira etapa na criação de um trabalho **Importar** é preparar sua unidade que será enviada para importação. Para preparar sua unidade, você deve conectá-lo em um servidor local e executar a ferramenta de cliente de importação/exportação do Azure no servidor local. Esta ferramenta de cliente facilita a cópia seus dados para a unidade, criptografando os dados na unidade com BitLocker e gerar os arquivos de diário de unidade.

Os arquivos de diário armazenam informações básicas sobre seu trabalho e a unidade como número de série de unidade e nome da conta de armazenamento. Este arquivo de diário não é armazenado na unidade. Ele é usado durante a criação do trabalho de importação. Passo a passo detalhes sobre a criação de trabalho são fornecidos posteriormente neste artigo.

A ferramenta cliente só é compatível com o sistema de operacional do Windows de 64 bits. Consulte a seção de [sistema operacional](#operating-system) para versões específicas do sistema operacional com suporte.

Baixe a versão mais recente da [ferramenta de cliente do Azure importar/exportar](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Para obter mais detalhes sobre como usar a ferramenta de importação/exportação do Azure, consulte a [Referência de ferramenta de importação/exportação do Azure](http://go.microsoft.com/fwlink/?LinkId=329032).

### <a name="hard-disk-drives"></a>Unidades de disco rígido

Somente 3,5 polegadas unidades de disco rígido interno SATA II/III são suportadas para uso com o serviço de importação/exportação. Você pode usar unidades de disco rígido até 10TB.
Para trabalhos de importação, apenas o primeiro volume de dados na unidade será processado. O volume de dados deve ser formatado com NTFS.
Ao copiar dados em seu disco rígido, você pode anexá-lo diretamente usando um conector SATA ou você pode anexá-lo externamente usando um adaptador SATA II/III USB externo. Recomendamos usando um dos seguintes adaptadores de SATA II/III USB externos:

- Anker 68UPSATAA - 02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Se você tiver um conversor que não estiver listado acima, você pode tentar executar a ferramenta de importação/exportação do Azure usando seu conversor para preparar a unidade e verifique se ele funciona antes de adquirir um conversor de compatível.

> [AZURE.IMPORTANT] Unidades de disco rígido externas que vêm com um adaptador USB interno não são suportadas por esse serviço. Além disso, o disco interno a capitalização de uma unidade de disco rígido externa não pode ser usado; Não envie unidades de disco rígido externas.

### <a name="encryption"></a>Criptografia

Os dados na unidade devem ser criptografados usando criptografia de unidade BitLocker. Isso protege os dados enquanto ela estiver em trânsito.

Para trabalhos de importação, há duas maneiras para realizar a criptografia. A primeira forma é usar o / criptografar parâmetro ao executar a ferramenta de cliente durante a preparação de unidade. A segunda forma é ativar manualmente na unidade de disco BitLocker e especificar a chave de criptografia na linha de comando de ferramentas de cliente durante a preparação de unidade.

Para trabalhos de exportação, depois de seus dados são copiados para as unidades, o serviço irá criptografar a unidade usando o BitLocker antes da entrega para você. A chave de criptografia será fornecida a você através do portal de clássico.  

### <a name="operating-system"></a>Sistema Operacional

Você pode usar um dos seguintes sistemas operacionais de 64 bits para preparar o disco rígido usando a ferramenta de importação/exportação do Azure antes da entrega a unidade Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, empresarial do Windows 8, Windows 8.1 Pro, empresarial do Windows 8.1, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Todos esses sistemas operacionais oferecem suporte a criptografia de unidade de disco BitLocker.

> [AZURE.IMPORTANT] <sup>1</sup> Se você estiver usando um computador Windows 10 para preparar seu disco rígido, baixe a versão mais recente da ferramenta de importação/exportação do Azure.

### <a name="locations"></a>Locais

O serviço de importação/exportação do Azure suporta copiando dados de e para todas as contas de armazenamento do Azure público. Você pode entregar unidades de disco rígido para um dos seguintes locais. Se sua conta de armazenamento estiver em um local de Azure público que não é especificado aqui, um local alternativo de remessa será fornecido quando você estiver criando o trabalho usando o portal de clássico ou a API REST de importação/exportação.

Há suporte para locais de remessas:

- Leste EUA

- Oeste EUA

- Leste dos EUA 2

- Centro dos EUA

- Centro Norte dos EUA

- Centro Sul dos EUA

- Norte da Europa

- Europa Ocidental

- Da Ásia Oriental

- Sudeste Asiático

- Austrália Oriental

- Austrália Sudeste

- Japão Oeste

- Japão Leste

- Índia central


### <a name="shipping"></a>Remessa

**Unidades de remessa ao data center:**

Ao criar um trabalho de importação ou exportação, será fornecido um endereço de remessa de um dos locais com suporte para enviar as unidades. O endereço de remessa fornecido dependerá a localização da sua conta de armazenamento, mas não pode ser o mesmo que seu local de conta de armazenamento.

Você pode usar transportadoras como FedEx, DHL, UPS ou o serviço Postal para enviar suas unidades para o endereço de entrega.

**Unidades de remessa do data center:**

Ao criar um trabalho de importação ou exportação, você deve fornecer um endereço de retorno para a Microsoft usar quando as unidades de remessa depois de seu trabalho for concluído. Certifique-se de que você forneça um endereço de retorno válido para evitar atrasos no processamento.

Você também deve fornecer uma operadora de FedEx ou DHL válida conta o número a ser usado pela Microsoft para entregar as unidades de volta. Um número de conta FedEx é necessário para unidades de remessa volta dos Estados Unidos e Europa locais. Um número de conta DHL é necessário para unidades de remessa volta de locais da Ásia e Austrália. Você pode criar uma conta de transportadora [DHL](http://www.dhl.com/) (Ásia e Austrália) de [FedEx](http://www.fedex.com/us/oadr/) (para EUA e Europa) ou se não tiver um. Se você já tiver um número de conta de transportadora, verifique se ele está válido.

Em seus pacotes de remessa, você deve seguir os termos em [Termos de serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Observe que a mídia física que você estiver enviando talvez precise cross fronteiras internacionais. Você é responsável por garantir que sua mídia física e os dados são importados e/ou exportados de acordo com as leis aplicáveis. Antes da remessa mídia física, verifique com seu supervisores para verificar se sua mídia e dados legalmente podem ser enviados para o Centro de dados identificados. Isso ajudará a garantir que ele chegue Microsoft em tempo hábil. Por exemplo, qualquer pacote que será cross fronteiras internacionais precisa de uma fatura comercial para ser acompanhados com o pacote (exceto se interseção bordas dentro de União Europeia). Você pode imprimir uma cópia preenchida da fatura comercial do site da transportadora. Exemplo de comercial fatura [Fatura comercial FedEx](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf)ou são [DHL fatura comercial] (http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf). Certifique-se de que a Microsoft não foi indicada como o exportador.

## <a name="how-does-the-azure-importexport-service-work"></a>Como funciona o serviço de importação/exportação do Azure?

Você pode transferir dados entre o seu site local e o armazenamento de blob do Microsoft Azure usando o serviço de importação/exportação do Azure Criando trabalhos e unidades de disco rígido para uma central de dados Azure de remessa. Cada unidade de disco rígido que entregar está associada um único trabalho. Cada trabalho está associado uma conta de armazenamento único. Examine a [seção de pré-requisitos](#pre-requisites) cuidadosamente para saber mais sobre as especificações desse tipos de blob do serviço como suportado, tipos de disco, locais e remessa.

Nesta seção, podemos descreverá em um alto nível as etapas envolvidas na importação e exportação de trabalhos. Mais tarde na [seção início rápido](#quick-start), forneceremos instruções passo a passo para criar uma importação e exportação de trabalho.

### <a name="inside-an-import-job"></a>Dentro de um trabalho de importação

Em um alto nível, um trabalho de importação envolve as seguintes etapas:

- Determine os dados a serem importados e o número de unidades que será necessário.
- Identifique os blobs de destino para os seus dados no armazenamento de Blob.
- Use a ferramenta de importação/exportação do Azure para copiar os dados para uma ou mais unidades de disco rígido e criptografá-las com BitLocker.  
- Crie um trabalho de importação na sua conta de armazenamento clássico de destino usando o portal de clássico ou a API REST de importação/exportação. Se usando o portal de clássico, carregue os arquivos de diário de unidade.
- Forneça o endereço do remetente e o número de conta de transportadora a ser usado para as unidades de remessa para você.
- Envie as unidades de disco rígido para o endereço de entrega fornecidas durante a criação de trabalho.
- Atualizar a número nos detalhes de trabalho de importação de acompanhamento de entrega e enviar o trabalho de importação.
- Unidades são recebidas e processadas no Azure data center.
- Unidades são fornecidas usando sua conta de transportadora para o endereço de retorno fornecido no trabalho de importação.

    ![Figura 1:Import fluxo de trabalho](./media/storage-import-export-service/importjob.png)


### <a name="inside-an-export-job"></a>Dentro de um trabalho de exportação

Em um alto nível, um trabalho de exportação envolve as seguintes etapas:

- Determine os dados a ser exportado e o número de unidades que será necessário.
- Identifique os caminhos de contêiner dos seus dados no armazenamento de Blob ou blobs de origem.
- Crie um trabalho de exportação na sua conta de armazenamento de origem usando o portal de clássico ou a API REST de importação/exportação.
- Especifique os blobs de origem ou caminhos de contêiner dos seus dados no trabalho de exportação.
- Forneça o endereço do remetente e número de conta de transportadora para a ser usado para as unidades de remessa para você.
- Envie as unidades de disco rígido para o endereço de entrega fornecidas durante a criação de trabalho.
- Atualizar a número nos detalhes de trabalho de exportação de acompanhamento de entrega e enviar o trabalho de exportação.
- As unidades são recebidas e processadas no Azure data center.
- As unidades são criptografadas com BitLocker; as teclas estão disponíveis através do portal de clássico.  
- As unidades são fornecidas usando sua conta de transportadora para o endereço de retorno fornecido no trabalho de importação.

    ![Figura 2:Export fluxo de trabalho](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-status"></a>Exibir o status de trabalho

Você pode acompanhar o status da importação ou exportação trabalhos a partir do portal de clássico. Navegue até sua conta de armazenamento no portal do clássico e clique na guia de **Importação/exportação** . Uma lista dos seus trabalhos aparecerão na página. Você pode filtrar a lista no status do trabalho, o nome de trabalho, o tipo de trabalho ou o número de controle.

Você verá um dos seguintes status de trabalho dependendo de onde sua unidade está no processo.

| Status do trabalho   | Descrição                                                                                                                                                                                                                                                                                                                                                                        |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Criando     | Seu trabalho foi criado, mas você ainda não forneceu suas informações de entrega.                                                                                                                                                                                                                                                                                                |
| Remessa     | Seu trabalho foi criado e você tiver fornecido as informações de entrega. **Observação**: quando a unidade for entregue para o Centro de dados do Azure, o status ainda pode mostrar "Remessa" por algum tempo. Depois que o serviço começa a copiar seus dados, o status será alterado para "Transferir". Se você quiser ver mais específicas status da sua unidade, você pode usar a API REST de importação/exportação. |
| Transferindo | Seus dados estão sendo transferidos do disco rígido (para um trabalho de importação) ou em seu disco rígido (para uma tarefa de exportação).                                                                                                                                                                                                                                                                 |
| Embalagem    | A transferência de dados é concluída e seu disco rígido está sendo preparado para envio de volta para você.                                                                                                                                                                                                                                                                             |
| Concluir     | Seu disco rígido tiver sido enviado para você.                                                                                                                                                                                                                                                                                                                                      |

### <a name="time-to-process-job"></a>Tempo de trabalho de processo

A quantidade de tempo necessário para processar um trabalho de importação/exportação varia dependendo de fatores diferentes como hora de entrega, tipo, tipo e tamanho dos dados sendo copiados e o tamanho dos discos fornecidos de trabalho. O serviço de importação/exportação não tem um SLA. Você pode usar a API REST para controlar o andamento do trabalho mais detalhadamente. Não há um parâmetro completo porcentagem na operação de trabalhos de lista que fornece uma indicação de progresso de cópia. Comunique-se conosco se precisar de uma estimativa para concluir um trabalho de importação/exportação críticas de tempo.

### <a name="pricing"></a>Preços

**Taxa de tratamento de unidade**

Há uma taxa de manipulação de unidade para cada unidade processada como parte de sua importação ou exportação de trabalho. Consulte os detalhes sobre os [Preços de importação/exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Custos de remessa**

Quando você enviar unidades no Azure, você pagar o custo de remessa para a transportadora. Quando Microsoft retorna as unidades para você, o custo de remessa será cobrado a conta de transportadora que você forneceu no momento da criação de trabalho.

**Custos de transação**

Não há nenhum custos de transação ao importar dados para o armazenamento de blob. Os encargos de egresso padrão são aplicáveis quando dados exportados do armazenamento de blob. Para obter mais detalhes sobre os custos de transação, consulte [preços de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="quick-start"></a>Início rápido

Nesta seção, fornecemos instruções passo a passo para criar uma importação e um trabalho de exportação. Verifique se que você atender a todos os [pré-requisitos](#pre-requisites) antes de Avançar.

## <a name="how-to-create-an-import-job"></a>Como criar um trabalho de importação?

Crie um trabalho de importação para copiar dados para sua conta de armazenamento do Azure de unidades de disco rígido, fornecendo uma ou mais unidades que contém os dados para o Centro de dados especificada. O trabalho de importação transmita detalhes sobre unidades de disco rígido, dados a ser copiado, direcionar conta de armazenamento e informações de remessa para o serviço de importação/exportação do Azure. Criar um trabalho de importação é um processo de três etapas. Primeiro, prepare as unidades usando a ferramenta de cliente do Azure importar/exportar. Segundo, envie um trabalho de importação usando o portal de clássico. Terceiro, enviar as unidades para o endereço de entrega fornecidas durante a criação de trabalho e atualizar as informações de remessa em detalhes do seu trabalho.   

> [AZURE.IMPORTANT] Você pode enviar somente um trabalho por conta de armazenamento. Cada unidade que entregar pode ser importada para uma conta de armazenamento. Por exemplo, digamos que você deseja importar dados para duas contas de armazenamento. Você deve usar unidades de disco rígido separadas para cada conta de armazenamento e criar trabalhos separados por conta de armazenamento.

### <a name="prepare-your-drives"></a>Preparar as unidades

A primeira etapa ao importar dados usando o serviço de importação/exportação do Azure é preparar as unidades usando a ferramenta de cliente do Azure importar/exportar. Siga as etapas abaixo para preparar as unidades.

1.  Identifica os dados a serem importados. Isso pode ser diretórios e arquivos de autônomo no servidor local ou em um compartilhamento de rede.  

2.  Determine o número de unidades, que você precisará dependendo do tamanho total dos dados. Adquirir o número necessário de unidades de disco de rígido 3,5 polegadas SATA II/III.

3.  Identificar a conta de armazenamento de destino, contêiner, diretórios virtuais e blobs.

4.  Determine a diretórios e/ou arquivos autônomo que serão copiados para cada unidade de disco rígido.

5.  Use a [Ferramenta de importação/exportação do Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) para copiar os dados para uma ou mais unidades de disco rígido.

    - A ferramenta de importação/exportação do Azure cria sessões de cópia para copiar os dados de origem para as unidades de disco rígido. Em uma sessão única cópia, a ferramenta pode copiar um único diretório juntamente com seus subdiretórios ou um único arquivo.

    - Talvez você precise copiar várias sessões se seus dados de origem abrangem várias pastas.

    - Cada unidade de disco rígido que preparar exigirão sessão de pelo menos uma cópia.

6.  Você pode especificar o / criptografar parâmetro para ativar a criptografia Bitlocker na unidade de disco rígido. Alternativamente, você também pode habilitar a criptografia de Bitlocker manualmente na unidade de disco rígido e fornecer a chave ao executar a ferramenta.

7.  A ferramenta de importação/exportação do Azure gera um arquivo de diário de unidade para cada unidade como ele está preparado. O arquivo de diário de unidade está armazenado no computador local, não na própria unidade. Você irá carregar o arquivo de diário quando você cria o trabalho de importação. Um arquivo de diário de unidade inclui a ID de unidade e a chave do BitLocker, bem como outras informações sobre a unidade.
**Importante**: cada unidade de disco rígido preparar resultará em um arquivo de diário. Quando você estiver criando o trabalho de importação usando o portal de clássico, você deve carregar todos os arquivos de diário das unidades que fazem parte do trabalho importação. Unidades sem diário arquivos não serão processados.

8.  Não modifique os dados em unidades de disco rígido ou o arquivo de diário após concluir a preparação do disco.

Abaixo estão os comandos e exemplos para preparar o disco rígido usando a ferramenta de cliente do Azure importar/exportar.

Azure importação/exportação cliente ferramenta PrepImport comando para a primeira sessão de copiar copiar um diretório:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Exemplo:**

No exemplo abaixo, estamos está copiando todos os arquivos e sub diretórios de H:\Video a unidade de disco rígido montado em x. Os dados serão importados para a conta de armazenamento de destino especificada pela chave da conta de armazenamento e para o contêiner de armazenamento chamado de vídeo. Se o contêiner de armazenamento não estiver presente, ele será criado. Este comando também formatar e criptografar o disco rígido de destino.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

Azure importação/exportação cliente ferramenta PrepImport comando para sessões de cópia subsequentes copiar um diretório:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Para sessões de cópia subsequentes para a mesma unidade de disco rígido, especifique o mesmo nome de arquivo de diário e forneça uma nova identificação de sessão; não é necessário para fornecer a unidade de armazenamento conta chave e destino novamente, nem para formatar ou criptografar a unidade. Neste exemplo podemos estiver copiando a pasta de H:\Photo e seus subdiretórios para a mesma unidade de destino, para o contêiner de armazenamento chamado de foto.

    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

A ferramenta de cliente de importação/exportação do Azure PrepImport comando para a primeira sessão de copiar copiar um arquivo:

    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

A ferramenta de cliente de importação/exportação do Azure PrepImport comando para sessões de cópia subsequentes copiar um arquivo:

    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Lembre-se**: por padrão, os dados serão importados como Blobs de bloco. Você pode usar o parâmetro /BlobType para importar dados como uma Blobs de página. Por exemplo, se você estiver importando arquivos VHD que serão montados como discos em uma máquina virtual do Azure, você deve importá-los como Blobs de página. Se você não tiver certeza de qual blob tipo para usar, você pode especificar /blobType:auto e podemos ajudará a determinar o tipo certo. Nesse caso, todos os arquivos vhd e vhdx serão importados como Blobs de página e o restante será importado como Blobs de bloco.

Ver mais detalhes sobre como usar a ferramenta de cliente do Azure importação/exportação no [Preparando unidades de disco rígido para importação](https://msdn.microsoft.com/library/dn529089.aspx).

Além disso, consulte o [Fluxo de trabalho de amostra para preparar unidades de disco rígido para um trabalho de importação](https://msdn.microsoft.com/library/dn529097.aspx) para obter instruções detalhadas.  

### <a name="create-the-import-job"></a>Criar o trabalho de importação

1.  Depois de preparar sua unidade, navegue até sua conta de armazenamento no [portal de clássico](https://manage.windowsazure.com) e visualizar o painel. Em **Resumo rápido de geral**, clique em **criar um trabalho de importação**. Examine as etapas e marque a caixa de seleção para indicar que você tenha preparado sua unidade e que você tenha o arquivo de diário de unidade disponível.

2.  Na etapa 1, forneça informações de contato para a pessoa responsável por esse trabalho de importação e um endereço de retorno válido. Se desejar salvar os dados de log detalhado para o trabalho de importação, marque a opção para **Salvar o log detalhado em meu contêiner de blob 'waimportexport'**.

3.  Na etapa 2, carregue os arquivos de diário de unidade obtido durante a etapa de preparação de unidade. Você precisará carregar um arquivo para cada unidade preparada por você.

    ![Criar trabalho de importação - etapa 3](./media/storage-import-export-service/import-job-03.png)

4.  Na etapa 3, insira um nome descritivo para o trabalho de importação. Observe que o nome inserido pode conter apenas letras minúsculas, números, hifens e sublinhados, deve começar com uma letra e não pode conter espaços. Você usará o nome que você escolhe para acompanhar os trabalhos enquanto estiverem em andamento e depois que eles são concluídos.

    Em seguida, selecione sua região do Centro de dados da lista. A região do Centro de dados indicará o Centro de dados e o endereço para o qual você deve enviar seu pacote. Consulte as perguntas Frequentes abaixo para obter mais informações.

5.  Na etapa 4, selecione sua operadora de retorno da lista e digite o número da sua conta de transportadora. Microsoft usará essa conta para enviar as unidades para você quando seu trabalho de importação for concluído.

    Se você tiver o número de controle, selecione sua operadora de entrega na lista e insira seu número de controle.

    Se você não tiver um número de controle ainda, escolha **eu fornecerá minhas informações de remessas para este trabalho de importação assim que eu tenha enviado meu pacote**, conclua o processo de importação.

6. Para digitar o número de acompanhamento após você ter enviado seu pacote, volte para a página de **Importação/exportação** para sua conta de armazenamento no portal do clássico, selecione o seu trabalho na lista e escolha **Informações de entrega**. Navegue pelo assistente e insira seu número de controle na etapa 2.

    Se o número de controle não é atualizado em 2 semanas de criação do trabalho, o trabalho irá expirar.

    Se o trabalho estiver no estado criando, remessa ou transferindo, você também pode atualizar seu número de conta de transportadora na etapa 2 do assistente. Depois que o trabalho estiver no estado de embalagem, você não pode atualizar seu número de conta de transportadora para trabalho.

7. Você pode controlar o andamento do trabalho no painel portal. Veja o que significa que cada estado de trabalho na seção anterior exibindo [seu status de trabalho](#viewing-your-job-status).

## <a name="how-to-create-an-export-job"></a>Como criar um trabalho de exportação?

Crie um trabalho de exportação para notificar o serviço de importação/exportação que você vai ser enviando uma ou mais unidades vazias para o Centro de dados para que os dados podem ser exportados de sua conta de armazenamento para as unidades e as unidades e depois enviados a você.

### <a name="prepare-your-drives"></a>Preparar as unidades

Pré-verificações a seguir são recomendadas para preparar as unidades para um trabalho de exportação:

1. Verificar o número de discos necessários usando o comando de PreviewExport da ferramenta Azure importar/exportar. Para obter mais informações, consulte [Visualizar o uso de unidade para um trabalho de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). Ele ajuda você a visualizar o uso da unidade para os blobs selecionado, com base no tamanho das unidades que você vai usar.

2. Verifique se você pode leitura/gravação no disco rígido que será enviada para o trabalho de exportação.

### <a name="create-the-export-job"></a>Criar o trabalho de exportação

1.  Para criar um trabalho de exportação, navegue até sua conta de armazenamento no [portal de clássico](https://manage.windowsazure.com)e visualizar o painel. Em **Resumo rápido de geral**, clique em **criar um trabalho de exportação** e prossiga no assistente.

2.  Na etapa 2, forneça informações de contato para a pessoa responsável por esse trabalho de exportação. Se desejar salvar os dados de log detalhado para o trabalho de exportação, marque a opção para **Salvar o log detalhado meu contêiner de blob 'waimportexport'**.

3.  Na etapa 3, especifique quais dados de blob que deseja exportar de sua conta de armazenamento para sua unidade em branco ou unidades. Você pode optar por exportar todos os dados de blob na conta de armazenamento, ou você pode especificar qual blobs ou conjuntos de blobs para exportar.

    Para especificar um blob para exportar, use o seletor **Igual a** e especifique o caminho relativo para o blob, começando com o nome do contêiner. Use *$root* para especificar o recipiente raiz.

    Para especificar todos os blobs começando com um prefixo, use o seletor **Começa com** e especifique o prefixo, começando com uma barra '/'. O prefixo pode ser o prefixo do nome do contêiner, o nome de recipiente completo ou o nome de recipiente completo seguido o prefixo do nome do blob.

    A tabela a seguir mostra exemplos de caminhos de blob válidos:

    Seletor|Caminho de blob|Descrição
    ---|---|---
    Começa com|/|Exporta todos os blobs na conta de armazenamento
    Começa com|/$root /|Exporta todos os blobs no recipiente raiz
    Começa com|/Book|Exporta todos os blobs em qualquer recipiente que começa com o prefixo de **catálogo**
    Começa com|/Music/|Exporta todos os blobs no contêiner **música**
    Começa com|/ música/amor|Exporta todas as bolhas no contêiner **música** que começam com o prefixo **adora**
    Igual a|$root/logo.bmp|Exportações blob **logo.bmp** no recipiente raiz
    Igual a|videos/Story.mp4|Exportações blob **story.mp4** contêiner **vídeos**

    Você deve fornecer os caminhos de blob em formatos válidos para evitar erros durante o processamento, conforme mostrado nesta captura de tela.

    ![Criar trabalho de exportação - etapa 3](./media/storage-import-export-service/export-job-03.png)


4.  Na etapa 4, insira um nome descritivo para o trabalho de exportação. O nome que você insira pode conter apenas letras minúsculas, números, hifens e sublinhados, deve começar com uma letra e não pode conter espaços.

    A região do Centro de dados indicará o Centro de dados à qual você deve enviar seu pacote. Consulte as perguntas Frequentes abaixo para obter mais informações.

5.  Na etapa 5, selecione sua operadora de retorno da lista e digite o número da sua conta de transportadora. Microsoft usará essa conta para enviar as unidades de volta para você quando seu trabalho de exportação estiver concluído.

    Se você tiver o número de controle, selecione sua operadora de entrega na lista e insira seu número de controle.

    Se você não tiver um número de controle ainda, escolha **eu fornecerá minhas informações de remessas para este trabalho de exportação assim que eu tenha enviado meu pacote**, conclua o processo de exportação.

6. Para digitar o número de acompanhamento após você ter enviado seu pacote, volte para a página de **Importação/exportação** para sua conta de armazenamento no portal do clássico, selecione o seu trabalho na lista e escolha **Informações de entrega**. Navegue pelo assistente e insira seu número de controle na etapa 2.

    Se o número de controle não é atualizado em 2 semanas de criação do trabalho, o trabalho irá expirar.

    Se o trabalho estiver no estado criando, remessa ou transferindo, você também pode atualizar seu número de conta de transportadora na etapa 2 do assistente. Depois que o trabalho estiver no estado de embalagem, você não pode atualizar seu número de conta de transportadora para trabalho.

    > [AZURE.NOTE] Se o blob a ser exportado estiver em uso no momento da cópia no disco rígido, serviço de importação/exportação do Azure tirar um instantâneo do blob e copiar o instantâneo.

7.  Você pode controlar o andamento do trabalho no painel no portal do clássico. Ver o que significa cada estado de trabalho na seção anterior em "exibindo seu status de trabalho".

8.  Depois de receber as unidades com seus dados exportados, você pode exibir e copiar as chaves do BitLocker geradas pelo serviço por sua unidade. Navegue até sua conta de armazenamento no portal do clássico e clique na guia de importação/exportação. Selecione o seu trabalho de exportação na lista e clique no botão Exibir chaves. As chaves do BitLocker aparecem conforme mostrado abaixo:

    ![Exibir chaves de BitLocker para o trabalho de exportação](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Percorra a seção Perguntas Frequentes abaixo pois abrange as perguntas mais comuns que os clientes encontram ao usar esse serviço.

## <a name="frequently-asked-questions"></a>Perguntas frequentes ##


**Quanto tempo levará para copiar meus dados após minha unidades atinge o Centro de dados?**

O tempo de cópia varia dependendo da diferentes fatores como o tipo de trabalho, tipo e o tamanho dos dados sendo copiados, o tamanho dos discos fornecido e existente a carga de trabalho. Ele pode variar de alguns dias para algumas semanas, dependendo desses fatores. Portanto, é difícil fornecer uma estimativa geral. O serviço tenta otimizar seu trabalho copiando várias unidades em paralelo quando possível. Se você tiver um trabalho de importação/exportação críticas de tempo, comunique-se com uma estimativa.

**Quando devo usar o serviço de importação/exportação do Azure?**
Um considere usando Azure importar exportar se upload ou download em rede leva aproximadamente estimar mais de 7 dias. Você pode calcular quanto tempo levará usando qualquer Calculadora online ou [baixando](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/archive/master.zip) aquele localizado no nosso Azure importar exportar REST API amostra no repositório de amostras Azure em [Calculadora de velocidade de transferência de dados](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). Isso não é um cálculo exato, mas somente uma indicação estimada.

**Pode usar o serviço de importação/exportação do Azure com uma conta de armazenamento do Gerenciador de recursos?**

Não, você não pode copiar dados de ou para uma conta de armazenamento do Gerenciador de recursos diretamente usando o serviço de importação/exportação do Azure. O serviço só oferece suporte a contas de armazenamento clássico. Suporte de conta de armazenamento do Gerenciador de recursos estarão em breve. Como alternativa, você pode importar dados para uma conta de armazenamento clássico e migrados para sua conta de armazenamento do Gerenciador de recursos usando [AzCopy](storage-use-azcopy.md) ou CopyBlob.

**Posso copiar arquivos do Azure usando o serviço de importação/exportação do Azure?**

Não, o serviço de importação/exportação do Azure suporta apenas Blobs de bloco e Blobs de página. Todos os outros tipos de armazenamento, incluindo arquivos do Azure, tabelas e filas não são suportados.

**O serviço de importação/exportação do Azure está disponível para assinaturas de CSP?**

Não, o serviço de importação/exportação do Azure não oferece suporte para assinaturas do CSP. O suporte será adicionado no futuro.

**Posso pode ignorar a etapa de preparação de unidade para um trabalho de importação ou para preparar uma unidade sem copiar?**

Qualquer unidade que você deseja enviar para importação de dados deve ser preparada usando a ferramenta de cliente do Azure importar/exportar. Você deve usar a ferramenta cliente para copiar dados para a unidade.

**Eu preciso para executar qualquer preparação do disco ao criar um trabalho de exportação?**

Pré-verificações de sem, mas alguns são recomendadas. Verificar o número de discos necessários usando o comando de PreviewExport da ferramenta Azure importar/exportar. Para obter mais informações, consulte [Visualizar o uso de unidade para um trabalho de exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). Ele ajuda você a visualizar o uso da unidade para os blobs selecionado, com base no tamanho das unidades que você vai usar. Também verificar que você pode ler e gravar no disco rígido que será enviada para o trabalho de exportação.

**O que acontece se eu acidentalmente enviar uma unidade de disco rígido que não atendem aos requisitos com suporte?**

O Azure data center retornará a unidade compatíveis com os requisitos com suporte para você. Se apenas algumas das unidades no pacote atendem aos requisitos de suporte, essas unidades serão processadas e as unidades que não atende aos requisitos serão retornadas para você.

**Para cancelar o meu trabalho?**

Você pode cancelar um trabalho quando seu status estiver criando ou entrega.

**Por quanto tempo pode exibir o status de trabalhos concluídos no portal de clássico?**

Você pode exibir o status de trabalhos concluídos por até 90 dias. Trabalhos concluídos serão excluídos após 90 dias.

**Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?**

Uma importação ou exportação trabalho pode fazer referência apenas 10 unidades em um único trabalho para o serviço de importação/exportação. Se você quiser enviar mais de 10 unidades, você pode criar vários trabalhos. Unidades que são associadas com o mesmo trabalho devem ser enviadas juntos no mesmo pacote.

**Posso usar um adaptador USB SATA que não esteja na lista recomendada?**

Se você tiver um conversor que não estiver listado acima, você pode tentar executar a ferramenta de importação/exportação do Azure usando seu conversor para preparar a unidade e verifique se ele funciona antes de adquirir um conversor de compatível.

**O serviço de formatar as unidades antes de retorná-los?**

Não. Todas as unidades são criptografadas com BitLocker.

**Pode adquirir unidades para trabalhos de importação/exportação da Microsoft?**

Não. Você precisará enviar suas próprias unidades para importação e exportação de trabalhos.

**Após concluir o trabalho de importação, qual será meus dados aparência na conta de armazenamento? Será meu hierarquia de diretório preservada?**

Ao preparar um disco rígido para um trabalho de importação, o destino especificado por na /dstdir: parâmetro. Este é o contêiner de destino na conta de armazenamento para o qual os dados do disco rígido são copiados. Dentro deste contêiner de destino, diretórios virtuais são criados para pastas do disco rígido e blobs são criados para arquivos.

**Se a unidade tiver arquivos que já existem na minha conta de armazenamento, o serviço substituirá blobs existentes da minha conta de armazenamento?**

Ao preparar a unidade, você pode especificar se os arquivos de destino devem ser substituídos ou ignorados usando o parâmetro chamado /Disposition: < renomear | não-sobrescrever | substituir >. Por padrão, o serviço será renomear os novos arquivos em vez de sobrescrever blobs existentes.

**A ferramenta de cliente do Azure importação/exportação é compatível com sistemas operacionais de 32 bits?**
Não. A ferramenta cliente só é compatível com sistemas de operacional do Windows de 64 bits. Consulte a seção sistemas operacionais os [pré-requisitos](#pre-requisites) para uma lista completa de versões com suporte do sistema operacional.

**Devem ser incluídos algo diferente de unidade de disco rígido em meu pacote?**

Por favor fornecidos apenas suas unidades de disco rígido. Não inclua itens como cabos de alimentação ou cabos USB.

**Preciso enviar meu unidades usando FedEx ou DHL?**

Você pode entregar unidades para o Centro de dados usando qualquer carrier conhecido como FedEx, DHL, UPS ou serviço Postal. No entanto, para as unidades de volta para você do data center de remessa, você deve fornecer um número de conta FedEx nos EUA e da UE ou um número de conta DHL nas regiões Ásia e Austrália.

**Existe alguma restrição com minha unidade internacionalmente de remessa?**

Observe que a mídia física que você estiver enviando talvez precise cross fronteiras internacionais. Você é responsável por garantir que sua mídia física e os dados são importados e/ou exportados de acordo com as leis aplicáveis. Antes da remessa mídia física, verifique com seu consultores para verificar se sua mídia e dados legalmente podem ser enviados para o Centro de dados identificados. Isso ajudará a garantir que ele chegue Microsoft em tempo hábil.

**Ao criar um trabalho, o endereço de entrega é um local diferente do meu local de conta de armazenamento. O que devo fazer?**

Alguns locais de conta de armazenamento são mapeados para locais de remessa alternativo. Anteriormente locais de remessas disponíveis também podem ser temporariamente mapeadas para locais alternativos. Sempre verificar o endereço de entrega fornecido durante a criação de trabalho antes de entregar as unidades.

**Por que meu status de trabalho no clássico portal diga "envio" quando o site da transportadora mostra meu pacote seja entregue?**

O status no portal do clássico alterado de remessa para transferir quando a unidade de processamento começa. Se sua unidade tiver atingido o recurso, mas não iniciou o processamento, seu status de trabalho mostrará como envio.

**Quando minha unidade de remessa, o carro solicita o nome de contato de centro de dados e o número de telefone. O que deve fornecer?**

O número de telefone é fornecido a você durante a criação de trabalho. Se precisar de um nome de contato, entre em contato conosco em waimportexport@microsoft.com e podemos fornecerá essas informações.

**Pode usar o serviço de importação/exportação do Azure para copiar caixas de correio de PST e dados do SharePoint para o Office 365?**

Consulte [os arquivos PST de importação ou dados do SharePoint para o Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Pode usar o serviço de importação/exportação do Azure para copiar meus backups offline para o serviço de Backup do Azure?**

Consulte [fluxo de trabalho de Backup Offline no Azure Backup](../backup/backup-azure-backup-import-export.md).

## <a name="see-also"></a>Consulte também:

- [Configurando a ferramenta de cliente do Azure importação/exportação](https://msdn.microsoft.com/library/dn529112.aspx)

- [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)

- [Amostra de API do Azure importação exportação restante](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
