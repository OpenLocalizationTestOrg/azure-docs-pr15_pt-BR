<properties
   pageTitle="Usar a análise de fluxo Azure com depósito de dados do SQL | Microsoft Azure"
   description="Dicas para usar a análise de fluxo Azure com depósito de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Usar a análise de fluxo Azure com depósito de dados do SQL

Azure Analytics fluxo é um serviço totalmente gerenciado fornecendo processamento de eventos complexos baixa latência, altamente disponível e escaláveis ao longo do fluxo de dados na nuvem. Você pode aprender as Noções básicas lendo [Introdução ao Azure fluxo Analytics][]. Você pode, em seguida, saiba como criar uma solução de ponta a ponta com a análise de fluxo, seguindo o tutorial de [Introdução ao uso do Azure a análise de fluxo][] .

Neste artigo, você aprenderá como usar o banco de dados de depósito de dados do SQL Azure como um receptor de saída para seus trabalhos de análise de vapor.

## <a name="prerequisites"></a>Pré-requisitos

Primeiro, execute as seguintes etapas no tutorial [começar a usar a análise de fluxo Azure][] .  

1. Criar uma entrada de Hub de evento
2. Configurar e iniciar o aplicativo de gerador de evento
3. Provisionar um trabalho de análise de fluxo
4. Especifique a entrada de trabalho e de consulta

Em seguida, crie um banco de dados de depósito de dados do SQL Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especifique a saída de trabalho: banco de dados de depósito de dados SQL Azure

### <a name="step-1"></a>Etapa 1

No seu trabalho de análise de fluxo clique **saída** da parte superior da página e, em seguida, clique em **Adicionar saída**.

### <a name="step-2"></a>Etapa 2

Selecione o banco de dados SQL e clique em Avançar.

![][add-output]

### <a name="step-3"></a>Etapa 3
Insira os seguintes valores na próxima página:

- *Alias de saída*: insira um nome amigável para essa saída de trabalho.
- *Assinatura*:
    - Se seu banco de dados do SQL Data Warehouse na mesma assinatura como o trabalho de análise de fluxo, selecione Usar banco de dados do SQL assinatura atual.
    - Se seu banco de dados estiver em uma assinatura diferente, selecione usar SQL Database da outra assinatura.
- *Banco de dados*: especifique o nome de um banco de dados de destino.
- *Nome do servidor*: especificar o nome do servidor do banco de dados especificada. Você pode usar o Portal de clássico do Azure para encontrá-los.

![][server-name]

- *Nome de usuário*: especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
- *Senha*: fornecer a senha da conta de usuário especificado.
- *Tabela*: especificar o nome da tabela de destino no banco de dados.

![][add-database]

### <a name="step-4"></a>Etapa 4

Clique no botão de seleção para adicionar esta saída de trabalho e para verificar se a análise de fluxo com êxito pode se conectar ao banco de dados.

![][test-connection]

Quando a conexão ao banco de dados tiver êxito, você verá uma notificação na parte inferior do portal. Você pode clicar em Testar Conexão na parte inferior para testar a conexão ao banco de dados.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral de integração, consulte [Visão geral de integração do SQL Data Warehouse][].

Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento de depósito de dados do SQL][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introdução ao Azure fluxo Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Começar a usar a análise de fluxo do Azure]: ../stream-analytics/stream-analytics-get-started.md
[Visão geral de desenvolvimento de depósito de dados do SQL]:  ./sql-data-warehouse-overview-develop.md
[Visão geral de integração de depósito de dados do SQL]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
