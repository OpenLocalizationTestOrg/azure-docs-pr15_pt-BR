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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Usar o serviço do Gerenciador de StorSimple para clonar um volume

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Visão geral

A página de **Catálogo de Backup** de serviço do Gerenciador de StorSimple exibe todos os conjuntos de backup que são criados quando são realizados backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou um volume, selecione ou excluir backups ou usar um backup para restaurar ou clonar um volume.

![Página de catálogo de backup](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Este tutorial descreve como você pode usar um conjunto de backup para clonar um volume individual. Ela também explica a diferença entre clones *temporárias* e *permanentes* . 

## <a name="create-a-clone-of-a-volume"></a>Criar uma cópia de um volume

Você pode criar um clonar no mesmo dispositivo, outro dispositivo ou mesmo uma máquina virtual usando um local ou um instantâneo de nuvem.

#### <a name="to-clone-a-volume"></a>Para clonar um volume

1. Na página Gerenciador de StorSimple serviço, clique na guia **Catálogo de Backup** e selecione um conjunto de backup.

2. Expanda o backup definido para exibir os volumes associados. Clique em e selecione um volume do conjunto de backup.

     ![Clonar um volume](./media/storsimple-clone-volume/HCS_Clone.png) 

3. Clique em **clonar** para começar a clonagem o volume selecionado.

4. No Assistente de Volume de clonar, em **local e especificar o nome**:

  1. Identifica um dispositivo de destino. Este é o local onde o clonar será criado. Você pode escolher o mesmo dispositivo ou especificar outro dispositivo. Se você escolher um volume associado a outros provedores de serviços de nuvem (não Azure), a lista suspensa do dispositivo de destino mostrará somente dispositivos físicos. Você não pode clonar um volume associado a outros provedores de serviços de nuvem em um dispositivo virtual.

        >  [AZURE.NOTE] Certifique-se de que a capacidade necessária para o clonar é menor do que a capacidade disponível no dispositivo de destino.
  2. Especifique um nome de volume exclusivo para sua clonar. O nome deve conter entre 3 e 127 caracteres.
  3. Clique no ícone de seta ![ícone de seta](./media/storsimple-clone-volume/HCS_ArrowIcon.png) para prosseguir para a próxima página.

5. Em **hosts de especificar que podem usar este volume**:

  1. Especifique um registro de controle de acesso (ACR) para a clonar. Você pode adicionar um novo ACR ou escolha na lista existente.
  2. Clique no ícone de seleção ![ícone de verificação](./media/storsimple-clone-volume/HCS_CheckIcon.png)para concluir a operação.

6. Um trabalho de clonar será iniciado e você será notificado quando a clonar é criado com êxito. Clique em **Exibir trabalho** para monitorar o trabalho clonar na página de **trabalhos** .

7. Depois que o trabalho de clonar seja concluído:

  1. Vá para a página de **dispositivos** e selecione a guia **Contêineres de Volume** . 
  2. Selecione o contêiner de volume que está associado com o volume de origem que você duplicado. Na lista de volumes, você deve ver o clonar que acabou de ser criada.

>[AZURE.NOTE] Monitoramento e padrão backup são desabilitados automaticamente em um volume clonado.

Um clonar criado dessa maneira é um clonar temporária. Para obter mais informações sobre tipos de clonar, consulte [temporárias versus clones permanentes](#transient-vs.-permanent-clones).

Este clonar agora é um volume regular e qualquer operação que é possível em um volume estará disponível para o clonar. Você precisará configurar esse volume para qualquer backups.

## <a name="transient-vs-permanent-clones"></a>Transitório versus clones permanentes

Clones temporárias e permanentes são criadas somente quando você estiver clonagem para um dispositivo diferente. Você pode clonar um volume específico de um conjunto de backup para um dispositivo diferente. Um clonar criado dessa maneira é um clonar *temporárias* . A clonar temporária terá referências para o volume original e use esse volume para ler durante a gravação localmente. 

Depois de você tirar um instantâneo de nuvem de um clonar temporária, o clonar resultante será um clonar *permanente* . A clonar permanente é independente e não tem quaisquer referências para o volume original que ela foi duplicada de.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Cenários para clones temporárias e permanentes

As seções a seguir descrevem situações de exemplo na qual clones temporárias e permanentes podem ser usados.

### <a name="item-level-recovery-with-a-transient-clone"></a>Recuperação de nível de item com um clonar temporária

Você precisa recuperar um arquivo de apresentação do Microsoft PowerPoint um anos. O administrador de TI identifica o backup específico do quadro tempo e filtra o volume. Em seguida, o administrador clona o volume, localiza o arquivo que você está procurando e fornece a você. Neste cenário, um clonar temporária é usado. 
 
![Vídeo disponível](./media/storsimple-clone-volume/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que demonstra como você pode usar o clonar e restaurar recursos no StorSimple para recuperar arquivos excluídos, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Teste no ambiente de produção com um clonar permanente

Você precisa verificar um bug teste no ambiente de produção. Você pode criar um clonar do volume no ambiente de produção fazendo um instantâneo de nuvem deste clonar. Volume clonado agora é independente. Neste cenário, um clonar permanente é usado.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [restaurar um volume StorSimple um conjunto de backup](storsimple-restore-from-backup-set.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

 
