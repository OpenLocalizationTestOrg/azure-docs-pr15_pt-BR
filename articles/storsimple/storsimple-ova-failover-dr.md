<properties
   pageTitle="Failover de dispositivo e recuperação de desastres para sua matriz Virtual StorSimple"
   description="Saiba mais sobre como failover sua matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Failover de dispositivo e recuperação de desastres para sua matriz Virtual StorSimple


## <a name="overview"></a>Visão geral

Este artigo descreve a recuperação de dados para sua matriz Virtual do Microsoft Azure StorSimple (também conhecido como o StorSimple local virtual dispositivo) incluindo as etapas detalhadas necessárias para alternar para outro dispositivo virtual em caso de um desastre. Um failover permite migrar seus dados de um dispositivo de *origem* no data center para outro dispositivo de *destino* localizado no mesmo ou em um local geográfico diferente. O failover de dispositivo é para todo o dispositivo. Durante o failover, os dados de nuvem para o dispositivo de origem altera a propriedade do dispositivo de destino.

Dispositivo failover é coordenada por meio do recurso de recuperação (DR) desastres e é iniciada a partir da página de **dispositivos** . Esta página organizará todos os dispositivos de StorSimple conectados ao seu serviço de Gerenciador de StorSimple. Para cada dispositivo, o nome amigável, o status, a capacidade de provisionado e máxima, o tipo e o modelo são exibidas.

![](./media/storsimple-ova-failover-dr/image15.png)

Este artigo se aplica a matrizes Virtual StorSimple somente. Para falhar ao longo de um dispositivo de 8000 série, vá para [Failover e recuperação de dados do seu dispositivo de StorSimple](storsimple-device-failover-disaster-recovery.md).


## <a name="what-is-disaster-recovery"></a>O que é recuperação?

Em um cenário de recuperação de desastres, o dispositivo primário parou de funcionar. Nessa situação, você pode mover os dados de nuvem associados ao dispositivo falha para outro dispositivo usando o dispositivo primário como a *origem* e outro dispositivo que especifica como o *destino*. Esse processo é conhecido como o *failover*. Durante o failover, todos os volumes ou os compartilhamentos do dispositivo de origem alterar a propriedade e são transferidos para o dispositivo de destino. Sem filtragem de dados é permitida.

DR é estabelecido como uma restauração de dispositivo completo usando o calor baseada em mapa hierárquico e controle. Um mapa de calor é definido atribuindo um valor de calor aos dados com base em ler e gravar padrões. Este calor mapear depois níveis os blocos de dados de calor mais baixos para a nuvem primeiro enquanto mantém os blocos de dados de calor alta (mais usada) no nível local. Durante um DR, o mapa de calor é usado para restaurar e realimentar os dados da nuvem. O dispositivo busca todos os volumes/compartilhamentos no último backup recente (conforme determinado internamente) e executa uma restauração do backup. Todo o processo de DR é coordenado pelo dispositivo.


## <a name="prerequisites-for-device-failover"></a>Pré-requisitos para failover de dispositivo


### <a name="prerequisites"></a>Pré-requisitos

Para qualquer falha de dispositivo, os seguintes pré-requisitos devem ser atendidos:

- O dispositivo de origem precisa estar em um estado **Deactivated** .

- O dispositivo de destino precisa aparecer como **ativo** no portal de clássico do Azure. Você precisará provisionar um dispositivo virtual de destino da capacidade igual ou superior. Você deve usar da web local UI configurar e registrar com êxito o dispositivo virtual.

    > [AZURE.IMPORTANT] Não tente configurar o dispositivo virtual registrado através do serviço clicando em **configuração do dispositivo concluída**. Nenhuma configuração de dispositivo deve ser executada por meio do serviço.

- O dispositivo de origem e destino precisa ser do mesmo tipo. Você só pode falhar ao longo de um dispositivo virtual configurado como um servidor de arquivos para outro servidor de arquivo. O mesmo é verdadeiro para um servidor iSCSI.

- Para um servidor de arquivos DR, recomendamos que você ingressar o dispositivo de destino para o mesmo domínio da fonte de modo que as permissões de compartilhamento sejam resolvidas automaticamente. Somente o failover para um dispositivo de destino no mesmo domínio é compatível com esta versão.

### <a name="other-considerations"></a>Outras considerações

- Recomendamos que você levar todos os volumes ou compartilhamentos no dispositivo de origem offline.

- Se for um failover planejado, recomendamos que você faça um backup do dispositivo e prossiga com o failover para minimizar a perda de dados. Se for um failover não planejado, o backup mais recente será usado para restaurar o dispositivo.

- Os dispositivos de destino disponível para DR são que têm a mesma ou maior capacidade em comparação com o dispositivo de origem. Os dispositivos que estão conectados ao seu serviço, mas não atende aos critérios de espaço suficiente não estará disponíveis como dispositivos de destino.

### <a name="dr-prechecks"></a>DR prechecks

Antes de inicia o DR, prechecks são executadas no dispositivo. Essas verificações ajudam a garantir que nenhum erro ocorrerá quando DR começa. Os prechecks incluem:

- Validando a conta de armazenamento

- Verificando a conectividade de nuvem para o Azure

- Verificando o espaço disponível no dispositivo de destino

- Verificar se um dispositivo de origem do servidor iSCSI tem nomes ACR válidos, IQN (sem ultrapassar 220 caracteres) e senha de CHAP (12 e 16 caracteres de comprimento) associadas os volumes

Se algum das prechecks acima falhar, você não pode continuar com o DR. Você precisa resolver esses problemas e tente novamente DR.

Após o DR foi concluído com êxito, a propriedade dos dados nuvem no dispositivo de origem é transferida para o dispositivo de destino. O dispositivo de origem, em seguida, não está mais disponível no portal. Acesso a todos os volumes/compartilhamentos no dispositivo de origem está bloqueado e o dispositivo de destino se torna ativo.

