<properties
   pageTitle="Migrar: Data Warehouse utilitário de migração | Microsoft Azure"
   description="Migre para o SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="data-warehouse-migration-utility-preview"></a>Utilitário de migração de depósito de dados (prévia)

> [AZURE.SELECTOR]
- [Baixe o utilitário de migração][]

O utilitário de migração de depósito de dados é uma ferramenta projetada para migrar o esquema e dados do SQL Server e o banco de dados do Azure SQL depósito de dados do SQL Azure. Durante a migração de esquema, a ferramenta automaticamente mapeia o esquema correspondente de fonte para destino. Após o esquema foi migrado, as ferramentas fornece a opção para mover dados com scripts gerados automaticamente.

Além de migração de dados e esquema, essa ferramenta oferece a opção para gerar relatórios de compatibilidade que resumem incompatibilidades entre as instâncias de origem e destino que podem impedir a migração simplificada.

## <a name="get-started"></a>Introdução
Como um pré-requisito para a instalação, será necessário o utilitário de linha de comando BCP para executar scripts de migração e o Office para exibir o relatório de compatibilidade. Depois de iniciar o executável que é baixado você será solicitado para aceitar um EULA padrão antes da ferramenta será instalada.

Além disso, para executar o Utiliy de migração, você precisará aquele seguindo as permissões no banco de dados que você está procurando para migrar: Criar banco de dados, alterar qualquer banco de dados ou definição de qualquer modo de exibição.

### <a name="launching-the-tool-and-connecting"></a>Iniciando a ferramenta e conectando
Inicie a ferramenta clicando no ícone da área de trabalho que aparece pós-instalação. Ao abrir a ferramenta, você será solicitado com uma página de conexão inicial onde você pode escolher sua origem e destino para a ferramenta de migração. No momento, oferecemos suporte do SQL Server e o banco de dados do Azure SQL como origens e SQL Data Warehouse como um destino. Após selecionar esta você será solicitado a conectar ao seu servidor de origem preenchendo o nome do servidor e autenticação e clicando em 'Conectar'.

Depois de autenticar, a ferramenta mostrará uma lista de bancos de dados que estão presentes no servidor que você está conectado. Você pode começar a migração selecionando um banco de dados que você gostaria de migrar e, em seguida, clicando em 'Migrar selecionada'.

## <a name="migration-report"></a>Relatório de migração
Selecionar 'Verificar a compatibilidade de banco de dados' na ferramenta gerará um relatório resumindo todas as incompatibilidades de objeto do banco de dados solicitado para migrar. Uma lista maior de algumas das funcionalidades do SQL Server que não está presente em SQL Data Warehouse pode ser encontrada na nossa [documentação de migração][]. Depois que o relatório é gerado será capaz de salvar e abrir o relatório no Excel.

Observe que ao gerar o esquema de migração, a maioria dos problemas identificados como 'Object' será ajustado para permitir migração imediata dos dados. Examine as alterações para garantir que você não deseja fazer ajustes adicionais antes de aplicar o esquema.

## <a name="migrate-schema"></a>Migrar esquema

Depois de conectar, selecionar 'migrar ' esquema irá gerar um script de migração de esquema para as tabelas selecionadas. Portas este script a estrutura da tabela, dados incompatíveis mapas tipos de formulários mais compatíveis e cria esquema e credenciais de segurança, se isso é indicado pelo usuário nas configurações de migração. Este código pode ser executado com a instância do SQL Data Warehouse alvo, salvas em um arquivo, copiado para sua área de transferência ou mesmo editadas em linha antes de tomar uma ação adicional.  

Como observado acima, quando revisar de esquema migrando a migração alterações que a ferramenta fez para garantir que você entende totalmente-los.  

## <a name="migrate-data"></a>Migrar dados

Clicando na opção 'Migrar dados', você pode gerar scripts BCP que moverá seus dados primeiro arquivos simples no seu servidor, e então diretamente em seu Data Warehouse SQL. Recomendamos que esse processo para mover pequenas quantidades de dados e, como tentativas não são internos e falhas podem ocorrer se houver uma perda significativa da conexão de rede. Para executar isso, você precisará ter o utilitário de linha de comando BCP instalado e o esquema de dados deve já ter sido criado.

Após você preencheu os parâmetros acima basta clique em migração de execução e um conjunto de dois pacotes será gerado para seu local especificado. Execute o arquivo de exportação para exportar dados de sua fonte de migração em arquivos simples e execute o arquivo de importação para importar seus dados para o SQL Data Warehouse.

## <a name="next-steps"></a>Próximas etapas
Agora que você já migrado alguns dados, confira como [desenvolver][].

<!--Image references-->

<!--Article references-->
[documentação de migração]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Baixe o utilitário de migração]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
