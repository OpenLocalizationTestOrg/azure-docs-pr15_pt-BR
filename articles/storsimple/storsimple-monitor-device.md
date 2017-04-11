<properties 
   pageTitle="Monitorar seu dispositivo StorSimple | Microsoft Azure"
   description="Descreve como usar o serviço do Gerenciador de StorSimple para monitorar o desempenho, utilização da capacidade, produtividade de rede e desempenho do dispositivo e/s."
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
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Usar o serviço do Gerenciador de StorSimple para monitorar seu dispositivo StorSimple 

## <a name="overview"></a>Visão geral

Você pode usar o serviço do Gerenciador de StorSimple para monitorar dispositivos específicos em sua solução de StorSimple. Você pode criar gráficos personalizados com base em i/o desempenho, utilização da capacidade, produtividade de rede e métricas de desempenho do dispositivo. 

Para exibir as informações de monitoramento para um dispositivo específico, no portal de clássico do Azure, selecione o serviço de Gerenciador de StorSimple. Clique na guia **Monitor** e selecione na lista de dispositivos. A página de **Monitor** contém as informações a seguir.

## <a name="io-performance"></a>Desempenho e/s 

Métricas de faixas de **desempenho de e/s** relacionados ao número de leitura e gravação operações entre um as interfaces do iniciador iSCSI no servidor host e o dispositivo ou o dispositivo e a nuvem. Esse desempenho pode ser medido para um volume específico, um contêiner de volume específico ou todos os contêineres de volume.

O gráfico abaixo mostra a i / o para o iniciador ao seu dispositivo para todos os volumes de um dispositivo de produção. As métricas plotadas sejam lidas e gravar bytes por segundo, ler e gravar operações de IO por segundo e ler e gravar latências.

