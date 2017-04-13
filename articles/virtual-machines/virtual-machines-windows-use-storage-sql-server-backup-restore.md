<properties
    pageTitle="Como usar o armazenamento do Azure para SQL Server backup e restauração | Microsoft Azure"
    description="Saiba como fazer backup do SQL Server ao armazenamento do Azure. Explica os benefícios de fazer backup de bancos de dados SQL ao armazenamento do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="MikeRayMSFT"
    manager="jhubbard"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="mikeray"/>

# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Usar o armazenamento do Azure para SQL Server Backup e restauração

## <a name="overview"></a>Visão geral

Começando com o SQL Server 2012 SP1 CU2, agora você pode escrever backups do SQL Server diretamente para o serviço de armazenamento de Blob do Azure. Você pode usar essa funcionalidade backup e restauração do serviço do Azure Blob com um banco de dados do SQL Server local ou um banco de dados do SQL Server em uma máquina virtual Azure. Backup em nuvem oferece benefícios de disponibilidade, armazenamento externo ilimitado replicado geográfica e facilidade de migração de dados para e da nuvem. Você pode emitir declarações de BACKUP ou restauração usando Transact-SQL ou SMO.

SQL Server 2016 introduz novos recursos; Você pode usar o [backup de instantâneo de arquivos](http://msdn.microsoft.com/library/mt169363.aspx) para realizar backups quase instantâneos e restaura muito rápida.

Este tópico explica por que você pode optar por usar o armazenamento do Azure para backups SQL e, em seguida, descreve os componentes envolvidos. Você pode usar os recursos fornecidos no final do artigo para acessar orientações e informações adicionais para começar a usar esse serviço com os backups do SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Benefícios de usar o serviço de blob do Microsoft Azure SQL Server backups

Há vários desafios ao fazer backup do SQL Server. Esses desafios incluem gerenciamento de armazenamento, o risco de falha de armazenamento, acesso ao armazenamento externo e configuração de hardware. Muitos desses desafios endereçados usando o serviço de armazenamento de Blob do Azure para backups do SQL Server. Considere os seguintes benefícios:

- **Facilidade de uso**: armazenar seus backups em bolhas Azure pode ser uma conveniente, flexível e fácil acessar opção externo. Criar armazenamento externo para os backups do SQL Server pode ser tão fácil quanto modificando os scripts/trabalhos existentes para usar a sintaxe de **BACKUP para URL** . Armazenamento externo deve ser tipicamente suficiente do local de banco de dados de produção para impedir que um único desastre que possam afetar ambos os locais de banco de dados externo e produção. Escolhendo a [localização geográfica replicar o Azure blobs](../storage/storage-redundancy.md), você tem uma camada extra de proteção em caso de um desastre que pode afetar a região inteira.
- **Arquivo de backup**: O armazenamento de Blob do Azure serviço oferece uma alternativa melhor para a opção de fita usadas com frequência para arquivar backups. Armazenamento em fita pode exigir transporte físico para um recurso externo e medidas para proteger a mídia. Armazenar os backups em armazenamento de Blob do Azure fornece uma instantâneas, altamente disponíveis e um duráveis arquivamento opção.
- **Gerenciado hardware**: não há nenhuma sobrecarga de gerenciamento de hardware com os serviços do Azure. Os serviços do Azure gerenciar o hardware e fornecem replicação geográfica para redundância e proteção contra falhas de hardware.
- **Ilimitado de armazenamento**: Habilitando um backup direto para blobs Azure, você tem acesso ao armazenamento praticamente ilimitado. Como alternativa, fazer backup em um disco do Azure máquina virtual tem limites com base no tamanho de máquina. Não há um limite quanto ao número de discos que você pode anexar a uma máquina virtual Azure backups. Esse limite é 16 discos para uma instância extra grande e menos para instâncias menores.
- **Disponibilidade de backup**: Backups armazenados em bolhas Azure estão disponíveis em qualquer lugar e a qualquer momento e pode ser facilmente acessados para restaura um SQL Server local ou outro SQL Server em execução em uma Azure Máquina Virtual, sem a necessidade de desanexar/anexar banco de dados ou para baixar e anexar o VHD.
- **Custo**: pagar somente para o serviço que é usado. Pode ser econômico como uma opção de arquivamento externo e backup. Consulte a [Calculadora de preços Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de preços")e os [preços do Azure artigo](http://go.microsoft.com/fwlink/?LinkId=277059 "preços artigo") para obter mais informações.
- **Instantâneos de armazenamento**: quando os arquivos de banco de dados são armazenados em um blob Azure e você estiver usando o SQL Server 2016, você pode usar o [backup de instantâneo de arquivos](http://msdn.microsoft.com/library/mt169363.aspx) para executar backups quase instantâneos e restaura muito rápida.

Para obter mais detalhes, consulte [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Duas seções a seguir apresentam o serviço de armazenamento de Blob do Azure, incluindo os componentes necessários do SQL Server. É importante entender os componentes e sua interação com êxito usar backup e restauração do serviço de armazenamento de Blob do Azure.

## <a name="azure-blob-storage-service-components"></a>Componentes de serviço de armazenamento de blob do Microsoft Azure

Os seguintes componentes Azure são usados quando o backup para o serviço de armazenamento de Blob do Azure.

| Componente               | Descrição                          |
|---------------------|-------------------------------|
| **Conta de armazenamento** | A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para acessar um serviço de armazenamento de Blob do Azure, primeiro crie uma conta de armazenamento do Azure. Para obter mais informações sobre o serviço de armazenamento de Blob do Azure, veja [como usar o serviço de armazenamento de Blob do Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contêiner** | Um contêiner fornece um agrupamento de um conjunto de blobs e pode armazenar um número ilimitado de Blobs. Para escrever um SQL Server backup para um serviço de Blob do Azure, você deve ter pelo menos o contêiner raiz criado. |
| **Blob** | Um arquivo de qualquer tipo e tamanho. BLOBs são acessados usando o seguinte formato de URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Para saber mais sobre Blobs de página, consulte [Noções básicas sobre bloco e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes do SQL Server

Os seguintes componentes do SQL Server são usados quando o backup para o serviço de armazenamento de Blob do Azure.

| Componente               | Descrição                          |
|---------------------|-------------------------------|
| **URL** | Um URL especifica um identificador URI (Uniform Resource) para um arquivo de backup exclusivo. A URL é usada para fornecer o local e o nome do arquivo de backup do SQL Server. A URL deve apontar para um blob real, não apenas um contêiner. Se o blob não existir, ele será criado. Se um blob existente for especificado, BACKUP falhar, a menos que o > opção com o formato especificada. A seguir é um exemplo de URL seria especificado no comando BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS é recomendável, mas não é necessário. |
| **Credencial** | As informações necessárias para se conectar e autenticar ao serviço de armazenamento de Blob do Azure estão armazenadas como uma credencial.  Em ordem para o SQL Server gravar backups um Azure Blob ou restauração dele, uma credencial do SQL Server deve ser criada. Para obter mais informações, consulte [Credencial do SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [AZURE.NOTE] Se você optar por copiar e carregar um arquivo de backup para o serviço de armazenamento de Blob do Azure, você deve usar um tipo de blob de página como sua opção de armazenamento se você planeja usar este arquivo para operações de restauração. RESTAURAR a partir de um tipo de blob do bloco falhará com um erro.

## <a name="next-steps"></a>Próximas etapas

1. Crie uma conta do Azure se ainda não tiver uma. Se você estiver avaliando o Azure, considere a [avaliação gratuita](https://azure.microsoft.com/free/).

1. Vá através de um dos seguintes tutoriais que o orientam através de criação de uma conta de armazenamento e executar uma restauração.

    - **SQL Server 2014**: [Tutorial: SQL Server 2014 Backup e restauração do Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
    - **SQL Server 2016**: [Tutorial: usando o serviço de armazenamento de Blob do Microsoft Azure com bancos de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

1. Revisar documentação adicional, começando com o [SQL Server Backup e restauração com o serviço de armazenamento de Blob do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Se você tiver problemas, examine o tópico de [Backup do SQL Server URL práticas recomendadas e solução de problemas](https://msdn.microsoft.com/library/jj919149.aspx).

Para outros SQL Server fazer backup e restaurar opções, consulte [Backup e restauração para o SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
