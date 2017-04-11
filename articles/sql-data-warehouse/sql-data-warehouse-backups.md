<properties
   pageTitle="Backups de SQL Data Warehouse | Microsoft Azure"
   description="Saiba mais sobre backups de banco de dados interno de depósito de dados do SQL que permitem que você restaure um depósito de dados do SQL Azure um ponto de restauração ou uma região geográfica diferente."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lakshmi1812"
   manager="barbkess"
   editor="monicar"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="lakshmir;barbkess"/>

# <a name="sql-data-warehouse-backups"></a>Backups de depósito de dados do SQL

SQL Data Warehouse oferece backups locais e geográficos, como parte de seus recursos de backup de depósito de dados. Eles incluem instantâneos Blob de armazenamento do Azure e armazenamento redundante de localização geográfica. Use backups de depósito de dados para restaurar seu data warehouse de um ponto de restauração na região primária ou restaurar para uma região geográfica diferente. Este artigo explica as especificações de backups em SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>O que é um backup de depósito de dados?

Um backup de depósito de dados é os dados que você pode usar para restaurar um depósito de dados para uma hora específica.  Como o SQL Data Warehouse é um sistema distribuído, um backup de depósito de dados consiste em muitos arquivos que estão armazenados em bolhas Azure. 

Backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade de negócios porque eles proteger seus dados contra danos acidentais ou exclusão. Para obter mais informações, consulte [Visão geral de continuidade de negócios](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Redundância de dados

SQL Data Warehouse protege seus dados armazenando seus dados em localmente (LRS) Azure Premium redundantes. Este recurso de armazenamento do Azure armazena várias cópias síncronas dos dados no Centro de dados locais para garantir a proteção de dados transparente se houver falhas localizadas. A redundância de dados garante que os seus dados podem sobreviver problemas de infraestrutura como falhas de disco. Redundância de dados garante a continuidade de negócios com uma tolerância e infraestrutura altamente disponível.

Para saber mais sobre:

- O armazenamento do Azure Premium, consulte [Introdução ao armazenamento do Azure Premium](../storage/storage-premium-storage.md).
- Armazenamento localmente redundante, consulte [replicação de armazenamento do Azure](../storage/storage-redundancy.md#locally-redundant-storage).


## <a name="azure-storage-blob-snapshots"></a>Azure instantâneos de armazenamento de Blob

Como um benefício de usar o armazenamento do Azure Premium, SQL Data Warehouse usa instantâneos Blob de armazenamento do Azure para fazer backup do data warehouse localmente. Você pode restaurar um depósito de dados para um ponto de restauração instantâneo. Instantâneos iniciar um mínimo de cada oito horas e estão disponíveis para sete dias.  

Para saber mais sobre:

- Instantâneos de blob do Microsoft Azure, consulte [criar um instantâneo de blob](../storage/storage-blob-snapshots.md).


## <a name="geo-redundant-backups"></a>Backups geográfica redundantes

A cada 24 horas, SQL Data Warehouse armazena o data warehouse completo no armazenamento padrão. O data warehouse completo é criado para corresponder a hora do último instantâneo. Armazenamento padrão pertence a uma conta de armazenamento geográfica redundantes com acesso de leitura (ar-GRS). O recurso de armazenamento do Azure ar-GRS replica os arquivos de backup para uma [Central de dados pares](../best-practices-availability-paired-regions.md). Essa replicação geográfica garante que você pode restaurar depósito de dados, caso você não consegue acessar os instantâneos na sua região primária. 

Esse recurso está ativado por padrão. Se você não quiser usar backups geográfica redundantes, você pode desativar essa opção. 

>[AZURE.NOTE] De armazenamento do Azure, o termo *replicação* se refere ao copiar arquivos de um local para outro. Do SQL *replicação de banco de dados* se refere a manter a vários bancos de dados secundários sincronizados com um banco de dados principal. 

Para saber mais sobre:
- Armazenamento de localização geográfica redundantes, consulte [replicação de armazenamento do Azure](../storage/storage-redundancy.md).
- Armazenamento de ar GRS, consulte [armazenamento geográfica redundantes de acesso de leitura](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Data warehouse agendamento de backup e período de retenção

SQL Data Warehouse cria instantâneos em seu depósitos de dados on-line a cada quatro a oito horas e mantém cada instantâneo por sete dias. Você pode restaurar o banco de dados online a um dos pontos de restauração nos últimos sete dias. 

Para ver quando o último instantâneo iniciado, execute essa consulta em seu Data Warehouse on-line do SQL. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Se você precisar reter um instantâneo por mais de sete dias, você pode restaurar um ponto de restauração para um novo depósito de dados. Após a conclusão da restauração, SQL Data Warehouse inicia a criação de instantâneos na nova data warehouse. Se você não fizer alterações para o novo depósito de dados, os instantâneos ficar vazios e, portanto, o custo de instantâneo é mínimo. Você também pode pausar o banco de dados para manter o SQL Data Warehouse criação de instantâneos.


### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>O que acontece com meu retenção backup enquanto minha depósito de dados está pausado?

SQL Data Warehouse não cria instantâneos e não expire instantâneos enquanto um depósito de dados está pausado. A idade de instantâneo não altera enquanto o data warehouse está pausado. Retenção de instantâneo baseia-se no número de dias que do data warehouse é online, não os dias de calendário.

Por exemplo, se um instantâneo começa 1 de outubro em 4 pm e data warehouse está pausado 3 de outubro às 16: 00, o instantâneo é dois dias. Sempre que o data warehouse vem online novamente o instantâneo é dois dias. Se o data warehouse vem online 5 de outubro às 16: 00, o instantâneo dois dias e permanece por mais cinco dias.

Quando o data warehouse trata online novamente, o SQL Data Warehouse currículos instantâneos novo e expira instantâneos quando eles têm mais de sete dias de dados.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Quanto tempo dura o período de retenção para um depósito de dados descartados?
Quando um depósito de dados é solta, data warehouse e os instantâneos são salvos por sete dias e, em seguida, removidos. Você pode restaurar o data warehouse para qualquer um dos pontos de restauração salvos.

> [AZURE.IMPORTANT] Se você excluir uma instância do SQL server lógica, todos os bancos de dados que pertencem a instância também são excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.

## <a name="data-warehouse-backup-costs"></a>Custos de backup de depósito de dados

O custo total de seu depósito de dados principal e sete dias de instantâneos do Azure Blob será arredondado para o mais próximo TB. Por exemplo, se seu data warehouse é 1,5 TB e os instantâneos usam 100 GB, você será cobrado para 2 TB de dados em taxas de armazenamento do Azure Premium. 

>[AZURE.NOTE] Cada instantâneo está vazio inicialmente e aumenta à medida que você faça alterações no depósito de dados principal. Todos os instantâneos aumentam o tamanho como as alterações de depósito de dados. Portanto, os custos de armazenamento para instantâneos crescem acordo com a taxa de alteração.

Se você estiver usando armazenamento redundante de localização geográfica, você recebe um encargo de armazenamento separado. O armazenamento de localização geográfica redundante é cobrado pela taxa padrão de armazenamento do acesso de leitura geograficamente redundante (ar-GRS).

Para obter mais informações sobre os preços de depósito de dados do SQL, consulte [Preços de depósito de dados do SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Usando backups de banco de dados

O uso principal para backups de depósito de dados do SQL é restaurar o data warehouse para um dos pontos de restauração dentro do período de retenção.  

- Para restaurar um depósito de dados do SQL, consulte [restaurar um depósito de dados do SQL](sql-data-warehouse-restore-database-overview.md).


## <a name="related-topics"></a>Tópicos relacionados

### <a name="scenarios"></a>Cenários

- Para obter uma visão de continuidade de negócios, consulte [Visão geral de continuidade de negócios](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

- Para restaurar um depósito de dados, consulte [restaurar um depósito de dados do SQL](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

