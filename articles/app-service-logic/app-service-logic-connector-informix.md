<properties
   pageTitle="Utilizando o conector Informix no serviço de aplicativo do Microsoft Azure | Microsoft Azure"
   description="Como usar o conector Informix com ações e disparadores de aplicativo de lógica"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# <a name="informix-connector"></a>Conector do Informix
>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica.

Conector da Microsoft para Informix é um aplicativo de API para conexão de aplicativos por meio de serviço de aplicativo do Azure aos recursos armazenados em um banco de dados IBM Informix. Conector inclui um cliente Microsoft para se conectar a computadores remotos de servidor Informix em uma conexão de rede TCP/IP, incluindo as conexões Azure híbrido para os servidores de Informix local usando a retransmissão de barramento de serviço do Azure. Conector suporta as seguintes operações de banco de dados:

- Selecionar linhas de leitura usando
- Votação ler linhas usando selecione contagem seguido selecione
- Adicionar uma linha ou várias linhas (em massa) usando inserir
- Alterar uma linha ou várias linhas (em massa) usando a atualização
- Remover uma linha ou várias linhas (em massa) usando DELETE
- Leitura alterar linhas usando o CURSOR selecione seguido por atualização local atual do CURSOR
- Lido para remover linhas usando o CURSOR selecione seguido por atualização local atual do CURSOR
- Executar o procedimento com parâmetros de entrada e saídos, valor, conjunto de resultados, usando a chamada de retorno
- Operações compostas usando SELECT, INSERT, atualizar, excluir e comandos personalizados

## <a name="triggers-and-actions"></a>Disparadores e ações
Conector suporta as seguintes gatilhos de aplicativo de lógica e ações:

Disparadores | Ações
--- | ---
<ul><li>Dados de votação</li></ul> | <ul><li>Inserção em massa</li><li>Inserir</li><li>Atualização em massa</li><li>Atualização</li><li>Chamada</li><li>Exclusão em massa</li><li>Excluir</li><li>Selecione</li><li>Atualização condicional</li><li>Postar em EntitySet</li><li>Excluir condicional</li><li>Selecione entidade única</li><li>Excluir</li><li>Upsert ao EntitySet</li><li>Comandos personalizados</li><li>Operações compostas</li></ul>


## <a name="create-the-informix-connector"></a>Criar o conector Informix
Você pode definir um conector dentro de um aplicativo de lógica ou do Azure Marketplace, como no exemplo a seguir:  

1. No startboard Azure, selecione **Marketplace**.
2. Na lâmina **tudo** , digite **informix** na caixa **Pesquisar tudo** e, em seguida, clique na tecla enter.
3. Em Pesquisar tudo o painel de resultados, selecione o **conector Informix**.
4. Na lâmina Informix conector descrição, selecione **criar**.
5. Na lâmina Informix conector pacote, insira o nome (por exemplo, "InformixConnectorNewOrders"), o plano de serviço de aplicativo e outras propriedades.
6. Selecione **configurações do pacote**e insira as seguintes configurações de pacote.

    Nome | Necessário |  Descrição
--- | --- | ---
ConnectionString | Sim | Cadeia de conexão do cliente Informix (por exemplo, "endereço de rede = NomeServidor; Porta de rede = 9089; ID de usuário = username; Senha = senha; catálogo inicial = nwind; esquema padrão = informix ").
Tabelas | Sim | Lista separada por vírgulas de tabela, modo de exibição e alias nomes necessários para operações de OData e gerar documentação swagger com exemplos (por exemplo, "NEWORDERS").
Procedimentos | Sim | Lista separada por vírgulas de nomes de função e procedimento (por exemplo, "SPORDERID").
OnPremise | Não | Implante usando o Azure retransmissão de barramento de serviço no local.
ServiceBusConnectionString | Não | Cadeia de caracteres de conexão de retransmissão de barramento de serviço Azure.
PollToCheckData | Não | Selecione contagem instrução para usar com um gatilho de aplicativo lógica (por exemplo, "Selecione contagem (\*) de NEWORDERS onde SHIPDATE IS NULL").
PollToReadData | Não | Instrução SELECT para usar com um gatilho de aplicativo lógica (por exemplo, "Selecione \* de NEWORDERS onde SHIPDATE é nulo para atualização").
PollToAlterData | Não | ATUALIZAR ou instrução DELETE para usar com um gatilho de aplicativo lógica (por exemplo, "atualização NEWORDERS definir SHIPDATE = atual Data onde atual da &lt;CURSOR&gt;").

