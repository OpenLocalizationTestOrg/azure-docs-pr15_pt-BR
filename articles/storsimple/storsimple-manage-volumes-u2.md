<properties
   pageTitle="Gerenciar seus volumes de StorSimple (U2) | Microsoft Azure"
   description="Explica como adicionar, modificar, monitorar e excluir volumes de StorSimple e como fazê-lo offline, se necessário."
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
   ms.workload="NA"
   ms.date="10/28/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Use o serviço do Gerenciador de StorSimple para gerenciar volumes (atualização 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Visão geral

Este tutorial explica como usar o serviço do Gerenciador de StorSimple para criar e gerenciar volumes no dispositivo StorSimple e dispositivo virtual StorSimple com atualização 2 instalado.

O serviço do Gerenciador de StorSimple é uma extensão no portal de clássico do Azure que permite que você gerencie sua solução de StorSimple de uma única interface da web. Além de gerenciar volumes, você pode usar o serviço do Gerenciador de StorSimple para criar e gerenciar serviços de StorSimple, exibir e gerenciar dispositivos, exibir alertas, exibir e gerenciar políticas de backup e o catálogo de backup.

## <a name="volume-types"></a>Tipos de volume

Volumes de StorSimple podem ser:

- **Localmente fixos volumes**: dados nesses volumes permanecem no dispositivo local StorSimple todas as vezes.
- **Volumes de hierárquico**: dados nesses volumes podem passando para a nuvem.

Um volume de arquivamento é um tipo de volume hierárquico. O tamanho da parte da duplicação maior usado para volumes de arquivamento permite ao dispositivo transferir segmentos maiores de dados para a nuvem. 

