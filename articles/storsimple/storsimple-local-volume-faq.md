<properties 
   pageTitle="StorSimple localmente fixos volumes perguntas Frequentes | Microsoft Azure"
   description="Fornece respostas para perguntas frequentes sobre volumes de StorSimple fixada localmente."
   services="storsimple"
   documentationCenter="NA"
   authors="manuaery"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/16/2016"
   ms.author="manuaery" />

# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple localmente fixos volumes: perguntas frequentes (FAQ)

## <a name="overview"></a>Visão geral

A seguir estão perguntas e respostas que você possa ter quando você cria um volume StorSimple localmente fixos, converter um volume hierárquico para um volume localmente fixado (e vice-versa), ou fazer backup e restaurar um volume localmente fixado.

Perguntas e respostas são organizadas em categorias a seguir

- Criando um volume localmente fixado
- Fazer backup de um localmente fixado
- Convertendo um volume hierárquico para um volume localmente fixado
- Restaurar um volume localmente fixado
- Falha ao longo de um volume localmente fixado

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Perguntas sobre a criação de um volume localmente fixado

**P.** O que é o tamanho máximo de um volume localmente fixado que eu pode criar nos dispositivos a 8000 série?

**R** você pode provisionar volumes localmente fixados até 8,5 TB ou volumes hierárquicos até 200 TB no dispositivo 8100. No dispositivo 8600 maior, você pode provisionar volumes localmente fixados até 22,5 TB ou volumes hierárquicos até 500 TB.

**P.** Eu meu dispositivo 8100 atualizado recentemente para atualização 2 e ao tentar criar um volume localmente fixado, o tamanho máximo disponível é apenas 6 TB e não 8,5 TB. Por que não posso criar um volume de 8,5 TB?

**R** você pode provisionar localmente fixados volumes até 8,5 TB ou hierárquico volumes de até 200 TB no dispositivo 8100. Se seu dispositivo já tem hierárquico volumes, em seguida, o espaço disponível para a criação de um volume localmente fixado será proporcionalmente menor que esse limite máximo. Por exemplo, se 100 TB de volumes hierárquicos já tiver sido configurado em seu dispositivo 8100 (que é a metade da capacidade hierárquica), em seguida, o tamanho máximo de um volume local que você pode criar no dispositivo 8100 será correspondente reduzido 4 TB (aproximadamente metade do máximo localmente fixos capacidade de volume).

Como alguns espaço local no dispositivo é usado para hospedar o conjunto de trabalho de volumes hierárquicos, o espaço disponível para a criação de um volume localmente fixado é reduzido se o dispositivo tem hierárquico volumes. Por outro lado, a criação de um volume localmente fixado proporcionalmente reduz o espaço disponível para volumes hierárquicos. A tabela a seguir resume a capacidade de hierárquica disponível nos dispositivos 8100 e 8600 quando volumes localmente fixados são criados.

|Capacidade de provisionado volumes localmente fixado|Capacidade disponível para ser provisionado para volumes hierárquicos - 8100|Capacidade disponível para ser provisionado para volumes hierárquicos - 8600|
|-----|------|------|
|0 | 200 TB | 500 TB |
|1 TB | 176.5 TB | 477.8 TB|
|4 TB | 105.9 TB | 411.1 TB |
|8.5 TB | TB 0 | 311.1 TB|
|10 TB | DISP | 277.8 TB |
|15 TB | DISP | 166.7 TB |
|22.5 TB | DISP | TB 0 |


**P.** Por que a criação de volume localmente fixada uma longa operação em execução? 

**R.** Volumes localmente fixados thickly são provisionados. Para criar espaço nos níveis locais do dispositivo, alguns dados de volumes hierárquicos existentes podem ser enviados para a nuvem durante o processo de provisionamento. E já isso depende do tamanho do volume sendo provisionado, os dados existentes no seu dispositivo e a largura de banda disponível para a nuvem, o tempo necessário para criar um volume local pode ser várias horas.

**P.** Quanto tempo leva para criar um volume localmente fixado?