7. Selecione **Okey**e selecione **criar**.
8. Ao concluir, as configurações de pacote aparência semelhantes à seguinte:  
![][1]


## <a name="logic-app-with-informix-connector-action-to-add-data"></a>Aplicativo de lógica com ação de conector Informix para adicionar dados ##
Você pode definir uma ação de aplicativo lógica para adicionar dados a uma tabela Informix usando um API inserir ou postagem a operação de OData de entidade. Por exemplo, você pode inserir um novo registro de ordem de cliente, processando uma instrução SQL INSERT em relação a uma tabela definida com uma coluna de identidade, retornando o valor de identidade ou as linhas afetadas aplicativo lógica (Selecionar ORDID da tabela FINAL (valores de inserir em NEWORDERS (CUSTID, SHIPNAME, SHIPADDR, SHIPCITY, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

> [AZURE.TIP] Conexão de Informix "*postagem para EntitySet*" retorna o valor da coluna de identidade e "*API inserir*" retorna linhas afetadas

1. No startboard Azure, selecione **+** (sinal de mais), **Web + Mobile**e, em seguida, **lógica de aplicativo**.
2. Insira o nome (por exemplo, "NewOrdersInformix"), o plano de serviço de aplicativo, outras propriedades e clique em seguida **criar**.
3. Na startboard Azure, selecione a lógica aplicativo recém-criado, **configurações**e, em seguida, **disparadores e ações**.
4. Na lâmina disparadores e ações, selecione **Criar do zero** dentro do aplicativo de lógica modelos.
5. No painel aplicativos da API, selecione **Recorrência**, defina uma frequência e intervalo e, em seguida, **marca de seleção**.
6. No painel aplicativos da API, selecione **Informix conector**, expanda a lista de operações para selecionar **Inserir em NEWORDER**.
7. Expanda a lista de parâmetros para Insira os seguintes valores:  

    Nome | Valor
--- | --- 
CUSTID | 10042
CÓDIGODATRANSP | 10000
SHIPNAME | K preguiça Kountry repositório 
SHIPADDR | Orquestra 12 terraço
SHIPCITY | Walla Walla 
SHIPREG | SP
SHIPZIP | 99362 

8. Selecione a **marca de seleção** para salvar as configurações de ação e, em seguida, **Salve**.
9. As configurações devem ter a seguinte aparência:  
![][3]  
10. Na lista **Todos é executado** em **operações**, selecione o item listados em primeiro lugar (executar mais recente). 
11. Na lâmina **lógica aplicativo executar** , selecione o item de **ação** **informixconnectorneworders**.
12. Na lâmina **ação de aplicativo de lógica** , selecione o **LINK de ENTRADAS**. Conector do Informix usa as entradas para processar uma instrução INSERT parametrizada.
13. Na lâmina **ação de aplicativo de lógica** , selecione o **LINK de SAÍDAS**. As entradas devem ter a seguinte aparência:  
![][4]

#### <a name="what-you-need-to-know"></a>O que você precisa saber

- Conector trunca nomes de tabela Informix quando formando nomes de ação de aplicativo de lógica. Por exemplo, a operação **Inserir em NEWORDERS** é truncada para **Inserir em NEWORDER**.
- Depois de salvar o aplicativo de lógica **disparadores e ações**, o aplicativo de lógica processa a operação. Pode haver um atraso de um número de segundos (por exemplo, 3 a 5 segundos) antes de lógica de aplicativo processa a operação. Opcionalmente, você pode clicar em **Executar agora** para processar a operação.
- Conector do Informix define os membros de EntitySet com atributos, incluindo se o membro corresponde a uma coluna de Informix com um padrão ou gerado colunas (por exemplo, identidade). Aplicativo de lógica exibe um asterisco vermelho ao lado do nome de identificação de membro de EntitySet, para indicar colunas Informix que exigem valores. Você não deve inserir um valor para o membro ORDID, que corresponde à coluna de identidade Informix. Você pode inserir valores para outros membros opcionais (itens, ORDDATE, REQDATE, CÓDIGODATRANSP, FRETE, SHIPCTRY), que correspondem às colunas Informix com valores padrão. 
- Conector do Informix retorna ao aplicativo de lógica a resposta da postagem ao EntitySet que inclui os valores para as colunas de identidade, que é derivado de SQLDARD a DRDA (dados de resposta de área de dados do SQL) na instrução SQL INSERT preparada. Servidor de Informix não retornar os valores inseridos para colunas com valores padrão.  


## <a name="logic-app-with-informix-connector-action-to-add-bulk-data"></a>Aplicativo de lógica com ação de conector Informix para adicionar dados em massa ##
Você pode definir uma ação de aplicativo lógica para adicionar dados a uma tabela Informix usando uma operação de inserção do API em massa. Por exemplo, você pode inserir dois novos clientes ordem registros, processando uma instrução SQL INSERT usando uma matriz de valores de linha em relação a uma tabela definida com uma coluna de identidade, retornando as linhas afetadas aplicativo lógica (Selecionar ORDID da tabela FINAL (valores de inserir em NEWORDERS (CUSTID, SHIPNAME, SHIPADDR, SHIPCITY, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

1. No startboard Azure, selecione **+** (sinal de mais), **Web + Mobile**e, em seguida, **lógica de aplicativo**.
2. Insira o nome (por exemplo, "NewOrdersBulkInformix"), o plano de serviço de aplicativo, outras propriedades e clique em seguida **criar**.
3. Na startboard Azure, selecione a lógica aplicativo recém-criado, **configurações**e, em seguida, **disparadores e ações**.
4. Na lâmina disparadores e ações, selecione **Criar do zero** dentro do aplicativo de lógica modelos.
5. No painel aplicativos da API, selecione **Recorrência**, defina uma frequência e intervalo e, em seguida, **marca de seleção**.
6. No painel aplicativos da API, selecione **Informix conector**, expanda a lista de operações para selecionar **Em massa inserir no novo**.
7. Insira o valor de **linhas** como uma matriz. Por exemplo, copie e cole o seguinte:  

    ```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
    ```
        
8. Selecione a **marca de seleção** para salvar as configurações de ação e, em seguida, **Salve**. As configurações devem ter a seguinte aparência:  
![][6]

9. Na lista **Todos é executado** em **operações**, clique no item listados em primeiro lugar (executar mais recente).
10. Na lâmina **lógica aplicativo executar** , clique no item de **ação** .
11. Na lâmina **ação de aplicativo de lógica** , clique no **LINK de ENTRADAS**. Saídas devem ter a seguinte aparência:  
[][7]
12. Na lâmina **ação de aplicativo de lógica** , clique no **LINK de SAÍDAS**. Saídas devem ter a seguinte aparência:  
![][8]

#### <a name="what-you-need-to-know"></a>O que você precisa saber

- Conector trunca nomes de tabela Informix quando formando nomes de ação de aplicativo de lógica. Por exemplo, a operação **Em massa insira NEWORDERS** será truncada **em massa**inserir no novo.
- Banco de dados Informix pode diferenciar maiusculas de minúsculas para nomes de coluna e tabela. Por exemplo, os nomes de coluna de matriz de operação em massa inserir talvez precise ser especificado em letras minúsculas ("custid") em vez de maiusculas ("CUSTID").
- Omitindo colunas de identidade (por exemplo, ORDID), colunas anuláveis (por exemplo, SHIPDATE) e colunas com valores padrão (por exemplo, ORDDATE, REQDATE, CÓDIGODATRANSP, FRETE, SHIPCTRY), o banco de dados Informix gera valores.
- Especificando "hoje" e "amanhã", conector Informix gera "Data atual" e "Data atual + 1 dia" funções (por exemplo, REQDATE). 


## <a name="logic-app-with-informix-connector-trigger-to-read-alter-or-delete-data"></a>Aplicativo de lógica com Informix gatilho de conector para ler, alterar ou excluir dados ##
Você pode definir um gatilho de aplicativo lógica para pesquisar e ler dados de uma tabela Informix usando uma operação de composto API votação dados. Por exemplo, você pode ler um ou mais nova ordem registros de clientes, retornando os registros para o aplicativo de lógica. As configurações de pacote/aplicativo Informix Conexão devem ter a seguinte aparência:

    Configuração de aplicativo | Valor
--- | --- | ---
PollToCheckData | Selecione contagem (\*) de NEWORDERS onde SHIPDATE é nulo
PollToReadData | Selecione \* de NEWORDERS onde SHIPDATE é nulo para atualização
PollToAlterData | <no value specified>


Além disso, você pode definir um gatilho de aplicativo lógica para poll, ler e alterar os dados em uma tabela de Informix usando uma operação de composto API votação dados. Por exemplo, você pode ler um ou mais registros ordem nova de cliente, atualizar os valores da linha, retornando os registros selecionados (antes da atualização) para o aplicativo de lógica. As configurações de pacote/aplicativo Informix Conexão devem ter a seguinte aparência:

    Configuração de aplicativo | Valor
--- | --- | ---
PollToCheckData | Selecione contagem (\*) de NEWORDERS onde SHIPDATE é nulo
PollToReadData | Selecione \* de NEWORDERS onde SHIPDATE é nulo para atualização
PollToAlterData | ATUALIZAÇÃO NEWORDERS conjunto SHIPDATE = data atual onde OF atual &lt;CURSOR&gt;


Além disso, você pode definir um gatilho de aplicativo lógica para poll, leia e remover dados de uma tabela Informix usando uma operação de composto API votação dados. Por exemplo, você pode ler um ou mais novos clientes ordem registros, excluir as linhas, retornando os registros selecionados (antes de excluir) para o aplicativo de lógica. As configurações de pacote/aplicativo Informix Conexão devem ter a seguinte aparência:

    Configuração de aplicativo | Valor
--- | --- | ---
PollToCheckData | Selecione contagem (\*) de NEWORDERS onde SHIPDATE é nulo
PollToReadData | Selecione \* de NEWORDERS onde SHIPDATE é nulo para atualização
PollToAlterData | Excluir NEWORDERS onde OF atual &lt;CURSOR&gt;

Neste exemplo, lógica aplicativo será poll, ler, atualizar e, em seguida, leia novamente os dados na tabela Informix.

1. No startboard Azure, selecione **+** (sinal de mais), **Web + Mobile**e, em seguida, **lógica de aplicativo**.
2. Insira o nome (por exemplo, "ShipOrdersInformix"), o plano de serviço de aplicativo, outras propriedades e clique em seguida **criar**.
3. Na startboard Azure, selecione a lógica aplicativo recém-criado, **configurações**e, em seguida, **disparadores e ações**.
4. Na lâmina disparadores e ações, selecione **Criar do zero** dentro do aplicativo de lógica modelos.
5. No painel aplicativos da API, selecione o **conector Informix**, defina uma frequência e intervalo e, em seguida, **marca de seleção**.
6. No painel aplicativos da API, selecione **Informix conector**, expanda a lista de operações para selecionar **Selecione NEWORDERS**.
7. Selecione a **marca de seleção** para salvar as configurações de ação e, em seguida, **Salve**. As configurações devem ter a seguinte aparência:  
![][10]
8. Clique para fechar a lâmina **disparadores e ações** e clique em para fechar a lâmina de **configurações** .
9. Na lista **Todos é executado** em **operações**, clique no item listados em primeiro lugar (executar mais recente).
10. Na lâmina **lógica aplicativo executar** , clique no item de **ação** .
11. Na lâmina **ação de aplicativo de lógica** , clique no **LINK de SAÍDAS**. Saídas devem ter a seguinte aparência:  
![][11]


## <a name="logic-app-with-informix-connector-action-to-remove-data"></a>Aplicativo de lógica com ação de conector Informix para remover dados ##
Você pode definir uma ação de aplicativo lógica para remover dados de uma tabela Informix usando um API Delete ou postagem a operação de OData de entidade. Por exemplo, você pode inserir um novo registro de ordem de cliente, processando uma instrução SQL INSERT em relação a uma tabela definida com uma coluna de identidade, retornando o valor de identidade ou as linhas afetadas aplicativo lógica (Selecionar ORDID da tabela FINAL (valores de inserir em NEWORDERS (CUSTID, SHIPNAME, SHIPADDR, SHIPCITY, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

## <a name="create-logic-app-using-informix-connector-to-remove-data"></a>Criar aplicativo de lógica usando Informix conector para remover dados ##
Você pode criar um novo aplicativo de lógica de dentro do Azure Marketplace e, em seguida, use o conector Informix como uma ação para remover pedidos de clientes. Por exemplo, você pode usar a operação de exclusão Informix conector condicional para processar uma instrução SQL DELETE (excluir de NEWORDERS onde ORDID > = 10000).

1. No menu hub do quadro Azure **Iniciar** , clique em **+** (sinal de mais), clique em **Web + Mobile**e clique em **aplicativo de lógica**. 
2. Na lâmina **criar lógica de aplicativo** , digite um **nome**, por exemplo **RemoveOrdersInformix**.
3. Selecione ou defina valores para as outras configurações (por exemplo, o plano de serviço, o grupo de recursos).
4. As configurações devem ter a seguinte aparência. Clique em **criar**:  
![][12]
5. Na lâmina **configurações** , clique em **disparadores e ações**.
6. Na lâmina **disparadores e ações** , na lista de **aplicativos de lógica modelos** , clique em **Criar do zero**.
7. Na lâmina **disparadores e ações** , no painel **Aplicativos da API** , no grupo de recursos, clique em **Recorrência**.
8. Na superfície de design de aplicativo lógica, clique no item de **Recorrência** , defina uma **frequência** e **intervalo**, por exemplo, **dias** e **1**e clique na **marca de seleção** para salvar as configurações de item de recorrência.
9. Na lâmina **disparadores e ações** , no painel **Aplicativos da API** , no grupo de recursos, clique em **conector Informix**.
10. Na superfície de design de aplicativo lógica, clique no item de ação **Informix conector** , clique nas reticências (**…**) para expandir a lista de operações e clique em **Excluir condicional de N**.
11. No item de ação de conector Informix, digite **ordid ge 10000** para uma **expressão que identifica um subconjunto de entradas**.
12. Clique na **marca de seleção** para salvar as configurações de ação e, em seguida, clique em **Salvar**. As configurações devem ter a seguinte aparência:  
![][13]
13. Clique para fechar a lâmina **disparadores e ações** e clique em para fechar a lâmina de **configurações** .
14. Na lista **Todos é executado** em **operações**, clique no item listados em primeiro lugar (executar mais recente).
15. Na lâmina **lógica aplicativo executar** , clique no item de **ação** .
16. Na lâmina **ação de aplicativo de lógica** , clique no **LINK de SAÍDAS**. Saídas devem ter a seguinte aparência:  
![][14]

**Observação:** Designer de aplicativo de lógica trunca nomes de tabela. Por exemplo, a operação **condicional excluir de NEWORDERS** é truncada para **condicional excluir de N**.


> [AZURE.TIP] Use instruções SQL a seguir para criar a tabela de exemplo e procedimentos armazenados. 

Você pode criar a tabela NEWORDERS de amostra usando instruções Informix SQL DDL a seguir:
 
    create table neworders (  
        ordid serial(10000) unique ,  
        custid int not null ,  
        empid int not null default 10000 ,  
        orddate date not null default today ,  
        reqdate date default today ,  
        shipdate date ,  
        shipid int not null default 10000 ,  
        freight decimal (9,2) not null default 0.00 ,  
        shipname char (40) not null ,  
        shipaddr char (60) not null ,  
        shipcity char (20) not null ,  
        shipreg char (15) not null ,  
        shipzip char (10) not null ,  
        shipctry char (15) not null default ''USA'' 
        )


Você pode criar o procedimento SPORDERID armazenado de amostra usando a seguinte instrução Informix DDL:
 
    create procedure sporderid ( ord_id int)  
        returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
        define xordid, xcustid, xempid, xshipid int;  
        define xorddate, xreqdate, xshipdate date;  
        define xfreight decimal (9,2);  
        define xshipname char (40);  
        define xshipaddr char (60);  
        define xshipcity char (20);  
        define xshipreg, xshipctry char (15);  
        define xshipzip char (10);  
        select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
            into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
            from neworders where ordid = ord_id;  
        return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## <a name="hybrid-configuration-optional"></a>Configuração híbrida (opcional)

> [AZURE.NOTE] Esta etapa é necessária apenas se você estiver usando DB2 conector locais por trás do firewall.

Serviço de aplicativo usa o Gerenciador de configuração híbrida para conectar-se com segurança ao seu sistema de local. Se o conector usa um local IBM DB2 de servidor para Windows, o Gerenciador de Conexão híbrida é necessário.

Consulte [usando o Gerenciador de Conexão híbrido](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Faça mais com o conector
Agora que o conector for criado, você pode adicioná-lo para um fluxo de trabalho de negócios usando um aplicativo de lógica. Consulte [quais são os aplicativos de lógica?](app-service-logic-what-are-logic-apps.md).

Crie aplicativos API usando APIs REST. Consulte a [referência de aplicativos de API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode analisar segurança de controle e as estatísticas de desempenho ao conector. Consulte [Gerenciar e monitorar seus aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png


