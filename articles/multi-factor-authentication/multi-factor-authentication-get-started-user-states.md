<properties 
    pageTitle="Estados de usuário de autenticação do Microsoft Azure multifator"
    description="Saiba mais sobre os estados de usuário no Azure MFA."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="user-states-in-azure-multi-factor-authentication"></a>Estados de usuário no Azure autenticação multifator

Contas de usuário no Azure multi-Factor Authentication tem os seguintes três estados distintos:

Estado | Descrição |Aplicativos de navegador não afetados| Anotações
:-------------: | :-------------: |:-------------: |:-------------: |
Desativado | O estado padrão para um novo usuário não registrado no autenticação multifator.|Não|O usuário não está usando a autenticação multifator.
Habilitado |O usuário foi cadastrado em autenticação multifator.|Não.  Eles continuam a funcionar até que o processo de registro seja concluído.|O usuário está habilitado, mas não concluiu o processo de registro. Ele será solicitado para concluir o processo ao entrar próximo.
Impostas|O usuário foi cadastrado e concluiu o processo de registro para usar a autenticação multifator.|Sim.  Aplicativos exigem senhas de aplicativos. | O usuário pode ou não tenha concluído registro. Se ele tem concluído o processo de registro, elas estiverem usando autenticação multifator. Caso contrário, o usuário será solicitado a concluir o processo ao entrar próximo.

## <a name="changing-a-user-state"></a>Alterando o estado do usuário
Um estado de usuários altera dependendo se ele foi configurado para MFA e se o usuário tiver concluído o processo.  Quando você ativar o MFA para um usuário, os usuários estado será alterado de desabilitaram para ativado.  Depois que o usuário, cujo estado é alterado para habilitado, entrarem nele e conclui o processo, seu estado será alterado para imposta.  

### <a name="to-view-a-users-state"></a>Para exibir o estado de um usuário
--------------------------------------------------------------------------------
1.  Entre no **portal clássico Azure** como administrador.
2.  À esquerda, clique em **Active Directory**.
3.  Em, **diretório** clique no diretório para o usuário que você deseja ativar.
![Clique em diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **usuários**.
5.  Na parte inferior da página, clique em **Gerenciar multifator Auth**.
![Clique em diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isso abrirá uma nova guia do navegador.  Você poderá exibir o estado de usuários.
![Clique em diretório](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###<a name="to-change-the-state-from-disabled-to-enabled"></a>Para alterar o estado de desabilitado para habilitado
1.  Entre no **portal clássico Azure** como administrador.
2.  À esquerda, clique em **Active Directory**.
3.  Em, **diretório** clique no diretório para o usuário que você deseja ativar.
![Clique em diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **usuários**.
5.  Na parte inferior da página, clique em **Gerenciar multifator Auth**.
![Clique em diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isso abrirá uma nova guia do navegador.  Localize o usuário que você deseja ativar para autenticação multifator. Talvez você precise alterar o modo de exibição na parte superior. Certifique-se de que o status é **desabilitado.** 
 ![Permitir que o usuário](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque um **Verificar** na caixa ao lado do nome dele.
7.  À direita, clique em **Ativar**.
![Habilitar usuário](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Clique em **Habilitar a autenticação multifator**.
![Habilitar usuário](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Você deve observar que o estado do usuário mudou de **desativado** para **ativado**.
![Permitir que os usuários](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Depois de você habilitou seus usuários, é recomendável que você notificá-los por email.  Ele também deverá informá-los como eles podem usar seus aplicativos não-localizador para evitar sendo bloqueada.

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Para alterar o estado de habilitado/impostas para desativado
1.  Entre no **portal clássico Azure** como administrador.
2.  À esquerda, clique em **Active Directory**.
3.  Em, **diretório** clique no diretório para o usuário que você deseja ativar.
![Clique em diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **usuários**.
5.  Na parte inferior da página, clique em **Gerenciar multifator Auth**.
![Clique em diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isso abrirá uma nova guia do navegador.  Localize o usuário que você deseja desativar. Talvez você precise alterar o modo de exibição na parte superior. Certifique-se de que o status é um dos **habilitado** ou **impostas.**
7.  Coloque um **Verificar** na caixa ao lado do nome dele.
7.  À direita, clique em **Desativar**.
![Desabilitar usuário](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Você será solicitado a confirmar isso.  Clique em **Sim**.
![Desabilitar usuário](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  Em seguida, você verá que ele foi bem-sucedido.  Clique em **feche.** 
 ![Desativar usuário](./media/multi-factor-authentication-get-started-user-states/userstate4.png)
