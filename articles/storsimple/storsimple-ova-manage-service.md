<properties 
   pageTitle="Implantar o serviço do Gerenciador de StorSimple para array virtual StorSimple | Microsoft Azure"
   description="Explica como criar e excluir o serviço do Gerenciador de StorSimple no portal de clássico do Azure e descreve como gerenciar a chave do registro de serviço."
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
   ms.date="05/19/2016"
   ms.author="alkohli" />

# <a name="deploy-the-storsimple-manager-service-for-storsimple-virtual-array"></a>Implantar o serviço do Gerenciador de StorSimple para StorSimple Virtual matriz

## <a name="overview"></a>Visão geral

O serviço do Gerenciador de StorSimple é executado no Microsoft Azure e se conecta a vários dispositivos de StorSimple. Depois de criar o serviço, você pode usá-lo para gerenciar os dispositivos do portal clássico do Microsoft Azure em execução em um navegador. Isso permite que você monitore todos os dispositivos que estão conectados ao serviço Gerenciador de StorSimple de um único local central, minimizando o trabalho administrativo.

A página inicial do Gerenciador de StorSimple lista todos os serviços do Gerenciador de StorSimple que você pode usar para gerenciar os dispositivos de armazenamento StorSimple. Para cada serviço do Gerenciador de StorSimple, as seguintes informações são apresentadas na página Gerenciador de StorSimple:

- **Nome** – o nome que tenha sido atribuído ao seu serviço de Gerenciador de StorSimple quando ela foi criada. O nome do serviço não pode ser alterado depois que o serviço é criado.

- **Status** – o status do serviço, que pode ser **ativo**, **Criando**ou **Online**.

- **Local** – a localização geográfica na qual o dispositivo de StorSimple será implantado.

- **Assinatura** – a assinatura de cobrança associado ao seu serviço.

As tarefas comuns que podem ser executadas através da página do Gerenciador de StorSimple são:

- Criar um serviço
- Excluir um serviço
- Obter a chave do registro de serviço
- Gerar novamente a chave do registro de serviço

Este tutorial descreve como realizar cada uma dessas tarefas. As informações contidas neste artigo se aplica somente a matrizes Virtual StorSimple. Para obter mais informações sobre a série 8000 StorSimple, vá para [implantar um serviço de Gerenciador de StorSimple](storsimple-manage-service.md).

## <a name="create-a-service"></a>Criar um serviço

Use a opção de **Criação rápida** para criar um serviço Gerenciador de StorSimple se quiser implantar o seu dispositivo StorSimple. Para criar um serviço, você precisa ter:

- Uma assinatura com um contrato Enterprise
- Uma conta de armazenamento do Microsoft Azure active
- As informações de cobrança que são usadas para gerenciamento de acesso

Você também pode optar por gerar uma conta de armazenamento padrão quando você criar o serviço.

Um único serviço pode gerenciar vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço para trabalhar com diferentes assinaturas, organizações ou até mesmo locais de implantação.  

> [AZURE.NOTE] Você precisa instâncias separadas do serviço do Gerenciador de StorSimple para gerenciar dispositivos de série de StorSimple 8000 e StorSimple Virtual matrizes.

Execute as seguintes etapas para criar um serviço.

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## <a name="delete-a-service"></a>Excluir um serviço

Antes de excluir um serviço, certifique-se de que nenhuma dispositivos conectados são usá-lo. Se o serviço está em uso, desative os dispositivos conectados. A operação de desativar a conexão entre o dispositivo e o serviço de servidor, mas preservar os dados do dispositivo na nuvem. 

> [AZURE.IMPORTANT] Depois que um serviço é excluído, a operação não pode ser revertida. 

Execute as seguintes etapas para excluir um serviço.

### <a name="to-delete-a-service"></a>Para excluir um serviço

1. Na página **serviço Gerenciador de StorSimple** , selecione o serviço que você deseja excluir.

1. Clique em **Excluir** na parte inferior da página.

1. Clique em **Sim** na notificação de confirmação. Pode levar alguns minutos para o serviço a ser excluída.

## <a name="get-the-service-registration-key"></a>Obter a chave do registro de serviço

Após ter criado com êxito um serviço, você precisará registrar seu dispositivo StorSimple com o serviço. Para registrar seu dispositivo StorSimple primeiro, você precisará a chave do registro de serviço. Para registrar dispositivos adicionais com um serviço de StorSimple existente, você precisará a chave do registro e a chave de criptografia de dados de serviço (que é gerada no primeiro dispositivo durante o registro). Para obter mais informações sobre a chave de criptografia de dados de serviço, consulte [obter a chave de criptografia de dados de serviço da web local da interface do usuário](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key). 

Execute as seguintes etapas para obter a chave do registro de serviço.

[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

Manter a chave do registro de serviço em um local seguro. Você precisará esta chave, bem como a chave de criptografia de dados de serviço, registre dispositivos adicionais com esse serviço. Depois de obter a chave do registro de serviço, você precisará configurar seu dispositivo através do Windows PowerShell para a interface StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Gerar novamente a chave do registro de serviço

Você precisará gerar uma chave de registro de serviço se você é necessários para executar a rotação de chaves ou se a lista de administradores de serviço foi alterada. Quando você gerar novamente a chave, a nova chave é usada apenas para registrar dispositivos subsequentes. Os dispositivos que já foram registrados não são afetados por este processo.

Execute as seguintes etapas para gerar uma chave de registro de serviço.

### <a name="to-regenerate-the-service-registration-key"></a>Para gerar novamente a chave do registro de serviço

1. Na página **serviço Gerenciador de StorSimple** , clique em **Chave do registro**.

1. Na caixa de diálogo **Chave do registro de serviço** , clique em **Gerar**.

1. Você verá uma mensagem de confirmação. Clique em **Okey** para continuar com a nova geração.

1. Uma nova chave de registro de serviço aparecerão.

1. Copie esta tecla e salvá-lo para registrar novos dispositivos com esse serviço.

1. Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-ova-manage-service/image7.png) para fechar a caixa de diálogo.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [começar](storsimple-ova-deploy1-portal-prep.md) com uma matriz virtual StorSimple.
    
- Saiba como [administrar seu dispositivo StorSimple](storsimple-ova-web-ui-admin.md).

 
