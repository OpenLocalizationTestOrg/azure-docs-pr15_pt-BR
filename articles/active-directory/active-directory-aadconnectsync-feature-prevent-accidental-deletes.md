<properties
   pageTitle="Sincronização do Azure AD Connect: impedir exclusões acidentais | Microsoft Azure"
   description="Este tópico descreve o recurso impedir exclusões acidentais (impedir exclusões acidentais) Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronização do Azure AD Connect: impedir exclusões acidentais
Este tópico descreve o recurso impedir exclusões acidentais (impedir exclusões acidentais) Azure AD Connect.

Quando a instalação do Azure AD Connect, impedem acidentais exclusões é ativada por padrão e configurado para não permitir uma exportação com mais de 500 exclusões. Este recurso destina-se a proteger você contra alterações acidentais de configuração e alterações ao seu diretório local que possa afetar muitos usuários e outros objetos.

Cenários comuns quando você vir exclui muitos incluem:

- Alterações à [filtragem](active-directory-aadconnectsync-configure-filtering.md) onde um inteiro [unidade Organizacional](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) ou [domínio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) está desmarcada.
- Todos os objetos em uma unidade Organizacional são excluídos.
- Uma unidade Organizacional é renomeada para que todos os objetos contidos são considerados sair do escopo de sincronização.

O valor padrão de 500 objetos pode ser alterado com o PowerShell usando `Enable-ADSyncExportDeletionThreshold`. Você deve configurar esse valor para ajustar o tamanho da sua organização. Como o Agendador de sincronização é executado a cada 30 minutos, o valor é o número de exclusões vistos em 30 minutos.

Se houver muitos exclui preparado para ser exportados para o Azure AD, então a exportação é interrompida e você recebe um email como esta:

![Impedir exclusões acidentais email](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Hello (contato técnico). Em (tempo) o serviço de sincronização de identidade detectado que o número de exclusões excedeu o limite de exclusão configurado para (nome da organização). Um total de (número) de objetos foram enviadas para exclusão nesta sincronização de identidade executar. Isso atendidas ou excedeu o valor de limite de exclusão configurado de objetos (número). Precisamos que você forneça confirmação que essas exclusões devem ser processadas antes de nós continuará. Consulte as impedir exclusões acidentais para obter mais informações sobre o erro listado nesta mensagem de email.*

Você também pode ver o status `stopped-deletion-threshold-exceeded` quando você examinar o **Gerenciador de serviços de sincronização** da interface do usuário para o perfil de exportação.
![Impedir exclusões acidentais UI Gerenciador de serviços de sincronização](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se foi inesperado, investigar e tome ações corretivas. Para ver os objetos que estão prestes a ser excluído, faça o seguinte:

1. Inicie o **serviço de sincronização** do Menu Iniciar.
2. Vá para **conectores**.
3. Selecione o conector com tipo **Azure Active Directory**.
4. Em **ações** à direita, selecione o **Espaço de pesquisa de conector**.
5. No menu pop-up em **escopo**, selecione **Desconectado desde** e selecione um tempo no passado. Clique em **Pesquisar**. Esta página fornece um modo de exibição de todos os objetos prestes a ser excluída. Clicando em cada item, você pode obter informações adicionais sobre o objeto. Você também pode clicar em **Configuração de coluna** para adicionar atributos adicionais para estar visível na grade.

![Espaço do conector de pesquisa](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Se todas as exclusões são desejadas, faça o seguinte:

1. Para desativar essa proteção temporariamente e permitir que essas exclusões percorrer, execute o cmdlet do PowerShell: `Disable-ADSyncExportDeletionThreshold`. Forneça uma conta de Administrador Global do Azure AD e senha.
![Credenciais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Com o Azure Active Directory Connector ainda selecionado, selecione a ação **Executar** e selecione **Exportar**.
3. Para ativar novamente a proteção, execute o cmdlet do PowerShell: `Enable-ADSyncExportDeletionThreshold`.

## <a name="next-steps"></a>Próximas etapas

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
