<properties
   pageTitle="Backups de banco de dados do SQL - automático, localização geográfica redundantes | Microsoft Azure" 
   description="Banco de dados SQL automaticamente cria um backup do banco de dados local cada cinco minutos e usa armazenamento redundantes de localização geográfica do acesso de leitura Azure (ar-GRS) para fornecer redundância de localização geográfica. "
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2016"
   ms.author="carlrab;barbkess"/>

<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.


Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."
------------------>

<!----------------

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)
------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
    
    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="learn-about-sql-database-backups"></a>Saiba mais sobre backups do banco de dados SQL

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:
    
 

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a local database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

Banco de dados SQL automaticamente cria um backup do banco de dados local cada poucos minutos e usa armazenamento redundantes de localização geográfica do Azure acesso de leitura para redundância de localização geográfica. Backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade de negócios porque eles proteger seus dados contra danos acidentais ou exclusão. 

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.
    
    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>O que é um backup do banco de dados SQL?  

<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->

Um backup do banco de dados SQL inclui tanto backups de banco de dados local e redundantes de localização geográfica. Esses backups são criados automaticamente e sem nenhum custo adicional. Você não precisa fazer mais nada para facilitar a acontecer.

<!----------------- 
    Explains first component of the backup feature
------------------>

Para backups locais, o banco de dados SQL usa tecnologia do SQL Server para criar backups [completo](https://msdn.microsoft.com/library/ms186289.aspx), [diferencial](https://msdn.microsoft.com/library/ms175526.aspx )e [log de transação](https://msdn.microsoft.com/library/ms191429.aspx) . Os backups de log de transação acontecem a cada cinco minutos, que permite que você faça uma restauração no momento no mesmo servidor que hospeda o banco de dados. Ao restaurar um banco de dados, o serviço descobre qual log completo, diferencial e transação backups precisam ser restaurados.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Use um backup do banco de dados local para:

- Restaure um banco de dados a um ponto no tempo no período de retenção. Com um backup do banco de dados que você pode restaurar um banco de dados a um ponto no tempo, restaurar um banco de dados excluído a hora em que ele foi excluído ou restaurar um banco de dados para outra área geográfica. Para executar uma restauração, consulte [restaurar um banco de dados de um backup do banco de dados](sql-database-recovery-using-backups.md).

- Copie um banco de dados para um SQL server na região iguais ou diferente. A cópia é transacionalmente consistente com o banco de dados SQL atual. Para realizar uma cópia, consulte [Copiar de banco de dados](sql-database-copy.md).

- Arquive um backup do banco de dados além do período de retenção de backup. Para executar um arquivo morto, [Exportar um banco de dados SQL para uma BACPAC](sql-database-export.md) arquivo. Você pode arquivar o BACPAC ao armazenamento de longo prazo e armazená-la além do seu período de retenção. Ou, use o BACPAC para transferir uma cópia do seu banco de dados SQL Server, locais ou em uma Azure virtuais de máquina.

<!----------------- 
    Explains first component of the backup feature
------------------>

Para backups redundantes de localização geográfica, o banco de dados SQL usa [replicação de armazenamento do Azure](../storage/storage-redundancy.md). Banco de dados SQL armazena arquivos de backup do banco de dados local em uma conta de [Armazenamento de localização geográfica redundantes de acesso de leitura (ar-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) . Azure replica os arquivos de backup para uma [Central de dados pares](../best-practices-availability-paired-regions.md). 

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Use um backup geográfica redundantes para:

- Restaure um banco de dados para uma região geográfica diferente caso você não consegue acessar o backup do banco de dados da sua região de banco de dados principal. 

>[AZURE.NOTE] De armazenamento do Azure, o termo *replicação* se refere ao copiar arquivos de um local para outro. Do SQL *replicação de banco de dados* se refere a manter a vários bancos de dados secundários sincronizados com um banco de dados principal. 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quanto armazenamento de backup está incluído sem custo?

Banco de dados SQL fornece até 200% de seu armazenamento de banco de dados provisionados máximo como armazenamento de backup sem nenhum custo adicional. Por exemplo, se você tiver uma instância de banco de dados padrão com um tamanho de DB provisionado de 250 GB, você tem 500 GB de armazenamento de backup sem nenhum custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, você pode optar por reduzir o período de retenção contatando o suporte do Azure. Outra opção é pagar extra para armazenamento de backup que é cobrado pela taxa padrão de armazenamento do acesso de leitura geograficamente redundante (ar-GRS). 

## <a name="how-often-do-backups-happen"></a>Com que frequência acontecem backups?

Para backups de banco de dados local, backups de banco de dados completo acontecem semanalmente, backups de banco de dados diferencial acontecem por hora e log de transação backups acontecem a cada cinco minutos. O primeiro backup completo está agendado imediatamente após um banco de dados é criado. Geralmente é concluída em 30 minutos, mas pode levar mais tempo, quando o banco de dados de um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo em um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os backups mais são agendados automaticamente e gerenciados silenciosamente em segundo plano. O tempo exato completo e [diferencial](https://msdn.microsoft.com/library/ms175526.aspx) backups de banco de dados é determinado conforme ele equilibra a carga de trabalho geral do sistema. 

Para backups geográfica redundantes, backups completos e diferenciais são copiados acordo com a agenda de replicação de armazenamento do Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Por quanto tempo manter meus backups?

Cada backup do banco de dados SQL tem um período de retenção com base no [nível de serviço](sql-database-service-tiers.md) do banco de dados. O período de retenção para um banco de dados a:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

- Nível de serviço básico é sete dias.
- Nível de serviço padrão é 35 dias.
- Nível de serviço Premium é 35 dias.


Se você reduzir seu banco de dados dos níveis de serviço Standard ou Premium básica, os backups são salvos por sete dias. Todos os backups existentes mais de sete dias não estão mais disponíveis. 

Se você atualizar seu banco de dados da camada de serviço básico para padrão ou Premium, o banco de dados SQL mantém backups existentes até que fiquem 35 dias. Ele mantém novos backups conforme eles ocorrem para 35 dias.
 
Se você excluir um banco de dados, o banco de dados SQL mantém os backups da mesma forma que faria para um banco de dados online. Por exemplo, suponha que você excluir um banco de dados básico que tenha um período de retenção de sete dias. Um backup que quatro dias é salvo por três dias mais.

>[AZURE.IMPORTANT]
    Se você excluir o Azure SQL server que hospeda bancos de dados SQL, todos os bancos de dados que fazem parte de servidor também são excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Próximas etapas

Backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade de negócios porque eles proteger seus dados contra danos acidentais ou exclusão. Para ver como backups de banco de dados em uma estratégia mais ampla, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md).


