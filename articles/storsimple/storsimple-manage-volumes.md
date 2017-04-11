<properties
   pageTitle="Gerenciar seus volumes de StorSimple | Microsoft Azure"
   description="Explica como adicionar, modificar, monitorar e excluir volumes de StorSimple e como fazê-lo offline, se necessário."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/11/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes"></a>Use o serviço do Gerenciador de StorSimple para gerenciar volumes

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Visão geral

Este tutorial explica como usar o serviço do Gerenciador de StorSimple para criar e gerenciar volumes no dispositivo StorSimple e dispositivo virtual StorSimple.

O serviço do Gerenciador de StorSimple é uma extensão do portal de clássico do Azure que permite que você gerencie sua solução de StorSimple de uma única interface da web. Além de gerenciar volumes, você pode usar o serviço do Gerenciador de StorSimple para criar e gerenciar serviços de StorSimple, exibir e gerenciar dispositivos, exibir alertas, exibir e gerenciar políticas de backup e o catálogo de backup.

> [AZURE.NOTE] StorSimple Azure pode criar apenas volumes de provisionamento thin. Não é possível criar totalmente provisionado ou parcialmente provisionado volumes em um sistema de StorSimple do Azure.
>
> Provisionamento fino é uma tecnologia de virtualização no qual o armazenamento disponível aparece exceda recursos físicos. Em vez de reserva armazenamento suficiente com antecedência, StorSimple Azure usa provisionamento fino para alocar espaço apenas o suficiente para atender às necessidades atuais. A natureza elástica de armazenamento em nuvem facilita essa abordagem porque Azure StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às exigências de alteração.

## <a name="the-volumes-page"></a>A página de Volumes

A página de **Volumes** permite que você gerencie os volumes de armazenamento que são provisionados no dispositivo Microsoft Azure StorSimple para os iniciadores (servidores). Ele exibe a lista de volumes em seu dispositivo de StorSimple.

 ![Página de volumes](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

Um volume consiste em uma série de atributos:

- **Nome** – um nome descritivo que deve ser exclusivo e ajuda a identificar o volume. Esse nome também é usado em relatórios do monitoring quando você filtrar em um volume específico.

- **Status** – pode ser online ou offline. Se um volume se offline, não é visível para iniciadores (servidores) que têm permissão de acesso para usar o volume.

- **Capacidade** – Especifica como grande volume, conforme considerado pelo iniciador (servidor). Capacidade Especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Volumes thin são provisionados e dados é eliminação de duplicação. Isso significa que o seu dispositivo não previamente aloca capacidade de armazenamento físico internamente ou na nuvem de acordo com a capacidade de volume configurado. A capacidade de volume é alocada e consumida sob demanda.

- **Tipo** – o tipo de volume pode ser hierárquico ou arquivamento (um subtipo de hierárquico)

- **Acesso** – Especifica os iniciadores (servidores) que têm permissão de acesso para este volume. Iniciadores que não são membros de registro de controle de acesso (ACR) que está associado com o volume não verão o volume.

- **Monitoramento** – especifica ou não um volume está sendo monitorado. Um volume terá monitoramento ativado por padrão quando ele é criado. Monitoramento será, no entanto, ser desabilitada por clonar um volume. Para habilitar o monitoramento para um volume, siga as instruções no Monitor um volume.

As tarefas mais comuns associadas a um volume são:

- Adicionar um volume
- Modificar um volume
- Excluir um volume
- Colocar um volume offline
- Monitorar um volume

## <a name="add-a-volume"></a>Adicionar um volume

Você [criou um volume](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) durante a implantação da solução StorSimple. Adicionar um volume é um procedimento semelhante.

### <a name="to-add-a-volume"></a>Para adicionar um volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** .

2. Selecione um contêiner de volume e clique na seta na linha correspondente para acessar os volumes associados com o contêiner.

3. Clique em **Adicionar** na parte inferior da página. Adicionar um Assistente de volume é iniciado.

     ![Assistente de volume configurações básicas de adição](./media/storsimple-manage-volumes/AddVolume1.png)

4. No Assistente adicionar um volume, em **Configurações básicas**, faça o seguinte:

  1. Forneça um **nome** para o volume.
  2. Especifica a **Capacidade provisionado** para seu volume em GB ou TB. A capacidade deve estar entre 1 GB e 64 TB para um dispositivo físico. A capacidade máxima que pode ser provisionada para um volume em um dispositivo virtual StorSimple é 30 TB.
  3. Selecione o **Tipo de uso** para o volume. Se você estiver usando o volume hierárquico para dados de arquivamento, marcando a caixa de seleção **usar este volume para dados de arquivamento menos acessados** altera o tamanho da parte da duplicação para o volume para 512 KB. Se você não selecionar essa opção, o volume de hierárquico correspondente usará um tamanho de bloco de 64 KB. Um tamanho maior de bloco de duplicação permite que o dispositivo para acelerar a transferência de dados de arquivamento grandes para a nuvem. (Volumes hierárquicos estavam anteriormente chamados volumes primários.)
  5. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)para ir para a página de **Configurações adicionais** .

        ![Adicionar configurações adicionais do Assistente de Volume](./media/storsimple-manage-volumes/AddVolume2.png)

