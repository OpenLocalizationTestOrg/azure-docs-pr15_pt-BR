<properties
   pageTitle="Solucionar problemas de serviços de nuvem usando o aplicativo ideias | Microsoft Azure"
   description="Saiba como solucionar problemas de serviço de nuvem usando o aplicativo ideias para processar dados de diagnóstico do Azure."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# <a name="troubleshoot-cloud-services-using-application-insights"></a>Solucionar problemas de serviços de nuvem usando a obtenção de informações de aplicativo

Com extensão de diagnóstico do [Azure SDK 2,8](https://azure.microsoft.com/downloads/) e Azure 1,5 você agora pode enviar seus dados de diagnóstico do Azure para o seu serviço de nuvem diretamente de obtenção de informações do aplicativo. Os vários tipos de logs coletados pelo diagnóstico do Azure incluindo logs de aplicativo, logs de eventos do windows, ETW logs e contadores de desempenho agora podem ser enviados para a obtenção de informações de aplicativo e visualizados no portal de obtenção de informações do aplicativo da interface do usuário. Quando usado juntamente com o SDK de obtenção de informações do aplicativo agora você pode obter ideias para métricas e logs provenientes de seu aplicativo, bem como os dados de nível sistema e infraestrutura vêm do diagnóstico do Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurar o diagnóstico do Azure para enviar dados para obtenção de informações de aplicativo

Siga estas etapas para configurar seu projeto de serviço de nuvem para enviar dados de diagnóstico do Azure para obtenção de informações do aplicativo.

1) No Visual Studio Solution Explorer clique com botão direito em uma função e selecione **Propriedades** para abrir o designer de função

![Propriedades da função Explorer solução][1]

2) No designer de função sob a seção de diagnóstico, selecione a caixa de seleção para **Enviar dados de diagnóstico de obtenção de informações de aplicativo**

![Designer de função enviar dados de diagnóstico para obtenção de informações de aplicativo][2]

3) Na caixa de diálogo pop-up, selecione o recurso de obtenção de informações de aplicativo que você gostaria de enviar os dados de diagnóstico do Azure para. A caixa de diálogo permite que você selecione um recurso de obtenção de informações de aplicativo existente de sua assinatura ou especificar manualmente uma chave de instrumentação para um recurso de obtenção de informações do aplicativo. Se você não tiver um recurso de obtenção de informações de aplicativo existente, em seguida, você pode criar no clicando no link **criar um novo recurso** que abrirá uma janela do navegador para o portal de clássico Azure onde você pode criar um recurso de obtenção de informações do aplicativo. Para obter mais informações sobre como criar um recurso de obtenção de informações do aplicativo, consulte [criar um novo recurso de obtenção de informações de aplicativo](../application-insights/app-insights-create-new-resource.md)

![Selecione o recurso de obtenção de informações de aplicativo][3]

4) Após adicionar o recurso de obtenção de informações do aplicativo, a chave de instrumentação para esse recurso é armazenada como uma configuração de serviço com o nome **APPINSIGHTS_INSTRUMENTATIONKEY**. Você pode alterar essa configuração para cada ambiente ou a configuração do serviço, selecionando uma configuração diferente na lista suspensa de configuração do serviço para baixo e especificando uma nova chave de instrumentação para essa configuração.

![Selecione a configuração de serviço][4]

A configuração **APPINSIGHTS_INSTRUMENTATIONKEY** é usada pelo Visual Studio para configurar a extensão de diagnóstico com as informações apropriadas de recurso de obtenção de informações do aplicativo durante a publicação. A configuração é uma maneira conveniente de definir chaves de instrumentação diferente para as configurações de serviço diferente. Visual Studio será traduzir essa configuração e inseri-lo na configuração de público de extensão de diagnóstico durante a publicação. Para simplificar o processo de configuração a extensão de diagnóstico com o PowerShell, o pacote de saída do Visual Studio também contém a configuração pública XML com a instrumentação de obtenção de informações do aplicativo apropriada chave incluído. Os arquivos config público são criados na pasta Extensions e siga o padrão PaaSDiagnostics. <RoleName>. PubConfig.xml. Qualquer implantações do PowerShell com base podem usar esse padrão para mapear cada configuração para uma função.

5) Habilitando a **Enviar dados de diagnóstico de obtenção de informações de aplicativo** configurará automaticamente diagnóstico do Azure para enviar todos os contadores de desempenho e logs de nível de erro que estão sendo coletados pelo agente do diagnóstico do Azure de obtenção de informações do aplicativo. Se você quiser configurar quais dados são enviados para a obtenção de informações de aplicativo e em seguida, você precisa editar manualmente o arquivo *diagnostics.wadcfgx* para cada função. Consulte [Configurar o diagnóstico do Azure para enviar dados de obtenção de informações do aplicativo](../azure-diagnostics-configure-applicationinsights.md) para saber mais sobre como atualizar manualmente a configuração.

Garantir que a extensão do diagnóstico do Azure é habilitada quando o serviço de nuvem é configurado para enviar dados de diagnóstico do Azure para obtenção de informações de aplicativo que você pode implantá-lo no Azure como faria normalmente. Consulte a [publicação de um serviço de nuvem usando o Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Exibindo dados de diagnóstico do Azure no aplicativo ideias
A Azure telemetria diagnóstico aparecerá no recurso ideias de aplicativo configurado para o seu serviço de nuvem.

Eis como o diagnóstico do Azure vários logon tipos mapa conceitos de obtenção de informações do aplicativo:  

-  Contadores de desempenho são exibidos como métricas personalizada na obtenção de informações de aplicativo
-  Logs de eventos do Windows são mostrados como rastreamentos e eventos personalizados em ideias de aplicativo
-  Logs de aplicativo, Logs de ETW e qualquer logs de diagnóstico infraestrutura são mostrados como rastreamentos em ideias de aplicativo.

Para exibir dados de diagnóstico do Azure em ideias de aplicativo:

- Use o [explorer métricas](../application-insights/app-insights-metrics-explorer.md) para visualizar qualquer contadores de desempenho personalizados ou contagens de diferentes tipos de eventos de log de eventos do windows.

![Métricas personalizadas no Explorador de métricas][5]

- Use a [pesquisa](../application-insights/app-insights-diagnostic-search.md) para procurar entre os vários logs de rastreamento enviados por diagnóstico do Azure. Por exemplo, se você tinha uma exceção não tratada em uma função que causou a função falhar e Lixeira essas informações aparecerão no canal de *aplicativo* de *Log de eventos do Windows*. Você pode usar a funcionalidade de pesquisa para examinar o erro de Log de eventos do Windows e obter o rastreamento de pilha completo para a exceção permitindo que você encontrar a causa do problema.

![Rastreamentos de pesquisa][6]

## <a name="next-steps"></a>Próximas etapas

- [Adicionar o SDK de ideias de aplicativo ao serviço de nuvem](../application-insights/app-insights-cloudservices.md) para enviar dados sobre solicitações, exceções, dependências e qualquer telemetria personalizada do seu aplicativo. Combinado com o diagnóstico do Azure dados que você pode obter uma visão completa do seu aplicativo e o sistema no mesmo recurso de visão de aplicativo.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
