<properties
   pageTitle="Clonar o volume de StorSimple | Microsoft Azure"
   description="Descreve os tipos diferentes de clonar e quando usá-los e explica como você pode usar um conjunto de backup para clonar um volume individual."
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
   ms.date="07/27/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>Usar o serviço do Gerenciador de StorSimple para clonar um volume (atualização 2)

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Visão geral

A página de **Catálogo de Backup** de serviço do Gerenciador de StorSimple exibe todos os conjuntos de backup que são criados quando são realizados backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou um volume, selecione ou excluir backups ou usar um backup para restaurar ou clonar um volume.

![Página de catálogo de backup](./media/storsimple-clone-volume-u2/backupCatalog.png)  

Este tutorial descreve como você pode usar um conjunto de backup para clonar um volume individual. Ela também explica a diferença entre clones *temporárias* e *permanentes* .

>[AZURE.NOTE] 
>
>Um volume localmente fixado será possível clonar como um volume hierárquico. Se você precisar volume clonado para ser fixado localmente, você pode converter o clonar a um volume localmente fixado após a operação de clonagem foi concluída com êxito. Para obter informações sobre como converter um volume hierárquico para um volume localmente fixado, vá para [alterar o tipo de volume](storsimple-manage-volumes-u2.md#change-the-volume-type).
>
>Se você tentar converter um volume clonado de hierárquico para localmente fixos imediatamente após clonagem (quando ele ainda estiver um clonar temporária), a conversão falhará com a seguinte mensagem de erro:
>
>`Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
>
>Este erro é recebido somente se você estiver clonagem para um dispositivo diferente. Você pode converter com êxito o volume localmente fixos se primeiro converter a clonar temporária para um clonar permanente. Para converter a clonar temporária para um clonar permanente, tirar um instantâneo de nuvem dele.

## <a name="create-a-clone-of-a-volume"></a>Criar uma cópia de um volume

Você pode criar um clonar no mesmo dispositivo, outro dispositivo ou mesmo uma máquina virtual usando um local ou um instantâneo de nuvem.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Na página Gerenciador de StorSimple serviço, clique na guia **Catálogo de Backup** e selecione um conjunto de backup.

2. Expanda o backup definido para exibir os volumes associados. Clique em e selecione um volume do conjunto de backup.

     ![Clonar um volume](./media/storsimple-clone-volume-u2/CloneVol.png) 

3. Clique em **clonar** para começar a clonagem o volume selecionado.

4. No Assistente de Volume de clonar, em **local e especificar o nome**:

  1. Identifica um dispositivo de destino. Este é o local onde o clonar será criado. Você pode escolher o mesmo dispositivo ou especificar outro dispositivo. Se você escolher um volume associado a outros provedores de serviços de nuvem (não Azure), a lista suspensa do dispositivo de destino mostrará somente dispositivos físicos. Você não pode clonar um volume associado a outros provedores de serviços de nuvem em um dispositivo virtual.

        >[AZURE.NOTE] Certifique-se de que a capacidade necessária para o clonar é menor do que a capacidade disponível no dispositivo de destino.

  2. Especifique um nome de volume exclusivo para sua clonar. O nome deve conter entre 3 e 127 caracteres. 
    
        >[AZURE.NOTE] O campo **Clonar Volume como** será **hierárquico** , mesmo se você estiver clonagem um volume localmente fixado. Você não pode alterar essa configuração; No entanto, se você precisar volume clonado para ser localmente fixado também, você pode converter o clonar a um volume localmente fixado depois de criar a clonar com êxito. Para obter informações sobre como converter um volume hierárquico para um volume localmente fixado, vá para [alterar o tipo de volume](storsimple-manage-volumes-u2.md#change-the-volume-type).

        ![Assistente de clonar 1](./media/storsimple-clone-volume-u2/clone1.png) 

  3. Clique no ícone de seta ![ícone de seta](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) para prosseguir para a próxima página.

5. Em **hosts de especificar que podem usar este volume**:

  1. Especifique um registro de controle de acesso (ACR) para a clonar. Você pode adicionar um novo ACR ou escolha na lista existente.

        ![Assistente de clonar 2](./media/storsimple-clone-volume-u2/clone2.png) 

  2. Clique no ícone de seleção ![ícone de verificação](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)para concluir a operação.

6. Um trabalho de clonar será iniciado e você será notificado quando a clonar foi criado com êxito. Clique em **Exibir trabalho** para monitorar o trabalho clonar na página de **trabalhos** . Quando o trabalho de clonar for concluído, você verá a seguinte mensagem:

    ![Mensagem de clonar](./media/storsimple-clone-volume-u2/CloneMsg.png) 

7. Depois que o trabalho de clonar seja concluído:

  1. Vá para a página de **dispositivos** e selecione a guia **Contêineres de Volume** . 
  2. Selecione o contêiner de volume que está associado com o volume de origem que você duplicado. Na lista de volumes, você deve ver o clonar que acabou de ser criada.

>[AZURE.NOTE] Monitoramento e padrão backup são desabilitados automaticamente em um volume clonado.

Um clonar criado dessa forma é uma clonar temporária. Para obter mais informações sobre tipos de clonar, consulte [temporárias versus clones permanentes](#transient-vs.-permanent-clones).

Este clonar agora é um volume regular e qualquer operação que é possível em um volume estará disponível para o clonar. Você precisará configurar esse volume para qualquer backups.

## <a name="transient-vs-permanent-clones"></a>Transitório versus clones permanentes

Clones temporárias são criadas somente quando você estiver clonagem para um dispositivo diferente. Você pode clonar um volume específico de um conjunto de backup para um dispositivo diferente gerenciado pelo Gerenciador de StorSimple. A clonar temporária terá referências aos dados em volume original e usará esses dados para ler e gravar localmente no dispositivo de destino. 

Depois de você tirar um instantâneo de nuvem de um clonar temporária, o clonar resultante será um clonar *permanente* . Durante esse processo, é criada uma cópia dos dados na nuvem e o tempo para copiar estes dados é determinado pelo tamanho dos dados e as latências Azure (essa é uma cópia do Azure para Azure). Esse processo pode levar dias para semanas. A clonar temporária se torna um clonar permanente dessa maneira e não tem quaisquer referências para os dados de volume original que ela foi duplicada de. 

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para clones temporárias e permanentes

As seções a seguir descrevem situações de exemplo na qual clones temporárias e permanentes podem ser usados.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação de nível de item com um clonar temporária

Você precisa recuperar um arquivo de apresentação do Microsoft PowerPoint um anos. O administrador de TI identifica o backup específico do quadro tempo e filtra o volume. Em seguida, o administrador clona o volume, localiza o arquivo que você está procurando e fornece a você. Neste cenário, um clonar temporária é usado. 
 
![Vídeo disponível](./media/storsimple-clone-volume-u2/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que demonstra como você pode usar o clonar e restaurar recursos no StorSimple para recuperar arquivos excluídos, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Teste no ambiente de produção com um clonar permanente

Você precisa verificar um bug teste no ambiente de produção. Crie um clonar do volume no ambiente de produção e, em seguida, tirar um instantâneo de nuvem deste clonar para criar um volume clonado independente. Neste cenário, um clonar permanente é usado.  

## <a name="next-steps"></a>Próximas etapas
- Saiba como [restaurar um volume StorSimple um conjunto de backup](storsimple-restore-from-backup-set-u2.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

 