![Desempenho de IO do iniciador ao dispositivo](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Para o mesmo dispositivo, as operações de e/s são plotadas para os dados do dispositivo para a nuvem para todos os contêineres de volume. Neste dispositivo, os dados forem apenas na camada linear e nada tem derramado na nuvem. Não há nenhum operações de leitura e gravação ocorrendo do dispositivo para a nuvem. Portanto, os picos no gráfico estão em um intervalo de 5 minutos que corresponde à frequência na qual a pulsação está marcada entre o dispositivo e o serviço. 

![Desempenho de IO do dispositivo para a nuvem](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


Para o mesmo dispositivo, um instantâneo de nuvem foi criado para dados de volume começando em 2:00 pm. Isso resultou em dados fluindo do dispositivo para a nuvem. Leituras gravações foram served para a nuvem nessa duração. O gráfico de IO mostra um pico nas várias métricas correspondente a hora quando o instantâneo foi criado. 

![Desempenho de IO dispositivo e nuvem após instantâneo de nuvem](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## <a name="capacity-utilization"></a>Uso da capacidade 

**Utilização da capacidade** rastreia métricas relacionadas à quantidade de espaço de armazenamento de dados que é usado pelos volumes, contêineres de volume ou dispositivo. Você pode criar relatórios baseados na utilização de capacidade de seu armazenamento principal, seu armazenamento em nuvem ou seu armazenamento de dispositivo. Uso da capacidade pode ser medido em um volume específico, um contêiner de volume específico ou todos os contêineres de volume.


Nuvem principal, e a capacidade de armazenamento do dispositivo pode ser descrita da seguinte maneira:

###<a name="primary-storage-capacity-utilization"></a>Utilização de capacidade de armazenamento principal
 
Esses gráficos mostram a quantidade de dados gravados volumes de StorSimple antes dos dados são eliminação de duplicação e compactados. Você pode exibir a utilização de armazenamento principal por todos os volumes ou para um único volume.

Quando você exibir os gráficos de utilização de capacidade de volume do armazenamento principal para todos os volumes versus cada um dos volumes individuais e soma dados primários ambas nesses casos, pode haver uma incompatibilidade entre os dois números. Dados primários totais em todos os volumes não podem somar o total de soma dos dados primários dos volumes individuais. Isto pode ser devido a um destes procedimentos:

- **Incluído em todos os volumes de dados de instantâneo**: esse comportamento é visto apenas se você estiver executando a versão anterior à atualização 3. Dados primários mostrados para todos os volumes são a soma dos dados primários para cada volume e os dados do instantâneo. Dados principais de um determinado volume exibidos corresponde ao apenas a quantidade de dados alocados no volume (e não inclui os dados correspondentes de instantâneo de volume).

    Isso também pode ser explicado pela seguinte equação:

    *Dados principais (todos os volumes) = soma (dados primários (volume i) + tamanho dos dados de instantâneo (volume i))*
    
    *onde, dados primários (volume i) = tamanho dos dados primários alocados para o volume i*
 
    Se os instantâneos forem excluídos através do serviço, a exclusão é feita assíncrona no plano de fundo. Pode levar alguns minutos para que o tamanho de dados do volume sejam atualizadas após a exclusão de instantâneo. 

    Se executando Update 3 ou posterior, os dados do instantâneo não estão incluídos nos dados de volume. E a utilização principal é calculada da seguinte maneira:

    * Dados principal (todos os volumes) = soma de (dados principais (volume i)
    
    *onde, dados primários (volume i) = tamanho dos dados primários alocados para o volume i*
 
- **Volumes com monitoramento desabilitado incluídos em todos os volumes**: se você tiver volumes em seu dispositivo para o qual monitoramento está desativado, os dados de monitoramento para esses volumes individuais não estará disponíveis nos gráficos. No entanto, os dados de todos os volumes no gráfico incluem os volumes para os quais monitoramento está desativado. 
 
- **Excluídos volumes com backups associados ao vivo incluídos para todos os volumes**: se volumes que contém os dados de instantâneo são excluídos, mas os instantâneos associados ainda existem, em seguida, você poderá ver uma incompatibilidade.

- **Volumes excluídos incluídos para todos os volumes**: em alguns casos, volumes antigos podem existir mesmo que elas foram excluídas. O efeito de exclusão não é visto e o dispositivo pode mostrar menor capacidade disponível. Você precisa entrar em contato com o Microsoft Support para remover esses volumes.

Os gráficos a seguir mostram o uso da capacidade de armazenamento principal de um dispositivo StorSimple antes e depois de um instantâneo de nuvem foi criado. Como isso é apenas os dados de volume, um instantâneo de nuvem não deve alterar o armazenamento principal. Como você pode ver, o gráfico não mostra nenhuma diferença da utilização de capacidade principal como resultado de tirar um instantâneo de nuvem. O instantâneo de nuvem iniciado em torno 2:00 pm nesse dispositivo.

![Utilização da capacidade principal instantâneo de nuvem antes](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilização da capacidade principal após instantâneo de nuvem](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Se você estiver executando atualizar 2 ou superior, você pode dividir o uso da capacidade de armazenamento principal por um volume individual, todos os volumes, todos os volumes hierárquicos e todos os volumes localmente fixados conforme mostrado abaixo. Dividindo por todos os volumes localmente fixados permitirá que você determine rapidamente quanto da camada local é usado para cima.

![Utilização da capacidade principal para todos os volumes localmente fixados](./media/storsimple-monitor-device/localvolumes.png)


###<a name="cloud-storage-capacity-utilization"></a>Utilização de capacidade de armazenamento de nuvem

Esses gráficos mostram a quantidade de armazenamento em nuvem usado. Esses dados é eliminação de duplicação e compactados. O valor inclui instantâneos de nuvem que podem conter dados que não são refletidos em qualquer volume principal e são mantidos para fins de retenção herdado ou necessários. Você pode comparar o principal e nuvem valores de consumo de armazenamento para ter uma ideia da taxa de redução de dados, embora o número não será exato. Os gráficos a seguir mostram a utilização de capacidade de armazenamento de nuvem de um dispositivo StorSimple antes e depois de um instantâneo de nuvem foi criado. O instantâneo de nuvem iniciado em torno 2:00 pm nesse dispositivo e você pode ver o uso da capacidade de nuvem captura ao mesmo tempo, aumentando de MB 5,73 4.04 GB.

![Utilização de capacidade de nuvem antes instantâneo de nuvem](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilização de capacidade de nuvem após instantâneo de nuvem](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###<a name="device-storage-capacity-utilization"></a>Utilização de capacidade de armazenamento de dispositivos

Esses gráficos mostram a utilização total para o dispositivo, qual será a utilização de armazenamento principal mais de porque ele inclui a camada linear SSD. Esta camada contém uma quantidade de dados que também existe no dispositivo do outros níveis. A capacidade na camada linear SSD é alternada para que quando novos dados for recebida, os dados antigos são movidos para o nível de disco rígido (momento em que ele é eliminação de duplicação e compactado) e subsequentemente na nuvem.

Ao longo do tempo, utilização da capacidade principal e utilização de capacidade de dispositivo provavelmente aumentará juntas até que os dados começam a ser hierárquico para a nuvem. Neste ponto, o uso da capacidade de dispositivo provavelmente começará a estabilidade, mas a utilização de capacidade principal aumentará conforme mais dados são gravados.

Os gráficos a seguir mostram o uso da capacidade de armazenamento principal de um dispositivo StorSimple antes e depois de um instantâneo de nuvem foi criado. O instantâneo de nuvem Introdução às 2:00 e o uso da capacidade de dispositivo iniciado diminuindo nesse momento. O uso da capacidade de armazenamento do dispositivo foi desativado de 11.58 GB para 7.48 GB. Isso indica que provavelmente os dados descompactados na camada de SSD linear foi eliminação de duplicação, compactados e movidos para a camada de disco rígido. Observe que, se o dispositivo já tiver uma grande quantidade de dados em camadas SSD tanto o disco rígido, você pode não ver essa diminuição. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização de capacidade de dispositivos antes instantâneo de nuvem](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilização de capacidade de dispositivos após instantâneo de nuvem](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## <a name="network-throughput"></a>Taxa de transferência de rede

**Taxa de transferência de rede** rastreia métricas relacionadas à quantidade de dados transferidos de interfaces de rede do iniciador iSCSI no servidor host e o dispositivo e entre o dispositivo e a nuvem. Você pode monitorar essa métrica para cada iSCSI interfaces de rede em seu dispositivo.

Os gráficos a seguir mostram a taxa de transferência de rede para os dados 0 e 4 de dados, as duas interfaces de rede GbE 1 em seu dispositivo. Neste exemplo, dados 0 foi habilitado para a nuvem enquanto 4 de dados foi habilitado para iSCSI. Você pode ver a entrada e o tráfego de saída para o seu dispositivo StorSimple. A linha simples no gráfico iniciando de 3:24 pm é devido ao fato de que coletar dados somente a cada 5 minutos e deve ser ignoradas. 

![Taxa de transferência de rede para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Taxa de transferência de rede para Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## <a name="device-performance"></a>Desempenho do dispositivo 

**Desempenho do dispositivo** rastreia métricas relacionadas ao desempenho do seu dispositivo. O gráfico a seguir mostra as estatísticas de utilização de CPU para um dispositivo em produção.

![Utilização de CPU para dispositivo](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o painel de dispositivo de serviço do Gerenciador de StorSimple](storsimple-device-dashboard.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