5. Em **Configurações adicionais**, adicione um novo registro de controle de acesso (ACR):

  1. Selecione um registro de controle de acesso (ACR) na lista suspensa. Como alternativa, você pode adicionar um novo ACR. ACRs determinam quais hosts podem acessar seus volumes combinando o host IQN com que listado no registro.
  2. Recomendamos que você habilite um backup padrão marcando a caixa de seleção **Habilitar um backup padrão para este volume** .
   3. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-manage-volumes/HCS_CheckIcon.png) para criar o volume com as configurações especificadas.

Novo volume agora está pronto para uso.

## <a name="modify-a-volume"></a>Modificar um volume

Modificar um volume quando você precisar expandi-lo ou alterar os hosts que acessar o volume.

> [AZURE.IMPORTANT]
>
> - Se você modificar o tamanho do volume do dispositivo, o tamanho do volume precisa ser alterado no host também.
> - As etapas do lado do host descritas aqui são para Windows Server 2012 (2012R2). Procedimentos para Linux ou outros sistemas operacionais de host serão diferentes. Consulte as instruções do sistema operacional de host ao modificar o volume em um host executando outro sistema operacional.

### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nele e, em seguida, clique na guia de **Contêiner de Volume** . Esta página lista em um formato tabular todos os contêineres de volume que estão associados com o dispositivo.

2. Selecione um contêiner de volume e clique nele para exibir a lista de todos os volumes dentro do contêiner.

3. Na página **Volumes** , selecione um volume e clique em **Modificar**.

4. No Assistente de volume de modificar, em **Configurações básicas**, você pode fazer o seguinte:

  - Edite o **nome** e **tipo** , se você deseja modificar um volume hierárquico para um volume de arquivamento marcando a caixa de seleção **usar este volume para dados de arquivamento menos acessados** para alterar o tamanho de bloco de duplicação para o volume para 512 KB.
  - Aumente a **capacidade de provisionado**. A **Capacidade de provisionado** só pode ser aumentado. Você não pode reduzir um volume após sua criação.

    > [AZURE.NOTE] Você não pode alterar o contêiner de volume depois de ser atribuída a um volume.

