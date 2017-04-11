<properties
   pageTitle="Use o painel de controle de dispositivo do Gerenciador de StorSimple | Microsoft Azure"
   description="Descreve o painel de dispositivo de serviço do Gerenciador de StorSimple e como usá-lo para exibir métricas de armazenamento e iniciadores conectados e encontrar o número de série e IQN."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-device-dashboard"></a>Use o painel de controle de dispositivo do Gerenciador de StorSimple

## <a name="overview"></a>Visão geral

O painel de controle de dispositivo do Gerenciador de StorSimple apresenta uma visão geral das informações para um dispositivo de StorSimple específico, em contraste com o painel de serviço, que fornece informações sobre todos os dispositivos incluídos em sua solução Microsoft Azure StorSimple.

![Página de painel de dispositivo](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

O painel contém as seguintes informações:

- **Área do gráfico** – você pode ver as métricas de armazenamento relevantes na área do gráfico na parte superior do painel de controle. Nesse gráfico, você pode exibir métricas para o armazenamento total principal (a quantidade de dados gravados por hosts para seu dispositivo) e o armazenamento em nuvem total consumida por seu dispositivo durante um período de tempo.

     Nesse contexto, *armazenamento principal* referencia a quantidade total de dados gravados pelo host e podem ser divididas por tipo de volume: *armazenamento hierárquico principal* inclui ambos os dados armazenados localmente e dados hierárquico na nuvem; *primária localmente fixos armazenamento* inclui apenas os dados armazenados localmente. *Armazenamento em nuvem*, por outro lado, é uma medida da quantidade total de dados armazenados na nuvem. Isso inclui backups e hierárquica de dados. Observe que os dados armazenados na nuvem é eliminação de duplicação e compactados, enquanto o armazenamento principal indica a quantidade de armazenamento usada antes dos dados são eliminação de duplicação e compactados. (Você pode comparar esses dois números para ter uma ideia da taxa de compactação). Para ambas as primário e armazenamento em nuvem, os valores mostrados se baseará a frequência de acompanhamento de configurar. Por exemplo, se você escolher uma frequência de uma semana, em seguida, o gráfico mostrará dados para cada dia na semana anterior.

     Você pode configurar o gráfico da seguinte maneira:

     - Para ver a quantidade de armazenamento em nuvem consumida ao longo do tempo, selecione a opção de **Armazenamento em nuvem usado** . Para ver o total de armazenamento que foram gravado pelo host, selecione as opções de **Primária HIERÁRQUICO armazenamento usada** e **primária LOCALMENTE fixos armazenamento** . Na ilustração, ambas as opções são selecionadas; Portanto, o gráfico mostra valores de armazenamento para armazenamento principal e de nuvem. Observe que qualquer armazenamento principal usado antes de instalar a atualização 2 é representado por linha **Primária HIERÁRQUICO armazenamento usado** .
     - Use o menu suspenso no canto superior direito do gráfico para especificar um período de tempo de 1 semana, 1 mês, 3 meses ou 1 ano. Observe que o gráfico de nível superior é atualizado apenas uma vez por dia e, portanto, refletirão totais do dia anterior.

     Para obter mais informações, consulte [usar o serviço de Gerenciador de StorSimple para monitorar seu dispositivo StorSimple](storsimple-monitor-device.md).

- **Visão geral do uso** – na área de **Visão geral do uso** , você pode ver a quantidade de armazenamento principal usado, a quantidade de armazenamento provisionado e a capacidade de armazenamento máximo para seu dispositivo. Comparando esses números de uso para a quantidade máxima de armazenamento disponível, você pode ver rapidamente se você precisar obter armazenamento adicional. Observe que esta visão geral é atualizado a cada 15 minutos e, devido a diferença na frequência de atualização, pode mostrar números diferentes daqueles mostrados na área do gráfico acima, que é atualizada diariamente. Para obter mais informações, consulte [usar o serviço de Gerenciador de StorSimple para monitorar seu dispositivo StorSimple](storsimple-monitor-device.md).


- **Alertas** – a área de **alertas** contém uma visão geral dos alertas para seu dispositivo. Alertas são agrupadas por gravidade, e é fornecida uma contagem do número de alertas em cada nível de gravidade. Clicar a gravidade de alerta abre um modo de exibição de escopo da guia alertas para mostrar somente os alertas que do nível de gravidade para esse dispositivo.

- **Trabalhos** – a área de **trabalhos** mostra o resultado de atividade de trabalho recentes. Isso pode garantir que o sistema estiver funcionando conforme esperado, ou ele pode informar que você precisa tomar medidas corretivas. Para ver mais informações sobre trabalhos concluídos recentemente, clique em **trabalhos com êxito nas últimas 24 horas**.

- A área de **rapidamente** à direita do painel fornece informações úteis como modelo do dispositivo, número de série, status, descrição e número de volumes.

Você também pode configurar failover e exibir iniciadores conectados no painel do dispositivo.

As tarefas comuns que podem ser executadas nesta página são:

- Exibir iniciadores conectados

- Localizar o número de série do dispositivo

- Encontrar o destino de dispositivo IQN

## <a name="view-connected-initiators"></a>Exibir iniciadores conectados

Você pode exibir os iniciadores iSCSI conectados ao seu dispositivo clicando no link **Exibir conectados iniciadores** fornecido na área **rapidamente** do seu painel de dispositivo. Esta página fornece uma listagem tabular os iniciadores de que tiver conectado com êxito ao seu dispositivo. Para cada iniciador, você pode ver:

- O iSCSI qualificado nome (IQN) do iniciador conectado.

- O nome do registro de controle de acesso (ACR) que permite este iniciador conectada.

- O endereço IP do iniciador conectado.

- As interfaces de rede que o iniciador está conectado ao dispositivo de armazenamento. Eles podem variar de dados 0 a 5 de dados.

- Todos os volumes que o iniciador conectado tem permissão para acessar acordo com a configuração de ACR atual.

Se você vir iniciadores inesperados nesta lista ou não vir os esperados, examine a configuração de ACR. Um máximo de 512 iniciadores pode se conectar ao seu dispositivo.

## <a name="find-the-device-serial-number"></a>Localizar o número de série do dispositivo

O número de série do dispositivo pode ser necessário quando você configura o i/o de vários caminhos da Microsoft (MPIO) no dispositivo. Execute as seguintes etapas para localizar o número de série do dispositivo.

#### <a name="to-find-the-device-serial-number"></a>Para encontrar o número de série do dispositivo

1. Navegue até **dispositivos** > **Dashboard**.

2. No painel à direita do painel de controle, localize a área **rapidamente** .

3. Role para baixo e localize o número de série.

## <a name="find-the-device-target-iqn"></a>Encontrar o destino de dispositivo IQN

Talvez você precise de destino de dispositivo IQN quando você configura o protocolo de autenticação de Handshake de desafio (CHAP) em seu dispositivo de StorSimple. Execute as seguintes etapas para localizar o destino de dispositivo IQN.

### <a name="to-find-the-device-target-iqn"></a>Para encontrar o destino de dispositivo IQN

1. Navegue até **dispositivos** > **Dashboard**.

1. No painel à direita do painel de controle, localize a área **rapidamente** .

1. Role para baixo e localize o destino IQN.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Painel de StorSimple Gerenciador de serviço](storsimple-service-dashboard.md).
- Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