**R.** Porque volumes localmente fixados thickly são provisionados, alguns dados existentes de volumes hierárquicos podem ser enviados para a nuvem durante o processo de provisionamento. Portanto, o tempo necessário para criar um volume localmente fixado depende de vários fatores, incluindo o tamanho do volume, os dados no seu dispositivo e a largura de banda disponível. Em um dispositivo instalado recentemente que não tem volumes, o tempo para criar um volume localmente fixado é cerca de 10 minutos por terabytes de dados. Entretanto, criação de volumes locais pode levar algumas horas com base nos fatores explicados acima em um dispositivo que está em uso.

**P.** Quero criar um volume localmente fixado. Existem qualquer práticas recomendadas que eu preciso saber?

**R.** Volumes localmente fixados são adequados para cargas de trabalho que exigem garantias locais dos dados em todos os momentos e são sensíveis a nuvem latências. Considerando o uso da volumes locais para qualquer uma das suas cargas de trabalho, esteja ciente do seguinte:

- Volumes localmente fixados thickly são provisionados e criar volumes locais afeta o espaço disponível para volumes hierárquicos. Portanto, é recomendável iniciar com volumes menores e crescer como seu aumentos de requisito de armazenamento.

- Provisionamento de volumes locais é uma operação demorada que pode envolver insistem dados existentes de volumes hierárquicos para a nuvem. Como resultado, você pode enfrentar desempenho reduzido nesses volumes.

- Provisionamento de volumes locais é uma operação demorada. O tempo real envolvidos depende de vários fatores: o tamanho do volume sendo provisionado, os dados em seu dispositivo e a largura de banda disponível. Se você não tiver feito backup os volumes existentes na nuvem, a criação de volume é mais lenta. Sugerimos que você instantâneos nuvem dos volumes existentes antes de você provisionar um volume local.
 
- Você pode converter volumes hierárquicos existentes em volumes localmente fixados e essa conversão envolve provisionamento de espaço no dispositivo para o volume localmente fixado resultante (além de desativação hierárquica de dados [se qualquer] da nuvem). Novamente, essa é uma operação de execução demorada que depende de fatores que discutimos acima. Sugerimos que você faça backup os volumes existentes antes da conversão conforme o processo será ainda mais lento se volumes existentes não são copiados. Seu dispositivo também pode enfrentar desempenho reduzido durante este processo.
    
