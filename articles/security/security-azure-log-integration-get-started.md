<properties
   pageTitle="Introdução ao integração do Azure log | Microsoft Azure"
   description="Saiba como instalar o serviço de integração de log Azure e integrar logs de armazenamento do Azure, Logs de auditoria do Azure e alertas da Central de segurança do Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="get-started-with-azure-log-integration-preview"></a>Introdução ao integração de log Azure (visualização)

Integração do Azure log permite integrar brutos logs de seus recursos Azure seus sistemas de gerenciamento de evento (SIEM) e informações de segurança do local. Essa integração oferece um painel unificado para todos os seus ativos, local ou na nuvem, para que você pode agregar, correlação, analisar e de alerta para eventos de segurança associados com seus aplicativos.

Este tutorial orienta como instalar a integração do Azure log e integrar logs do armazenamento do Azure, Logs de auditoria do Azure e alertas da Central de segurança do Azure. Tempo estimado para concluir este tutorial é uma hora.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter o seguinte:

- Uma máquina (local ou na nuvem) para instalar o serviço de integração de log Azure. Este computador deve estar executando um sistema operacional Windows de 64 bits com .net 4.5.1 instalado. Esta máquina é chamada de **Azlog Integrator**.
- Assinatura do Azure. Se você não tiver um, você pode inscrever para uma [conta gratuita](https://azure.microsoft.com/free/).
- Diagnóstico do Azure habilitado para seu Azure VMs (máquinas virtuais). Para habilitar o diagnóstico para serviços de nuvem, consulte [Habilitando o diagnóstico do Azure nos serviços de nuvem do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Para habilitar o diagnóstico para uma VM Azure executando o Windows, consulte [Usar o PowerShell para habilitar o diagnóstico do Azure na máquina Virtual executando o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- Conectividade de integrador Azlog para armazenamento do Azure e para autenticar e autorizar a assinatura Azure.
- Para logs de máquina virtual do Azure, o agente SIEM (por exemplo, Splunk Universal encaminhadores, um agente de coleta de eventos do HP ArcSight Windows ou IBM QRadar WinCollect) deve ser instalado no integrador Azlog.

## <a name="deployment-considerations"></a>Considerações de implantação

Você pode executar várias instâncias do integrador Azlog se o volume de evento está alto. Balanceamento de carga de contas de armazenamento do diagnóstico do Azure para Windows *(WAD)* e o número de assinaturas para fornecer às instâncias deve ser baseado na sua capacidade.

Em uma máquina de 8-processador (principal), uma única instância do Azlog Integrator pode processar cerca de 24 milhões de eventos por dia (~1M/hour).

Em uma máquina de processador de 4 (principal), uma única instância do Azlog Integrator pode processar cerca de 1,5 milhões de eventos por dia (~62.5K/hour).

## <a name="install-azure-log-integration"></a>Instalar a integração do Azure log

Baixe a [integração de logon do Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração de log Azure coleta dados de telemetria da máquina em que ele está instalado.  Dados de telemetria coletados são:

- Integração de log de exceções que ocorrem durante a execução do Azure
- Métricas sobre o número de consultas e eventos processados
- Estatísticas sobre quais Azlog.exe opções de linha de comando estão sendo usadas

> [AZURE.NOTE] Você pode desativar a coleta de dados de telemetria desmarcando essa opção.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Integre os registros de máquina virtual do Azure das suas contas de armazenamento do diagnóstico do Azure

1. Verificar os pré-requisitos listados acima para garantir que sua conta de armazenamento WAD está coletando logs antes de continuar a integração de log Azure. Não execute as seguintes etapas se sua conta de armazenamento WAD não está coletando logs.

2. Abra o prompt de comando e o **cd** em **c:\Program Files\Microsoft integração de Log do Azure**.

3. Execute o comando

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Substitua StorageAccountName com o nome da conta de armazenamento do Azure configurado para receber eventos de diagnóstico de sua máquina virtual.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Se você quiser a id da assinatura apareça em eventos XML, acrescente a identificação de assinatura para o nome amigável:

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Aguarde 30 a 60 minutos (ele pode levar até uma hora) e exibir os eventos que são extraídos da conta de armazenamento. Para exibir, abra **Visualizador de eventos > Logs do Windows > eventos encaminhados** em integrador Azlog.

5. Certifique-se de que seu conector SIEM padrão instalado na máquina está configurado para escolha eventos da pasta **Eventos encaminhados** e conduza-las à sua instância SIEM. Examine a configuração específica do SIEM para configurar e ver os logs de integração.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>O que acontece se dados não estiver aparecendo na pasta eventos encaminhados?

Se depois de uma hora dados não estiver aparecendo na pasta **Eventos encaminhados** , então:

1. Verifique a máquina e confirme que ele pode acessar o Azure. Para testar a conectividade, tente abrir o [portal do Azure](http://portal.azure.com) do navegador.

2. Verifique se que a conta de usuário **azlog** tem a permissão Gravar na pasta **users\azlog**.

3. Verifique se a conta de armazenamento adicionada o comando **adicionar fonte de azlog** estiver listada quando você executa o comando **azlog lista de origem**.
4. Vá para **Visualizador de eventos > Logs do Windows > aplicativo** para ver se há erros relatados com a integração de log Azure.

Se você ainda não vir os eventos, então:

1. Baixe o [Gerenciador de armazenamento do Microsoft Azure](http://storageexplorer.com/).

2. Conectar-se para a conta de armazenamento adicionada o comando **adicionar fonte de azlog**.

3. No Microsoft Azure Storage Explorer, navegue até **WADWindowsEventLogsTable** para ver se há dados de tabela. Caso contrário, em seguida, diagnósticos na máquina virtual não está configurado corretamente.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar logs de auditoria Azure e alertas da Central de segurança

1. Abra o prompt de comando e o **cd** em **c:\Program Files\Microsoft integração de Log do Azure**.

2. Execute o comando

        azlog createazureid

      Este comando solicita o Azure login. O comando cria um [Principal de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) nos Azure locatários do AD que hospedam as assinaturas Azure em que o usuário conectado é um administrador, um administrador de colegas ou um proprietário. O comando falhará se o usuário conectado é apenas um usuário convidado no Azure AD locatário. Autenticação no Azure é feita por meio do Azure Active Directory (AD).  Criando um principal de serviço para integração de Azlog cria a identidade do Azure AD terá acesso de leitura de assinaturas Azure.

3. Execute o comando

        azlog authorize <SubscriptionID>

      Isso atribui o acesso de leitor na inscrição no serviço principal criado na etapa 2. Se você não especificar um SubscriptionID, ele tenta atribuir a função de leitor principal do serviço para todas as assinaturas à qual você tem qualquer acesso.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Você pode ver avisos se você executar o comando **Autorizar** imediatamente após o comando **createazureid** . Há algumas latência entre quando a conta do Azure AD é criada e quando a conta está disponível para uso. Se você esperar cerca de 10 segundos após executar o comando **createazureid** para executar o comando **Autorizar** , em seguida, você não verá esses avisos.

4. Verifique as seguintes pastas para confirmar que os arquivos JSON de log de auditoria estão lá:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Verifique as seguintes pastas para confirmar a existam de alertas de segurança central neles:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Aponte o conector de encaminhadores de arquivo SIEM padrão para a pasta adequada para conduza os dados para a instância SIEM. Talvez seja necessário alguns mapeamentos de campo com base no produto SIEM que você está usando.

Se você tiver dúvidas sobre a integração de Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como instalar a integração do Azure log e integrar logs de armazenamento do Azure. Para saber mais, consulte o seguinte:

- [Integração de Log do Microsoft Azure para logs Azure (visualização)](https://www.microsoft.com/download/details.aspx?id=53324) – Centro de Download para obter detalhes, requisitos do sistema e instruções de instalação a integração de log Azure.
- [Introdução à integração de log Azure](security-azure-log-integration-overview.md) – este documento apresenta integração de log Azure, seus recursos principais e como ele funciona.
- [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra como configurar a integração de log Azure para trabalhar com soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
- [Log azure integração perguntas frequentes (FAQ)](security-azure-log-integration-faq.md) - perguntas frequentes sobre este responde perguntas sobre a integração de log Azure.
- [Alertas do Centro de segurança integrando com o Azure logon integração](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar os alertas do Centro de segurança, juntamente com coletados pelo diagnóstico do Azure e Logs de auditoria do Azure, com sua solução SIEM ou a análise de log de eventos de segurança de máquina virtual.
- [Novos recursos para diagnóstico do Azure e Logs de auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta postagem de blog apresenta os Logs de auditoria do Azure e outros recursos que ajudam você obtém ideias para as operações de seus recursos Azure.
