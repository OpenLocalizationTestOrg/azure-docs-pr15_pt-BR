<properties
   pageTitle="Introdução ao detecção de ameaças de depósito de dados do SQL"
   description="Como começar com a detecção de ameaças"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# <a name="get-started-with-threat-detection"></a>Introdução ao detecção de ameaças

> [AZURE.SELECTOR]
- [Auditoria](sql-data-warehouse-auditing-overview.md)
- [Detecção de ameaças](sql-data-warehouse-security-threat-detection.md)

## <a name="overview"></a>Visão geral

Detecção de ameaças detecta atividades de banco de dados anômalos indicando possíveis ameaças de segurança ao banco de dados. Detecção de ameaças é na visualização e tem suporte para o SQL Data Warehouse.

Detecção de ameaças fornece uma nova camada de segurança, que permite que os clientes detectar e responder a possíveis ameaças conforme eles ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários podem explorar os eventos suspeitos usando [Auditoria de depósito de dados do Azure SQL](sql-data-warehouse-auditing-overview.md) para determinar se eles resultam de uma tentativa de acessar, violação ou explorar dados no data warehouse.
Detecção de ameaças facilita a tratar de possíveis ameaças ao data warehouse sem precisar ser uma especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançada.

Por exemplo, a detecção de ameaças detecta determinadas atividades de banco de dados anômalos indicando possíveis tentativas de inclusão de SQL. Inserção de SQL é um dos problemas comuns de segurança de aplicativo da Web na Internet, usado para atacar aplicativos orientados a dados. Ataques tirar proveito de vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionado nos campos de entrada do aplicativo, para quebrar ou modificar dados no banco de dados.


## <a name="set-up-threat-detection-for-your-database"></a>Configurar a detecção de ameaças para seu banco de dados

1. Abra o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

2. Navegue até a lâmina de configuração do Data Warehouse SQL que você deseja monitorar. Na lâmina configurações, selecione **auditoria e detecção de ameaças**.

    ![Painel de navegação][1]

3. Na lâmina configuração **auditoria & detecção de ameaças** Ative **Diante** auditoria, que exibirá as configurações de detecção de ameaças.

    ![Painel de navegação][2]

4. Ative a detecção de ameaças **ativado** .

5. Configure a lista de emails que vai receber alertas de segurança sobre a detecção de atividades de depósito de dados anômalos.

6. Clique em **Salvar** na lâmina **detecção de auditoria e ameaças** configuração para salvar o novo ou atualizado de auditoria e política de detecção de ameaças.

    ![Painel de navegação][3]


## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades de depósito de dados anômalos após a detecção de um evento suspeito

1. Você receberá uma notificação por email sobre a detecção de atividades anormais de banco de dados. <br/>
O email fornecerá informações sobre o evento de segurança suspeitos incluindo a natureza das atividades anormais, nome do banco de dados, o nome do servidor e a hora do evento. Além disso, ele fornecerá informações sobre as possíveis causas e ações para investigar e atenuar as ameaças potenciais ao banco de dados recomendadas.<br/>

    ![Painel de navegação][4]

2. No email, clique no link **O Log de auditoria do SQL Azure** , que iniciará o Portal de clássico do Azure e mostrar os registros de auditoria relevantes perto da hora do evento suspeito.

    ![Painel de navegação][5]

3. Clique nos registros de auditoria para exibir mais detalhes sobre as atividades de banco de dados suspeito como instrução SQL, IP de cliente e motivo de falha.

    ![Painel de navegação][6]

4. Na lâmina registros de auditoria, clique em **Abrir no Excel** para abrir um pré-configurado modelo para importar e executar uma análise mais profunda de log de auditoria perto da hora do evento suspeito do excel.<br/>
**Observação:** No Excel 2010 ou posterior, o Power Query e a configuração de **Combinação rápida** é necessária

    ![Painel de navegação][7]

5. Para configurar a configuração de **Combinação rápida** - na faixa de opções **POWER QUERY** , selecione **Opções** para exibir a caixa de diálogo de opções. Selecione a seção de privacidade e escolher a segunda opção - 'Ignorar os níveis de privacidade e melhorar o desempenho potencialmente':

    ![Painel de navegação][8]

6. Para carregar logs de auditoria do SQL, certifique-se de que os parâmetros nas configurações de guia estão definidas corretamente e, em seguida, selecione a faixa de opções 'Dados' e clique no botão 'Atualizar tudo'.

    ![Painel de navegação][9]

7. Os resultados aparecem na folha de **Logs de auditoria do SQL** que permite que você execute uma análise mais profunda das atividades anormais detectados e reduzir o impacto do evento de segurança em seu aplicativo.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
