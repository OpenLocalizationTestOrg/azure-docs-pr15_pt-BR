<properties
    pageTitle="Azure AD Connect operações de integridade."
    description="Este artigo descreve operações adicionais que podem ser executadas depois de ter implantado Azure AD conectar integridade."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-operations"></a>Azure AD Connect operações de integridade

O tópico a seguir descreve as várias operações que podem ser executadas usando o Azure AD conectar integridade.

## <a name="enable-email-notifications"></a>Ativar notificações por Email
Você pode configurar o serviço Azure AD de integridade se conectar para enviar notificações por email quando os alertas são geradas indicando que sua infraestrutura de identidade não está íntegra. Isso ocorrerá quando um alerta é gerado, bem como quando ela é marcada como resolvido. Siga as instruções abaixo para configurar notificações por email.

![Azure AD Connect integridade Email descobrir de notificação](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] Notificações por email são desabilitadas por padrão.


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para habilitar o Azure AD conectar integridade notificações por Email

1. Abra a lâmina de alertas para o serviço para o qual você deseja receber notificação por email.
2. Clique no botão "Configurações de notificação" da barra de ação.
3. Ative a opção de notificação por Email para ativado.
4. Marque a caixa de seleção para configurar todos os administradores globais para receber notificações por email.
5. Se você deseja receber notificações de email em qualquer outro endereço de email, você pode especificá-las na caixa de destinatário de Email adicional. Para remover um endereço de email dessa lista, clique com o botão direito na entrada e selecione Excluir.
6. Para finalizar a alterações, clique no "Salvar". Todas as alterações terão efeitos somente depois que você seleciona "Salvar".

## <a name="delete-a-server-or-service-instance"></a>Excluir uma instância do servidor ou serviço

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>Excluir um servidor do Azure AD conectar integridade do serviço
Em alguns casos, você poderá remover um servidor do sob monitoramento. Siga as instruções abaixo para remover um servidor do Azure AD conectar integridade do serviço.

Ao excluir um servidor, lembre-se dos seguintes procedimentos:

- Esta ação INTERROMPERÁ a coleta de qualquer dado adicional do servidor. Este servidor será removido do serviço de monitoramento. Após essa ação, não será possível exibir novos alertas, monitoramento ou uso de dados de análise para este servidor.
- Esta ação não será desinstalar ou remover o agente de integridade do seu servidor. Se você não tiver desinstalado o agente de integridade antes de realizar esta etapa, você poderá ver eventos de erro no servidor relacionado ao agente de integridade.
- Esta ação não excluirá os dados coletados já deste servidor. Esses dados serão excluídos de acordo com a política de retenção de dados do Microsoft Azure.
- Depois de executar essa ação, se você deseja começar a monitorar o mesmo servidor novamente, você precisará desinstalar e reinstalar o agente de integridade neste servidor.


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>Excluir um servidor do Azure AD conectar integridade do serviço

Azure AD Connect integridade para o AD FS & Azure AD conexão (Sincronizar):

1. Abra a lâmina do servidor da lâmina de lista do servidor, selecionando o nome do servidor a ser removido.
2. No Blade Server, clique no botão "Excluir" da barra de ação.
3. Confirme a ação para excluir o servidor digitando o nome do servidor na caixa de confirmação.
4. Clique no botão "Excluir".

Azure AD Connect integridade para o AD DS:

1. Abra o painel de controladores de domínio.
2. Selecione o controlador de domínio a ser removido.
3. Clique no botão "Excluir selecionado" da barra de ação.
4. Confirme a ação para excluir o servidor.
5. Clique no botão "Excluir".

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Excluir uma instância de serviço do serviço de integridade conectar Azure AD

Em alguns casos, você poderá remover uma instância do serviço. Siga as instruções abaixo para remover uma instância do serviço do Azure AD conectar integridade do serviço.

Ao excluir uma instância de serviço, lembre-se dos seguintes procedimentos:

- Esta ação removerá a instância atual do serviço do serviço de monitoramento.
- Esta ação não será desinstalar ou remover o agente de integridade de qualquer um dos servidores que foram monitorados como parte desta instância de serviço. Se você não tiver desinstalado o agente de integridade antes de realizar esta etapa, você poderá ver eventos de erro nos servidores relacionados ao agente de integridade.
- Todos os dados desta instância de serviço serão excluídos de acordo com a política de retenção de dados do Microsoft Azure.
- Depois de executar essa ação, se você quiser começar a monitorar o serviço, desinstale e reinstale o agente de integridade em todos os servidores que serão monitorados. Depois de executar essa ação, se você deseja começar a monitorar o mesmo servidor novamente, você precisará desinstalar e reinstalar o agente de integridade neste servidor.


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>Para excluir uma instância de serviço do serviço de integridade conectar Azure AD

1. Abra a lâmina de serviço da lâmina de lista do serviço selecionando o identificador de serviço (nome de farm) que você deseja remover.
2. No Blade Server, clique no botão "Excluir" da barra de ação.
3. Confirme o nome do serviço digitando-a na caixa de confirmação. (por exemplo: sts.contoso.com)
4. Clique no botão "Excluir".
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Gerenciar o acesso com base em funções controle de acesso
### <a name="overview"></a>Visão geral
[Controle de acesso com base em função](role-based-access-control-configure.md) do Azure AD conectar integridade fornece acesso serviço de integridade de conectar-se do Azure AD para usuários e/ou grupos fora os administradores globais. Isso é alcançado atribuindo funções para os usuários pretendidos e/ou grupos e fornece um mecanismo para limitar os administradores globais no diretório.

