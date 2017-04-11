<properties
   pageTitle="Protegendo o serviço do SQL Azure na Central de segurança do Azure | Microsoft Azure"
   description="Este endereços de documento recomendações na Central de segurança do Azure que ajudarão a proteger serviço SQL Azure e permanecer em conformidade com políticas de segurança."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-azure-sql-service-in-azure-security-center"></a>Protegendo o serviço do SQL Azure na Central de segurança do Azure

O Centro de segurança do Azure analisa o estado de segurança de seus recursos Azure. Quando o Centro de segurança identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientação você durante o processo de configuração controles necessários.  Recomendações se aplicam a tipos de recursos Azure: VMs (máquinas virtuais), rede, SQL e aplicativos.

Este artigo aborda recomendações que se aplicam ao serviço do SQL Azure.  Azure SQL recomendações atendimento ao redor de ativar a auditoria para servidores do SQL Azure e bancos de dados e habilitando criptografia para bancos de dados SQL.  Use a tabela a seguir como uma referência para ajudá-lo a compreender as recomendações de serviço SQL disponíveis e o que cada uma delas fará se aplicá-lo.

## <a name="available-sql-service-recommendations"></a>Recomendações de serviço SQL disponíveis

|Recomendação|Descrição|
|-----|-----|
|[Habilitar a auditoria do SQL do servidor](security-center-enable-auditing-on-sql-servers.md)|Recomenda-se de que você ative a auditoria para servidores do SQL Azure (serviço SQL Azure; não incluir apenas SQL em execução no suas máquinas virtuais).|
|[Habilitar a auditoria do SQL do banco de dados](security-center-enable-auditing-on-sql-databases.md)|Recomenda-se de que você ative a auditoria para bancos de dados do SQL Azure (serviço SQL Azure; não incluir apenas SQL em execução no suas máquinas virtuais).|
|[Habilitar criptografia transparente de dados em bancos de dados SQL](security-center-enable-transparent-data-encryption.md)|Recomenda-se de que você habilite a criptografia para bancos de dados do SQL (somente serviço SQL Azure).|

## <a name="see-also"></a>Consulte também

Para saber mais sobre as recomendações que se aplicam a outros tipos de recurso Azure, consulte o seguinte:

- [Protegendo suas máquinas virtuais na Central de segurança do Azure](security-center-virtual-machine-recommendations.md)
- [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
- [Protegendo sua rede na Central de segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para assinaturas do Azure e grupos de recursos.
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerenciar e responder a alertas de segurança.
- [Perguntas frequentes sobre o Centro de segurança do azure](security-center-faq.md) – localizar perguntas frequentes sobre como usar o serviço.
