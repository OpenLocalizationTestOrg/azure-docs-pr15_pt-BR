<properties
   pageTitle="Serviço Central de segurança do Azure e Azure SQL Database | Microsoft Azure"
   description="Este artigo mostra como o Centro de segurança pode ajudar você a proteger seus bancos de dados no Azure SQL Database."
   services="sql-database"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-and-azure-sql-database-service"></a>Serviço Central de segurança do Azure e Azure SQL Database

[O Centro de segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda você a evitar, detectar e responder a ameaças. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas Azure, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

Este artigo mostra como o Centro de segurança pode ajudar você a proteger seus bancos de dados no Azure SQL Database.

## <a name="why-use-security-center"></a>Por que usar o Centro de segurança?

Central de segurança ajuda você a proteger os dados no banco de dados do SQL, fornecendo visibilidade a segurança de todos os seus servidores e bancos de dados. Com o Centro de segurança, você pode:

- Defina políticas para criptografia do banco de dados SQL e auditoria.
- Monitore a segurança dos recursos de banco de dados SQL em todas as suas assinaturas.
- Identificar e corrigir problemas de segurança rapidamente.
- Integre alertas de [detecção de ameaças do Azure SQL Database](../sql-database/sql-database-threat-detection-get-started.md).

Além de ajudar a proteger seus recursos de banco de dados SQL, o Centro de segurança também fornece monitoramento de segurança e gerenciamento do Azure máquinas virtuais, serviços de nuvem, os serviços de aplicativo, redes virtuais e muito mais. Saiba mais sobre o Centro de segurança [aqui](security-center-intro.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar o Centro de segurança, você deve ter uma assinatura do Microsoft Azure. A camada livre da Central de segurança é habilitada com a sua assinatura. Para obter mais informações sobre o Centro de segurança gratuitos e níveis padrão, consulte [Preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/).

Centro de segurança oferece suporte a acesso baseado em função. Para saber mais sobre o controle de acesso baseado em função (RBAC) no Azure, consulte [Controle de acesso baseado em função do Azure Active Directory](../active-directory/role-based-access-control-configure.md). As perguntas Frequentes Centro de segurança fornece informações sobre [como as permissões são tratadas no Centro de segurança](security-center-faq.md#how-are-permissions-handled-in-azure-security-center).

## <a name="access-security-center"></a>Central de segurança do Access

Você pode acessar o Centro de segurança a partir do [portal do Azure](https://azure.microsoft.com/features/azure-portal/). [Entrar no portal](https://portal.azure.com/) e selecione a **opção central de segurança**.

![Opção de Central de segurança][1]

A lâmina do **Centro de segurança** é aberta.
![Blade de centro de segurança][2]

## <a name="set-security-policy"></a>Política de segurança do conjunto

Uma política de segurança define o conjunto de controles que são recomendados para recursos dentro da inscrição especificada ou o grupo de recursos. No Centro de segurança, você definir políticas para suas assinaturas ou grupos de recursos de acordo com as necessidades de segurança da sua empresa e do tipo de aplicativos ou sigilo dos dados de cada assinatura.

Você pode definir uma política para mostrar recomendações para auditoria de SQL e criptografia de dados transparente SQL (TDE).

- Quando você ativa **SQL auditoria e detecção de ameaças**, o Centro de segurança recomenda que auditoria de acesso ao banco de dados do Azure ser habilitados para fins de conformidade, detecção e fins de investigação avançadas.
- Quando você ativar a **criptografia de dados transparente SQL**, o Centro de segurança recomenda que a criptografia inativos estar habilitada para seu banco de dados do SQL Azure, backups associados e arquivos de log de transação.

Para definir uma política de segurança, selecione o bloco de **política** na lâmina Central de segurança. Na lâmina **política de segurança** , selecione a assinatura que você deseja habilitar a política de segurança. Selecione **a política de prevenção** e **Ative as recomendações de segurança que você deseja usar essa assinatura** .
![Política de segurança][3]

Para saber mais, consulte [definir políticas de segurança](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Gerenciar recomendação de segurança

Centro de segurança periodicamente analisa o estado de segurança de seus recursos Azure. Quando o Centro de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações. As recomendações orientação-lo no processo de configuração controles necessários.

Depois de definir uma política de segurança, o Centro de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. As recomendações são exibidas em um formato de tabela em que cada linha representa uma recomendação específica. Use a tabela a seguir como uma referência para ajudá-lo a compreender as recomendações disponíveis para o banco de dados do SQL Azure e o que faz cada recomendação se aplicá-lo. Selecionar uma recomendação leva você para um artigo que explica como implementar a recomendação no Centro de segurança.

| Recomendação | Descrição |
| ----- | ----- |
| [Ativar a detecção de auditoria e ameaças nos servidores SQL](security-center-enable-auditing-on-sql-servers.md) | Recomenda-se de que você ative a detecção de auditoria e ameaças para os servidores de banco de dados SQL. (Somente para serviço de banco de dados SQL. Não incluir executados em suas máquinas virtuais do Microsoft SQL Server). |
| [Ativar a detecção de auditoria e ameaças em bancos de dados SQL](security-center-enable-auditing-on-sql-databases.md) | Recomenda-se de que você ative a detecção de auditoria e ameaças para bancos de dados do banco de dados SQL. (Somente para serviço de banco de dados SQL. Não incluir executados em suas máquinas virtuais do Microsoft SQL Server). |
| [Habilitar a criptografia de dados transparente](security-center-enable-transparent-data-encryption.md) | Recomenda-se de que você habilite a criptografia para bancos de dados SQL. (Banco de dados SQL service somente). |

Para ver recomendações para seus recursos Azure, selecione o bloco de **recomendações** na lâmina Central de segurança. Na lâmina **recomendações** , selecione uma recomendação para ver detalhes. Neste exemplo, vamos selecione **Habilitar auditoria e detecção de ameaças em servidores de SQL**.

![Recomendações][4]

Como mostrado abaixo, o Centro de segurança mostra os servidores SQL onde a detecção de auditoria e ameaças não estão ativados. Depois de Ativar auditoria, você pode configurar as configurações de detecção de ameaças e configurações de email para receber alertas de segurança. Detecção de ameaças alerta quando detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados. Os alertas são exibidos no painel do Centro de segurança.
![Detecção de auditoria e ameaças][5]

Siga as etapas em [Introdução ao detecção de ameaças de banco de dados SQL](../sql-database/sql-database-threat-detection-get-started.md) para ativar e configurar a detecção de ameaças e configurar a lista de emails que vai receber alertas de segurança sobre a detecção de atividades anormais.

Para saber mais sobre as recomendações, consulte [Gerenciando recomendações de segurança](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorar a saúde de segurança

Depois de habilitar [políticas de segurança](security-center-policies.md) para recursos de uma assinatura, o Centro de segurança analisará a segurança dos seus recursos para identificar possíveis vulnerabilidades.  Você pode exibir o estado de segurança de seus recursos do bloco de **integridade de segurança do recurso** . Quando você clica **dados** no bloco **integridade dos recursos de segurança** , a lâmina de **Recursos de dados** é aberta com recomendações de SQL para problemas como criptografia de dados de auditoria e transparente não está habilitado. Ele também tem recomendações para o estado de integridade geral do banco de dados.
![Integridade de segurança do recurso][6]

Para saber mais, consulte [monitoramento de integridade de segurança](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gerenciar e responder a alertas de segurança

Centro de segurança automaticamente coleta, analisa e integra dados do log de [Detecção de ameaças do SQL Azure](../sql-database/sql-database-threat-detection-get-started.md), bem como outros recursos Azure, para detectar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioridades é mostrada no Centro de segurança, juntamente com as informações que necessárias para investigar rapidamente o problema e recomendações sobre como corrigir um ataque.

Para ver alertas, selecione o bloco de **alertas de segurança** na lâmina Central de segurança. Na lâmina **alertas de segurança** , selecione um alerta para saber mais sobre os eventos que disparou o alerta e, se houver, as etapas necessárias para remediar um ataque. Neste exemplo, vamos selecionar a **inclusão de SQL possíveis**.
![Alertas de segurança][7]

Como mostrado abaixo, o Centro de segurança fornece detalhes adicionais que oferecem percepção que disparou o alerta, o recurso de destino, quando aplicável o endereço IP de origem e recomendações sobre como corrigir.
![Possível inclusão de SQL][8]

Para saber mais, consulte [Gerenciar e responder a alertas de segurança](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes sobre o Centro de segurança](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço.
- [Guia de planejamento e operações do Centro de segurança](security-center-planning-and-operations-guide.md) - acompanhar um conjunto de etapas e tarefas para otimizar o uso da Central de segurança com base em requisitos de segurança e o modelo de gerenciamento de nuvem da sua organização.
- [Segurança de dados do Centro de segurança](security-center-data-security.md) – Saiba como o Centro de segurança coleta e processa dados sobre os recursos do Azure, incluindo informações de configuração, metadados, logs de eventos, arquivos de despejo de falha e muito mais.
- [Ocorrências de segurança de tratamento](security-center-incident.md) - Aprenda a usar o recurso de alerta de segurança na Central de segurança para ajudá-lo a lidar com ocorrências de segurança.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
