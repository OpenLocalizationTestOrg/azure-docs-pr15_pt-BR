<properties
    pageTitle="Azure AD Connect: Próximas etapas e como gerenciar o Azure AD Connect | Microsoft Azure"
    description="Saiba como estender a configuração padrão e tarefas operacionais do Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Próximas etapas e como gerenciar o Azure AD Connect
A seguir é operacionais tópicos avançada que permitem que você personalize Azure Active Directory conectar-se para atender às necessidades e os requisitos da sua organização.  

## <a name="add-additional-sync-administrators"></a>Adicionar administradores de sincronização adicionais
Por padrão somente o usuário que fez a instalação e administradores locais poderão gerenciar o mecanismo de sincronização instalado. Para outras pessoas possam acessar e gerenciar o mecanismo de sincronização, localize o grupo chamado ADSyncAdmins no servidor local e adicioná-las a esse grupo.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Atribuição de licenças para usuários do Azure AD Premium e mobilidade corporativos

Agora que seus usuários foram sincronizados na nuvem, você precisará atribuí-las uma licença para que eles podem Familiarize-se com aplicativos de nuvem como o Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Para atribuir um Azure AD Premium ou uma licença de pacote de mobilidade do Enterprise
--------------------------------------------------------------------------------
1. Entrar no Portal do Azure como administrador.
2. À esquerda, selecione **Active Directory**.
3. Na página do Active Directory, clique duas vezes no diretório com os usuários que você deseja ativar.
4. Na parte superior da página de diretório, selecione **licenças**.
5. Na página licenças, selecione Active Directory Premium ou o pacote de mobilidade do Enterprise e, em seguida, clique em **atribuir**.
6. Na caixa de diálogo, selecione os usuários que você deseja atribuir licenças e clique no ícone de marca de seleção para salvar as alterações.


## <a name="verifying-the-scheduled-synchronization-task"></a>Verificando a tarefa de sincronização agendada
Se você quiser verificar o status de uma sincronização, você pode fazer isso marcando no portal do Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Para verificar a tarefa de sincronização agendada
--------------------------------------------------------------------------------
1. Entrar no Portal do Azure como administrador.
2. À esquerda, selecione **Active Directory**.
3. Na página do Active Directory, clique duas vezes no diretório com os usuários que você deseja ativar.
4. Na parte superior da página de diretório, selecione **Integração de diretório**.
5. Em integração com anotação do active directory local a última vez em sincronização.

<center>![Nuvem](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Iniciando uma tarefa de sincronização agendada
Se você precisar executar uma tarefa de sincronização, você pode fazer isso executando através do Assistente do Azure AD Connect novamente.  Você precisará fornecer suas credenciais do Azure AD.  No assistente, selecione a tarefa de **Personalizar as opções de sincronização** e clique em Avançar no assistente. No final, certifique-se de que a caixa **Iniciar o processo de sincronização assim que a configuração inicial for concluída** está marcada.

<center>![Nuvem](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Para obter mais informações sobre a sincronização do Azure AD Connect: Agendador, consulte [Agendador de conectar-se do Azure AD](active-directory-aadconnectsync-feature-scheduler.md)


## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, você pode sempre inicie o assistente novamente do Azure AD Connect atalho do iniciar página ou área de trabalho.  Você notará que percorrer todo o assistente novamente fornece algumas novas opções do formulário de tarefas adicionais.  

A tabela a seguir fornece um resumo dessas tarefas e uma breve descrição sobre cada um deles.

![Participar de regra](./media/active-directory-aadconnect-whats-next/addtasks.png)


Tarefa adicional | Descrição
------------- | ------------- |
Exibir o cenário selecionado  |Permite que você exiba sua solução atual do Azure AD Connect.  Isso inclui configurações gerais, diretórios sincronizados, configurações de sincronização, etc.
Personalizar as opções de sincronização | Permite que você altere a configuração atual, incluindo a adição adicionais florestas do Active Directory para a configuração ou ativar opções de sincronização como usuário, grupo, dispositivo ou senha regravação.
Habilitar o modo de teste |  Isso permite que você as informações de fase posterior serão sincronizadas, mas nada será exportado Azure AD ou do Active Directory.  Isso permite que você visualize as sincronizações antes que ocorram.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
