<properties
   pageTitle="Auditoria no depósito de dados do SQL Azure | Microsoft Azure"
   description="Introdução ao auditoria no depósito de dados do SQL Azure"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="auditing-in-azure-sql-data-warehouse"></a>Auditoria no depósito de dados do SQL Azure

> [AZURE.SELECTOR]
- [Auditoria](sql-data-warehouse-auditing-overview.md)
- [Detecção de ameaças](sql-data-warehouse-security-threat-detection.md)

SQL Data Warehouse auditoria permite registro de eventos no seu banco de dados a uma auditoria faça logon em sua conta de armazenamento do Azure. Auditoria pode ajudá-lo a manter Conformidade regulamentar, entender a atividade de banco de dados e percepção discrepâncias e anomalias que podem indicar preocupações de negócios ou suspeitas de violações de segurança. SQL Data Warehouse auditoria também se integra ao Microsoft Power BI para análise e relatórios busca detalhada.

Ferramentas de auditoria habilitam e facilitar a conformidade com os padrões de conformidade, mas não garantem a conformidade. Para obter mais informações sobre os programas do Azure que oferecem suporte à conformidade com padrões, consulte o <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Central de confiabilidade do Azure</a>.

+ [Fundamentos de auditoria do banco de dados]
+ [Configurar auditoria para seu banco de dados]
+ [Analisar os logs de auditoria e relatórios]

##<a id="subheading-1"></a>Fundamentos de auditoria de banco de dados do depósito de dados de SQL Azure


Auditoria de banco de dados do SQL Data Warehouse permite que você:

- **Manter** uma trilha de auditoria dos eventos selecionados. Você pode definir categorias de ações de banco de dados a serem auditados.
- **Relatório** de atividade de banco de dados. Você pode usar relatórios pré-configurado e um painel para começar rapidamente com relatórios de eventos e atividade.
- Relatórios de **análise** . Você pode encontrar eventos suspeitos, atividade incomum e tendências.

Você pode configurar a auditoria para as seguintes categorias de evento:

**SQL simples** e **SQL parâmetros** para os quais logs de auditoria coletada são classificados como  

- **Acesso a dados**
- **Alterações de esquema (DDL)**
- **Alterações de dados (DML)**
- **Contas, funções e permissões (DCL)**
- **Procedimento armazenado**, **Login** e **gerenciamento de transação**.

Para cada categoria de evento, auditoria de **sucesso** e as operações de **Falha** são configuradas separadamente.

Para obter mais detalhes sobre as atividades e eventos auditados, consulte a <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referência de formato de Log de auditoria (download de arquivo de documento)</a>.

Logs de auditoria são armazenados na sua conta de armazenamento do Azure. Você pode definir um período de retenção de log de auditoria.

Pode ser definida uma política de auditoria para um banco de dados específico ou como uma política de servidor padrão. Uma política de auditoria de servidor padrão será aplicada a todos os bancos de dados em um servidor que não têm uma específico substituição banco de dados auditoria política definida.

