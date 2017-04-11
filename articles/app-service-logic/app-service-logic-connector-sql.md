<properties
   pageTitle="Utilizando o conector SQL nos aplicativos de lógica | Serviço de aplicativo do Microsoft Azure"
   description="Como criar e configurar o aplicativo de conector do SQL ou API e usá-lo em um aplicativo de lógica no serviço de aplicativo do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="sameerch"/>


# <a name="get-started-with-the-microsoft-sql-connector-and-add-it-to-your-logic-app"></a>Comece a usar o Microsoft SQL Connector e adicioná-lo ao seu aplicativo de lógica
>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica. Para a versão de esquema do Azure SQL 2015-08-01-visualização, clique em [API do SQL Azure](../connectors/connectors-create-api-sqlazure.md).

Conecte a um local do SQL Server ou um banco de dados do SQL Azure para criar e alterar suas informações ou dados. Conectores podem ser usados nos aplicativos de lógica para recuperar, processo, ou enviar dados como parte de um "fluxo de trabalho". Quando você usa o conector de SQL no seu fluxo de trabalho, você pode obter uma variedade de cenários. Por exemplo, você pode:

- Expor uma seção dos dados que residem no seu banco de dados do SQL usando a web ou o aplicativo móvel.
- Inserir dados em uma tabela de banco de dados SQL para armazenamento. Por exemplo, você pode inserir registros de funcionário, atualizar ordens de venda e assim por diante.
- Obter dados do SQL e usá-lo em um processo de negócios. Por exemplo, você pode obter registros do cliente e colocar esses registros de clientes em SalesForce.

Você pode adicionar o conector de SQL aos seus dados de fluxo de trabalho e o processo de negócios como parte deste fluxo de trabalho dentro de um aplicativo de lógica. 

## <a name="triggers-and-actions"></a>Disparadores e ações
*Disparadores* são eventos que ocorrem. Por exemplo, quando uma ordem é atualizada ou quando um novo cliente é adicionado. Uma *ação* é o resultado do disparador. Por exemplo, quando uma ordem é atualizada, envie um alerta para o vendedor. Ou, quando um novo cliente é adicionado, envie um email de boas-vindas para o novo cliente.

O conector de SQL pode ser usado como um disparador ou uma ação em uma lógica aplicativo e suporta dados em formatos JSON e XML. Para cada tabela incluída no seu pacote configurações (mais neste tópico), há um conjunto de ações de JSON e um conjunto de ações de XML.

O conector de SQL tem os seguintes disparadores e ações disponíveis:

Disparadores | Ações
--- | ---
Dados de votação | <ul><li>Inserir em tabela</li><li>Atualizar sumário</li><li>Selecione da tabela</li><li>Excluir da tabela</li><li>Chamar procedimento armazenado</li></ul>

## <a name="create-the-sql-connector"></a>Criar o conector SQL

Um conector pode ser criado em um aplicativo de lógica ou ser criado diretamente a partir do Azure Marketplace. Para criar um conector do Marketplace:  

1. No startboard Azure, selecione **Marketplace**.
2. Procurar por "SQL Connector", selecione-o e selecione **criar**.
3. Insira o nome do plano de serviço de aplicativo e outras propriedades.
4. Insira as seguintes configurações de pacote:

    Nome | Necessário |  Descrição
