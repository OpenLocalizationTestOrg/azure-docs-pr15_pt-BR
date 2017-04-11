<properties 
   pageTitle="Gerenciar sua conta de armazenamento StorSimple | Microsoft Azure"
   description="Explica como você pode usar a página Gerenciador de StorSimple configurar para adicionar, editar, excluir ou girar as chaves de segurança para uma conta de armazenamento associado a matriz Virtual StorSimple."
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
   ms.date="09/29/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>Usar o serviço do Gerenciador de StorSimple para gerenciar contas de armazenamento para Array Virtual StorSimple

## <a name="overview"></a>Visão geral

A página **Configurar** apresenta os parâmetros de serviço global que podem ser criados no serviço do Gerenciador de StorSimple. Esses parâmetros podem ser aplicados a todos os dispositivos conectados ao serviço e incluem:

- Contas de armazenamento 
- Registros de controle de acesso 

Este tutorial explica como você pode usar a página **Configurar** para adicionar, editar ou excluir contas de armazenamento para sua matriz Virtual StorSimple. As informações neste tutorial se aplica somente a matriz Virtual StorSimple executando software de versão de março de 2016 GA.

 ![Configurar página](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

Contas de armazenamento contêm as credenciais que o dispositivo usa para acessar sua conta de armazenamento com seu provedor de serviços de nuvem. Para contas de armazenamento do Microsoft Azure, estas são as credenciais como o nome da conta e a chave primária de acesso. 

Na página **Configurar** , todas as contas de armazenamento que são criadas para a assinatura de cobrança são exibidas em um formato tabular que contém as seguintes informações:

- **Nome** – o nome exclusivo atribuído à conta quando ela foi criada.
- **SSL habilitado** – se o SSL está ativado e comunicação de dispositivo à nuvem é sobre o canal seguro.

As tarefas mais comuns relacionadas a contas de armazenamento que podem ser realizadas na página **Definir** são:

- Adicionar uma conta de armazenamento 
- Editar uma conta de armazenamento 
- Excluir uma conta de armazenamento 


## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Há três tipos de contas de armazenamento que podem ser usados com o dispositivo StorSimple.

- **Contas de armazenamento geradas automaticamente** – como o nome sugere, este tipo de conta de armazenamento é gerado automaticamente quando o serviço é criado. Para saber mais sobre como esta conta de armazenamento é criada, consulte [criar um novo serviço](storsimple-ova-manage-service.md#create-a-service). 
- **Contas de armazenamento na assinatura do serviço** – essas são as contas de armazenamento do Azure que estão associadas a mesma assinatura do serviço. Para saber mais sobre como essas armazenamento contas são criadas, consulte [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md). 
- **Contas de armazenamento fora a assinatura do serviço** – essas são as contas de armazenamento do Azure que não estão associadas ao seu serviço e provavelmente existente antes do serviço foi criado.

Cada Array Virtual StorSimple cria um contêiner (com hcs um prefixo) na conta de armazenamento associado. Este contêiner tem todos os dados de nuvem para o seu dispositivo. Não exclua este contêiner acessando-o por meio do serviço de armazenamento do Azure como esta ação resultará em perda de dados.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento

Você pode adicionar uma conta de armazenamento para sua configuração de serviço do Gerenciador de StorSimple, fornecendo um nome amigável exclusivo e credenciais de acesso que estão vinculadas à conta de armazenamento. Você também tem a opção de habilitar o modo de seguro sockets layer (SSL) para criar um canal seguro para comunicação de rede entre seu dispositivo e a nuvem.

Você pode criar várias contas para um provedor de serviços de nuvem determinado. Enquanto a conta de armazenamento está sendo salvo, o serviço tenta se comunicar com seu provedor de serviços de nuvem. As credenciais e o material de acesso que você forneceu serão autenticados neste momento. Uma conta de armazenamento é criada somente se a autenticação for bem-sucedida. Se a autenticação falhar, será exibida uma mensagem de erro apropriada.

Também há suporte para contas de armazenamento do Gerenciador de recursos criadas no portal do Azure StorSimple. As contas de armazenamento do Gerenciador de recursos não aparecerá na lista suspensa de seleção, somente o armazenamento contas criadas no portal de clássico do Azure serão exibidas. Contas de armazenamento do Gerenciador de recursos precisa ser adicionada usando o procedimento para adicionar uma conta de armazenamento, conforme descrito abaixo.

O procedimento para adicionar uma conta de armazenamento do Azure clássico é detalhado abaixo.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Você pode editar uma conta de armazenamento utilizada pelo seu dispositivo. Se você editar uma conta de armazenamento que está sendo usada, os campos disponíveis para modificar são a tecla de acesso e o modo SSL para a conta de armazenamento. Você pode fornecer a nova tecla de acesso de armazenamento ou modificar a seleção de **modo de ativar SSL** e salvar as configurações atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Na página inicial do serviço, selecione o serviço, clique duas vezes no nome do serviço e clique em **Configurar**.

2. Clique em **Adicionar/editar contas de armazenamento**.

3. Na caixa de diálogo **Adicionar/editar contas de armazenamento** :

  1. Na lista suspensa de **Contas de armazenamento**, escolha uma conta existente que você deseja modificar. 
  2. Se necessário, você pode modificar a seleção de **Ativar o modo de SSL** .
  3. Você pode optar por gerar suas chaves de acesso de conta de armazenamento. Para obter mais informações, consulte [gerar as chaves de conta de armazenamento](storage-create-storage-account.md#manage-your-storage-access-keys). Fornece a nova chave de conta de armazenamento. Para uma conta de armazenamento do Azure, esta é a chave primária de acesso. 
  4. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-manage-storage-accounts/checkicon.png) para salvar as configurações. As configurações serão atualizadas na página **Definir** . 
  5. Na parte inferior da página, clique em **Salvar** para salvar as configurações recém atualizadas. 

     ![Editar uma conta de armazenamento](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

> [AZURE.IMPORTANT] Você pode excluir uma conta de armazenamento somente se ele não está em uso. Se uma conta de armazenamento estiver em uso, você será notificado.

#### <a name="to-delete-a-storage-account"></a>Para excluir uma conta de armazenamento

1. Na página Gerenciador de StorSimple serviço inicial, selecione o serviço, clique duas vezes no nome do serviço e clique em **Configurar**.

2. Na lista de contas de armazenamento tabular, passe o mouse sobre a conta que você deseja excluir.

3. Um ícone Excluir (**x**) aparecerá na coluna à direita extrema dessa conta de armazenamento. Clique no ícone **x** para excluir as credenciais.

4. Quando solicitado para confirmação, clique em **Sim** para continuar com a exclusão. A listagem tabular será atualizada para refletir as alterações.

5. Na parte inferior da página, clique em **Salvar** para salvar as configurações recém atualizadas.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).
