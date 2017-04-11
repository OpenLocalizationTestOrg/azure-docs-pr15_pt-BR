<properties 
   pageTitle="StorSimple failover e recuperação de desastres | Microsoft Azure"
   description="Saiba como Falha ao longo do seu dispositivo StorSimple para si mesmo, outro dispositivo físico ou um dispositivo virtual."
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
   ms.date="09/16/2016"
   ms.author="alkohli" />

# <a name="failover-and-disaster-recovery-for-your-storsimple-device"></a>Failover e recuperação de desastres para seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial descreve as etapas necessárias para falhar ao longo de um dispositivo de StorSimple em caso de um desastre. Um failover permitirá que você migrar seus dados de um dispositivo de origem no data center para outro física ou até mesmo um dispositivo virtual localizado no mesmo ou em um local geográfico diferente. 

Recuperação de dados (DR) é coordenada por meio do recurso de falha de dispositivo e é iniciada a partir da página de **dispositivos** . Esta página organizará todos os dispositivos de StorSimple conectados ao seu serviço de Gerenciador de StorSimple. Para cada dispositivo, o nome amigável, o status, a capacidade de provisionado e máxima, o tipo e o modelo são exibidas.

![Página de dispositivos](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

As orientações neste tutorial se aplica a dispositivos físicos e virtuais de StorSimple em todas as versões de software.



## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperação de dados (DR) e failover de dispositivo

Em um cenário de recuperação de desastres, o dispositivo primário parou de funcionar. Nessa situação, você pode mover os dados de nuvem associados ao dispositivo falha para outro dispositivo usando o dispositivo primário como a *origem* e outro dispositivo que especifica como o *destino*. Você pode selecionar um ou mais contêineres de volume para migrar para o dispositivo de destino. Esse processo é conhecido como o *failover*. 

Durante o failover, os contêineres de volume do dispositivo de origem alterar a propriedade e são transferidos para o dispositivo de destino. Depois que os contêineres de volume alterar a propriedade, estes são excluídos do dispositivo de origem. Após a exclusão estiver concluída, o dispositivo de destino, em seguida, pode falhar novamente.

Normalmente seguindo um DR, o backup mais recente é usado para restaurar os dados para o dispositivo de destino. Entretanto, se houver várias diretivas de backup para o mesmo volume, a política de backup com o maior número de volumes obtém separada, e o backup mais recente dessa política é usado para restaurar os dados no dispositivo de destino.

Como exemplo, se houver duas políticas de backup (um padrão e uma personalizada) *defaultPol*, *customPol* com os seguintes detalhes:

- *defaultPol* : um volume, *volume 1*, executa diária começando em 10:30 PM.
- *customPol* : quatro volumes, *volume 1*, *volume 2*, *vol3*, *vol4*, executa diária começando em 10:00 PM.

Nesse caso, *customPol* será usado como ela tem mais volumes e podemos priorizar para consistência de falha. O backup mais recente dessa política é usado para restaurar os dados.


## <a name="considerations-for-device-failover"></a>Considerações para failover de dispositivo

Em caso de um desastre, você pode optar por falhar ao longo do seu dispositivo de StorSimple:

- Para um dispositivo físico 
- A mesmo
- Para um dispositivo virtual

Para qualquer falha de dispositivo, tenha em mente o seguinte:

- Os pré-requisitos para DR são que todos os volumes dentro os contêineres de volume estiverem offline e os contêineres de volume tem um associado instantâneo de nuvem. 
- Os dispositivos de destino disponível para DR são dispositivos que tem espaço suficiente para acomodar os contêineres de volume selecionado. 
- Os dispositivos que estão conectados ao seu serviço, mas não atende aos critérios de espaço suficiente não estará disponíveis como dispositivos de destino.
- Seguindo um DR, por um período limitado, o desempenho de acesso de dados pode ser afetado significativamente, pois o dispositivo será necessário acessar os dados da nuvem e armazená-lo localmente.

#### <a name="device-failover-across-software-versions"></a>Failover de dispositivos entre versões de software

Um serviço de Gerenciador de StorSimple em uma implantação pode ter vários dispositivos, físicos e virtuais, todas as versões de software trabalhar com diferentes. Dependendo da versão do software, os tipos de volume nos dispositivos também podem ser diferentes. Por exemplo, um dispositivo em execução atualização 2 ou superior seria tem localmente fixos e hierárquico volumes (com arquivamento sendo um subconjunto de hierárquico). Um dispositivo de pré-atualização 2 por outro lado pode ter hierárquico e volumes de arquivamento. 

Use a tabela a seguir para determinar se você pode falhar ao longo para outro dispositivo executando uma versão de software diferente e o comportamento de tipos de volume durante DR.

| Falha ao longo de                                      | Permitidos para dispositivo físico                                                                                                                                                      | Permitidos para dispositivo virtual                            |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
| Atualizar 2 para pré-atualizar 1 (lançamento, 0.1, 0.2, 0.3) | Não                                                                                                                                                                               | Não                                                    |
| Atualizar 2 para atualizar 1 (1, 1.1, 1.2)                 | Sim <br></br>Se usando localmente fixos ou hierárquico volumes ou uma combinação de dois, os volumes sempre são falha sobre como hierárquico.                  | Sim<br></br>Se usando localmente fixado volumes, estes são falha ultrapassa hierárquico. |
| Atualização 2 para atualização 2 (versão posterior)                               | Sim<br></br>Se usar volumes localmente fixados ou hierárquicos ou uma combinação de dois, os volumes são sempre falha sobre como o tipo de volume inicial; hierárquico como localmente fixados e hierárquicas fixos como localmente. | Sim<br></br>Se usando localmente fixado volumes, estes são falha ultrapassa hierárquico. |


#### <a name="partial-failover-across-software-versions"></a>Failover parcial entre versões de software

Siga este guia se você pretende realizar um failover parcial usando um dispositivo de origem StorSimple executando o pré-atualização 1 para um destino executando Update 1 ou posterior. 


| Failover parcial de                                      | Permitidos para dispositivo físico                                                                                                                                                      | Permitidos para dispositivo virtual                            |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
|Pré-atualizar 1 (lançamento, 0.1, 0.2, 0.3) a atualização 1 ou posterior  | Sim, veja abaixo a dica de práticas recomendada.                                                                                                                                                                               | Sim, veja abaixo a dica de práticas recomendada.                                                    |


>[AZURE.TIP] Houve uma nuvem metadados e dados de alteração de formato no Update 1 e versões posteriores. Portanto, não recomendamos um failover parcial de pré-atualização 1 para Update 1 ou versões posteriores. Se for necessário realizar um failover parcial, recomendamos que você aplica primeiro Update 1 ou posterior em ambos os dispositivos (origem e destino) e, em seguida, prossiga com o failover. 

## <a name="fail-over-to-another-physical-device"></a>Alternar para outro dispositivo físico

Execute as seguintes etapas para restaurar o seu dispositivo para um dispositivo físico de destino.

1. Verifique se que o contêiner de volume que você deseja alternar associado instantâneos de nuvem.

1. Na página **dispositivos** , clique na guia **Contêineres de Volume** .

1. Selecione um contêiner de volume que você gostaria de alternar para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes dentro deste contêiner. Selecione um volume e clique em **Levar Offline** para colocar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.

1. Repita a etapa anterior para todos os contêineres de volume que você gostaria de alternar para outro dispositivo.

1. Na página **dispositivos** , clique em **Failover**.

1. No assistente que é aberto, em **Escolher contêiner de volume falha sobre**:

    1. Na lista de contêineres de volume, selecione os contêineres de volume que você gostaria de alternar.
    **Somente os contêineres de volume com instantâneos de nuvem associado e volumes off-line são exibidos.**

    1. Em **Escolher um dispositivo de destino** para os volumes nos contêineres de selecionada, selecione um dispositivo de destino na lista suspensa de dispositivos disponíveis. Apenas os dispositivos que têm a capacidade disponível são exibidos na lista suspensa.

    1. Por fim, revise todas as configurações de failover em **Confirmar failover**. Clique no ícone de seleção ![ícone de seleção](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Um trabalho de failover é criado que é possível monitorar por meio da página de **trabalhos** . Se o contêiner de volume que você falhou tiver volumes locais, você verá trabalhos de restauração individuais para cada volume local (não para volumes hierárquicos) no contêiner. Estas restaurar trabalhos podem levar algum tempo para ser concluída. É provável que o trabalho de failover pode ser concluído anteriormente. Observe que esses volumes terá garantias locais somente depois que os trabalhos de restauração foram concluídos. Após o failover, vá para a página de **dispositivos** .                                            

    1. Selecione o dispositivo que foi usado como o dispositivo de destino para o processo de failover.

    1. Vá para a página de **Contêineres de Volume** . Todos os contêineres de volume, juntamente com os volumes do dispositivo antigo, devem estar listados.

## <a name="failover-using-a-single-device"></a>Failover usando um único dispositivo

Execute as seguintes etapas se você só tiver um único dispositivo e precisa para realizar um failover.

1. Tirar instantâneos de nuvem de todos os volumes no seu dispositivo.

1. Reinicie o dispositivo padrões de fábrica. Siga as instruções detalhadas de [como redefinir um dispositivo StorSimple configurações padrão de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

1. Configurar seu dispositivo e registre-o novamente com o serviço do Gerenciador de StorSimple.

1. Na página **dispositivos** , o dispositivo antigo deve mostrar como **Offline**. O dispositivo recém registrado deve mostrar como **Online**.

1. Para o novo dispositivo, conclua a configuração mínima do dispositivo primeiro. 
                                                
    >[AZURE.IMPORTANT] **Se a configuração mínima não for concluída pela primeira vez, seu DR falhará como resultado de um bug na implementação atual. Esse comportamento será corrigido em uma versão posterior.**

1. Selecione o dispositivo antigo (status off-line) e clique em **Failover**. No assistente apresentados, falhar sobre este dispositivo e especifique o dispositivo de destino como o dispositivo recém registrado. Para obter instruções detalhadas, consulte [Alternar para outro dispositivo físico](#fail-over-to-another-physical-device).

1. Será criado um trabalho de restauração do dispositivo que você pode monitorar na página de **trabalhos** .

1. Depois que o trabalho foi concluída com êxito, acesse o novo dispositivo e navegue até a página de **Contêineres de Volume** . Todos os contêineres de volume do dispositivo antigo agora devem ser migrados para o novo dispositivo.

## <a name="fail-over-to-a-storsimple-virtual-device"></a>Alternar para um dispositivo virtual StorSimple

Você deve ter uma StorSimple dispositivo virtual criado e configurado antes de executar este procedimento. Se executando atualização 2, considere usar um dispositivo virtual 8020 para DR que tem 64 TB e usa o armazenamento de Premium. 
 
Execute as seguintes etapas para restaurar o dispositivo para um dispositivo virtual de StorSimple de destino.

1. Verifique se que o contêiner de volume que você deseja alternar associado instantâneos de nuvem.

1. Na página **dispositivos** , clique na guia **Contêineres de Volume** .

1. Selecione um contêiner de volume que você gostaria de alternar para outro dispositivo. Clique no contêiner de volume para exibir a lista de volumes dentro deste contêiner. Selecione um volume e clique em **Levar Offline** para colocar o volume offline. Repita esse processo para todos os volumes no contêiner de volume.

1. Repita a etapa anterior para todos os contêineres de volume que você gostaria de alternar para outro dispositivo.

1. Na página **dispositivos** , clique em **Failover**.

1. No assistente que é aberto, em **Escolher contêiner de volume para failover**, preencha o seguinte:
                                                    
    a. Na lista de contêineres de volume, selecione os contêineres de volume que você gostaria de alternar.

    **Somente os contêineres de volume com instantâneos de nuvem associado e volumes off-line são exibidos.**

    b. Em **Escolher um dispositivo de destino para os volumes nos contêineres de selecionada**, selecione o dispositivo virtual StorSimple na lista suspensa de dispositivos disponíveis. **Somente os dispositivos que têm capacidade suficiente são exibidos na lista suspensa.**  
    

1. Por fim, revise todas as configurações de failover em **Confirmar failover**. Clique no ícone de seleção ![ícone de seleção](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Após o failover, vá para a página de **dispositivos** .
                                                    
    a. Selecione o dispositivo virtual StorSimple que foi usado como o dispositivo de destino para o processo de failover.
    
    b. Vá para a página de **Contêineres de Volume** . Todos os contêineres de volume, juntamente com os volumes do dispositivo antigo agora devem estar listados.

![Vídeo disponível](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que demonstra como você pode restaurar uma falha ao longo do dispositivo físico para um dispositivo virtual na nuvem, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).


## <a name="failback"></a>Failback

Para atualização 3 e versões posteriores, StorSimple também suporta failback. Após o failover estiver concluído, ocorrem as seguintes ações:

- Os contêineres de volume que são falhou são removidos do dispositivo de origem.

- Um trabalho de plano de fundo por contêiner de volume (falhou) é iniciado no dispositivo de origem. Se você tentar failback enquanto o trabalho está em andamento, você receberá uma notificação a respeito. Você precisará esperar até que o trabalho for concluído para começar o failback. 

    O tempo para concluir a exclusão de contêineres de volume depende de vários fatores como quantidade de dados, idade dos dados, número de backups e a largura de banda de rede disponível para a operação. Se você estiver planejando teste failovers/failbacks, recomendamos que você teste contêineres de volume com menos dados (GB). Na maioria dos casos, você pode iniciar o failback 24 horas após o failover é concluído. 




## <a name="frequently-asked-questions"></a>Perguntas frequentes

P. **O que acontece se o DR falha ou tenha êxito parcial?**

R. Se o DR falhar, recomendamos que você tente novamente. Na segunda vez, DR sabe o que foi feito e quando o processo interrompida na primeira vez. Inicia o processo de DR desse ponto em diante. 

P. **Posso excluir um dispositivo enquanto o dispositivo failover está em andamento?**

R. Você não pode excluir um dispositivo enquanto um DR está em andamento. Você só pode excluir seu dispositivo após a conclusão da DR.

P.  **Quando a coleta de lixo inicia no dispositivo de origem para que os dados locais no dispositivo de origem são excluídos?**

R. Coleta de lixo será habilitada no dispositivo de origem somente depois que o dispositivo estiver completamente limpo. A limpeza inclui a limpeza de objetos com Falha ao longo do dispositivo de origem como volumes, objetos de backup (não dados), contêineres de volume e políticas.

P. **O que acontece se o trabalho de exclusão associado com os contêineres de volume do dispositivo de origem falhar?**

R.  Se o trabalho de exclusão falhar, você precisará acionar manualmente a exclusão dos contêineres volume. Na página **dispositivos** , selecione seu dispositivo de origem e clique em **contêineres de Volume**. Selecione os contêineres de volume que falhou ao longo e na parte inferior da página, clique em **Excluir**. Depois de excluir todas as falha sobre contêineres de volume do dispositivo de origem, você pode iniciar o failback.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Business continuidade recuperação de dados (BCDR)

Um cenário de recuperação (BCDR) de desastres de continuidade de negócios ocorre quando inteiro data center Azure parar de funcionar. Isso pode afetar o serviço do Gerenciador de StorSimple e os dispositivos de StorSimple associados.

Se houver dispositivos StorSimple que foram registrados antes que um desastre ocorreu, em seguida, esses dispositivos StorSimple talvez precise sofrer uma redefinição de fábrica. Após o desastre, o dispositivo StorSimple será mostrado como offline. O dispositivo de StorSimple deve ser excluído do portal e uma redefinição de fábrica deve ser feita, seguido por um novo registro.


## <a name="next-steps"></a>Próximas etapas

- Após ter executado um failover, você talvez precise [desativar ou excluir seu dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

- Para obter informações sobre como usar o serviço do Gerenciador de StorSimple, vá para [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
 
