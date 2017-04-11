<properties
    pageTitle="Introdução ao backup do Azure DPM | Microsoft Azure"
    description="Uma introdução ao backup dos servidores DPM usando o serviço de Backup do Azure"
    services="backup"
    documentationCenter=""
    authors="Nkolli1"
    manager="shreeshd"
    editor=""
    keywords="System Center Data Protection Manager, Gerenciador de proteção de dados, backup de dpm"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="trinadhk;giridham;jimpark;markgal"/>

# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparando para fazer backup de cargas de trabalho para o Azure com o DPM

> [AZURE.SELECTOR]
- [Servidor de Backup Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Servidor de Backup Azure (clássico)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (clássico)](backup-azure-dpm-introduction-classic.md)


Este artigo fornece uma introdução ao uso do Microsoft Azure Backup para proteger seus servidores de System Center Data Protection Manager (DPM) e cargas de trabalho. Lendo-lo, você vai compreender:

- Como funciona o backup de servidor DPM do Azure
- Os pré-requisitos para obter uma experiência de backup suave
- Os erros típicos encontrados e como lidar com eles
- Cenários com suporte

System Center DPM backup dos dados de arquivos e aplicativos. Dados de backup ao DPM podem ser armazenados em fita, em disco, ou o backup Azure com Backup do Microsoft Azure. DPM interage com o Azure Backup da seguinte maneira:

- **DPM implantado como física servidor ou local máquina virtual** — DPM se for implantado como um servidor físico ou uma máquina virtual de Hyper-V de locais que você pode fazer backup de dados para um Backup do Azure cofre além de disco e fita backup.
- **DPM implantado como uma máquina virtual Azure** — do System Center 2012 R2 com atualização 3, DPM pode ser implantado como uma máquina virtual Azure. Se o DPM é implantado como uma máquina virtual Azure que você pode fazer backup de dados para discos Azure anexados à máquina virtual DPM Azure ou você pode descarregar o armazenamento de dados, fazendo-o até um cofre de Backup do Azure.

## <a name="why-backup-your-dpm-servers"></a>Por que fazer backup de seus servidores DPM?

Os benefícios de negócios do uso de Backup do Azure para fazer backup de servidores DPM incluem:

- Para implantação do DPM local, você pode usar o backup Azure como uma alternativa para implantação de longo prazo em fita.
- Para implantações de DPM no Azure, Azure Backup permite transferir o armazenamento do Azure disco, permitindo que você dimensionar armazenando dados antigos em Backup do Azure e novos dados no disco.

## <a name="how-does-dpm-server-backup-work"></a>Como funciona o backup de servidor DPM?
Para fazer backup de uma máquina virtual, primeiro é necessário um instantâneo point-in-time dos dados. O serviço de Backup do Azure inicia o trabalho de backup no horário agendado e aciona a extensão de backup para tirar um instantâneo. A extensão de backup coordenadas com o serviço VSS no convidado para obter consistência e invoca blob instantâneo API do serviço de armazenamento do Azure depois de consistência foi atingida. Isso é feito para obter um instantâneo consistente dos discos da máquina virtual, sem ter que fechá-lo.

Depois que o instantâneo foi criado, os dados são transferidos pelo serviço Azure Backup ao Cofre de backup. O serviço cuida de identificação e transferindo apenas dos blocos que foram alterados desde o último backup, tornando o armazenamento de backups e a rede eficiente. Quando a transferência de dados for concluída, o instantâneo é removido, e um ponto de recuperação é criado. Neste ponto de recuperação pode ser visto no portal de clássico do Azure.

>[AZURE.NOTE] Para máquinas virtuais de Linux, é possível apenas consistente de arquivo de backup.

## <a name="prerequisites"></a>Pré-requisitos
Prepare o Backup do Azure para fazer backup de dados do DPM da seguinte maneira:

1. **Criar um cofre de Backup** — criar um cofre no console de Backup do Azure.
2. **Credenciais para download cofre** — no Azure Backup, carregue o certificado de gerenciamento criado para o cofre.
3. **Instalar o agente de Backup do Azure e registrar o servidor** — do Azure Backup, instalar o agente em cada servidor DPM e registrar o servidor DPM no cofre de backup.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## <a name="requirements-and-limitations"></a>Requisitos (e limitações)

- DPM pode ser executados como um servidor físico ou uma máquina virtual do Hyper-V instalado no System Center 2012 SP1 ou System Center 2012 R2. Ele também pode estar executando como uma máquina virtual Azure em execução no System Center 2012 R2 pelo menos DPM 2012 R2 cumulativo de atualizações 3 ou um computador de virtual do Windows no VMWare em execução no System Center 2012 R2 pelo menos 5 de Rollup de atualização.
- Se você estiver executando o DPM com System Center 2012 SP1, você deve instalar o Update Rollup 2 para System Center Data Protection Manager SP1. Isso é necessário antes de instalar o agente de Backup do Azure.
- O servidor DPM deve ter o Windows PowerShell e .net Framework 4,5 instalado.
- DPM pode fazer backup maioria das cargas de trabalho de Backup do Azure. Para obter uma lista completa do que tem suporte consulte o Backup do Azure suporte itens abaixo.
- Dados armazenados em Backup Azure não podem ser recuperados com a opção "Copiar para fita".
- Você precisará de uma conta do Azure com o recurso de Backup do Azure habilitado. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Leia sobre [preços do Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
- Usando o Backup do Azure requer o agente de Backup do Azure ser instalado nos servidores que você deseja fazer backup. Cada servidor deve ter pelo menos de 10% do tamanho dos dados que está sendo feitos backup, disponível como armazenamento gratuito no local. Por exemplo, fazer backup de 100 GB de dados requer um mínimo de 10 GB de espaço livre no local de rascunho. Enquanto o mínimo é 10%, 15% de espaço de armazenamento gratuito no local a ser usado para o local do cache é recomendado.
- Dados serão armazenados no armazenamento cofre Azure. Não há nenhum limite para a quantidade de dados que você pode voltar até um Backup do Azure cofre, mas o tamanho de uma fonte de dados (por exemplo uma máquina virtual ou um banco de dados) não deve exceder 54,400 GB.

Esses tipos de arquivo são suportados para até o Azure:

- Criptografado (somente backups completos)
- Compactado (backups incrementais suportados)
- Dispersos (backups incrementais suportados)
- Compactados e esparsos (tratadas como dispersos)

E eles não têm suportados:

- Servidores nos sistemas de arquivo diferencia maiusculas de minúsculas não têm suporte.
- Links de disco rígido (ignorado)
- Nova análise pontos (ignorado)
- Criptografados e compactados (ignorado)
- Criptografados e esparsos (ignorados)
- Fluxo compactado
- Fluxo esparso

>[AZURE.NOTE] No System Center 2012 DPM com SP1 em diante, você pode backup cargas de trabalho protegidas pelo DPM no Azure usando o Backup do Microsoft Azure.
