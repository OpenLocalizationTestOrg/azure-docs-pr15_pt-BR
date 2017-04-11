<properties 
   pageTitle="Gerenciar os registros de controle de acesso para a matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve como gerenciar registros de controle de acesso (ACRs) para determinar quais hosts podem se conectar a um volume da matriz Virtual StorSimple."
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
   ms.date="05/03/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Usar o serviço do Gerenciador de StorSimple para gerenciar os registros de controle de acesso para a matriz Virtual StorSimple 

## <a name="overview"></a>Visão geral

Registros de controle de acesso (ACRs) permitem que você especifique quais hosts podem se conectar a um volume da matriz de Virtual StorSimple (também conhecido como o StorSimple local virtual dispositivo). ACRs são definidas como um volume específico e contenham os nomes qualificados iSCSI (IQNs) dos hosts. Quando um host tenta se conectar a um volume, o dispositivo verifica a ACR associada ao volume do nome IQN e, se houver uma correspondência, a conexão for estabelecida. A seção de **registros de controle de acesso** na página **Definir** exibe todos os registros de controle de acesso com os IQNs correspondentes dos hosts.

Este tutorial explica as seguintes tarefas comuns ACR relacionados:

- Obtenha o IQN
- Adicionar um registro de controle de acesso 
- Editar um registro de controle de acesso 
- Excluir um registro de controle de acesso 

> [AZURE.IMPORTANT] 
> 
> - Ao atribuir um ACR a um volume, tome cuidado que o volume não está simultaneamente acessado por mais de um host sem cluster porque isso pode danificar o volume. 
> - Ao excluir um ACR de um volume, certifique-se de que o host correspondente não está acessando o volume porque a exclusão pode resultar em uma interrupção de leitura e gravação.

## <a name="get-the-iqn"></a>Obtenha o IQN

Execute as seguintes etapas para obter o IQN de um host do Windows que está executando o Windows Server 2012.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adicionar um ACR

Você pode usar a página de **configuração** de serviço do Gerenciador de StorSimple adicionar ACRs. Normalmente, você irá associar um ACR um volume.

Para obter informações sobre como associar um ACR com um volume, vá para [Adicionar um volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

>[AZURE.IMPORTANT] 
> 
>Ao atribuir um ACR a um volume, tome cuidado que o volume não está acessado simultaneamente por mais de um host sem cluster porque isso pode danificar o volume.
 
Execute as seguintes etapas para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Na página inicial do serviço, selecione o serviço, clique duas vezes no nome do serviço e, em seguida, clique na guia **configuração** .

    ![Guia de configuração](./media/storsimple-ova-manage-acrs/acr1.png)

2. Na lista de tabular em **registros de controle de acesso**, forneça um **nome** para seu ACR.

3. Em **nome do iniciador iSCSI**, forneça o nome IQN do seu host do Windows. 

4. Clique em **Salvar** na parte inferior da página para salvar a ACR recém-criado. Você verá a seguinte mensagem de confirmação.

    ![mensagem de confirmação](./media/storsimple-ova-manage-acrs/acr2.png)

5. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-manage-acrs/check-icon.png). A listagem tabular será atualizada para refletir essa adição.

## <a name="edit-an-acr"></a>Editar um ACR

Você pode usar a página de **configuração** no portal de clássico do Azure para editar ACRs. 

> [AZURE.NOTE] Você deve modificar apenas essas ACRs que não estão em uso. Para editar um ACR associada a um volume que está em uso, você deve primeiro colocar o volume offline.

Execute as seguintes etapas para editar um ACR.

#### <a name="to-edit-an-acr"></a>Para editar um ACR

1. Na página inicial do serviço, selecione o serviço, clique duas vezes no nome do serviço e, em seguida, clique na guia **configuração** .

2. Na lista de tabular os registros de controle de acesso, focalize a ACR que você deseja modificar.

3. Fornece um novo nome e/ou IQN para a ACR.

4. Clique em **Salvar** na parte inferior da página para salvar a ACR modificada. Você verá uma mensagem de confirmação. 

5. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-manage-acrs/check-icon.png). A listagem tabular será atualizada para refletir essa alteração.

## <a name="delete-an-access-control-record"></a>Excluir um registro de controle de acesso

Você pode usar a página de **configuração** no portal de clássico do Azure para excluir ACRs. 

> [AZURE.NOTE] 
> 
> - Você deve excluir apenas essas ACRs que não estão em uso. Para excluir um ACR associada a um volume que está em uso, você deve primeiro colocar o volume offline.
> - Ao excluir um ACR de um volume, certifique-se de que o host correspondente não está acessando o volume porque a exclusão pode resultar em uma interrupção de leitura e gravação.

Execute as seguintes etapas para excluir um registro de controle de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para excluir um registro de controle de acesso

1. Na página inicial do serviço, selecione o serviço, clique duas vezes no nome do serviço e, em seguida, clique na guia **configuração** .

2. Na lista de tabular os registros de controle de acesso (ACRs), passe o mouse sobre a ACR que você deseja excluir.

3. Um ícone Excluir (**x**) será exibido na coluna à extrema direita para a ACR que você selecionar. Clique no ícone **x** para excluir a ACR. Você verá a seguinte mensagem de confirmação.

    ![mensagem de confirmação](./media/storsimple-ova-manage-acrs/acr3.png)

5. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-manage-acrs/check-icon.png). A listagem tabular será atualizada para refletir a exclusão.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Adicionando volumes e configurando ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).