Se necessário, você pode alterar o volume tipo de local para hierárquico ou de hierárquico para local. Para obter mais informações, vá para [alterar o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes localmente fixados

Localmente fixada são totalmente provisionados volumes que eu não dados em camadas para a nuvem, assegurando que local garante para dados principais, independentemente de conectividade de nuvem. Dados em volumes localmente fixados não é eliminação de duplicação e compactados; No entanto, instantâneos dos volumes localmente fixados são eliminação de duplicação. 

Volumes localmente fixados totalmente são provisionados; Portanto, você deve ter espaço suficiente em seu dispositivo quando você as cria. Você pode provisionar localmente fixados volumes até o tamanho máximo de 8 TB no dispositivo StorSimple 8100 e 20 TB no dispositivo 8600. StorSimple reserva o espaço restante local no dispositivo para instantâneos, metadados e processamento de dados. Você pode aumentar o tamanho de um volume localmente fixado no máximo espaço disponível, mas não é possível diminuir o tamanho de um volume uma vez criado.

Quando você cria um volume localmente fixado, o espaço disponível para a criação de volumes hierárquicos é reduzido. O inverso também é verdadeiro: se você tiver volumes hierárquicos existentes, o espaço disponível para criar localmente fixos volumes será menor que os limites máximos mencionados acima. Para obter mais informações sobre volumes locais, consulte as [Perguntas frequentes em volumes localmente fixados](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Volumes hierárquicos

Volumes hierárquicos são volumes de provisionamento thin no qual os dados acessados permanece no mesmo locais no dispositivo e menos dados usados com frequência é hierárquico automaticamente para a nuvem. Provisionamento fino é uma tecnologia de virtualização no qual o armazenamento disponível aparece exceda recursos físicos. Em vez de reserva armazenamento suficiente com antecedência, StorSimple usa provisionamento fino para alocar espaço apenas o suficiente para atender às necessidades atuais. A natureza elástica de armazenamento em nuvem facilita essa abordagem porque StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às exigências de alteração.

Se você estiver usando o volume hierárquico para dados de arquivamento, marcando a caixa de seleção **usar este volume para dados de arquivamento menos acessados** altera o tamanho da parte da duplicação para o volume para 512 KB. Se você não selecionar essa opção, o volume de hierárquico correspondente usará um tamanho de bloco de 64 KB. Um tamanho maior de bloco de duplicação permite que o dispositivo para acelerar a transferência de dados de arquivamento grandes para a nuvem.

>[AZURE.NOTE] Arquivamento volumes criados com uma versão de 2 de pré-atualização do StorSimple serão importados como hierárquico com a caixa de seleção arquivamento selecionada.

### <a name="provisioned-capacity"></a>Capacidade de provisionamento

Consulte a tabela a seguir para capacidade máxima provisionada para cada tipo de dispositivo e volume. (Observe que volumes localmente fixados não estão disponíveis em um dispositivo virtual.)

|             | Tamanho máximo do volume hierárquico | Máximo localmente fixos tamanho do volume |
|-------------|----------------------------|------------------------------------|
| **Dispositivos físicos** |       |       |
| 8100                 | 64 TB | 8 TB |
| 8600                 | 64 TB | 20 TB |
| **Dispositivos virtuais**  |       |       |
| 8010                | 30 TB | N/D   |
| 8020               | 64 TB | N/D   |

## <a name="the-volumes-page"></a>A página de Volumes

A página de **Volumes** permite que você gerencie os volumes de armazenamento que são provisionados no dispositivo Microsoft Azure StorSimple para os iniciadores (servidores). Ele exibe a lista de volumes em seu dispositivo de StorSimple.

 ![Página de volumes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Um volume consiste em uma série de atributos:

- **Nome do volume** – um nome descritivo que deve ser exclusivo e ajuda a identificar o volume. Esse nome também é usado em relatórios do monitoring quando você filtrar em um volume específico.

- **Status** – pode ser online ou offline. Se um volume estiver offline, não é visível para iniciadores (servidores) que têm permissão de acesso para usar o volume.

- **Capacidade** – Especifica a quantidade total de dados que podem ser armazenados pelo iniciador (servidor). Volumes localmente fixos são totalmente provisionados e residem no dispositivo StorSimple. Hierárquicos volumes thin são provisionados e os dados é eliminação de duplicação. Com volumes de provisionamento thin, seu dispositivo previamente não aloca a capacidade de armazenamento físico internamente ou na nuvem de acordo com a capacidade de volume configurado. A capacidade de volume é alocada e consumida sob demanda.

- **Tipo** – indica se o volume é **hierárquico** (padrão) ou **localmente fixos**.

- **Backup** – indica se uma política de backup padrão existe para o volume.

- **Acesso** – Especifica os iniciadores (servidores) que têm permissão de acesso para este volume. Iniciadores que não são membros de registro de controle de acesso (ACR) que está associado com o volume não verão o volume.

- **Monitoramento** – especifica ou não um volume está sendo monitorado. Um volume terá monitoramento ativado por padrão quando ele é criado. Monitoramento será, no entanto, ser desabilitada por clonar um volume. Para habilitar o monitoramento para um volume, siga as instruções no [Monitor um volume](#monitor-a-volume). 

Use as instruções neste tutorial para executar as seguintes tarefas:

- Adicionar um volume 
- Modificar um volume 
- Alterar o tipo de volume
- Excluir um volume 
- Colocar um volume offline 
- Monitorar um volume 

## <a name="add-a-volume"></a>Adicionar um volume

Você [criou um volume](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implantação da solução StorSimple. Adicionar um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** .

2. Selecione um contêiner de volume na lista e clique duas vezes nele para acessar os volumes associados com o contêiner.

3. Clique em **Adicionar** na parte inferior da página. Adicionar um Assistente de volume é iniciado.

     ![Assistente de volume configurações básicas de adição](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. No Assistente adicionar um volume, em **Configurações básicas**, faça o seguinte:

  1. Forneça um **nome** para o volume.
  2. Selecione um **Tipo de uso** na lista suspensa. Para cargas de trabalho que exigem dados disponível localmente no dispositivo em todos os momentos, selecione **Fixos localmente**. Para todos os outros tipos de dados, selecione **hierárquico**. (**Hierárquico** é o padrão).
  3. Se você selecionou **hierárquico** na etapa 2, você pode selecionar a caixa de seleção **usar este volume para dados de arquivamento menos acessados** para configurar um volume de arquivamento.
  4. Insira a **Capacidade provisionado** para seu volume em GB ou TB. Consulte [provisionado capacidade](#provisioned-capacity) para tamanhos máximos para cada tipo de dispositivo e volume. Examine a **Capacidade disponível** para determinar a quantidade de armazenamento é realmente disponível no seu dispositivo.

5. Clique no ícone de seta![Ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Se você estiver configurando um volume localmente fixado, você verá a seguinte mensagem.

    ![Mensagem de alteração de tipo de Volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)novamente para ir para a página de **Configurações adicionais** .

    ![Adicionar configurações adicionais do Assistente de Volume](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. Em **Configurações adicionais**, adicione um novo registro de controle de acesso (ACR):
  
  1. Selecione um registro de controle de acesso (ACR) na lista suspensa. Como alternativa, você pode adicionar um novo ACR. ACRs determinam quais hosts podem acessar seus volumes combinando o host IQN com que listado no registro. Se você não especificar um ACR, você verá a seguinte mensagem.

        ![Especificar ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Recomendamos que você selecione a caixa de seleção **Habilitar um backup padrão para este volume** .
  3. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) para criar o volume com as configurações especificadas.

Novo volume agora está pronto para uso.

>[AZURE.NOTE] Se você cria um volume localmente fixado e, em seguida, criar outro volume localmente fixado imediatamente posteriormente, a criação de volume trabalhos executados sequencialmente. O primeiro trabalho de criação de volume deve terminar antes de começar o próximo trabalho de criação de volume.

## <a name="modify-a-volume"></a>Modificar um volume

Modificar um volume quando você precisar expandi-lo ou alterar os hosts que acessar o volume.

> [AZURE.IMPORTANT] 
>
> - Se você modificar o tamanho do volume do dispositivo, o tamanho do volume precisa ser alterado no host também. 
> - As etapas do lado do host descritas aqui são para Windows Server 2012 (2012R2). Procedimentos para Linux ou outros sistemas operacionais de host serão diferentes. Consulte as instruções do sistema operacional de host ao modificar o volume em um host executando outro sistema operacional. 

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** .

2. Selecione um contêiner de volume na lista e clique duas vezes nela para exibir os volumes associados com o contêiner.

3. Selecione um volume e na parte inferior da página, clique em **Modificar**. Inicia o Assistente para modificar volume.

4. No Assistente de volume de modificar, em **Configurações básicas**, você pode fazer o seguinte:

  - Edite o **nome**.
  - Converter o **Tipo de uso** do localmente fixos para hierárquico ou de hierárquico para localmente fixos (consulte [alterar o tipo de volume](#change-the-volume-type) para obter mais informações).
  - Aumente a **capacidade de provisionado**. A **Capacidade de provisionado** só pode ser aumentado. Você não pode reduzir um volume após sua criação.

5. Em **Configurações adicionais**, você pode modificar a ACR, desde que o volume está offline. Se o volume estiver online, você precisará colocá-lo offline primeiro. Consulte as etapas em [levar um volume offline](#take-a-volume-offline) antes de modificar a ACR.

    > [AZURE.NOTE] Você não pode alterar a opção **Habilitar um backup padrão** para o volume.

6. Salvar suas alterações, clicando no ícone de seleção ![ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). O portal de clássico Azure exibirá uma mensagem de volume atualização. Ele exibirá uma mensagem de sucesso quando o volume foi atualizado com êxito.

7. Se você estiver expandindo um volume, conclua as etapas a seguir no computador de host Windows:

   1. Vá para **gerenciamento de computador** ->**gerenciamento de disco**.
   2. Clique com botão direito **Gerenciamento de disco** e selecione **Examinar discos**.
   3. Na lista de discos, selecione o volume que você atualizou, com o botão direito e, em seguida, selecione **Estender Volume**. Inicia o Assistente de estender Volume. Clique em **Avançar**.
   4. Conclua o assistente, aceitando os valores padrão. Quando o assistente for concluído, o volume deve mostrar o tamanho aumentado.

    >[AZURE.NOTE] Se você expanda um volume localmente fixado e outro localmente fixado volume imediatamente posteriormente, os trabalhos de expansão de volume executados sequencialmente. O primeiro trabalho de expansão de volume deve terminar antes de começar o próximo trabalho de expansão de volume.

![Vídeo disponível](./media/storsimple-manage-volumes-u2/Video_icon.png) **vídeo disponível**

Para assistir a um vídeo que demonstra como expandir um volume, clique [aqui](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Alterar o tipo de volume

Você pode alterar o tipo de volume do hierárquico para localmente fixos ou de localmente fixos para hierárquico. No entanto, essa conversão não deve ser uma ocorrência frequente. Algumas razões para converter um volume de hierárquico para localmente fixos são:

- Locais garantias com relação ao desempenho e a disponibilidade de dados
- Eliminação de latências de nuvem e problemas de conectividade de nuvem.

Geralmente, esses são pequenos volumes existentes que você deseja acessar com frequência. Um volume localmente fixado totalmente está provisionado quando ele é criado. Se você estiver convertendo um volume hierárquico para um volume localmente fixado, StorSimple verifica que você tenha espaço suficiente em seu dispositivo antes de iniciar a conversão. Se você tem espaço suficiente, você receberá um erro e a operação será cancelada. 

> [AZURE.NOTE] Antes de começar uma conversão de hierárquico para localmente fixos, certifique-se de que você considerar os requisitos de espaço de suas outras cargas de trabalho. 

Talvez você queira alterar um volume localmente fixado para um volume hierárquico se precisar de mais espaço para provisionar outros volumes. Quando você converte o volume localmente fixado hierárquico, a capacidade disponível na dispositivo aumenta o tamanho da capacidade de lançamento. Se problemas de conectividade evitar a conversão de um volume do tipo de local para o tipo de hierárquico, volume local exibirão propriedades de um volume hierárquico até que a conversão seja concluída. Isso ocorre porque alguns dados podem ter derramado na nuvem. Estes dados derramados continuará ocupar espaço local do dispositivo que não podem ser livre até que a operação seja reiniciada e concluída.

>[AZURE.NOTE] Convertendo um volume pode levar algum tempo e não é possível cancelar uma conversão depois que ele for iniciado. O volume on-line durante a conversão e você pode realizar backups, mas você não pode expandir ou restaurar o volume enquanto a conversão estiver ocorrendo.  

Conversão de uma hierárquico para um volume localmente fixado pode afetar o desempenho do dispositivo. Além disso, os seguintes fatores podem aumentar o tempo necessário para concluir a conversão:

- Não há largura de banda insuficiente.

- Não há nenhum backup atual.

Para minimizar os efeitos que esses fatores podem ter:

- Examine sua largura de banda políticas e certifique-se de que uma largura de banda 40 por Mbps dedicado está disponível.
- Agende a conversão de pico.
- Tirar um instantâneo de nuvem antes de iniciar a conversão.

Se você estiver convertendo vários volumes (suporte diferentes cargas de trabalho), você deve priorizar a conversão de volume para que volumes de prioridade mais altos são convertidos primeiro. Por exemplo, você deve converter volumes que hospedam máquinas virtuais (VMs) ou com cargas de trabalho SQL antes de você converter volumes com cargas de trabalho de compartilhamento de arquivo.

#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** .

2. Selecione um contêiner de volume na lista e clique duas vezes nela para exibir os volumes associados com o contêiner.

3. Selecione um volume e na parte inferior da página, clique em **Modificar**. Inicia o Assistente para modificar volume.

4. Na página **Configurações básicas** , altere o tipo de uso, selecionando o novo tipo na lista suspensa **Tipo de uso** .

    - Se você estiver alterando o tipo para **localmente fixos**, StorSimple verifica se há capacidade suficiente.
    - Se você está alterando o tipo para **hierárquico** e este volume será usado para arquivamento dados, marque a caixa de seleção **usar este volume para dados de arquivamento menos acessados** .

        ![Caixa de seleção de arquivamento](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) para ir para a página de **Configurações adicionais** . Se você estiver configurando um volume localmente fixado, a seguinte mensagem será exibida.

    ![Mensagem de alteração de tipo de Volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) novamente para continuar.

7. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) para iniciar o processo de conversão. O portal do Azure exibirá uma mensagem de volume atualização. Ele exibirá uma mensagem de sucesso quando o volume foi atualizado com êxito.

## <a name="take-a-volume-offline"></a>Colocar um volume offline

Talvez você precise colocar um volume offline quando você estiver planejando modificá-la ou excluí-lo. Quando um volume está offline, ele não está disponível para acesso de leitura e gravação. Você precisará fazer o volume offline do host, bem como no dispositivo. 

#### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline

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

#### <a name="to-delete-a-volume"></a>Para excluir um volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** .

2. Selecione o contêiner de volume que possui o volume que você deseja excluir. Clique no contêiner de volume para acessar a página de **Volumes** .

3. Todos os volumes associados a este contêiner são exibidos em um formato tabular. Verificar o status do volume que você deseja excluir. Se o volume que você deseja excluir não estiver offline, colocá-lo offline primeiro, seguindo as etapas em [levar um volume offline](#take-a-volume-offline).

4. Depois que o volume está offline, clique em **Excluir** na parte inferior da página.

5. Quando solicitado para confirmação, clique em **Sim**. O volume será excluído e a página de **Volumes** mostrará a lista atualizada de volumes dentro do contêiner.

    >[AZURE.NOTE]Se você excluir um volume localmente fixado, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do Gerenciador de StorSimple atualiza o local espaço disponível periodicamente. Sugerimos que você aguarde alguns minutos antes de tentar criar o novo volume.<br> Além disso, se você excluir um volume localmente fixado e exclua outro localmente fixado volume imediatamente posteriormente, a exclusão de volume trabalhos executados sequencialmente. O trabalho de exclusão de volume primeiro deve terminar antes de começar o próximo trabalho de exclusão de volume.
 
## <a name="monitor-a-volume"></a>Monitorar um volume

Monitoramento de volume permite que você colete estatística eu/O-relacionados para um volume. Monitoramento é ativado por padrão para os primeiros 32 volumes que você criar. Monitoramento de volumes adicionais é desabilitado por padrão. Monitoramento de volumes clonados também é desabilitado por padrão.

Execute as seguintes etapas para ativar ou desativar o monitoramento para um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para habilitar ou desabilitar o monitoramento de volume

1. Na página **dispositivos** , selecione o dispositivo, clique duas vezes nela e clique na guia **Contêineres de Volume** .

2. Selecione o contêiner de volume em que reside o volume e, em seguida, clique no contêiner de volume para acessar a página de **Volumes** .

3. Todos os volumes associados a este contêiner estão listados na exibição tabular. Clique em e selecione o volume ou clonar de volume.

4. Na parte inferior da página, clique em **Modificar**.

5. No Assistente de modificar Volume, em **Configurações básicas**, selecione **Habilitar** ou **Desabilitar** na lista suspensa de **monitoramento** .

## <a name="next-steps"></a>Próximas etapas

- Saiba como [clonar um volume StorSimple](storsimple-clone-volume.md).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

 
