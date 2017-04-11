<properties
    pageTitle="Faça o backup de bancos de dados habilitados para alongar | Microsoft Azure"
    description="Saiba como fazer backup Alongar\-habilitado para bancos de dados."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="douglasl"/>

# <a name="backup-stretch-enabled-databases"></a>Bancos de dados habilitados para alongar backup

Backups de banco de dados ajudam você a recuperar muitos tipos de falhas, erros e desastres.  

-   Você precisa fazer backup de seu Alongar\-habilitado para bancos de dados do SQL Server.  

-   Microsoft Azure backup automaticamente os dados remotos que Alongar banco de dados tem migrados do SQL Server para Azure.  

>    [AZURE.NOTE] O backup é apenas uma parte de uma alta disponibilidade completa e a solução de continuidade de negócios. Para obter mais informações sobre alta disponibilidade, consulte [Soluções de disponibilidade de alta](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="back-up-your-sql-server-data"></a>Fazer backup de seus dados do SQL Server  

Para fazer backup de seu Alongar\-habilitado para bancos de dados do SQL Server, você pode continuar a usar os métodos de backup do SQL Server que você usa no momento. Para obter mais informações, consulte [fazer backup e restaurar do SQL Server bancos de dados](https://msdn.microsoft.com/library/ms187048.aspx).

Backups de um banco de dados habilitado para alongar SQL Server contenham apenas dados locais e dados elegível para migração no ponto no tempo quando o backup é executado. \(Dados qualificados são aqueles que ainda não tenham sido migrados, mas serão migradas para o Azure com base nas configurações de migração das tabelas.\) Isso é conhecido como um backup **superficial** e não inclui os dados já são migrados para o Azure.  

## <a name="back-up-your-remote-azure-data"></a>Fazer backup de seus dados do Azure remotos   

Microsoft Azure backup automaticamente os dados remotos que Alongar banco de dados tem migrados do SQL Server para Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure reduz o risco de perda de dados com backup automático  
O serviço de banco de dados do SQL Server Alongar no Azure protege seus bancos de dados remotos com instantâneos de armazenamento automático pelo menos a cada 8 horas. Retém cada instantâneo por 7 dias fornecer um intervalo de pontos de restauração possível.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure reduz o risco de perda de dados com geográfica\-redundância  
Backups Azure banco de dados são armazenados em localização geográfica\-armazenamento Azure redundante (ar\-GRS) e, portanto, localização geográfica\-redundantes por padrão. Localização geográfica\-armazenamento redundante replica seus dados para uma região secundária que é centenas de milhas longe da região primária. Em regiões primárias e secundários, seus dados são replicados cada três vezes, em domínios de falha separada e domínios de atualização. Isso garante que os seus dados são duráveis mesmo no caso de uma interrupção regional completa ou desastre que o torne uma das regiões Azure indisponível.

### <a name="stretchRPO"></a>Banco de dados Alongar reduz o risco de perda de dados para os dados do Azure mantendo linhas migradas temporariamente
Depois de banco de dados de alongar migra linhas qualificadas do SQL Server para Azure, ele retém essas linhas na tabela de preparação para um mínimo de 8 horas. Se você restaurar um backup do banco de dados do Azure, o banco de dados de alongar usa as linhas salvas na tabela de preparação reconciliar o SQL Server e bancos de dados do Azure.

Após restaurar um backup de seus dados do Azure, você precisa executar o procedimento armazenado [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) para se reconectar a Alongar\-banco de dados do SQL Server para o banco de dados remoto Azure habilitado. Quando você executa **sys.sp_rda_reauthorize_db**, o banco de dados de alongar reconcilia automaticamente o SQL Server e bancos de dados do Azure.

Para aumentar o número de horas de dados migrados que banco de dados de alongar temporariamente retém na tabela, execute o procedimento armazenado [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) e especifique um número de horas maiores que 8. Para decidir a quantidade de dados para reter, considere os seguintes fatores:
-   A frequência de backups automáticos Azure (pelo menos a cada 8 horas).
-   O tempo necessário após um problema para reconhecer o problema e decidir restaurar um backup.
-   A duração da operação de restauração Azure.

> [AZURE.NOTE] Aumentar a quantidade de dados que o banco de dados do Alongar temporariamente retém na tabela aumenta a quantidade de espaço necessário no SQL Server.

Para verificar o número de horas de dados que o banco de dados de alongar atualmente conserva temporariamente na tabela, execute o procedimento armazenado [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Consulte também

[Gerenciar e solucionar problemas de banco de dados de alongar](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Fazer backup e restauração de bancos de dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
