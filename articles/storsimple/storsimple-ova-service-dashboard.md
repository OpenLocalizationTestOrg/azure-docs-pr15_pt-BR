<properties 
   pageTitle="Painel de serviço do Gerenciador de StorSimple - Array Virtual | Microsoft Azure"
   description="Descreve o painel de serviço do Gerenciador de StorSimple e explica como usá-lo para monitorar a integridade da sua matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Use o painel de serviço do Gerenciador de StorSimple para a matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

A página de painel de serviço do Gerenciador de StorSimple fornece uma exibição de resumo das StorSimple Virtual matrizes (também conhecido como StorSimple local dispositivos virtuais ou dispositivos virtuais) que estão conectadas ao serviço Gerenciador de StorSimple, realce aqueles que precisam de atenção do administrador do sistema. Este tutorial apresenta a página de painel, explica a função e o conteúdo de painel e descreve as tarefas que você pode executar a partir desta página.

![Painel de serviço](./media/storsimple-ova-service-dashboard/dashboard1.png)

O painel de serviço do Gerenciador de StorSimple exibe as seguintes informações:

- Na área do **gráfico** , na parte superior da página, você pode ver as métricas relevantes para os seus dispositivos virtuais. Você pode exibir o armazenamento primário usado em todos os dispositivos virtuais, bem como o armazenamento em nuvem consumida por dispositivos virtuais durante um período de tempo. Use os controles no canto superior direito do gráfico para especificar o uso da relativo ou absoluto e ver uma escala de tempo de 1 semana, 1 mês, 3 meses ou 1 ano. Usar o controle de atualização ![controle de atualização](./media/storsimple-ova-service-dashboard/refresh-control.png) para atualizar o gráfico.

- A **Visão geral do uso** mostra o armazenamento principal que está provisionado e consumido por todos os dispositivos virtuais relativo o armazenamento total disponível em todos os dispositivos virtuais. **Provisionado** refere-se para a quantidade de armazenamento que está preparado e alocado para uso, **usada** refere-se ao uso de compartilhamentos ou volumes exibido pelos iniciadores conectados aos dispositivos virtuais e **capacidade máx.** mostra a capacidade máxima de todos os dispositivos virtuais.

- A área de **alertas** fornece um instantâneo de todos os alertas ativos em todos os dispositivos virtuais, agrupados por gravidade de alerta. Clicando no nível de gravidade abre a página de **alertas** , com escopo definida para mostrar os alertas. Na página **alertas** , você pode clicar em um alerta individual para exibir detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Você também pode limpar o alerta se o problema foi resolvido.

- A área de **trabalhos** fornece um instantâneo de trabalhos recentes em todos os dispositivos virtuais que estão conectados ao seu serviço. Há links que você pode usar para examinar trabalhos que estejam em andamento e aqueles que foi bem-sucedida ou falha em últimas 24 horas. 

- A área de **rapidamente** à direita da página fornece informações úteis como o status do serviço, o número total de dispositivos virtuais conectada para o serviço, o local do serviço e detalhes da assinatura que está associado com o serviço. Há também um link para o log de operações. Clique no link para ver uma lista de todas as operações de serviço do Gerenciador de StorSimple concluída. 

Você pode usar a página de painel de serviço do Gerenciador de StorSimple para iniciar as seguintes tarefas:

- Obtenha a chave do registro de serviço.
- Exiba os logs de operação.

## <a name="get-the-service-registration-key"></a>Obter a chave do registro de serviço

A chave do registro de serviço é usada para registrar um dispositivo virtual StorSimple com o serviço do Gerenciador de StorSimple, para que o dispositivo é exibido no portal de clássico do Azure para ainda mais ações de gerenciamento. A chave é criada no primeiro dispositivo virtual e compartilhada com os dispositivos virtuais restantes. 

Clicando em **Chave do registro** (na parte inferior da página) para abrir a caixa de diálogo de **Chave do registro de serviço** , onde você pode copiar a chave de registro atual do serviço para a área de transferência ou gerar novamente a chave do registro de serviço.

![chave do registro](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Gerar a chave não afeta os dispositivos virtuais registrados anteriormente: afeta apenas os dispositivos virtuais que são registrados com o serviço após a tecla for gerado novamente.

Para obter mais informações sobre como obter a chave do registro de serviço, vá para [obter a chave do registro de serviço](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Exibir os logs de operações

Você pode exibir os logs de operação clicando no link de logs de operação disponíveis no painel **consulta rápida** do painel de controle. Isso levará você à página de serviços de gerenciamento, onde você pode filtrar e ver os logs específicos ao seu serviço de Gerenciador de StorSimple.

![Log de operações](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [usar o interface de usuário para administrar sua matriz Virtual StorSimple de web local](storsimple-ova-web-ui-admin.md).