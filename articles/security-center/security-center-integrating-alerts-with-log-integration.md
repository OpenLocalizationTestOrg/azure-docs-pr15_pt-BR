<properties
   pageTitle="Integração de alertas da Central de segurança do Azure com integração de log Azure (visualização) | Microsoft Azure"
   description="Este artigo ajuda você a começar a usar a integração de alertas de segurança central com integração do Azure log."
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
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# <a name="integrating-security-center-alerts-with-azure-log-integration-preview"></a>Integração de alertas de segurança central com integração de log Azure (visualização)

Várias operações de segurança e equipes de resposta a incidente contam com uma solução de gerenciamento de evento (SIEM) e informações de segurança como ponto de partida para separação e investigar os alertas de segurança. Com a integração de log Azure, clientes podem sincronizar os alertas do Centro de segurança do Azure, juntamente com os eventos de segurança de máquina virtual coletados pelo diagnóstico do Azure e Logs de auditoria do Azure, com seu log analytics ou solução SIEM em quase em tempo real.

Integração do Azure log funciona com HP ArcSight, Splunk, IBM QRadar e outras pessoas.

## <a name="what-logs-can-i-integrate"></a>Quais registros pode integrar?

Azure produz log extensivo para cada serviço. Esses logs são categorizados como:

- **Logs de gerenciamento do controle** que dão visibilidade as operações Azure Gerenciador de recursos de criar, atualizar e excluir.
- **Logs de plano de dados** que dão visibilidade as eventos gerados ao usar um recurso Azure. Um exemplo é o log de eventos do Windows - segurança e logs de aplicativo em uma máquina virtual.

Integração do Azure log atualmente oferece suporte a integração do:

- Logs de máquina virtual Azure
- Logs de auditoria Azure
- Alertas do Centro de segurança do Azure

## <a name="install-azure-log-integration"></a>Instalar a integração do Azure log

Baixe a [integração de logon do Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração de log Azure coleta dados de telemetria da máquina em que ele está instalado.  Dados de telemetria coletados são:

- Integração de log de exceções que ocorrem durante a execução do Azure
- Métricas sobre o número de consultas e eventos processados
- Estatísticas sobre quais Azlog.exe opções de linha de comando estão sendo usadas

> [AZURE.NOTE] Você pode desativar a coleta de dados de telemetria desmarcando essa opção.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar alertas de Logs de auditoria do Azure e o Centro de segurança

1. Abra o prompt de comando e o **cd** em **c:\Program Files\Microsoft integração de Log do Azure**.

2. Execute o comando **azlog createazureid** criar um [Principal de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) no locatários Azure Active Directory (AD) que hospedam as assinaturas Azure.

    Você será solicitado para seu login Azure.

    > [AZURE.NOTE] Você deve ser a assinatura do proprietário ou administrador de colegas da assinatura.

    Autenticação do Azure é feita por meio do Azure AD.  Criando um principal de serviço de integração de log Azure criará a identidade do Azure AD terá acesso de leitura de assinaturas Azure.

3. Executar o **azlog autorizar <SubscriptionID> ** comando para atribuir acesso de leitor com assinaturas para a entidade de serviço criada na etapa 2. Se você não especificar um **SubscriptionID**, então o capital de serviço será atribuído a função Leitor para todas as assinaturas às quais você tem acesso.

    > [AZURE.NOTE] Você pode ver avisos se você executar o comando **Autorizar** imediatamente após o comando **createazureid** porque há alguns latência entre quando a conta do Azure AD é criada e quando a conta está disponível para uso. Se você esperar cerca de 10 segundos após executar o comando **createazureid** para executar o comando **Autorizar** , em seguida, você não verá esses avisos.

4. Verifique as seguintes pastas para confirmar que os arquivos JSON de log de auditoria estão lá:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Verifique as seguintes pastas para confirmar a existam de alertas de segurança central neles:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Aponte o conector de encaminhadores de arquivo SIEM padrão para a pasta adequada para conduza os dados para a instância SIEM. Consulte [configurações de SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) na configuração SIEM.

Se você tiver dúvidas sobre a integração de Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Logs de auditoria do Azure e definições de propriedade, consulte:

- [Operações de auditoria com o Gerenciador de recursos](../resource-group-audit.md)
- [Listar os eventos de gerenciamento de uma assinatura](https://msdn.microsoft.com/library/azure/dn931934.aspx) - recuperar eventos de log de auditoria.

Para saber mais sobre o Centro de segurança, consulte o seguinte:

- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerenciar e responder a alertas de segurança.
- [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — Obtenha as últimas notícias de segurança do Azure e informações.
