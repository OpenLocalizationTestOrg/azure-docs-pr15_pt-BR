<properties 
   pageTitle="Gerenciar sua conta de armazenamento StorSimple | Microsoft Azure"
   description="Explica como você pode usar a página de Gerenciador de StorSimple configurar para adicionar, editar, excluir ou girar as chaves de segurança para uma conta de armazenamento."
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
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Usar o serviço do Gerenciador de StorSimple para gerenciar sua conta de armazenamento

## <a name="overview"></a>Visão geral

A página **Configurar** apresenta todos os parâmetros de serviço globais que podem ser criados no serviço do Gerenciador de StorSimple. Esses parâmetros podem ser aplicados a todos os dispositivos conectados ao serviço e incluem:

- Contas de armazenamento 
- Modelos de largura de banda 
- Registros de controle de acesso 

Este tutorial explica como você pode usar a página **Configurar** para adicionar, editar ou excluir contas de armazenamento ou girar as chaves de segurança para uma conta de armazenamento.

 ![Configurar página](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Contas de armazenamento contêm as credenciais que o dispositivo usa para acessar sua conta de armazenamento com seu provedor de serviços de nuvem. Para contas de armazenamento do Microsoft Azure, estas são as credenciais como o nome da conta e a chave primária de acesso. 

Na página **Configurar** , todas as contas de armazenamento que são criadas para a assinatura de cobrança são exibidas em um formato tabular que contém as seguintes informações:

- **Nome** – o nome exclusivo atribuído à conta quando ela foi criada.
- **SSL habilitado** – se o SSL está ativado e comunicação de dispositivo à nuvem é sobre o canal seguro.
- **Usado por** – o número de volumes usando a conta de armazenamento.

As tarefas mais comuns relacionadas a contas de armazenamento que podem ser realizadas na página **Definir** são:

- Adicionar uma conta de armazenamento 
- Editar uma conta de armazenamento 
- Excluir uma conta de armazenamento 
- Rotação de chaves de contas de armazenamento 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Há três tipos de contas de armazenamento que podem ser usados com o dispositivo StorSimple.

- **Contas de armazenamento geradas automaticamente** – como o nome sugere, este tipo de conta de armazenamento é gerado automaticamente quando o serviço é criado. Para saber mais sobre como esta conta de armazenamento é criada, consulte [etapa 1: criar um novo serviço](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) em [implantar seu dispositivo de StorSimple local](storsimple-deployment-walkthrough.md). 
- **Contas de armazenamento na assinatura do serviço** – essas são as contas de armazenamento do Azure que estão associadas a mesma assinatura do serviço. Para saber mais sobre como essas armazenamento contas são criadas, consulte [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md). 
- **Contas de armazenamento fora a assinatura do serviço** – essas são as contas de armazenamento do Azure que não estão associadas ao seu serviço e provavelmente existente antes do serviço foi criado.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento

Você pode adicionar uma conta de armazenamento, fornecendo um nome amigável exclusivo e credenciais de acesso que estão vinculadas à conta de armazenamento (com o provedor de serviços de nuvem especificada). Você também tem a opção de habilitar o modo de seguro sockets layer (SSL) para criar um canal seguro para comunicação de rede entre seu dispositivo e a nuvem.

Você pode criar várias contas para um provedor de serviços de nuvem determinado. Esteja ciente, no entanto, depois de uma conta de armazenamento for criada, você não pode alterar o provedor de serviços de nuvem.

Enquanto a conta de armazenamento está sendo salvo, o serviço tenta se comunicar com seu provedor de serviços de nuvem. As credenciais e o material de acesso que você forneceu serão autenticados neste momento. Uma conta de armazenamento será criada somente se a autenticação for bem-sucedida. Se a autenticação falhar, será exibida uma mensagem de erro apropriada.

Também há suporte para contas de armazenamento do Gerenciador de recursos criadas no portal do Azure StorSimple. As contas de armazenamento do Gerenciador de recursos não aparecerá na lista suspensa de seleção quando tentar criar um contêiner de volume, somente as contas de armazenamento criadas no portal do clássico Azure será exibido. Contas de armazenamento do Gerenciador de recursos precisa ser adicionada usando o procedimento para adicionar uma conta de armazenamento descrita a seguir.

> [AZURE.NOTE] O procedimento para adicionar uma conta de armazenamento varia de acordo com a versão do software de StorSimple que você está usando. Certifique-se de seguir o procedimento correto para a sua versão de StorSimple.


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Você pode editar uma conta de armazenamento que é usada por um contêiner de volume. Se você editar uma conta de armazenamento que está sendo usada, o único campo disponível para modificar é a tecla de acesso para a conta de armazenamento. Você pode fornecer a nova tecla de acesso de armazenamento e salvar as configurações atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Na página inicial do serviço, selecione o serviço, clique duas vezes no nome do serviço e clique em **Configurar**.

2. Clique em **Adicionar/editar contas de armazenamento**.

3. Na caixa de diálogo **Adicionar/editar contas de armazenamento** :

  1. Na lista suspensa de **Contas de armazenamento**, escolha uma conta existente que você deseja modificar. Isso também pode incluir as contas de armazenamento que foram geradas automaticamente quando o serviço foi criado pela primeira vez.
  2. Se necessário, você pode modificar a seleção de **Ativar o modo de SSL** .
  3. Você pode optar por girar suas chaves de acesso de conta de armazenamento. Consulte [rotação de chave de contas de armazenamento](#key-rotation-of-storage-accounts) para obter mais informações sobre como executar a rotação de chaves.
  4. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) para salvar as configurações. As configurações serão atualizadas na página **Definir** . Clique em **Salvar** para salvar as configurações recém atualizadas.

     ![Editar uma conta de armazenamento](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

> [AZURE.IMPORTANT] Você pode excluir uma conta de armazenamento somente se ele não é usado por um contêiner de volume. Se uma conta de armazenamento está sendo usada por um contêiner de volume, exclua primeiro contêiner de volume e, em seguida, excluir a conta de armazenamento associado.

#### <a name="to-delete-a-storage-account"></a>Para excluir uma conta de armazenamento

1. Na página Gerenciador de StorSimple serviço inicial, selecione o serviço, clique duas vezes no nome do serviço e clique em **Configurar**.

2. Na lista de contas de armazenamento tabular, passe o mouse sobre a conta que você deseja excluir.

3. Um ícone Excluir (**x**) aparecerá na coluna à direita extrema dessa conta de armazenamento. Clique no ícone **x** para excluir as credenciais.

4. Quando solicitado para confirmação, clique em **Sim** para continuar com a exclusão. A listagem tabular será atualizada para refletir as alterações.

## <a name="key-rotation-of-storage-accounts"></a>Rotação de chaves de contas de armazenamento

Por razões de segurança, a rotação de chaves é normalmente um requisito em data centers. 

> [AZURE.NOTE] As seguintes informações de rotação de chaves e o procedimento de rotação aplicam somente contas do Microsoft Azure armazenamento. Se você estiver usando outro provedor de serviços de nuvem, você pode gerenciar chaves de conta de armazenamento por meio do painel de controle do provedor.
 
Cada assinatura do Microsoft Azure pode ter uma ou mais contas de armazenamento associado. O acesso a essas contas é controlado pelas teclas de acesso e de assinatura para cada conta de armazenamento. 

Quando você cria uma conta de armazenamento, Microsoft Azure gera duas teclas de acesso de armazenamento de 512 bits que são usadas para autenticação quando a conta de armazenamento for acessada. Ter duas teclas de acesso de armazenamento permite gerar as chaves com acesso a esse serviço ou sem interrupção do seu serviço de armazenamento. A chave que está sendo usada é a chave *primária* e a chave de backup é conhecida como a chave *secundária* . Uma dessas duas chaves deve ser fornecida quando seu dispositivo Microsoft Azure StorSimple acessa seu provedor de serviço de armazenamento de nuvem.

## <a name="what-is-key-rotation"></a>O que é a rotação de chaves?

Normalmente, aplicativos usam apenas uma das chaves para acessar seus dados. Após um determinado período de tempo, você pode fazer com que seus aplicativos alterna para o uso da chave de segunda. Depois que você tiver trocou seus aplicativos para a chave secundária, você pode retirar a primeira tecla e, em seguida, gerar uma nova chave. Usar as duas chaves dessa maneira permite que seus aplicativos acessem os dados sem gerar qualquer tempo de inatividade.

As chaves de conta de armazenamento sempre são armazenadas no serviço em um formulário criptografado. No entanto, eles podem ser redefinidos por meio do serviço do Gerenciador de StorSimple. O serviço pode obter a chave primária e chave secundária para todas as contas de armazenamento na mesma assinatura, incluindo contas criadas no serviço de armazenamento, bem como o armazenamento padrão criadas contas geradas quando o serviço do Gerenciador de StorSimple foi primeiro. O serviço do Gerenciador de StorSimple será sempre obtém essas chaves do portal clássico do Azure e armazená-las de forma criptografada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação

Um administrador do Microsoft Azure pode gerar ou alterar a chave primária ou secundária acessando diretamente a conta de armazenamento (por meio do serviço de armazenamento do Microsoft Azure). O serviço do Gerenciador de StorSimple não verá essa alteração automaticamente.

Para informar o serviço do Gerenciador de StorSimple da alteração, você será necessário para acessar o serviço do Gerenciador de StorSimple, acessar a conta de armazenamento e sincronize a chave primária ou secundária (dependendo de qual foi alterado). Em seguida, o serviço obtém a chave mais recente, criptografa as chaves e envia a chave criptografada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Para sincronizar teclas para contas de armazenamento na mesma assinatura como o serviço (somente Azure)

1. Na página de **Serviços** , clique na guia **Configurar** .

2. Clique em **Adicionar/editar contas de armazenamento**.

3. Na caixa de diálogo, faça o seguinte:

  1. Selecione a conta de armazenamento com a chave que você deseja sincronizar. As teclas de conta de armazenamento são criptografadas quando elas são exibidas.
  2. No serviço de Gerenciador de StorSimple, você precisa atualizar a chave que foi alterada anteriormente no serviço de armazenamento do Microsoft Azure. Se a chave primária de acesso foi alterada (gerado novamente), clique em **sincronizar a chave primária**. Se a chave secundária foi alterada, clique em **sincronizar chave secundária**.

    ![Sincronizar teclas](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar as chaves para contas de armazenamento fora a assinatura do serviço

1. Na página de **Serviços** , clique na guia **Configurar** .

2. Clique em **Adicionar/editar contas de armazenamento**.

3. Na caixa de diálogo, faça o seguinte:

  1. Selecione a conta de armazenamento com a chave de acesso que você deseja atualizar.
  2. Você precisará atualizar a tecla de acesso de armazenamento no serviço do Gerenciador de StorSimple. Nesse caso, você pode ver a tecla de acesso de armazenamento. Insira a nova chave na caixa **Chave de acesso da conta de armazenamento**y. 
  3. Salve as alterações. Sua chave de acesso da conta de armazenamento agora deve ser atualizado.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [segurança de StorSimple](storsimple-security.md).
- Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
