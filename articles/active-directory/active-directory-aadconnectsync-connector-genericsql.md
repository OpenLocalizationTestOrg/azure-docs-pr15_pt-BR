<properties
   pageTitle="Conector do SQL genérico | Microsoft Azure"
   description="Este artigo descreve como configurar conector de SQL genérico da Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-technical-reference"></a>Referência técnica de SQL Connector genérica

Este artigo descreve o conector de SQL genérico. O artigo aplica-se aos seguintes produtos:

- Gerenciador de identidades do Microsoft 2016 (MIM2016)
- Gerenciador de identidades Forefront 2010 R2 (FIM2010R2)
    -   Deve usar hotfix 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como um download a partir do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Para ver esse conector em ação, consulte o artigo [Genérico SQL Connector passo a passo](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) .

## <a name="overview-of-the-generic-sql-connector"></a>Visão geral do conector SQL genérico

O conector de SQL genérico permite integrar o serviço de sincronização com um sistema de banco de dados que oferece conectividade ODBC.  

De uma perspectiva de alto nível, os seguintes recursos são suportados pela versão atual do conector:

Recurso | Suporte
--- | ---
Fonte de dados conectada | O conector é compatível com todos os drivers ODBC de 64 bits. Ele foi testado com o seguinte: <li>Microsoft SQL Server e do SQL Azure</li><li>IBM DB2 10. x</li><li>IBM DB2 9</li><li>G Oracle 10 e 11</li><li>MySQL 5. x</li>
Cenários   | <li>Gerenciamento de ciclo de vida do objeto</li><li>Gerenciamento de senha</li>
Operações | <li>Importação de Delta, exportação e importação completa</li><li>Para exportar: Adicionar, excluir, atualização e substituir</li><li>Definir senha, alterar senha</li>
Esquema | <li>Detecção dinâmica de objetos e atributos</li>

### <a name="prerequisites"></a>Pré-requisitos
Antes de usar o conector, verifique se que você tem o seguinte no servidor de sincronização:

- 4.5.2 do Microsoft .NET Framework ou posterior
- drivers de cliente do ODBC de 64 bits

### <a name="permissions-in-connected-data-source"></a>Permissões na fonte de dados conectados
Para criar ou executar as tarefas com suporte no conector de SQL genérico, você deve ter:

- db_datareader
- db_datawriter

### <a name="ports-and-protocols"></a>Portas e protocolos
Para as portas necessárias para o driver ODBC trabalhar, consulte a documentação do fornecedor do banco de dados.

