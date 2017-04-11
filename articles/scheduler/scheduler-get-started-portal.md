<properties
 pageTitle="Começar a usar o Agendador do Azure no portal Azure | Microsoft Azure"
 description="Introdução ao Azure Agendador no portal do Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Introdução ao Azure Agendador no portal do Azure

É fácil criar trabalhos agendados no Agendador do Azure. Neste tutorial, você aprenderá a criar um trabalho. Você aprenderá também os recursos de monitoramento e gerenciamento do Agendador.

## <a name="create-a-job"></a>Criar um trabalho

1.  Entrar no [portal do Azure](https://portal.azure.com/).  

2.  Clique em **+ novo** > digite _Agendador_ na caixa de pesquisa > selecione **Agendador** nos resultados > clique em **criar**.

     ![][marketplace-create]

3.  Vamos criar um trabalho que simplesmente visitas http://www.microsoft.com/ com uma solicitação GET. Na tela de **Trabalho do Agendador** , insira as seguintes informações:

    1.  **Nome:**`getmicrosoft`  

    2.  **Assinatura:** Sua assinatura do Azure   

    3.  **Conjunto de trabalho:** Selecione um conjunto de trabalho existente ou clique em **Criar novo** > Insira um nome.

4.  Em seguida, em **Configurações da ação**, defina os seguintes valores:

    1.  **Tipo de ação:**` HTTP`  

    2.  **Método:**`GET`  

    3.  **URL:**` http://www.microsoft.com`  

      ![][action-settings]

5.  Por fim, vamos definir uma agenda. O trabalho poderia ser definido como um trabalho único, mas vamos escolher um cronograma de recorrência:

    1. **Recorrência**:`Recurring`

    2. **Iniciar**: a data de hoje

    3. **Recorrente cada**:`12 Hours`

    4. **Terminar em**: dois dias de hoje da data  

      ![][recurrence-schedule]

6.  Clique em **criar**

## <a name="manage-and-monitor-jobs"></a>Gerenciar e monitorar trabalhos

Depois que um trabalho é criado, ele aparece no painel Azure principal. Clique no trabalho e uma nova janela é aberta com as seguintes guias:

1.  Propriedades  

2.  Configurações de ação  

3.  Cronograma  

4.  Histórico

5.  Usuários

    ![][job-overview]

### <a name="properties"></a>Propriedades

Essas propriedades de somente leitura descrevem os metadados de gerenciamento de trabalho do Agendador.

   ![][job-properties]


### <a name="action-settings"></a>Configurações de ação

Clicar em um trabalho na tela de **trabalhos** permite que você configure o trabalho. Isso permite definir configurações avançadas, se você não configurá-los no Assistente de criação de rápida.

Para todos os tipos de ação, você pode alterar a política de repetição e a ação de erro.

Para os tipos de ação do trabalho HTTP e HTTPS, você pode alterar o método para qualquer verbo HTTP permitido. Você também pode adicionar, excluir ou alterar os cabeçalhos e informações de autenticação básica.

Para os tipos de ação de fila do armazenamento, você pode alterar a conta de armazenamento, nome da fila, token SAS e corpo.

Para tipos de ação de barramento de serviço, você pode alterar o espaço para nome, caminho tópico/fila, configurações de autenticação, tipo de transporte, propriedades de mensagem e corpo da mensagem.

   ![][job-action-settings]

### <a name="schedule"></a>Cronograma

Permite que você reconfigurar o cronograma, se você quiser alterar o agendamento que você criou no Assistente de criação de rápida.

Esta é uma oportunidade para construir [agendas complexas e recorrência avançada no seu trabalho](scheduler-advanced-complexity.md)

Você pode alterar a data de início e hora, agendamento de recorrência e final data e hora (se o trabalho é recorrente.)

   ![][job-schedule]


### <a name="history"></a>Histórico

A guia **histórico** exibe métricas selecionadas para cada execução de trabalho do sistema para o trabalho selecionado. Essas métricas fornecem os valores em tempo real sobre a integridade do seu Agendador:

1.  Status  

2.  Detalhes  

3.  Tentativas de repetição

4.  Ocorrência: 1º, 2º, 3º, etc.

5.  Hora de início de execução  

6.  Hora de término de execução

   ![][job-history]

Você pode clicar em uma execução para exibir os **Detalhes de histórico**, incluindo a resposta inteira para cada execução. Esta caixa de diálogo também permite que você copie a resposta para a área de transferência.

   ![][job-history-details]

### <a name="users"></a>Usuários

Azure controle de acesso baseado em função (RBAC) permite o gerenciamento de acesso refinados do Azure Scheduler. Para saber como usar a guia de usuários, consulte [Controle de acesso de Azure Role-Based](../active-directory/role-based-access-control-configure.md)


## <a name="see-also"></a>Consulte também

 [O que é o Agendador?](scheduler-intro.md)

 [Hierarquia de entidades, terminologia e conceitos do Agendador](scheduler-concepts-terms.md)

 [Planos e cobranças no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência do Agendador API REST](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell Agendador](scheduler-powershell-reference.md)

 [Agendador alta disponibilidade e confiabilidade](scheduler-high-availability-reliability.md)

 [Limites do Agendador, padrões e códigos de erro](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