Obter mais informações sobre como [criar um volume localmente fixado](storsimple-manage-volumes-u2.md#add-a-volume)

**P.** Pode criar vários volumes localmente fixados ao mesmo tempo?

**R.** Sim, mas os trabalhos de criação e expansão de volume localmente fixada são processados sequencialmente.

Volumes localmente fixados thickly são provisionados e isso requer a criação de espaço local do dispositivo (que pode resultar em dados existentes de volumes hierárquicos para ser colocado na nuvem durante o processo de provisionamento). Portanto, se um trabalho de provisionamento está em andamento, outros trabalhos de criação de volume local serão enfileirados até que trabalho está concluído.

Da mesma forma, se um volume local existente está sendo expandido ou um volume hierárquico está sendo convertido em um volume localmente fixado, em seguida, a criação de um novo volume localmente fixado está na fila até que o trabalho anterior seja concluído. Expandir o tamanho de um volume localmente fixado envolve a expansão do espaço de local existente para esse volume. Conversão de uma hierárquico para localmente fixos volume também envolve a criação de espaço local para o resultante localmente fixos volume. Em ambos essas operações, criação ou expansão de espaço local está um longo executando o trabalho.

Você pode visualizar esses trabalhos na página **trabalhos** do serviço do Gerenciador de StorSimple do Azure. O trabalho que está sendo processado ativamente continuamente é atualizado para refletir o progresso de provisionamento de espaço. Os trabalhos de volume localmente fixada restante está marcado como execução, mas seu andamento é interrompido e eles são selecionados na ordem em que eles foram enfileirados.

**P.** Posso excluído um volume localmente fixado. Por que não vejo o espaço recuperado refletido no espaço disponível ao tentar criar um novo volume? 

**R.** Se você excluir um volume localmente fixado, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do Gerenciador de StorSimple atualiza o local espaço disponível aproximadamente a cada hora. Sugerimos que você aguarde uma hora antes de tentar criar o novo volume.

**P.** Volumes localmente fixados são compatíveis com o aparelho de nuvem?

**R.** Volumes localmente fixados não são suportados no dispositivo nuvem (dispositivos 8010 e 8020, anteriormente conhecidos como o dispositivo virtual StorSimple).

**P.** Pode usar os cmdlets do PowerShell do Azure para criar e gerenciar volumes localmente fixados? 

**R.** Não, você não pode criar volumes localmente fixados via cmdlets do PowerShell do Azure (qualquer volume criado por meio do PowerShell do Azure é hierárquico). Também é recomendável que você não use cmdlets do PowerShell do Azure para modificar as propriedades de um volume localmente fixada, como ele terá o efeito indesejado de modificar o tipo de volume para hierárquico.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Perguntas sobre o backup de um volume localmente fixado

**P.** São instantâneos locais de volumes localmente fixados com suporte?

**R.** Sim, você pode tirar instantâneos locais dos volumes localmente fixados. No entanto, sugerimos que você faça backup seus volumes localmente fixados com instantâneos de nuvem para garantir que seus dados estão protegidos realizada caso ocorra um desastre regularmente.

**P.** Existem qualquer diretrizes para gerenciar instantâneos locais para volumes localmente fixados?

**R.** Frequentes instantâneos locais junto com uma alta taxa de rotatividade de dados no volume localmente fixada podem causar espaço local no dispositivo para ser consumida rapidamente e resultar em dados de volumes hierárquicos sendo enviados para a nuvem. Portanto, é recomendável que você minimiza o número de instantâneos locais.

**P.** Recebi um alerta informando que minha instantâneos locais de volumes localmente fixados podem ser invalidados. Quando isso acontece?

**R.** Frequentes instantâneos locais junto com uma alta taxa de rotatividade de dados no volume localmente fixada podem causar espaço local no dispositivo para ser consumida rapidamente. Se as camadas locais do dispositivo muito forem usadas, uma interrupção de nuvem estendido pode resultar em dispositivo se tornar completo e gravações recebidas o volume podem resultar em invalidação dos instantâneos (como não existe nenhum espaço para atualizar os instantâneos para consultar os blocos antigos de dados que tenham sido substituídos). Em uma situação as gravações no volume continuará a ser atendidas, mas os instantâneos locais podem ser inválidos. Não há nenhum impacto seu instantâneos de nuvem existentes.

O aviso de alerta é para notificá-lo a que essa situação pode surgir e certifique-se de que você o mesmo endereço em tempo hábil revisando agendas instantâneos locais tirar instantâneos locais menos frequentes ou excluindo instantâneos antigos de locais que não são mais necessários.

Se os instantâneos locais são invalidados, você receberá um alerta de informações notificá-lo a que os instantâneos locais para a política de backup específico tiveram sido invalidados junto com a lista de carimbos dos instantâneos locais que foram invalidados. Esses instantâneos serão automaticamente excluídos e será mais possível exibi-los na página de **Catálogos de Backup** no portal de clássico do Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Perguntas sobre a conversão de um volume hierárquico para um volume localmente fixado

**P.** Eu estou observando alguns lentidão no dispositivo ao converter um volume hierárquico para um volume localmente fixado. Por que isso acontece? 

**R.** O processo de conversão envolve duas etapas: 

  1. Provisionamento de espaço no dispositivo para o breve-para--convertidos localmente fixada volume.
  2. Baixando quaisquer dados hierárquicos de nuvem para garantir local garante.

Essas duas etapas forem longas executando operações que dependem do tamanho do volume convertido, dados no dispositivo e largura de banda disponível. Como alguns dados de volumes hierárquicos existentes podem passando na nuvem como parte do processo de provisionamento, seu dispositivo pode enfrentar desempenho reduzido durante esse período. Além disso, o processo de conversão pode ser mais lento se:

- Volumes existentes não tem sido feitos backup na nuvem; Portanto, é recomendável que você faça o backup de seus volumes antes de iniciar uma conversão.

- Políticas de otimização de largura de banda foram aplicadas, que podem restringir a largura de banda disponível para a nuvem; Portanto, recomendamos que você tiver um dedicado Mbps 40 ou mais conexão para a nuvem.

- O processo de conversão pode levar horas devido as vários fatores explicados acima; Portanto, é recomendável que você executar essa operação durante períodos não picos ou em um final de semana para evitar o impacto sobre consumidores finais.

Obter mais informações sobre como [converter um volume hierárquico para um volume localmente fixado](storsimple-manage-volumes-u2.md#change-the-volume-type)

**P.** Para cancelar a operação de conversão de volume?

**R.** Você não o cancelar a operação de conversão iniciada uma vez. Conforme discutido na pergunta anterior, esteja ciente de possíveis problemas de desempenho que você pode encontrar durante o processo e siga as práticas recomendadas listadas acima, quando você planejar sua conversão.

**P.** O que acontece com meu volume se a operação de conversão falhar?

**R.** Conversão de volume pode falhar devido a problemas de conectividade de nuvem. O dispositivo eventualmente pode parar o processo de conversão após uma série de tentativas para trazer dados hierárquico da nuvem. Nesse caso, o tipo de volume continuará a ser o tipo de volume de origem antes da conversão, e:

- Um alerta crítico será gerado para notificá-lo da falha de conversão de volume. Obter mais informações sobre [alertas relacionados a volumes localmente fixados](storsimple-manage-alerts.md#locally-pinned-volume-alerts)

- Se você estiver convertendo um hierárquico para um volume localmente fixado, o volume continuará exibem propriedades de um volume hierárquico como dados ainda podem residem na nuvem. Sugerimos que você resolver os problemas de conectividade e, em seguida, repita a operação de conversão.
 
- Da mesma forma, quando a conversão de uma localmente fixada em um volume hierárquico falha, embora o volume será marcado como um volume localmente fixado, ele funcionará como um volume hierárquico (porque dados poderiam ter derramado na nuvem). No entanto, ele continuará a ocupar espaço nos níveis locais do dispositivo. Este espaço não estará disponível para outros volumes localmente fixados. Sugerimos que você repetir essa operação para garantir que a conversão de volume for concluída e o espaço local no dispositivo pode ser recuperado.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Perguntas sobre como restaurar um volume localmente fixado

**P.** São volumes localmente fixados restaurados instantaneamente?

**R.** Sim, volumes localmente fixados são restaurados instantaneamente. Assim que as informações de metadados para o volume estão puxadas da nuvem como parte da operação de restauração, o volume é colocado online e pode ser acessado pelo host. No entanto, garantias locais para o volume de dados não estará presentes até que todos os dados foi baixado da nuvem e, em seguida, você pode enfrentar redução do desempenho nesses volumes a duração da restauração.

**P.** Quanto tempo leva para restaurar um volume localmente fixado?

**R.** Volumes localmente fixados são restaurados instantaneamente e colocados online, assim como as informações de metadados de volume são recuperadas da nuvem, enquanto os dados do volume continuam a ser baixado no plano de fundo. Esta última parte da operação de restauração – obter as garantias locais de volta para os dados de volume - é uma operação de execução demorada e pode levar horas para todos os dados sejam feitas local novamente. O tempo necessário para concluir a mesma depende de vários fatores, como o tamanho do volume sendo restaurado e a largura de banda disponível. Se o volume original que está sendo restaurado tiver sido excluído, tempo adicional será levado para criar o espaço local no dispositivo como parte da operação de restauração.

**P.** Preciso restaurar meu volume localmente fixada existente para um instantâneo mais antigo (obtido quando o volume foi hierárquico). Será o volume restaurado como hierárquico nesse caso?

**R.** Não, o volume será restaurado como um volume localmente fixado. Embora as datas de instantâneo para a hora quando o volume foi hierárquico, ao restaurar volumes existentes, StorSimple sempre usa o tipo de volume no disco como ele se encontra no momento.

**P.** Posso estendido Meu volume localmente fixada recentemente, mas agora preciso restaurar os dados em uma hora quando o volume foi menor em tamanho. Irá restaurar redimensionar o volume atual e será necessário estender o tamanho do volume depois que a restauração é concluída?

**R.** Sim, a restauração irá redimensionar o volume e você precisará estender o tamanho do volume concluída a restauração.

**P.** Pode alterar o tipo de um volume durante a restauração?

**A.** Não, você não pode alterar o tipo de volume durante a restauração.

- Volumes que foram excluídos são restaurados como o tipo armazenado no instantâneo.

- Volumes existentes são restaurados com base em seu tipo atual, independentemente do tipo armazenado no instantâneo (consulte as duas perguntas anterior).
 
**P.** Preciso restaurar meu volume localmente fixada, mas eu escolheu um ponto incorreto em instantâneo de tempo. Para cancelar a operação de restauração atual?

**R.** Sim, você pode cancelar uma operação de restauração em andamento. O estado do volume será revertido para o estado no início da restauração. Entretanto, todas as gravações feitas ao volume enquanto a restauração estava em andamento serão perdidas.

**P.** Posso iniciar uma operação de restauração em uma das minhas volumes localmente fixados e agora, vejo um instantâneo no meu Catálogo de acúmulo que eu não relembrar criando. O que isso é usado para?

**R.** Este é o instantâneo temporário que será criado antes da operação de restauração e é usado para reversão caso a restauração seja cancelada ou falhe. Não exclua esse instantâneo; ele serão automaticamente excluído quando a restauração for concluída. Esse comportamento pode ocorrer se o seu trabalho de restauração apenas localmente tem fixos volumes ou uma mistura de volumes localmente fixados e hierárquicos. Se o trabalho de restauração inclui apenas volumes hierárquicos, em seguida, esse comportamento não ocorrerá.

**P.** Posso clonar um volume localmente fixado?

**R.** Sim, você pode. No entanto, o volume localmente fixado será possível clonar como um volume hierárquico por padrão. Mais informações sobre como [clonar um volume localmente fixado](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Perguntas sobre falhem em um volume localmente fixado

**P.** Preciso de falha em meu dispositivo para outro dispositivo físico. Meu volumes localmente fixados ser falha acima como localmente fixos ou hierárquico?

**R.** Dependendo da versão de software do dispositivo de destino, volumes localmente fixados resultado serão a falha sobre como:

- Localmente fixos se o dispositivo de destino está executando StorSimple 8000 série atualizar 2
- Hierárquico se o dispositivo de destino está executando StorSimple 8000 série atualizar 1. x
- Hierárquico se o dispositivo de destino for o aparelho de nuvem (atualização de versão 2 do software ou atualizar 1. x)

Obter mais informações sobre [failover e DR de localmente fixos volumes entre versões](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**P.** Volumes localmente fixados são restaurados instantaneamente durante a recuperação de dados (DR)?

**R.** Sim, volumes localmente fixados são restaurados instantaneamente durante o failover. Assim que as informações de metadados para o volume estão puxadas da nuvem como parte da operação de failover, o volume é colocado online no dispositivo de destino e pode ser acessado pelo host. Enquanto isso, os dados do volume continuará a baixar no plano de fundo, e você pode enfrentar desempenho reduzido nesses volumes para a duração da cobertura de falha.

**P.** Posso ver o trabalho de failover concluído, como pode acompanhar o progresso do volume localmente fixada que está sendo restaurado no dispositivo de destino?

**R.** Durante uma operação de failover, o trabalho de failover está marcado como concluída, uma vez, todos os volumes no conjunto de failover foram instantaneamente restaurados e colocados on-line no dispositivo de destino. Isso inclui qualquer localmente fixados volumes que podem ter sido falhou; No entanto, as garantias de locais dos dados só estará disponíveis quando todos os dados para o volume foi baixado. Você pode controlar esse andamento para cada volume localmente fixada que falhou com os trabalhos de restauração correspondentes que são criados como parte da cobertura de falha de monitoramento. Esses trabalhos de restauração individuais só serão criados para volumes localmente fixados.

**P.** Pode alterar o tipo de um volume durante failover?

**R.** Não, você não pode alterar o tipo de volume durante um failover. Se você estiver falhando sobre para outro dispositivo físico que está executando o StorSimple 8000 série atualização 2, os volumes serão falhar com base no tipo de volume armazenado no instantâneo. Quando não sobre qualquer outra versão do dispositivo, consulte a pergunta acima do tipo de volume após um failover.

**P.** Pode falhar ao longo de um contêiner de volume com volumes localmente fixados no dispositivo de nuvem?

**R.** Sim, você pode. Os volumes localmente fixados resultado serão a falha sobre como volumes hierárquicos. Obter mais informações sobre [failover e DR de localmente fixos volumes entre versões](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)