Antes de definir up auditoria Auditoria verificar se você estiver usando um [Cliente de nível inferior ""](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configurar auditoria para seu banco de dados

1. Inicie o <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

2. Navegue até a lâmina de configuração de banco de dados SQL Data Warehouse / SQL Server que você deseja auditar. Clique no botão **configurações** na parte superior e, em seguida, na lâmina configuração e selecione **auditoria**.

    ![][1]

3. Na lâmina configuração auditoria, primeiro desmarca a caixa de seleção **Herdar as configurações de auditoria do servidor** . Isso permite especificar as configurações para um determinado banco de dados.

    ![][2]

4. Em seguida, habilite a auditoria clicando **no** botão.

    ![][3]

5. Na lâmina configuração auditoria, selecione **Detalhes de armazenamento** para abrir a lâmina de armazenamento de Logs de auditoria. Selecione a conta de armazenamento do Azure onde logs serão salvo e, o período de retenção. **Dica:** Use a mesma conta de armazenamento para todos os bancos de dados auditadas para tirar o máximo proveito os modelos de relatórios pré-configurado.

    ![][4]

6. Clique no botão **Okey** para salvar a configuração de detalhes de armazenamento.


7. Em **Log por evento**, clique em **sucesso** e **Falha** para registrar todos os eventos ou escolher categorias de eventos individuais.


8. Se você estiver configurando a auditoria para um banco de dados, talvez você precise alterar a cadeia de conexão do seu cliente para garantir que dados de auditoria é capturado corretamente. Consulte o tópico de [Modificar FDQN de servidor na cadeia de conexão](sql-data-warehouse-auditing-downlevel-clients.md) para conexões de cliente de nível inferior.

9. Clique em **Okey**.


##<a id="subheading-3">Analisar os logs de auditoria e relatórios</a>

Logs de auditoria são agregados em uma coleção de tabelas de armazenamento com um prefixo **SQLDBAuditLogs** na conta de armazenamento do Azure que você escolheu durante a instalação. Você pode exibir arquivos de log usando uma ferramenta como o <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Gerenciador de armazenamento do Azure</a>.

Um modelo de relatório de painel pré-configurado está disponível como uma <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">planilha do Excel para download</a> para ajudá-lo a analisar os dados de log rapidamente. Para usar o modelo em seus logs de auditoria, você precisa Excel 2013 ou superior e Power Query, que você pode baixar <a href="http://www.microsoft.com/download/details.aspx?id=39379">aqui</a>.

O modelo tem dados de exemplo fictício nele e você pode configurar o Power Query para importar o log de auditoria diretamente da sua conta de armazenamento do Azure.

Para obter instruções mais detalhadas sobre como trabalhar com o modelo de relatório, leia o <a href="http://go.microsoft.com/fwlink/?LinkId=506731">How To (download doc)</a>.

![][5]


##<a id="subheading-4">Práticas recomendadas para uso em produção</a>
A descrição nesta seção se refere a capturas de tela acima. <a href="https://portal.azure.com" target="_blank">Portal Azure</a> ou <a href= "https://manage.windowsazure.com/" target="_bank">Clássico Portal clássico do Azure</a> pode ser usado.


##<a id="subheading-5"></a>Nova geração de chave de armazenamento

Em produção você provavelmente serão atualizar suas chaves de armazenamento periodicamente. Suas chaves de atualização quando você precisar salvar novamente a política. O processo é da seguinte maneira:.


1. Na lâmina configuração auditoria (Configurar auditoria seção descrita acima) alterne a **Tecla de acesso de armazenamento** do *principal* para *secundário* e em **Salvar**.
![][4]
2. Vá para a lâmina de configuração de armazenamento e **Gerar** a *Chave primária de acesso*.

3. Vá para a lâmina de configuração de auditoria, alterne a **Tecla de acesso de armazenamento** de *secundário* para *primário* e pressione **Salvar**.

4. Volte para o armazenamento UI e **Gerar** a *Chave de acesso secundária* (como preparação para o próximo ciclo de atualização de chaves.

##<a id="subheading-6"></a>Automação
Há vários cmdlets do PowerShell, que você pode usar para configurar a auditoria no Azure SQL Database. Para acessar os cmdlets auditoria você deve estar executando o PowerShell no modo do Gerenciador de recursos do Azure.

> [AZURE.NOTE] O módulo do [Gerenciador de recursos do Azure](https://msdn.microsoft.com/library/dn654592.aspx) está no modo de visualização. Ele não pode fornecer os mesmos recursos de gerenciamento como o módulo Azure.

Quando você estiver no modo do Gerenciador de recursos do Azure, execute `Get-Command *AzureSql*` para listar os cmdlets disponíveis.


<!--Anchors-->
[Fundamentos de auditoria do banco de dados]: #subheading-1
[Configurar auditoria para seu banco de dados]: #subheading-2
[Analisar os logs de auditoria e relatórios]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