#### <a name="roles"></a>Funções
Azure AD conectar integridade é compatível com as seguintes funções internas.

| Função | Permissões |
| ----------- | ---------- |
| Proprietário | Proprietários podem ***Gerenciar o acesso*** (por exemplo, atribuir função a um usuário/grupo), ***Exibir todas as informações*** (por exemplo, exibir alertas) do portal e ***alterar as configurações*** (por exemplo, notificações de email) dentro do Azure AD conectar funcionamento. <br>Por padrão, os administradores globais do Azure AD são atribuídos a essa função e isso não pode ser alterado.  |
|Colaborador|  Os colaboradores podem ***Exibir todas as informações*** (por exemplo, exibir alertas) do portal e ***alterar as configurações*** (por exemplo, notificações de email) dentro do Azure AD conectar funcionamento.|
|Leitor| Leitores podem ***Exibir todas as informações*** (por exemplo, exibir alertas) a partir do portal dentro do Azure AD conectar funcionamento.|

Todas as outras funções (como 'DevTest Labs usuários' ou 'Administradores de acesso de usuário'), mesmo se disponível na experiência do portal, não têm impacto acessem dentro do Azure AD conectar funcionamento.

#### <a name="access-scope"></a>Escopo de acesso

Azure AD Connect oferece suporte a gerenciamento de acesso em dois níveis:

- ***Todas as instâncias de serviço***: Este é o caminho recomendado para a maioria dos clientes e controla o acesso para todas as instâncias de serviço (por exemplo, um farm ADFS) em todos os tipos de funções que estão sendo monitorados por Azure AD conectar integridade.

- ***Instância do serviço***: em alguns casos, talvez você precise separar o acesso com base em tipos de função ou por uma instância do serviço. Nesse caso, você pode gerenciar o acesso do nível de instância do serviço.  

Permissão é concedida se um usuário final tem acesso seja no nível do diretório ou instância do serviço.


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>Como permitir o acesso de usuários ou grupos a integridade de conectar-se do Azure AD
#### <a name="steps-1-select-the-appropriate-access-scope"></a>As etapas de 1: Selecione o escopo de acesso apropriado
Para permitir que um usuário pode acessar no nível de *todas as instâncias de serviço* dentro do Azure AD conectar funcionamento, abra o blade principal no Azure AD conectar integridade.<br>
#### <a name="step-2-add-users-groups-and-assign-roles"></a>Etapa 2: Adicionar usuários, grupos e atribua funções
1. Clique na parte "Usuários" da seção de configurar.<br>
![Azure AD Connect integridade RBAC principal Blade](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Selecione "Adicionar"
3. Selecione a "função" como "Proprietário"<br>
![Azure AD Connect integridade RBAC adicionar usuário](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Digite o nome ou o identificador de destino do usuário ou grupo. Você pode selecionar um ou mais usuários ou grupos ao mesmo tempo. Clique em "select".
![Azure AD Connect integridade RBAC Selecionar usuário](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Selecione "Okey".<br>

6. Uma vez concluída a atribuição de função, os usuários e/ou grupos aparecerão na lista.<br>
![Lista de usuários RBAC de integridade do Azure AD Connect](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Estas etapas permitirá que os usuários listados e acesso de grupo de acordo com as respectivas funções atribuídas.
>[AZURE.NOTE]
- Os administradores globais sempre têm acesso completo a todas as operações, mas contas de administrador global não estarão presentes na lista acima.
- Não há suporte para o recurso de "Convidar usuários" dentro do Azure AD conectar funcionamento.

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Etapa 3: Compartilhar a localização de blade com usuários ou grupos
1. Após atribuir permissões, um usuário pode acessar o Azure AD conectar Health indo para [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Uma vez na lâmina, o usuário pode fixar o blade ou diferentes partes ao painel simplesmente clicando "Pin ao painel"<br>
![Blade de pin do Azure AD conectar integridade RBAC](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Um usuário com a função de "Leitor" atribuída não será capaz de executar a operação "criar" para obter a extensão do Azure AD conectar integridade do Azure Marketplace. Este usuário ainda conseguem acessar o blade indo para o link acima. Para uso subsequente, o usuário pode fixar a lâmina ao painel.

### <a name="remove-users-andor-groups"></a>Remover usuários e/ou grupos
Você pode remover um usuário ou grupo adicionado à parte do Azure AD conectar integridade com base em controle de acesso função direita clicando e selecionando remover.<br>
![Azure AD Connect integridade RBAC remover usuário](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>Links relacionados

* [Azure AD Connect integridade](active-directory-aadconnect-health.md)
* [Instalação do agente de integridade do Azure AD Connect](active-directory-aadconnect-health-agent-install.md)
* [Usando o Azure AD conectar integridade com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD conectar Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usando o Azure AD conectar integridade com o AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect integridade perguntas Frequentes](active-directory-aadconnect-health-faq.md)
* [Histórico de versões de integridade do Azure AD Connect](active-directory-aadconnect-health-version-history.md)
