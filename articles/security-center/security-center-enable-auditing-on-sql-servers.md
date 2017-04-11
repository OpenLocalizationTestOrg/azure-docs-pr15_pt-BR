<properties
   pageTitle="Habilitar a auditoria nos servidores SQL na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **Habilitar a auditoria nos servidores SQL**."
   services="security-center"
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-auditing-on-sql-servers-in-azure-security-center"></a>Habilitar a auditoria nos servidores SQL na Central de segurança do Azure

O Centro de segurança do Azure será recomendável que você ative a auditoria para todos os bancos de dados nos seus servidores SQL Azure se auditoria já não estiver habilitada. Auditoria pode ajudá-lo a manter Conformidade regulamentar, entender a atividade de banco de dados e percepção discrepâncias e anomalias que podem indicar preocupações de negócios ou suspeitas de violações de segurança.

Depois que você tiver ativado auditoria você pode definir configurações de detecção de ameaças e emails para receber alertas de segurança. Detecção de ameaças detecta atividades de banco de dados anômalos indicando possíveis ameaças de segurança ao banco de dados. Isso permite detectar e responder a possíveis ameaças conforme eles ocorrem.

Essa recomendação se aplica ao serviço SQL Azure ele não inclui o SQL Server em execução em suas máquinas virtuais em serviços de infraestrutura do Azure (Azure IaaS).

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **Habilitar auditoria nos servidores SQL**.  Isso abre a lâmina **Habilitar auditoria nos servidores SQL** .
![Habilitar a auditoria nos servidores SQL][1]

2. Selecione um SQL server para habilitar a auditoria em. Isso abre a lâmina de **Configurações de auditoria** .
![Configurações de auditoria][2]
3. Na lâmina **Configurações de auditoria** , selecione **Diante** em **auditoria**.
![Ativar as configurações de auditoria][3]

4. Siga as etapas em [Introdução ao auditoria de banco de dados do SQL](../sql-database/sql-database-auditing-get-started.md) para configurar o armazenamento onde os logs de auditoria serão armazenados. Conta de armazenamento da assinatura para coleta de dados é a conta de armazenamento padrão.

5. Siga as etapas em [Introdução ao detecção de ameaças de banco de dados SQL](../sql-database/sql-database-threat-detection-get-started.md) para ativar e configurar a detecção de ameaças e configurar a lista de emails que vai receber alertas de segurança sobre a detecção de atividades anormais.

## <a name="see-also"></a>Consulte também

Este artigo de mostrar como implementar a recomendação do Centro de segurança "Habilitar a auditoria nos servidores SQL." Para saber mais sobre como proteger seu banco de dados do SQL, consulte o seguinte:

- [Proteger seu banco de dados do SQL](../sql-database/sql-database-security.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) - Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]:./media/security-center-enable-auditing-on-sql-server/enable-auditing.png
[3]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