5. Em **Configurações adicionais**, você pode fazer o seguinte:

  - Modificar os ACRs, desde que o volume está offline. Se o volume estiver online, você precisará colocá-lo offline primeiro. Consulte as etapas em [levar um volume offline](#take-a-volume-offline) antes de modificar a ACR.
  - Modificar a lista de ACRs após o volume está offline.

    > [AZURE.NOTE] Você não pode alterar a opção **Habilitar um backup padrão para este volume** para o volume.

6. Salvar suas alterações, clicando no ícone de seleção ![ícone de verificação](./media/storsimple-manage-volumes/HCS_CheckIcon.png). O portal de clássico Azure exibirá uma mensagem de volume atualização. Ele exibirá uma mensagem de sucesso quando o volume foi atualizado com êxito.

7. Se você estiver expandindo um volume, conclua as etapas a seguir no computador de host Windows:

   1. Vá para **gerenciamento de computador** ->**gerenciamento de disco**.
   2. Clique com botão direito **Gerenciamento de disco** e selecione **Examinar discos**.
   3. Na lista de discos, selecione o volume que você atualizou, com o botão direito e, em seguida, selecione **Estender Volume**. Inicia o Assistente de estender Volume. Clique em **Avançar**.
   4. Conclua o assistente, aceitando os valores padrão. Quando o assistente for concluído, o volume deve mostrar o tamanho aumentado.

![Vídeo disponível](./media/storsimple-manage-volumes/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que demonstra como expandir um volume, clique [aqui](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="take-a-volume-offline"></a>Colocar um volume offline

Talvez você precise colocar um volume offline quando você estiver planejando modificá-la ou excluí-lo. Quando um volume está offline, ele não está disponível para acesso de leitura e gravação. Você precisará fazer o volume offline do host, bem como no dispositivo. Execute as seguintes etapas para colocar um volume offline.

### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline

1. Certifique-se de que o volume em questão não está em uso antes de colocá-lo offline.

2. Levar o volume offline do host primeiro. Isso elimina qualquer risco de corrupção de dados no volume. Para obter etapas específicas, consulte as instruções para seu sistema operacional de host.

3. Após o host estiver offline, levar o volume do dispositivo offline, executando as seguintes etapas:

  1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** . Guia **Contêineres de Volume** listas em um formato tabular todos os contêineres de volume que estão associados com o dispositivo.
  2. Selecione um contêiner de volume e clique nele para exibir a lista de todos os volumes dentro do contêiner.
  3. Selecione um volume e clique em **colocar offline**.
  4. Quando solicitado para confirmação, clique em **Sim**. O volume agora deve estar offline.

    Após um volume estiver offline, a opção **Colocar on-line** fica disponível.

> [AZURE.NOTE] O comando **Levar Offline** envia uma solicitação para o dispositivo para colocar o volume offline. Se hosts ainda estiver usando o volume, isso resulta em conexões interrompidas, mas levando o volume offline não falhará.

## <a name="delete-a-volume"></a>Excluir um volume

> [AZURE.IMPORTANT] Você pode excluir um volume apenas se ele está offline.

Conclua estas etapas para excluir um volume.

### <a name="to-delete-a-volume"></a>Para excluir um volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** .

2. Selecione o contêiner de volume que possui o volume que você deseja excluir. Clique no contêiner de volume para acessar a página de **Volumes** .

3. Todos os volumes associados a este contêiner são exibidos em um formato tabular. Verificar o status do volume que você deseja excluir. Se o volume que você deseja excluir não estiver offline, colocá-lo offline primeiro, seguindo as etapas em [levar um volume offline](#take-a-volume-offline).

4. Depois que o volume está offline, clique em **Excluir** na parte inferior da página.

5. Quando solicitado para confirmação, clique em **Sim**. O volume será excluído e a página de **Volumes** mostrará a lista atualizada de volumes dentro do contêiner.

## <a name="monitor-a-volume"></a>Monitorar um volume

Monitoramento de volume permite que você colete estatística eu/O-relacionados para um volume. Monitoramento é ativado por padrão para os primeiros 32 volumes que você criar. Monitoramento de volumes adicionais é desabilitado por padrão. Monitoramento de volumes clonados também é desabilitado por padrão.

Execute as seguintes etapas para ativar ou desativar o monitoramento para um volume.

### <a name="to-enable-or-disable-volume-monitoring"></a>Para habilitar ou desabilitar o monitoramento de volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** .

2. Selecione o contêiner de volume em que reside o volume e, em seguida, clique no contêiner de volume para acessar a página de **Volumes** .

3. Todos os volumes associados a este contêiner estão listados na exibição tabular. Clique em e selecione o volume ou clonar de volume.

4. Na parte inferior da página, clique em **Modificar**.

5. No Assistente de modificar Volume, em **Configurações básicas**, selecione **Habilitar** ou **Desabilitar** na lista suspensa de **monitoramento** .

    ![Modificar um volume configurações básicas](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba como [clonar um volume StorSimple](storsimple-clone-volume.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
