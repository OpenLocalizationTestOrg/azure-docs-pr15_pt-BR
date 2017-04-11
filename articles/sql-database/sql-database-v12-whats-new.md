<properties
    pageTitle="O que há de novo no V12 de banco de dados do SQL | Microsoft Azure"
    description="Descreve por que terá a vantagem de sistemas de negócios que estão usando o banco de dados do SQL Azure na nuvem de atualização para versão V12 agora."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="genemi"/>


# <a name="whats-new-in-sql-database-v12"></a>O que há de novo no V12 de banco de dados SQL


Este tópico descreve as vantagens de muitos que a nova versão V12 do Azure SQL Database tem sobre versão V11.


Podemos continuar a adicionar recursos à V12. Portanto, recomendamos que você visite nossa página da Web de atualizações de serviço do Azure e usar seus filtros:


- Filtrado para o [serviço de banco de dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrado para disponibilidade geral [anúncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para os recursos de banco de dados SQL.


As informações mais recentes sobre os limites de recurso do banco de dados do SQL estão descritas no:<br/>[Limites de recursos de banco de dados do SQL azure](sql-database-resource-limits.md).


## <a name="increased-application-compatibility-with-sql-server"></a>Compatibilidade aprimorada com aplicativos com o SQL Server


O objetivo principal da V12 de banco de dados do SQL foi para melhorar a compatibilidade com o Microsoft SQL Server 2014 e para manter a compatibilidade à medida que são lançadas novas versões do SQL Server. Entre outras áreas, V12 atinge paridade com o SQL Server na área importante da capacidade de programação. Por exemplo:

- [Suporte JSON interno](https://msdn.microsoft.com/library/dn921897.aspx)

- [Funções de janela](http://msdn.microsoft.com/library/ms189798.aspx), com [sobre](http://msdn.microsoft.com/library/ms189461.aspx)

- [Índices XML](http://msdn.microsoft.com/library/bb934097.aspx) e [seletivos índices XML](http://msdn.microsoft.com/library/jj670104.aspx)

- [Controle de alterações](http://msdn.microsoft.com/library/bb933875.aspx)

- [SELECIONE... EM](http://msdn.microsoft.com/library/ms188029.aspx)

- [Pesquisa de texto completo](http://msdn.microsoft.com/library/ms142571.aspx)

- [ALTERAR a configuração de escopo do banco de dados (Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

Veja [aqui](sql-database-transact-sql-information.md) para o conjunto pequeno de recursos ainda não tem suportado no banco de dados SQL.


### <a name="compatibility-level-130"></a>Nível de compatibilidade 130


> [AZURE.IMPORTANT] Iniciando no **2016 de junho**, *recentemente* criado bancos de dados em V12 de banco de dados do SQL Azure tem seu nível de compatibilidade Iniciar em 130, que corresponde ao Microsoft SQL Server 2016 GA.
> 
> Você pode usar `ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120` se você preferir.
> 
> Bancos de dados criados antes de junho de 2016 não tem o nível de compatibilidade alterado por essa alteração do padrão. Nem é o nível de um banco de dados alterado por atualizá-lo de V11 para V12.



Para obter uma explicação de como você pode comparar suas consultas mais importantes entre as últimas versus nível de compatibilidade anterior, consulte:

- [Desempenho aprimorado de consulta com o nível de compatibilidade 130 no banco de dados do SQL Azure](sql-database-compatibility-level-query-performance-130.md)



## <a name="more-premium-performance-new-performance-levels"></a>Desempenho premium mais novos níveis de desempenho


Em V12, podemos aumentou as unidades de transação de banco de dados (DTUs) alocados para todos os níveis de desempenho Premium por 25% sem nenhum custo adicional. Ganhos de desempenho ainda maiores podem ser obtidos com novos recursos como:


- Suporte para na memória [columnstore índices](http://msdn.microsoft.com/library/gg492153.aspx).
- A [tabela partição por linhas](http://msdn.microsoft.com/library/ms187802.aspx) com aprimoramentos relacionados à [Tabela truncar](http://msdn.microsoft.com/library/ms177570.aspx).
- A disponibilidade de gerenciamento dinâmico vê [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx) para ajudar a monitorar e ajustar o desempenho.


### <a name="reliable-performance"></a>Desempenho confiável


Se o programa cliente se conecta ao V12 de banco de dados do SQL enquanto seu cliente é executado em uma máquina virtual Azure (máquina virtual), você deve abrir os seguintes intervalos de porta na VM:

- 11999 11000
- 14000-14999


Clique [aqui](sql-database-develop-direct-route-ports-adonet-v12.md) para obter detalhes sobre as portas para V12 de banco de dados do SQL. As portas são necessárias por melhorias de desempenho V12 de banco de dados do SQL.


## <a name="better-support-for-cloud-saas-vendors"></a>Melhor suporte para nuvem SaaS fornecedores


Somente em V12, podemos lançado, o novo nível de desempenho padrão S3 e a visualização pública do [pools elástica banco de dados](sql-database-elastic-pool.md). Pools de banco de dados elástica é uma solução criada para nuvem SaaS fornecedores.  Com pools de elástica banco de dados, você pode:


- Compartilhar DTUs entre bancos de dados para reduzir os custos para grandes números de bancos de dados.
- Execute [trabalhos de banco de dados elástica](sql-database-elastic-jobs-overview.md) para gerenciar bancos de dados em escala.


## <a name="security-enhancements"></a>Aprimoramentos de segurança


Segurança é um problema principal para quem executar seus negócios na nuvem. Os recursos de segurança mais recentes disponibilizados V12 incluem:


- [Segurança em nível de linha](http://msdn.microsoft.com/library/dn765131.aspx) RLS)
- [Máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md)
- [Bancos de dados independentes](http://msdn.microsoft.com/library/ff929188.aspx)
- [Funções do aplicativo](http://msdn.microsoft.com/library/ms190998.aspx) gerenciado com CONCEDER, negar, REVOGAR
- [Criptografia de dados transparente](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Conectando ao banco de dados do SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md)
 - Banco de dados SQL agora dá suporte à autenticação do Active Directory do Azure, um mecanismo de se conectar ao banco de dados do SQL usando identidades do Azure Active Directory (AD Azure). Com a autenticação do Active Directory do Azure, você pode gerenciar centralmente as identidades dos usuários de banco de dados e outros serviços da Microsoft em um local central.
- [Sempre criptografadas](https://msdn.microsoft.com/library/mt163865.aspx) (no modo de visualização) torna a criptografia transparente para aplicativos e permite que os clientes criptografar dados confidenciais dentro de aplicativos de cliente sem compartilhar as chaves de criptografia com o banco de dados SQL.


## <a name="increased-business-continuity-when-recovery-is-needed"></a>Maior continuidade de negócios quando a recuperação é necessária


V12 oferece objetivos de ponto de recuperação aprimorados (RPOs) e tempo de recuperação do estimado (ERTs):


| Recurso de continuidade de negócios | Versão anterior | V12 |
| :-- | :-- | :-- |
| Restaurar a localização geográfica | • RPO < 24 horas.<br/>• ERTER < 12 horas. | • RPO < 1 hora.<br/>• ERTER < 12 horas. |
| Replicação de localização geográfica ativa | • RPO < 5 minutos.<br/>• ERTER < 1 hora. | • RPO < 5 segundos.<br/>• ERTER < 30 segundos. |


Consulte [continuidade de negócios do banco de dados SQL](sql-database-business-continuity.md) para obter mais informações.


## <a name="more-reasons-to-upgrade-now"></a>Mais motivos para atualizar agora


Há várias boas razões por que os clientes devem atualizar agora para V12 de banco de dados do SQL Azure do V11:


- V12 de banco de dados SQL tem uma lista longa de recursos além dos recursos de V11.
- Podemos continuar a adicionar novos recursos à V12, mas não há novos recursos serão adicionados ao V11.
- A maioria dos novos recursos são lançados em V12 de banco de dados do SQL antes que eles estejam sendo lançados para o Microsoft SQL Server.


## <a name="are-you-using-v12-already"></a>Você está usando V12 já?


É uma maneira fácil de ver se você tem um banco de dados ou lógico execução server em uma versão anterior do serviço do banco de dados SQL fazer o seguinte:


1. Acesse o [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar**.
3. Clique em **servidores SQL**.
4. O ícone ao lado de seu servidor ou banco de dados revela a história:
 - ![Ícone para um servidor de v12](./media/sql-database-v12-whats-new/v12_icon.png) **servidor lógico V12**
 - ![Ícone para servidor de versão anterior](./media/sql-database-v12-whats-new/earlier_icon.png) **servidor lógico de versão anterior**


Outra técnica para determinar a versão é executado o `SELECT @@version;` instrução no seu banco de dados e exibir os resultados semelhantes a:


- **12**.0.2000.10 &nbsp; *(versão V12)*
- **11**.0.9228.18 &nbsp; *(versão V11)*


Um banco de dados V12 pode ser hospedado somente em um servidor lógico V12. E um servidor V12 pode hospedar somente V12 bancos de dados.


Se você não estiver executando ainda em V12, você pode atualizar seu servidor lógico seguindo as etapas em [atualizar para V12 de banco de dados do SQL no lugar](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Regiões de disponibilidade gerais


- Até 31 de julho de 2015, todas as regiões tinham foi promovidas para disponibilidade geral (GA).
- V12 foi lançado em dezembro de 2014, mas somente no status da visualização.

[Termos complementares de uso do Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
