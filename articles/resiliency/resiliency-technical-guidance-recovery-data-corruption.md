<properties
   pageTitle="Orientações técnicas de resiliência para recuperar dados corrompidos ou exclusões acidentais | Microsoft Azure"
   description="Artigo com noções básicas sobre como recuperar corrupção de dados de dados ou exclusão acidental de dados e a criação de aplicativos com tolerância a falhas flexível e altamente disponível, bem como planejamento de recuperação de dados"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Orientações técnicas resiliência Azure: recuperação de corrupção de dados ou exclusões acidentais

Parte de um plano de continuidade de negócios robusta está tendo um plano se seus dados obtém corrompidos ou acidentalmente excluídos. A seguir estão informações sobre a recuperação depois de dados foi danificados ou excluídos por engano, devido a erros de aplicativo ou erro de operador.

##<a name="virtual-machines"></a>Máquinas virtuais

Para proteger sua máquinas virtuais do Azure (às vezes chamado de infraestrutura como serviço VMs) de erros do aplicativo ou exclusões acidentais, use o [Backup do Azure](https://azure.microsoft.com/services/backup/). Backup Azure permite a criação de backups que são consistentes em vários discos de máquina virtual. Além disso, o Cofre de Backup podem ser replicado entre regiões para fornecer recuperação contra perda de região.

##<a name="storage"></a>Armazenamento

Observe que enquanto o armazenamento do Azure fornece resiliência de dados por meio de réplicas automatizadas, isso não impede que o código do aplicativo (ou desenvolvedores/usuários) de danificar dados por meio de exclusão não intencionais ou acidental, atualização e assim por diante. Manter fidelidade de dados no caso de erro de aplicativo ou usuário requer técnicas mais avançadas, como copiar os dados para um local de armazenamento secundário com um log de auditoria. Os desenvolvedores podem tirar proveito da [capacidade de instantâneo](https://msdn.microsoft.com/library/azure/ee691971.aspx)do blob, que pode criar instantâneos no momento de somente leitura do conteúdo de blob. Isso pode ser usado como base de uma solução de dados fidelidade para blobs de armazenamento do Azure.

###<a name="blob-and-table-storage-backup"></a>Blob e Backup de armazenamento de tabela

Enquanto blobs e tabelas são altamente duráveis, eles sempre representam o estado atual dos dados. Recuperação de indesejados modificação ou exclusão de dados pode exigir restaurando dados para um estado anterior. Isso pode ser obtido aproveitando os recursos fornecidos pelo Azure para armazenar e reter cópias point-in-time.

Para Blobs do Azure, você pode executar backups no momento usando o [recurso de instantâneo do blob](https://msdn.microsoft.com/library/ee691971.aspx). Para cada instantâneo, somente cobrado para o armazenamento necessário para armazenar as diferenças dentro o blob desde o último instantâneo estado. Os instantâneos são dependentes da existência do blob original que eles se baseiam, portanto, uma operação de cópia para outro blob ou até mesmo outra conta de armazenamento é aconselhável. Isso garante que os dados de backup está adequadamente protegidos contra exclusões acidentais. Para tabelas do Azure, você pode fazer cópias point-in-time para uma tabela diferente ou Blobs do Azure. Obter mais orientações e exemplos de realização de backups de nível de aplicativo de tabelas e blobs podem ser encontradas aqui:

  * [Protegendo suas tabelas em relação a erros do aplicativo](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [Protegendo seus Blobs contra erros de aplicativo](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##<a name="database"></a>Banco de dados

Há várias opções de [continuidade de negócios](../sql-database/sql-database-business-continuity.md) (backup e restauração) disponíveis para o Azure SQL Database. Bancos de dados podem ser copiados usando a funcionalidade de [Cópia do banco de dados](../sql-database/sql-database-copy.md) , ou [Exportando](../sql-database/sql-database-export.md) e [importando](https://msdn.microsoft.com/library/hh710052.aspx) um arquivo de bacpac do SQL Server. Cópia do banco de dados fornece resultados transacionalmente consistentes, enquanto uma bacpac (por meio do serviço de importação/exportação) não. Ambas as opções executar como serviços baseados na fila dentro do data center e atualmente não fornecem um SLA de tempo de conclusão.

>[AZURE.NOTE]As opções de cópia e importação/exportação de banco de dados coloque um grau significativo de carga sobre o banco de dados de origem. Eles podem disparar disputa de recursos ou a otimização de eventos.

###<a name="sql-database-backup"></a>Backup do banco de dados SQL

Backups no momento para o banco de dados do Microsoft Azure SQL são obtidos copiando [o banco de dados do SQL Azure](../sql-database/sql-database-copy.md). Você pode usar este comando para criar uma cópia transacionalmente consistente de um banco de dados no mesmo servidor de banco de dados lógico ou para um servidor diferente. Nos dois casos, a cópia do banco de dados é totalmente funcional e completamente independente do banco de dados de origem. Cada cópia que criar representa uma opção de recuperação no momento. Você pode recuperar o estado de banco de dados completamente renomeando novo banco de dados com o nome do banco de dados de origem. Como alternativa, você pode recuperar um subconjunto específico de dados do banco de dados novo usando consultas Transact-SQL. Para obter detalhes adicionais sobre o banco de dados SQL, consulte [Visão geral da continuidade de negócios com o Azure SQL Database](../sql-database/sql-database-business-continuity.md).

###<a name="sql-server-on-virtual-machines-backup"></a>SQL Server em máquinas virtuais Backup

Para o SQL Server usados com infraestrutura Azure como um máquinas virtuais de serviço (normalmente chamado IaaS ou IaaS VMs), há duas opções: backups tradicionais e envio de log. Usar backups tradicionais permite que você restaurar para um ponto específico no tempo, mas o processo de recuperação está lento. Restaurar backups tradicionais requer começando com um backup inicial completo e aplicando qualquer backups feitos depois disso. A segunda opção é configurar um sessão para atrasar a restauração de backups de log (por exemplo, por duas horas) de envio de log. Isso fornece uma janela recuperar de erros feitos na imagem principal.

##<a name="other-azure-platform-services"></a>Outros serviços de plataforma Windows Azure

Alguns serviços de plataforma Windows Azure armazenam informações em um banco de dados do SQL Azure ou uma conta de armazenamento controlado por usuário. Se o recurso de conta ou de armazenamento é excluído ou corrompido, isso pode causar erros graves com o serviço. Nesses casos, é importante manter backups que permitem que você crie novamente esses recursos se eles foram excluídos ou corrompidos.

Para Sites do Azure e os serviços do Azure Mobile, você deve fazer backup e manter os bancos de dados associados. Para o serviço de mídia do Azure e máquinas virtuais, você deve manter a conta associada do armazenamento do Azure e todos os recursos nessa conta. Por exemplo, para máquinas virtuais, você deve fazer backup e gerenciar discos máquina virtual no armazenamento de blob do Microsoft Azure.

##<a name="checklists-for-data-corruption-or-accidental-deletion"></a>Listas de verificação para corrupção de dados ou exclusões acidentais

##<a name="virtual-machines-checklist"></a>Lista de verificação de máquinas virtuais

  1. Examine a seção de máquinas virtuais deste documento.
  2. Fazer backup e manter os discos de máquina virtual com Backup Azure (ou seu próprio sistema de backup usando armazenamento de blob do Microsoft Azure e instantâneos VHD).

##<a name="storage-checklist"></a>Lista de verificação de armazenamento

  1. Examine a seção de armazenamento deste documento.
  2. Fazer backup de recursos de armazenamento críticos regularmente.
  3. Considere usar o recurso de instantâneo para blobs.

##<a name="database-checklist"></a>Lista de verificação de banco de dados

  1. Examine a seção de banco de dados deste documento.
  2. Crie backups no momento usando o comando Copiar do banco de dados.

##<a name="sql-server-on-virtual-machines-backup-checklist"></a>SQL Server na lista de verificação de Backup de máquinas virtuais

  1. Examine o SQL Server na seção de Backup de máquinas virtuais deste documento.
  2. Use tradicional de backup e restauração técnicas.
  3. Crie uma sessão de envio de log atrasado.

##<a name="web-apps-checklist"></a>Lista de verificação de aplicativos Web

  1. Fazer backup e manter o banco de dados associado, se houver.

##<a name="media-services-checklist"></a>Lista de verificação de serviços de mídia

  1. Fazer backup e manter os recursos de armazenamento associado.

##<a name="more-information"></a>Mais informações

Para obter mais informações sobre recursos de backup e restauração do Azure, consulte [armazenamento, cenários de recuperação e backup](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

##<a name="next-steps"></a>Próximas etapas

Este artigo é parte de uma série voltada para [orientações técnicas resiliência Azure](./resiliency-technical-guidance.md). Se você estiver procurando mais resiliência, recuperação de dados e recursos de alta disponibilidade, consulte resiliência Azure orientações técnicas [recursos adicionais](./resiliency-technical-guidance.md#additional-resources).