> [AZURE.IMPORTANT]
> 
> Embora o dispositivo não está mais disponível, a máquina virtual que você provisionado no sistema host ainda está consumindo recursos. Após o DR foi concluído com êxito, você poderá excluir esta máquina virtual do seu sistema de host.

## <a name="fail-over-to-a-virtual-array"></a>Alternar para uma matriz virtual

Recomendamos que você tenha outra matriz de Virtual StorSimple provisionado, configuradas por meio da interface do usuário da web local e registrados com o serviço de Gerenciador de StorSimple antes de executar este procedimento.


> [AZURE.IMPORTANT]
> 
> - Você não é permitidas Falha ao longo de um dispositivo de série 8000 StorSimple para um dispositivo virtual 1200.
> - Você pode falhar ao longo de um dispositivo virtual Federal Information Processing Standard (FIPS) habilitado implantado no portal do governo para um dispositivo virtual no Azure portal clássico. O inverso também é verdadeiro.

Execute as seguintes etapas para restaurar o dispositivo para um dispositivo virtual de StorSimple de destino.

1. Levar volumes/compartilhamentos offline do host. Consulte as instruções específicas do sistema operacional do host para colocar os volumes/compartilhamentos offline. Se não estiver offline, você precisará levar todos os volumes/compartilhamentos offline no dispositivo acessando **dispositivos > compartilhamentos** (ou **dispositivo > Volumes**). Selecione um compartilhamento/volume e clique em **colocar offline** na parte inferior da página. Quando solicitado para confirmação, clique em **Sim**. Repita esse processo para todos os compartilhamentos/volumes no dispositivo.

2. Na página **dispositivos** , selecione o dispositivo de origem para failover e clique em **Desativar**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)

3. Você será solicitado para confirmação. Desativação de dispositivo é um processo permanente que não pode ser desfeito. Você também será lembrado para fazer seus compartilhamentos/volumes offline do host.

    ![](./media/storsimple-ova-failover-dr/image18.png)

3. Após confirmação, a desativação será iniciado. Após a desativação foi concluída com êxito, você será notificado.

    ![](./media/storsimple-ova-failover-dr/image19.png)

4. Na página **dispositivos** , o estado do dispositivo agora será alterado para **Deactivated**.

    ![](./media/storsimple-ova-failover-dr/image20.png)

5. Selecione o dispositivo desativado e na parte inferior da página, clique em **Failover**.

6. No Assistente de failover confirmar que será aberta, faça o seguinte:

    1. Na lista suspensa de dispositivos disponíveis, escolha um **dispositivo de destino.** Somente os dispositivos que têm capacidade suficiente são exibidos na lista suspensa.

    2. Examine os detalhes associados com o dispositivo de origem como nome do dispositivo, capacidade total e os nomes dos compartilhamentos que vai ser falhou.

        ![](./media/storsimple-ova-failover-dr/image21.png)

7. Verifique **eu concorda que failover é uma operação permanente e depois o failover foi concluído com êxito, o dispositivo de origem será excluído**.

8. Clique no ícone de seleção ![](./media/storsimple-ova-failover-dr/image1.png).


9. Um trabalho de failover será iniciado e você será notificado. Clique em **Exibir trabalho** para monitorar o failover.

    ![](./media/storsimple-ova-failover-dr/image22.png)

10. Na página de **trabalhos** , você verá um trabalho de failover criado para o dispositivo de origem. Este trabalho executa os prechecks DR.

    ![](./media/storsimple-ova-failover-dr/image23.png)

    Após as prechecks DR forem bem-sucedidas, o trabalho de failover irá gerar trabalhos de restauração para cada compartilhamento/volume que existe em seu dispositivo de origem.

    ![](./media/storsimple-ova-failover-dr/image24.png)

11. Após o failover, vá para a página de **dispositivos** .

    a. Selecione o dispositivo virtual StorSimple que foi usado como o dispositivo de destino para o processo de failover.

    b. Ir para a página de **compartilhamentos** (ou **Volumes** se servidor iSCSI). Todos os compartilhamentos (volumes) do dispositivo antigo agora devem estar listados.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png)**Vídeo disponível**

Este vídeo demonstra como você pode falhar ao longo de um dispositivo virtual do StorSimple local para outro dispositivo virtual.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## <a name="business-continuity-disaster-recovery-bcdr"></a>Business continuidade recuperação de dados (BCDR)

Um cenário de recuperação (BCDR) de desastres de continuidade de negócios ocorre quando inteiro data center Azure parar de funcionar. Isso pode afetar o serviço do Gerenciador de StorSimple e os dispositivos de StorSimple associados.

Se houver dispositivos StorSimple que foram registrados antes que um desastre ocorreu, esses dispositivos StorSimple talvez seja necessário a ser excluída. Após o desastre, você pode recriar e configurar esses dispositivos.

## <a name="errors-during-dr"></a>Erros durante a DR

**Falha de conectividade de nuvem durante DR**

Se a conectividade de nuvem é interrompida depois do DR e antes que a restauração de dispositivo for concluída, o DR falhará e você será notificado. O dispositivo de destino que foi usado para DR depois está marcado como *inutilizável.* O mesmo dispositivo de destino não pode ser usado para DRs futuras.

**Não há dispositivos de destino compatível**

Se os dispositivos de destino disponível não tem espaço suficiente, você verá um erro para o efeito que não existem dispositivos destino compatível.

**Falhas de precheck**

Se um das prechecks não estiver satisfeito, você verá precheck falhas.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar sua matriz Virtual StorSimple usando o interface do usuário de web local](storsimple-ova-web-ui-admin.md).
