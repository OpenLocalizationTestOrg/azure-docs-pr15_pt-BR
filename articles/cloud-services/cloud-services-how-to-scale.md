<properties
    pageTitle="Auto dimensionar um serviço de nuvem no portal do | Microsoft Azure"
    description="(clássico) Saiba como usar o portal clássico para configurar regras de escala automática para uma função de web de serviço de nuvem ou trabalhador no Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="how-to-auto-scale-a-cloud-service"></a>Como escala automática um serviço na nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-scale-portal.md)
- [Azure portal clássico](cloud-services-how-to-scale.md)

Na página de escala do portal de clássico do Azure, é possível redimensionar manualmente sua função de web ou trabalhador ou você pode habilitar o dimensionamento automático com base na carga de CPU ou uma fila de mensagem.

>[AZURE.NOTE] Este artigo aborda em funções de trabalho e da web do serviço de nuvem. Quando você cria uma máquina virtual (clássico) diretamente, ele é hospedado em um serviço na nuvem. Algumas dessas informações se aplica a esses tipos de máquinas virtuais. Dimensionamento de um conjunto de disponibilidade de máquinas virtuais é realmente apenas fecha-las e desativar com base nas regras de escala que você configurar. Para obter mais informações sobre máquinas virtuais e conjuntos de disponibilidade, consulte [Gerenciar a disponibilidade das máquinas virtuais](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)

Considere as seguintes informações antes de configurar o dimensionamento do aplicativo:

- Dimensionamento é afetado pelo uso de núcleo. Instâncias de função maiores usam mais cores. Você pode dimensionar um aplicativo apenas dentro do limite de cores para sua assinatura. Por exemplo, se sua assinatura tem um limite de vinte cores e executar um aplicativo com dois médio porte (um total de quatro cores) de serviços de nuvem, você pode apenas crescerão outras implantações de serviço de nuvem em sua assinatura por 16 cores. Consulte [Tamanhos de serviço de nuvem](cloud-services-sizes-specs.md) para obter mais informações sobre tamanhos.

- Você deve criar uma fila e associá-la a uma função antes de você pode dimensionar um aplicativo baseado em um limite de mensagem. Para obter mais informações, consulte [como usar o serviço de armazenamento de fila](../storage/storage-dotnet-how-to-use-queues.md).

