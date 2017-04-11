<properties
   pageTitle="Habilitar a criptografia de dados transparente na Central de segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar a recomendação de Central de segurança do Azure **Habilitar criptografia transparente de dados**."
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

# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Habilitar a criptografia de dados transparente na Central de segurança do Azure

O Centro de segurança do Azure será recomendável que você ative criptografia de dados transparente (TDE) em bancos de dados do SQL se TDE já não estiver habilitado. TDE protege seus dados e ajuda você a atender aos requisitos de conformidade criptografando seu banco de dados, backups associados e arquivos de log de transação inativos, sem a necessidade de alterações no aplicativo. Para saber que mais, consulte [Criptografia transparente de dados com o Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Essa recomendação se aplica ao serviço SQL Azure não inclui SQL em execução no suas máquinas virtuais.

> [AZURE.NOTE] Este documento apresenta o serviço usando uma implantação de exemplo.  Isso não é um guia passo a passo.

## <a name="implement-the-recommendation"></a>Implementar a recomendação

1. Na lâmina **recomendações** , selecione **Habilitar criptografia transparente de dados**.
![Habilitar a criptografia de dados transparente][1]

2. Isso abre a lâmina de **Habilitar a criptografia transparente de dados em bancos de dados do SQL** . Selecione um banco de dados SQL para habilitar TDE em.
![Selecione o banco de dados SQL para habilitar TDE em][2]
3. Na lâmina **criptografia de dados transparente** , selecione **Diante** em criptografia de dados e selecione **Salvar** da faixa de opções superior da lâmina.
![Ativar TDE][3]

  Quando TDE estiver ativada no banco de dados SQL selecionado, o **status de criptografia** será alterado para **criptografado**.    

  ![Status de criptografia][4]

## <a name="see-also"></a>Consulte também

Este artigo de mostrar como implementar a recomendação do Centro de segurança "Habilitar criptografia transparente de dados". Para saber mais sobre SQL TDE, consulte o seguinte:

- [Criptografia de dados transparente com banco de dados do SQL Azure](https://msdn.microsoft.com/library/dn948096)
- [Começar com criptografia de dados transparente (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando recomendações de segurança na Central de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger seus recursos Azure.
- [Monitoramento de integridade de segurança na Central de segurança do Azure](security-center-monitoring.md) , Aprenda a monitorar a integridade dos seus recursos Azure.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Soluções de parceiros de monitoramento com o Centro de segurança do Azure](security-center-partner-solutions.md) , Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do azure](http://blogs.msdn.com/b/azuresecurity/) - Obtenha as últimas notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
