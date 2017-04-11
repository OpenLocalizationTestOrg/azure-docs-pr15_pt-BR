<properties 
   pageTitle="Desativar e excluir um dispositivo de StorSimple | Microsoft Azure"
   description="Descreve como remover dispositivo StorSimple de serviço por primeiro desativá-la e, em seguida, excluí-lo."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="anoobbacker" />

# <a name="deactivate-and-delete-a-storsimple-device"></a>Desativar e excluir um dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Você talvez queira colocar um dispositivo de StorSimple Cancelar serviço (por exemplo, se você estiver substituindo ou atualizar o seu dispositivo ou se você já não estiver usando StorSimple). Se esse for o caso, você precisará desativar o dispositivo antes de excluí-lo. Desativando servidores a conexão entre o dispositivo e o serviço de Gerenciador de StorSimple correspondente. Este tutorial explica como remover um dispositivo de StorSimple de serviço por desativá-la primeiro e, em seguida, excluí-lo. 

Quando você desativa um dispositivo, quaisquer dados que foram armazenados localmente no dispositivo será mais acessíveis. Apenas os dados associados com o dispositivo que foi armazenado na nuvem podem ser recuperados.  

>[AZURE.WARNING] Desativação é uma operação PERMANENTE e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço Gerenciador de StorSimple, a menos que ele é primeiro redefinido pela fábrica. 
>
>Fábrica redefine processo exclui todos os dados que foi armazenados localmente em seu dispositivo. Portanto, é essencial que você tirar um instantâneo de nuvem de todos os dados antes de desativar um dispositivo. Isso permitirá que você a recuperar todos os dados em um estágio posterior.

Este tutorial explica como:

- Desativar um dispositivo e excluir os dados
- Desativar um dispositivo e manter os dados

Ela também explica como desativação e exclusão funciona em um dispositivo virtual StorSimple.

>[AZURE.NOTE] Antes de desativar um dispositivo de StorSimple físico ou virtual, certifique-se parar ou excluir clientes e hosts que dependem desse dispositivo.

## <a name="deactivate-and-delete-data"></a>Desativar e excluir dados

Se você estiver interessado em excluindo o dispositivo completamente e não deseja manter os dados no dispositivo, conclua as etapas a seguir.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Para desativar o dispositivo e excluir os dados  

1. Antes de desativar um dispositivo, você deve excluir todo o volume contêineres (e os volumes) associado ao dispositivo. Você pode excluir os contêineres de volume somente após ter excluído os backups associados.

2. Desative o dispositivo da seguinte maneira:

    1. Na página Gerenciador de StorSimple serviço **dispositivos** , selecione o dispositivo que você deseja desativar e, na parte inferior da página, clique em **Desativar**.

    2. Uma mensagem de confirmação será exibida. Clique em **Sim** para continuar. O processo de desativar será iniciado e demorar alguns minutos para ser concluída.

3. Após a desativação, você pode excluir o dispositivo completamente. Excluir um dispositivo removerá a lista de dispositivos conectados ao serviço. O serviço pode gerenciar não está mais no dispositivo excluído. Use as etapas a seguir para excluir o dispositivo:

    1. Na página Gerenciador de StorSimple serviço **dispositivos** , selecione um dispositivo desativado que você deseja excluir.

    2. Na parte inferior da página, clique em **Excluir**.

    3. Você será solicitado para confirmação. Clique em **Sim** para continuar.

    Pode levar alguns minutos para o dispositivo a ser excluída.

## <a name="deactivate-and-retain-data"></a>Desativar e reter dados

Se você está interessado em excluindo o dispositivo, mas deseja manter os dados, conclua as etapas a seguir.

####<a name="to-deactivate-a-device-and-retain-the-data"></a>Para desativar um dispositivo e manter os dados 

1. Desative o dispositivo. Todos os contêineres de volume e os instantâneos do dispositivo permanecerão.

    1. Na página Gerenciador de StorSimple serviço **dispositivos** , selecione o dispositivo que você deseja desativar e, na parte inferior da página, clique em **Desativar**.

    2. Uma mensagem de confirmação será exibida. Clique em **Sim** para continuar. O processo de desativar será iniciado e demorar alguns minutos para ser concluída.

2. Você agora pode falhar sobre os contêineres de volume e os instantâneos associados. Para obter procedimentos, vá para [Failover e recuperação de desastres para seu dispositivo de StorSimple](storsimple-device-failover-disaster-recovery.md).

3. Após a desativação e failover, você pode excluir o dispositivo completamente. Excluir um dispositivo removerá a lista de dispositivos conectados ao serviço. O serviço pode gerenciar não está mais no dispositivo excluído. Conclua as seguintes etapas para excluir o dispositivo:
 
    1. Na página Gerenciador de StorSimple serviço **dispositivos** , selecione um dispositivo desativado que você deseja excluir.

    2. Na parte inferior da página, clique em **Excluir**.

    3. Você será solicitado para confirmação. Clique em **Sim** para continuar.

    Pode levar alguns minutos para o dispositivo a ser excluída.

## <a name="deactivate-and-delete-a-virtual-device"></a>Desativar e excluir um dispositivo virtual

Para um dispositivo virtual StorSimple, desativação desaloca na máquina virtual. Você pode excluir a máquina virtual e os recursos que criou quando ele foi provisionado. Depois do dispositivo virtual está desativado, ele não pode ser restaurado ao seu estado anterior. 

Desativação resulta as seguintes ações:

- O dispositivo virtual StorSimple será removido.

- O OSDisk e discos de dados criado para o dispositivo virtual StorSimple são removidos.

- O serviço hospedado e a rede Virtual que foram criados durante a configuração são mantidas. Se você não estiver usando essas entidades, você deve excluí-los manualmente.

- Instantâneos de nuvem criados pelo dispositivo virtual StorSimple são mantidos.

## <a name="next-steps"></a>Próximas etapas
- Para restaurar o dispositivo desativado para padrões de fábrica, vá para [Redefinir o dispositivo para as configurações padrão de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Para obter assistência técnica, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md).

- Para saber mais sobre como usar o serviço do Gerenciador de StorSimple, vá para [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md). 