- Você pode dimensionar recursos que estão vinculados ao seu serviço de nuvem. Para obter mais informações sobre recursos de vinculação, consulte [como: vincular um recurso a um serviço de nuvem](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- Para habilitar alta disponibilidade do seu aplicativo, você deve garantir que ele é implantado com duas ou mais ocorrências de função. Para obter mais informações, consulte [Os contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).



## <a name="schedule-scaling"></a>Dimensionamento de agenda

Por padrão, todas as funções não siga uma agenda específica. Portanto, as configurações alteradas aplicam a todos os horários e todos os dias ao longo do ano. Se desejar, você pode configurar o dimensionamento automático ou manual para:

- Dias úteis
- Fins de semana
- Noites de semana
- Feiras de manhã de semana
- Datas específicas
- Intervalos de datas específicas

Isso é conigured no [portal clássico Azure](https://manage.windowsazure.com/) na  
**Serviços de nuvem** > **\[seu serviço de nuvem\]** > **escala** > **\[produção ou preparação\] ** página.

Clique no botão **Configurar períodos de cronograma** para cada função que você deseja alterar.

![Dimensionamento automático com base em um cronograma de serviço em nuvem][scale_schedules]



## <a name="manual-scale"></a>Escala de manual

Na página de **escala** , você pode manualmente aumentar ou diminuir o número de instâncias em execução em um serviço de nuvem. Isso é configurado para cada agenda que você criou ou para sempre se você não tiver criado um cronograma.

1. No [portal do Azure clássico](https://manage.windowsazure.com/), clique em **Serviços de nuvem**e, em seguida, clique no nome do serviço de nuvem para abrir o painel.

    > [AZURE.TIP] Se você não vir seu serviço de nuvem, talvez você precise alterar de **produção** para **teste** ou vice-versa.

2. Clique em **escala**.

3. Selecione o agendamento que você deseja alterar as opções de escala para. Padrões para *não horários agendados* se você não tiver nenhum agendamentos definidos.

4. Localize a seção de **escala pela métrica** e selecione **Nenhum**. Esta é a configuração padrão para todas as funções.

5. Cada função no serviço de nuvem tem um controle deslizante para alterar o número de instâncias para usar.

    ![Dimensionar manualmente uma função de serviço de nuvem][manual_scale]

    Se precisar de mais ocorrências, talvez você precise alterar o [tamanho de máquina virtual do serviço de nuvem](cloud-services-sizes-specs.md).

6. Clique em **Salvar**.  
Instâncias de função serão adicionadas ou removidas com base nas suas seleções.

>[AZURE.TIP] Quando você vir ![][tip_icon] mova o mouse para ele e você pode obter ajuda sobre o que é uma configuração específica.


## <a name="automatic-scale---cpu"></a>Escala automática - CPU

Isso escalas se a porcentagem média de uso da CPU ficar acima ou abaixo de limites especificados; instâncias de função são criadas ou excluídas.

1. No [portal do Azure clássico](https://manage.windowsazure.com/), clique em **Serviços de nuvem**e, em seguida, clique no nome do serviço de nuvem para abrir o painel.

    > [AZURE.TIP] Se você não vir seu serviço de nuvem, talvez você precise alterar de **produção** para **teste** ou vice-versa.

2. Clique em **escala**.

3. Selecione o agendamento que você deseja alterar as opções de escala para. Padrões para *não horários agendados* se você não tiver nenhum agendamentos definidos.

4. Localize a seção de **escala pela métrica** e selecione **CPU**.

5. Agora você pode configurar um intervalo mínimo e máximo de instâncias de funções, o destino do uso de CPU (para acionar uma escala para cima) e quantas instâncias para cima e para baixo escala por.

![Dimensionar uma função de serviço de nuvem pela carga de cpu][cpu_scale]

>[AZURE.TIP] Quando você vir ![][tip_icon] mova o mouse para ele e você pode obter ajuda sobre o que é uma configuração específica.





## <a name="automatic-scale---queue"></a>Escala automática - fila

Isso expande automaticamente se o número de mensagens em uma fila ficar acima ou abaixo de um limite especificado; instâncias de função são criadas ou excluídas.

1. No [portal do Azure clássico](https://manage.windowsazure.com/), clique em **Serviços de nuvem**e, em seguida, clique no nome do serviço de nuvem para abrir o painel.

    > [AZURE.TIP] Se você não vir seu serviço de nuvem, talvez você precise alterar de **produção** para **teste** ou vice-versa.

2. Clique em **escala**.

3. Localize a seção de **escala pela métrica** e selecione **CPU**.

4. Agora você pode configurar um intervalo mínimo e máximo de instâncias de funções, na fila e a quantidade de mensagens de fila para processar para cada instância e quantas instâncias para cima e para baixo escala por.

![Dimensionar uma função de serviço de nuvem por uma fila de mensagem][queue_scale]

>[AZURE.TIP] Quando você vir ![][tip_icon] mova o mouse para ele e você pode obter ajuda sobre o que é uma configuração específica.


## <a name="scale-linked-resources"></a>Dimensionar recursos vinculados

Muitas vezes, quando você dimensionar uma função, é útil dimensionar o banco de dados que o aplicativo está usando também. Se você vincular o banco de dados para o serviço de nuvem, você pode acessar as configurações de escala para o recurso clicando no link apropriado.

1. No [portal do Azure clássico](https://manage.windowsazure.com/), clique em **Serviços de nuvem**e, em seguida, clique no nome do serviço de nuvem para abrir o painel.

    > [AZURE.TIP] Se você não vir seu serviço de nuvem, talvez você precise alterar de **produção** para **teste** ou vice-versa.

2. Clique em **escala**.

3. Localize a seção de **recursos vinculados** e clicado em **escala de gerenciar este banco de dados**.

    > [AZURE.NOTE] Se você não vir uma seção de **recursos vinculados** , você provavelmente não tem quaisquer recursos vinculados.

![][linked_resource]


[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
