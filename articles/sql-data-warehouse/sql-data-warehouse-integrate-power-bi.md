<properties
   pageTitle="Usar o Power BI com o SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar o Power BI com depósito de dados do SQL Azure para desenvolver soluções."
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
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-power-bi-with-sql-data-warehouse"></a>Usar o Power BI com depósito de dados do SQL
Como com o banco de dados do SQL Azure, conexão direta do SQL dados depósito permite usuário aproveitar poderoso empilhamento lógico junto com os recursos analíticos do Power BI.  Com conexão direta, as consultas são enviadas para seu depósito de dados do SQL Azure em tempo real conforme você explorar os dados.  Isso, combinado com a escala do SQL Data Warehouse, permite que os usuários criem relatórios dinâmicos em minutos contra terabytes de dados.  Além disso, a introdução do botão Abrir no Power BI permite que usuários se conectem diretamente o Power BI ao seu SQL Data Warehouse sem coletar informações de outras partes do Azure.

Ao usar conexão direta, nota:

+ Especifique o nome do servidor totalmente qualificado ao conectar (consulte abaixo para obter mais detalhes)
+ Certifique-se de que as regras de firewall para o banco de dados são configuradas como "Permitir acesso aos serviços Azure".
+ Cada ação como selecionando uma coluna ou adicionando um filtro irá consultar diretamente o data warehouse
+ Blocos são atualizados a cada 15 minutos aproximadamente (atualizar não precisa ser agendado)
+ P e r não está disponível para conjuntos de dados de conexão direta
+ Alterações de esquema não são selecionadas automaticamente
+ Todas as consultas de conexão direta expirará após 2 minutos

Estas restrições e anotações podem mudar como podemos continuar a aprimorar as experiências. As etapas para conectar são detalhadas abaixo.  

## <a name="using-the-open-in-power-bi-button"></a>Usando o botão 'Abrir no Power BI'
A maneira mais fácil para mover entre o SQL Data Warehouse e Power BI é com o botão Abrir no Power BI. Esse botão permite que você perfeitamente começar a criar novos painéis do Power BI.  

1.  Para começar a navegar até sua instância do SQL Data Warehouse no Portal de clássico do Azure.
2.  Clique no botão 'Abrir no Power BI'.
3.  Se não é possível entrar no diretamente, ou se você não tiver uma conta do Power BI, você precisará entrar.  
4.  Você será direcionado para a página de conexão de SQL Data Warehouse, com as informações do seu SQL Data Warehouse previamente preenchida.
5.  Depois de inserir suas credenciais você será totalmente conectado seu depósito de dados do SQL.

## <a name="connecting-through-the-power-bi-portal"></a>Conectando através do portal do Power BI
Além de usar o botão Abrir no Power BI, os usuários também podem se conectar ao seu SQL Data Warehouse por meio do Portal do Power BI.

1.  Clique em 'Obter dados' na parte inferior do painel de navegação.
2.  Selecione 'Databases'.
3.  Uma vez na página bancos de dados, selecione 'Depósito de dados do SQL Azure' e clique em 'Conectar'.
4.  Insira as informações de conexão necessárias.  Seu nome de servidor e banco de dados podem ser encontradas no Portal do Azure.
5.  Você será direcionado para a página principal do Power BI e depois que sua conexão é feita uma nova entrada em 'Conjuntos de dados' aparecerá com o nome da sua instância.  
6.   Você pode clicar em novo conjunto de dados para explorar todas as tabelas e modos de exibição no banco de dados. Selecionando uma coluna enviará uma consulta de volta para a fonte, criar dinamicamente o visual. Essas dicas visuais podem ser salvas em um novo relatório e fixos de volta ao seu painel.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