--- | --- | ---
Nome do servidor | Sim | Insira o nome do SQL Server. Por exemplo, insira *SQL Server/sqlexpress* ou *SQLserver.mydomain.com*.
Porta | Não | Padrão é 1433.
Nome de usuário | Sim | Insira um nome de usuário que pode efetuar o SQL Server. Se conectar a um SQL Server local, insira as credenciais de autenticação do SQL.
Senha | Sim | Insira a senha do nome de usuário.
Nome do banco de dados | Sim | Insira o banco de dados que você está se conectando. Por exemplo, você pode inserir *clientes* ou *dbo/ordens*.
Local | Sim | O padrão é falso. Insira falso se conectando a um banco de dados do SQL Azure. Digite verdadeiro se conectando a um SQL Server local.
Cadeia de Conexão do barramento de serviço | Não | Se você estiver se conectando ao local, insira a cadeia de conexão de retransmissão de barramento de serviço.<br/><br/>[Usando o Gerenciador de Conexão híbrido](app-service-logic-hybrid-connection-manager.md)<br/>[Barramento preços do serviço](https://azure.microsoft.com/pricing/details/service-bus/)
Nome do servidor parceiro | Não | Se o servidor primário não estiver disponível, você pode inserir um servidor parceiro como um servidor de backup ou alternativo.
Tabelas | Não | Lista as tabelas de banco de dados que podem ser atualizadas pelo conector. Por exemplo, insira *OrdersTable* ou *EmployeeTable*. Se nenhuma tabela forem digitada, todas as tabelas podem ser usadas. Tabelas válidas e/ou procedimentos armazenados são necessários para usar esse conector como uma ação.
Procedimentos armazenados | Não | Insira um procedimento armazenado existente que pode ser chamado pelo conector. Por exemplo, insira *sp_IsEmployeeEligible* ou *sp_CalculateOrderDiscount*. Tabelas válidas e/ou procedimentos armazenados são necessários para usar esse conector como uma ação.
Consulta de dados disponíveis | Para obter suporte disparadores | Instrução SQL para determinar se qualquer dado está disponível para uma tabela de banco de dados do SQL Server de sondagem. Isso deve retornar um valor numérico que representa o número de linhas de dados disponíveis. Exemplo: Selecione COUNT(*) table_name.
Consulta de dados de votação | Para obter suporte disparadores | A instrução SQL para pesquisar a tabela de banco de dados do SQL Server. Você pode inserir qualquer número de instruções SQL separadas por ponto e vírgula. Esta política é executada transacionalmente e comprometida somente quando os dados são armazenados com segurança em seu aplicativo de lógica. Exemplo: Selecione *table_name; DELETE FROM table_name. <br/>**Note**<br/>Você deve fornecer uma instrução de votação que evita um loop infinito excluindo, movendo ou atualizando dados selecionados para garantir que os mesmos dados não são pesquisados novamente.

5. Ao concluir, as configurações de pacote aparência semelhantes à seguinte:  
![][1]  

6. Selecione **criar**. 


## <a name="use-the-connector-as-a-trigger"></a>Usar o conector como um disparador
Vamos examinar um aplicativo de lógica simples que controla os dados de uma tabela do SQL, adiciona os dados em outra tabela e atualiza os dados.

Para usar o conector SQL como um disparador, insira os valores de **Consulta de dados disponíveis** e **Consulta de dados de votação** . **Consulta de dados disponíveis** é executado no agendamento que você insira e determina se qualquer dado está disponível. Como essa consulta só retorna um número escalar, pode ser ajustado e otimizado para execução frequente.

**Consulta de dados de votação** só é executado quando a consulta de dados disponíveis indica que dados estão disponíveis. Esta política executa dentro de uma transação e só fica comprometido quando os dados extraídos permanentemente são armazenados em seu fluxo de trabalho. É importante evitar indefinidamente extrair novamente os mesmos dados. A natureza transações dessa execução pode ser usada para excluir ou atualizar os dados para garantir que ele não é coletado na próxima vez em que dados são consultados.

> [AZURE.NOTE] O esquema retornado por este demonstrativo identifica as propriedades disponíveis no seu conector. Todas as colunas devem ser nomeadas.

#### <a name="data-available-query-example"></a>Exemplo de dados de consulta disponíveis

    SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### <a name="poll-data-query-example"></a>Exemplo de consulta de dados de votação

    SELECT *, GetData() as 'PollTime' FROM [Order]
        WHERE OrderStatus = 'ProcessedForCollection'
        ORDER BY Id DESC;
    UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
        WHERE Id =
        (SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### <a name="add-the-trigger"></a>Adicionar o disparador
1. Ao criar ou editar um aplicativo de lógica, selecione o conector de SQL que você criou como disparador. Lista disparadores disponíveis: **Sondagem dados JSON ()** e **Dados de votação (XML)**:  
![][5]

2. Selecione o disparador de **Votação dados JSON ()** , insira a frequência e clique o ✓:  
![][6]

3. O disparador agora aparece conforme configurado no aplicativo lógica. As saídas do disparador são mostradas e podem ser usadas como entradas em qualquer ações subsequentes:  
![][7]

## <a name="use-the-connector-as-an-action"></a>Usar o conector como uma ação
Usando nosso cenário de aplicativo de lógica simples que controla os dados de uma tabela do SQL, adiciona os dados em outra tabela e atualiza os dados.

Para usar o conector de SQL como uma ação, insira o nome da tabelas e/ou procedimentos armazenados que você inseriu quando você criou o conector de SQL:

1. Após o disparador (ou escolha 'executar essa lógica manualmente'), adicione o conector SQL que você criou na Galeria. Selecione uma das ações, inserir, como *Inserir em TempEmployeeDetails JSON ()*:  
![][8]

2. Insira os valores de entrada do registro a ser inserido e clique no ✓:  
![][9]

3. Na galeria, selecione o mesmo conector SQL que você criou. Como uma ação, selecione a ação de atualização na mesma tabela, como *EmployeeDetails de atualização*:  
![][11]

4. Insira os valores de entrada para a ação de atualização e clique no ✓:  
![][12]

Você pode testar o aplicativo de lógica, adicionando um novo registro na tabela que está sendo pesquisada.

## <a name="what-you-can-and-cannot-do"></a>O que você pode ou não fazer

Consulta SQL | Com suporte | Sem suporte
--- | --- | ---
Onde cláusula | <ul><li>Operadores: E, ou, =, <>, <, < =, >, > = e como</li><li>Várias condições de sub podem ser combinadas com '(' e')'</li><li>Cadeia de caracteres literais, Datetime (entre aspas), números (somente deve conter caracteres numéricos)</li><li>Terminantemente deve estar em um formato de expressão binário, como ((operand operator operand) e/ou (operando de operador do operando)) *</li></ul> | <ul><li>Operadores: Entre em</li><li>Todas as funções internas, como Add, Max () Now (), POWER() e assim por diante</li><li>Operadores matemáticos, como *, -, +, e assim por diante</li><li>Concatenações usando +.</li><li>Todas as ligações</li><li>É nulo e não é nulo</li><li>Qualquer números com caracteres não numéricos, como números hexadecimais</li></ul>
Campos (em consulta seleção) | <ul><li>Nomes de coluna válido separados por vírgulas. Sem prefixos de nome de tabela permitido (a conector funciona em uma tabela por vez).</li><li>Nomes podem ser ignorados com ' [' e ']'</li></ul> | <ul><li>Palavras-chave como superior, DISTINCT e assim por diante</li><li>Alias, como rua, cidade + Zip como endereço</li><li>Todas as funções internas, como Add, Max () Now (), POWER() e assim por diante</li><li>Operadores matemáticos, como *, -, +, e assim por diante</li><li>Concatenações usando +</li></ul>

#### <a name="tips"></a>Dicas

- Para consultas avançadas, sugerimos criando um procedimento armazenado e execute usando o procedimento armazenado de executar API.
- Ao usar consultas internas, usá-los em procedimentos armazenados.
- Para ingressar em várias condições, você pode usar o 'e' e operadores 'Ou'.

## <a name="hybrid-configuration-optional"></a>Configuração híbrida (opcional)

> [AZURE.NOTE] Esta etapa é necessária somente se você estiver usando o SQL Server local por trás do firewall.

Serviço de aplicativo usa o Gerenciador de configuração híbrida para conectar-se com segurança ao seu sistema de local. Se você estiver conector usa um SQL Server no local, o Gerenciador de Conexão híbrida é necessário.

> [AZURE.NOTE] Se você quiser começar a usar aplicativos do Azure lógica antes de se inscrever para uma conta do Azure, vá para [Experimentar lógica de aplicativo](https://tryappservice.azure.com/?appservice=logic), onde você pode criar imediatamente um aplicativo de lógica de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

Consulte [usando o Gerenciador de Conexão híbrido](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Faça mais com o conector
Agora que o conector for criado, você pode adicioná-lo para um fluxo de trabalho de negócios usando um aplicativo de lógica. Consulte [quais são os aplicativos de lógica?](app-service-logic-what-are-logic-apps.md).

Exiba a referência de API de REST Swagger a [referência de aplicativos de API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode analisar segurança de controle e as estatísticas de desempenho ao conector. Consulte [Gerenciar e monitorar seus aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png