## <a name="create-a-new-connector"></a>Criar um novo conector
Para criar um conector de SQL genérico, no **Serviço de sincronização** , selecione **Agente de gerenciamento** e **criar**. Selecione o conector **SQL genérico (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Conectividade
O conector usa um arquivo DSN ODBC para conectividade. Crie o arquivo DSN usando **Fontes de dados ODBC** encontrado no menu Iniciar, em **Ferramentas administrativas**. Na ferramenta de administrativas, crie um **DSN do arquivo** para que ele pode ser fornecido ao conector.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

A tela de conectividade é o primeiro quando você cria um novo conector de SQL genérico. Você precisa primeiro forneça as seguintes informações:

- Caminho do arquivo DSN
- Autenticação
    - Nome de usuário
    - Senha

O banco de dados deve oferecer suporte a um desses métodos de autenticação:

- **Autenticação do Windows**: O banco de dados de autenticação usa as credenciais do Windows para verificar o usuário. O nome de usuário/senha especificado é usado para autenticar com o banco de dados. Essa conta precisa de permissões para o banco de dados.
- **Autenticação do SQL**: O usa autenticação de banco de dados o nome de usuário/senha definido uma tela de conectividade para se conectar ao banco de dados. Se você armazenar a nome de usuário/senha no arquivo DSN, as credenciais fornecidas na tela de conectividade têm precedência.
- **Autenticação de banco de dados do Azure SQL**: para obter mais informações, consulte [conectar ao banco de dados por usando Azure Active Directory autenticação do SQL](..\sql-database\sql-database-aad-authentication.md).

**DN é âncora**: se você selecionar essa opção, o DN também é usado como o atributo de âncora. Ele pode ser usado para uma implementação simples, mas também tem as seguintes limitações:

-   Conector suporta somente um tipo de objeto. Portanto, os atributos de referência podem fazer referência apenas o mesmo tipo de objeto.

**Tipo de exportação: objeto substituir**: durante a exportação, quando apenas alguns atributos alterou, o objeto inteiro com todos os atributos é exportado e substitui o objeto existente.

### <a name="schema-1-detect-object-types"></a>Esquema 1 (detectar tipos de objeto)
Nesta página, você vai configurar como o conector vai encontrar os diferentes tipos de objeto do banco de dados.

Cada tipo de objeto é apresentado como uma partição e configurado ainda mais **Configurar partições e hierarquias**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Método de detecção de tipo de objeto**: O conector suporta esses métodos de detecção de tipo de objeto.

- **Valor fixo**: você fornecer a lista de tipos de objeto com uma lista separada por vírgulas. Por exemplo: `User,Group,Department`.  
![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Procedimento tabela/exibição/armazenado**: fornecer o nome do procedimento tabela/modo de exibição/armazenado e, em seguida, o nome da coluna que fornece a lista de tipos de objeto. Se você usar um procedimento armazenado, também fornecer parâmetros para ele no formato **[nome]: [direção]: [valor]**. Fornece cada parâmetro em uma linha separada (use Ctrl + Enter para começar uma nova linha).  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **Consulta SQL**: essa opção permite que você forneça uma consulta SQL que retorna uma única coluna com tipos de objeto, por exemplo `SELECT [Column Name] FROM TABLENAME`. A coluna retornada deve ser do tipo cadeia de caracteres (varchar).

### <a name="schema-2-detect-attribute-types"></a>Esquema 2 (tipos de atributo detectar)
Nesta página, você vai configurar como os nomes de atributo e tipos vai ser detectados. As opções de configuração são listadas para cada tipo de objeto detectado na página anterior.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Método de detecção de tipo de atributo**: O conector suporta esses métodos de detecção de tipo de atributo com cada tipo de objeto detectado na tela 1 de esquema.

- **Procedimento tabela/exibição/armazenado**: fornecer o nome do procedimento tabela/modo de exibição/armazenado que deve ser usado para localizar os nomes de atributo. Se você usar um procedimento armazenado, também fornecer parâmetros para ele no formato **[nome]: [direção]: [valor]**. Fornece cada parâmetro em uma linha separada (use Ctrl + Enter para começar uma nova linha). Para detectar os nomes de atributo em um atributo de valores múltiplos, forneça uma lista separada por vírgulas de tabelas ou exibições. Cenários de múltiplos valores não têm suporte quando o tabela pai e filho tem os mesmos nomes de coluna.
- **Consulta SQL**: essa opção permite que você forneça uma consulta SQL que retorna uma única coluna com nomes de atributo, por exemplo `SELECT [Column Name] FROM TABLENAME`. A coluna retornada deve ser do tipo cadeia de caracteres (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Esquema 3 (definir âncora e DN)
Esta página permite que você configure âncora e atributo DN para cada tipo de objeto detectado. Você pode selecionar vários atributo para tornar a âncora exclusivo.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Atributos de valores múltiplos e Boolianas não são listados.
- Mesmo atributo não pode usar para DN e âncora, a menos que **DN é âncora** está selecionada na página de conectividade.
- Se **DN é âncora** estiver selecionado na página de conectividade, esta página requer somente o atributo DN. Esse atributo também poderia ser usado como o atributo de âncora.
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Esquema 4 (Definir tipo de atributo, referência e direção)
Esta página permite que você configure o tipo de atributo, como um número inteiro, binário, ou booliano e direção para cada atributo. Todos os atributos de página **esquema 2** são listados, incluindo atributos de valores múltiplos.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Tipo de dados**: usado para mapear o tipo de atributo para esses tipos conhecido pelo mecanismo de sincronização. O padrão é usar o mesmo tipo como detectados no esquema de SQL, mas não são facilmente detectáveis DateTime e referência. Para aqueles, você precisa especificar **DateTime** ou **referência**.
- **Direção**: você pode definir a direção de atributo para importar, exportar ou ImportExport. ImportExport é o padrão.
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Observações:

- Se um tipo de atributo não for detectável pelo conector, ele usa o tipo de dados de cadeia de caracteres.
- **Tabelas de aninhado** pode ser considerado tabelas de banco de dados de uma coluna. Oracle armazena as linhas de uma tabela aninhada em nenhuma ordem específica. No entanto, quando você recupera a tabela aninhada em uma variável de PL/SQL, as linhas são fornecidas consecutivos subscritos começando em 1. Que lhe dá acesso de matriz semelhante às linhas individuais.
- **VARRYS** não são suportados no conector.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Esquema 5 (Definir partição para atributos de referência)
Nesta página, você deve configurar para todos os atributos de referência qual partição (tipo de objeto) um atributo está fazendo referência a.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Se você usar **DN é âncora**, você deve usar o mesmo tipo de objeto que você estiver fazendo referência de. Você não pode referenciar outro tipo de objeto.

### <a name="global-parameters"></a>Parâmetros globais
A página de parâmetros globais é usada para configurar a importação de Delta, o formato de data/hora e método de senha.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

O conector de SQL genérico suporta os seguintes métodos para importação de Delta:

- **Gatilho**: consulte [gerando modos de exibição de Delta usando gatilhos](https://technet.microsoft.com/library/cc708665.aspx).
- **Marca d'água**: uma abordagem genérica que pode ser usada com qualquer banco de dados. A consulta de marca d'água é pré-populadas baseadas no fornecedor do banco de dados. Uma coluna de marca d'água deve estar presente em cada tabela/exibição usada. Esta coluna deve controlar inserções e atualizações tabelas como e seus dependentes (valores múltiplos ou filho) tabelas. Os relógios entre o serviço de sincronização e o servidor de banco de dados devem ser sincronizados. Caso contrário, algumas entradas na importação de delta podem ser omitidas.  
Limitação:
    - Estratégia de marca d'água faz não os objetos excluído de suporte.
- **Instantâneo**: (funciona somente com o Microsoft SQL Server) [gerar exibições de Delta usando instantâneos](https://technet.microsoft.com/library/cc720640.aspx)
- **Controle de alterações**: (funciona somente com o Microsoft SQL Server) [sobre o controle de alterações](https://msdn.microsoft.com/library/bb933875.aspx)  
Limitações:
    - Âncora & atributo DN devem ser parte da chave primária para o objeto selecionado na tabela.
    - Consulta SQL é suportada durante a importação e exportação com controle de alterações.

**Parâmetros adicionais**: especificar o fuso horário de servidor de banco de dados que indica onde seu servidor de banco de dados está localizado. Esse valor é usado para dar suporte os vários formatos de data e hora atributos.

O conector sempre armazena data e a data e hora no formato UTC. Para poder converter corretamente a data e hora, o fuso horário do servidor de banco de dados e o formato usado deve ser especificado. O formato deve ser expressa em formato .net.

Durante a exportação cada atributo de tempo de data deve ser fornecido ao conector no formato de hora UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuração de senha**: O conector fornece recursos de sincronização de senha e suporte para define e alterar senha.

O conector fornece dois métodos para oferecer suporte à sincronização de senha:

- **Procedimento armazenado**: este método requer dois procedimentos armazenados para dar suporte a definir & Alterar senha. Digite todos os parâmetros para adicionar e alterar a operação de senha em **Definir senha SP** e parâmetros de **Alterar senha SP** respectivamente como por exemplo abaixo.
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Extensão de senha**: este método requer DLL (necessário para fornecer o nome da DLL de extensão que está implementando a interface [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) ) de extensão de senha. Assembly de extensão de senha deve ser colocado na pasta de extensão para que o conector pode carregar a DLL no tempo de execução.
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Você também precisa ativar o gerenciamento de senha na página **Configurar extensão** .
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar partições e hierarquias
Na página partições e hierarquias, selecione todos os tipos de objeto. Cada tipo de objeto é sua própria partição.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Você também pode substituir os valores definidos na página **conectividade** ou **Parâmetros globais** .

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Configurar âncoras
Esta página é somente leitura, pois a âncora já foi definida. O atributo de âncora selecionados sempre é acrescentado com o tipo de objeto para garantir que ela permanece exclusiva em tipos de objeto.

![âncoras](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Configurar parâmetro de etapa de execução
Estas etapas são configuradas nos perfis de executar o conector. Essas configurações fazem o trabalho real de importação e exportação de dados.

### <a name="full-and-delta-import"></a>Completo e importação de Delta
Genérico SQL Connector suporte completo e importação de Delta usando esses métodos:

- Tabela
- Modo de exibição
- Procedimento armazenado
- Consulta SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabela/modo de exibição**  
Para importar múltiplos atributos de um objeto, você deve fornecer o nome de tabela/exibição separados por vírgulas em **exibições da tabela nome de Multi-Valued** e condições de associação respectivos na **condição de associação** com a tabela pai.

Exemplo: Que você deseja importar o objeto de funcionário e todos os seus atributos de valores múltiplos. Há duas tabelas, chamadas funcionário (tabela principal) e departamento (valores múltiplos).
Faça o seguinte:

- Tipo **funcionários** no **Modo de exibição/tabela/SP**.
- Digite departamento **exibições da tabela nome de Multi-Valued**.
- Digite a condição de junção entre departamento e funcionários em **Condição de junção**, por exemplo `Employee.DEPTID=Department.DepartmentID`.
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procedimentos armazenados**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Se você tiver a quantidade de dados, é recomendável implementar paginação com os procedimentos armazenados.
- O procedimento armazenado dar suporte a paginação, é necessário fornecer iniciar índice e índice de fim. Consulte: [paginação com eficiência grandes quantidades de dados](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndexe @EndIndex são substituídos em tempo de execução com o valor de tamanho de página respectivos configurado na **Etapa de configurar** página. Por exemplo, quando o conector recupera a primeira página e o tamanho da página é definida 500, em situação @StartIndex seria 1 e @EndIndex 500. Esses valores aumentam quando conector recupera subsequentes páginas e altere o @StartIndex & @EndIndex valor.
- Para executar o procedimento armazenado parametrizada, fornecer os parâmetros no `[Name]:[Direction]:[Value]` formato. Insira cada parâmetro em uma linha separada (usar Ctrl + Enter para começar uma nova linha).
- Conector SQL genérico também oferece suporte a operação de importação de servidores vinculados no Microsoft SQL Server. Se informações devem ser recuperadas de uma tabela no servidor vinculado, tabela deve ser fornecida no formato:`[ServerName].[Database].[Schema].[TableName]`
- Conector de SQL genérico suporta somente os objetos que têm estrutura semelhante (tanto alias nome e tipo de dados) entre a executar as etapas a detecção de informações e esquema. Se o objeto selecionado de esquema e as informações fornecidas na etapa execução for diferente, o conector de SQL é possível suportam esse tipo de cenários.

**Consulta SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Consultas sem suporte do conjunto de resultados múltiplos.
- Consulta SQL ofereça suporte a paginação e forneça iniciar índice e índice de fim como uma variável para dar suporte a paginação.

### <a name="delta-import"></a>Importação delta
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Configuração de importação de delta requer alguma configuração mais em comparação com importação completa.

- Se você escolher a abordagem disparador ou instantâneo para controlar alterações delta, então fornece banco de dados de uma tabela de histórico ou instantâneo na caixa **nome do banco de dados de instantâneo ou de tabela de histórico** .
- Você também precisará fornecer condição de junção entre a tabela de histórico e tabela pai, por exemplo`Employee.ID=History.EmployeeID`
- Para controlar a transação na tabela pai da tabela de histórico, você deve fornecer o nome da coluna que contém as informações de operação (Adicionar/atualizar/excluir).
- Se você escolher a marca d'água para controlar alterações delta, fornece o nome da coluna que contém as informações de operação em **Nome de coluna de marca d'água**.
- A coluna **Alterar tipo atributo** é necessária para o tipo de alteração. Esta coluna mapeia uma alteração que ocorre na tabela primária ou tabela de múltiplos valores a um tipo de alteração no modo de exibição delta. Esta coluna pode conter Modify_Attribute alterar tipo de alteração de nível de atributo ou um adicionar, modificar ou excluir alterar tipo para um tipo de alteração do nível de objeto. Se for algo diferente do valor padrão adicionar, modificar ou excluir, você pode definir esses valores usando essa opção.

### <a name="export"></a>Exportar
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Suporte de SQL Connector genérico exportação usando quatro métodos com suporte como:

- Tabela
- Modo de exibição
- Procedimento armazenado
- Consulta SQL

**Tabela/modo de exibição**  
Se você escolher a opção de tabela/modo de exibição, o conector gera as respectivas consultas para fazer a exportação.

**Procedimentos armazenados**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Se você escolher a opção de procedimento armazenado, exportar requer três procedimentos armazenados diferentes para executar operações de inserir/atualizar/excluir.

- **Adicionar nome de SP**: This SP executa se qualquer objeto chegarem ao conector para inserção na tabela respectiva.
- **Atualizar o nome de SP**: This SP executa se qualquer objeto chegarem ao conector para atualização na tabela respectiva.
- **Excluir o nome de SP**: This SP executa se qualquer objeto chegarem ao conector para exclusão na tabela respectiva.
- Atributo selecionado do esquema usado como um valor de parâmetro para o procedimento armazenado. Por exemplo, `EmployeeName: INPUT: @EmployeeName` (EmployeeName está selecionado no esquema de conector e o conector substitui o respectivo valor enquanto efetua a exportação)
- Para executar o procedimento armazenado parametrizado, fornecer parâmetros no `[Name]:[Direction]:[Value]` formato. Insira cada parâmetro em uma linha separada (usar Ctrl + Enter para começar uma nova linha).

**Consulta SQL**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Se você escolher a opção de consulta SQL, exportar requer três consultas diferentes para executar operações de inserir/atualizar/excluir.

- **Inserir consulta**: essa consulta é executada se qualquer objeto chegarem ao conector para inserção na tabela respectiva.
- **Atualizar consulta**: essa consulta é executada se qualquer objeto chegarem ao conector para atualização na tabela respectiva.
- **Consulta exclusão**: essa consulta é executada se qualquer objeto chegarem ao conector para exclusão na tabela respectiva.
- Atributo selecionado do esquema usado como um valor de parâmetro à consulta, por exemplo`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Solução de problemas

-   Para obter informações sobre como habilitar o registro em log solucionar o conector, consulte [como habilitar o rastreamento ETW para conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
