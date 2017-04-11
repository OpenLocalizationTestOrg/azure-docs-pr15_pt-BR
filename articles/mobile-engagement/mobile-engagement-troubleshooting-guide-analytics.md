<properties 
   pageTitle="Azure contrato móvel guia - análise de solução de problemas" 
   description="Solucionar problemas de painel, segmentação, monitoramento e análise no contrato de celular do Azure" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Guia de solução de problemas para problemas de painel, segmentação, monitoramento e análise

Estes são os possíveis problemas que podem ser encontrados com como o Azure Mobile contrato coleta informações sobre seus aplicativos, dispositivos e usuários.

## <a name="missingdelayed-information"></a>Informações faltando/atrasado

### <a name="issue"></a>Problema
- Informações estão atrasadas em que aparecem no Analytics, segmentação ou no painel de controle.
- Faltam informações de monitoramento.
- Faltam informações de análise, segmentação ou painel.
- Limites de segmentação atingir.

### <a name="causes"></a>Causas

- Você pode usar a API de análise, API do Monitor, e segmentos API para ver se todos os dados está faltando na interface do usuário fica visível por meio de APIs.
- Se o SDK do contrato do Azure Mobile não está corretamente integrado ao seu aplicativo, em seguida, você não conseguirá ver informações na análise, segmentação, monitoramento ou painéis.
- Segmentos não podem ser alteradas depois que eles são criados, segmentos só podem ser "clonados" (copiados) ou "destruído" (excluídos). Segmentos podem conter apenas 10 critérios.
- A melhor maneira de testar informações ausentes do monitoramento é um dispositivo de teste de instalação, desinstale e/ou reinstale o aplicativo do dispositivo de teste.
- Informações são atualizadas a cada 24 horas para análise, segmentação ou painéis.
- Informações em novos segmentos podem não ser exibidas até 24 horas depois que eles são criados mesmo se o segmento baseia-se em informações anteriores.
- Filtrar seus dados de análise na interface de usuário mostrará todos os exemplos deste tipo independentemente da versão do seu aplicativo (por exemplo, "travar" filtrada por nome mostrará da versão 1 e 2 do seu aplicativo).
- O período de tempo para análise baseia-se na data dos usuários configurações de dispositivo, para que um usuário cuja telefone tem a data incorretamente definida poderia aparecem no período de tempo incorreto.
- Nenhum servidor dados são registrados quando você usar o botão "testar" envia, somente os dados serão registrados para campanhas de envio real.

## <a name="cant-locate-items-in-ui"></a>Não consigo localizar itens na interface do usuário

### <a name="issue"></a>Problema
- Não é possível criar segmentos com base em determinados integrado ou critérios de marca de informações de aplicativo personalizado.
- Não consegue encontrar determinados integrado ou informações de aplicativo personalizado marca critérios em análise, monitoramento ou painéis.
- Não é possível interpretar os dados em análise, monitoramento, segmentação ou painéis.

### <a name="causes"></a>Causas

- Alguns integrado itens e marcas de informações do aplicativo só estão disponíveis como critérios de envio, mas não podem ser adicionado a um segmento ou visível Analytics, monitoramento ou painel. 
- Para itens criados no e marcas de informações de aplicativo que não podem ser adicionadas a um segmento, você precisará lista de configuração de direcionamento de critérios em cada campanha para executar a mesma função direcionamento com base em um segmento.
- Ver os menus de contexto nas seções painéis, segmentação, monitoramento e análise da interface do usuário do Azure Mobile contrato para obter mais ajuda e como informações.

## <a name="crash-troubleshooting"></a>Solução de problemas de falhar

### <a name="issue"></a>Problema
- Aplicativo trava aparecendo em análise, monitoramento ou painel.

### <a name="causes"></a>Causas

- Para solucionar problemas de aplicativo trava visto em análise, monitoramento ou Dashboard Verifique as notas de versão para problemas conhecidos com versões anteriores do SDK.
- Para solucionar falhas de aplicativo executam um evento de um dispositivo de teste com seu aplicativo instalado e procure a sua ID de dispositivo na seção "Monitorar – eventos" da interface do usuário do Azure Mobile contrato. Execute o evento que está causando o seu aplicativo falhar e pesquisar informações adicionais na seção de "Monitor – falha" da interface do usuário do Azure Mobile contrato. 

 
