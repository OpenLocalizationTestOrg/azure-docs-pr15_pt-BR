<properties
    pageTitle="Sincronização do Azure AD Connect: configurar a filtragem | Microsoft Azure"
    description="Explica como configurar a filtragem na sincronização do Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronização do Azure AD Connect: configurar a filtragem
Com a filtragem, você pode controlar quais objetos deverão aparecer no Azure AD do seu diretório local. A configuração padrão leva todos os objetos em todos os domínios as florestas configurados. Em geral, esta é a configuração recomendada. Usuários finais usando cargas de trabalho do Office 365, como o Exchange Online e o Skype for Business, a vantagem de uma lista de endereços Global completa para que eles possam enviar email e chamar todos. Com a configuração padrão, eles teria a mesma experiência que eles fariam com uma implementação de local do Exchange ou do Lync.

Em alguns casos, é necessário fazer algumas alterações para a configuração padrão. Aqui estão alguns exemplos:

- Você planeja usar a [topologia de diretório do AD múltipla Azure](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Você precisará aplicar um filtro para controlar qual objeto deve ser sincronizado para uma determinada directory do Azure AD.
- Executar um piloto do Azure ou o Office 365 e você quer apenas um subconjunto de usuários no Azure AD. No piloto pequeno, não é importante ter uma lista de endereços Global completa para demonstrar a funcionalidade.
- Você tem muitas contas de serviço e outras contas de não pessoais que você não deseja no Azure AD.
- Por razões de conformidade que você não exclua qualquer usuário contas locais. Você só pode desativá-los. Mas no Azure AD desejar somente contas ativas esteja presente.

Este artigo aborda como configurar os diferentes métodos de filtragem.

> [AZURE.IMPORTANT]A Microsoft não suporta modificação ou operação de sincronização do Azure AD Connect fora essas ações documentadas anteriormente. Qualquer uma das seguintes ações pode resultar em um estado inconsistente ou sem suporte de sincronização do Azure AD Connect e como resultado, a Microsoft não pode fornecer suporte técnico para essas implantações.

## <a name="basics-and-important-notes"></a>Noções básicas e anotações importantes
Na sincronização do Azure AD Connect, você pode habilitar a filtragem a qualquer momento. Se você começar com uma configuração padrão de sincronização de diretório e configurar a filtragem, os objetos que são filtrados não estão mais sincronizados ao Azure AD. Como resultado dessa alteração, os objetos no Azure AD que anteriormente foram sincronizados, mas foram filtrados são excluídos no Azure AD.

Antes de começar a fazer é alterado para filtragem, verifique se você [desativar a tarefa agendada](#disable-scheduled-task) para que você não acidentalmente exportar as alterações que você ainda não verificou para estar correta.

Desde que a filtragem pode remover vários objetos ao mesmo tempo, você quer certificar-se de que seus novos filtros estão corretos antes de você inicia a exportação quaisquer alterações ao Azure AD. Depois de concluir as etapas de configuração, é altamente recomendável que você siga as [etapas de verificação](#apply-and-verify-changes) antes de exportar e fazer alterações em Azure AD.

Para proteger você contra excluindo muitos objetos por acidente, o recurso [impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) está ativada, por padrão. Se você excluir muitos objetos devido a filtragem (500 por padrão), você precisa siga as etapas neste artigo para permitir que o exclui percorrer para Azure AD.

Se você usa uma compilação antes de novembro de 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), fazer uma mudança de configuração do filtro e você usa a sincronização de senha, você precisa acionar uma sincronização completa de todas as senhas depois de concluir a configuração. Para obter etapas sobre como disparar uma sincronização completa de senha consulte [disparar uma sincronização completa de todas as senhas](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Se você estiver no 1.0.9125 ou posterior, a ação de **sincronização completa** de regulares também calcula se senhas deverão ser sincronizadas e esta etapa adicional não é mais necessária.

Se os objetos de **usuário** foram excluídos inadvertidamente no Azure AD devido a um erro de filtragem, você pode recriar os objetos de usuário no Azure AD removendo suas configurações de filtragem e sincronize suas pastas novamente. Esta ação restaura os usuários da Lixeira no Azure AD. No entanto, é possível cancelar a outros tipos de objeto. Por exemplo, se você excluir acidentalmente um grupo de segurança e ela era usada para ACL um recurso, o grupo e suas ACLs não podem ser recuperadas.

Azure AD Connect exclui apenas os objetos que ele tenha uma vez considerado no escopo. Se houver objetos no Azure AD que foram criados por outro mecanismo de sincronização e esses objetos não estão no escopo, adicionar filtragem não removê-los. Por exemplo, se você começar com um servidor DirSync e ele criado uma cópia completa do seu diretório inteiro no Azure AD e instalar um novo servidor de sincronização do Azure AD Connect em paralelo com filtragem habilitada desde o começo, ele não remove os objetos extras criados por DirSync.

A configuração de filtragem é mantida quando você instala ou atualizar para uma versão mais recente do Azure AD Connect. Sempre é uma prática recomendada para verificar que a configuração não foi inadvertidamente alterada após uma atualização para uma versão mais recente antes de executar a primeira sincronização circular.

Se você tiver mais de uma floresta, as configurações de filtragem descritas neste tópico devem ser aplicadas a cada floresta (presumindo que você deseja que a mesma configuração para todos eles).

### <a name="disable-scheduled-task"></a>Desabilitar a tarefa agendada
Para desativar o agendador interno que aciona um ciclo de sincronização a cada 30 minutos, siga estas etapas:

1. Vá para um PowerShell prompt.
2. Executar `Set-ADSyncScheduler -SyncCycleEnabled $False` para desabilitar o Agendador.
3. Faça as alterações conforme documentadas neste tópico.
4. Executar `Set-ADSyncScheduler -SyncCycleEnabled $True` para habilitar o Agendador novamente.

**Se você usa uma compilação do Azure AD Connect antes de 1.1.105.0**  
Para desativar a tarefa agendada que aciona um ciclo de sincronização cada 3 horas, siga estas etapas:

1. Inicie o **Agendador de tarefas** no menu Iniciar.
2. Diretamente em **Biblioteca do Agendador de tarefas**, localize a tarefa chamada **Agendador do Azure AD Sync**, o botão direito e selecione **Desativar**.  
![Agendador de tarefas](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Agora você pode fazer alterações de configuração e executar o mecanismo de sincronização manualmente a partir do console do **Gerenciador de serviços de sincronização** .

Depois de concluir todas as suas alterações filtragem, não se esqueça de vêm voltar e **Habilitar** a tarefa novamente.

## <a name="filtering-options"></a>Opções de filtragem
Os seguintes tipos de configuração de filtragem podem ser aplicados a ferramenta de sincronização de diretórios:

- [**Grupo baseado**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): filtragem baseada em um único grupo só pode ser configurado na instalação inicial usando o Assistente de instalação. Ele não é mais coberto neste tópico.

- [**Baseada em domínio**](#domain-based-filtering): essa opção permite que você selecione quais domínios que sincronizar ao Azure AD. Ele também permite adicionar e remover domínios da configuração do mecanismo de sincronização, se você fizer alterações em sua infraestrutura de local depois que você instalou o Azure AD Connect sincronização.

- [**Baseado em unidade organizacional**](#organizational-unitbased-filtering): esta opção de filtragem permite que você selecione quais unidades organizacionais sincronizar com o Azure AD. Essa opção é para todos os tipos de objeto em unidades organizacionais selecionados.

- [**Baseado no atributo**](#attribute-based-filtering): essa opção permite filtrar objetos com base em valores de atributo nos objetos. Você também pode ter diferentes filtros para diferentes tipos de objeto.

Você pode usar várias opções de filtragem ao mesmo tempo. Por exemplo, você pode usar a filtragem baseada em unidade Organizacional para incluir apenas os objetos em uma unidade Organizacional e com a mesma hora com base no atributo filtragem para filtrar ainda mais os objetos. Quando você usa vários métodos de filtragem, os filtros usam um e lógico entre os filtros.

## <a name="domain-based-filtering"></a>Filtragem baseada em domínio
Esta seção fornece as etapas para configurar seu filtro de domínio. Se você tiver adicionado ou removido de domínios em sua floresta após ter instalado o Azure AD Connect, você também precisa atualizar a configuração de filtragem.

A melhor maneira de alterar a filtragem baseada em domínio está executando a instalação assistente e alterar o [domínio e unidades organizacionais filtragem](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). O Assistente de instalação é automatizar todas as tarefas documentadas neste tópico.

Você só deve seguir estas etapas se por algum motivo não for possível executar o Assistente de instalação.

Configuração de filtragem baseada em domínio consiste estas etapas:

- [Selecionar os domínios](#select-domains-to-be-synchronized) que devem ser incluídos na sincronização.
- Para cada domínio adicionado e removido, ajuste a [Executar perfis](#update-run-profiles).
- [Aplicar e verifique alterações](#apply-and-verify-changes).

### <a name="select-domains-to-be-synchronized"></a>Selecionar domínios para serem sincronizados
**Para definir o filtro de domínio, siga estas etapas:**

1. Entrar no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Serviço de sincronização** do menu Iniciar.
3. Selecione **conectores** e na lista de **conectores** , selecione o conector com o tipo de **Serviços de domínio Active Directory**. Em **ações**, selecione **Propriedades**.  
![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Clique em **Configurar partições de diretório**.
5. Na lista **Selecionar partições de diretório** , selecione e desmarque os domínios, conforme necessário. Verifique se que somente as partições que você deseja sincronizar estão selecionadas.  
![Partições](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
Se você alterou seu local infraestrutura do AD e domínios adicionados ou removidos da floresta, clique no botão **Atualizar** para obter uma lista atualizada. Quando você atualizar, você será solicitado para credenciais. Fornece as credenciais de acesso de leitura ao seu Active Directory local. Ele não precisa ser o usuário que estiver preenchido na caixa de diálogo.  
![Atualização necessária](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Quando terminar, feche a caixa de diálogo de **Propriedades** , clicando em **Okey**. Se você tiver removido domínios da floresta, um mensagem pop-up dizendo que um domínio foi removido e que a configuração serão limpo.
7. Continue ajustar a [Executar perfis](#update-run-profiles).

### <a name="update-run-profiles"></a>Perfis de execução de atualização
Se você tiver atualizado seu filtro de domínio, você também precisa atualizar os perfis de execução.

1. Na lista de **conectores** , verifique se o conector que você alterou na etapa anterior está selecionado. Selecione **ações**, **Configurar perfis executar**.  
![Conector executar perfis](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

Você precisa ajustar os perfis a seguir:

- Importação completa
- Sincronização completa
- Importação delta
- Sincronização delta
- Exportar

Para cada um dos cinco perfis, siga estas etapas para cada domínio **adicionado** :

1. Selecione o perfil de execução e clique em **Nova etapa**.
2. Na página **Configurar Etapa** , na lista suspensa **tipo** , selecione o tipo de etapa com o mesmo nome como o perfil que você está configurando. Clique em **Avançar**.  
![Conector executar perfis](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. Na página de **Configuração do conector** , na lista suspensa **partição** , selecione o nome do domínio que você adicionou ao seu filtro de domínio.  
![Conector executar perfis](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Para fechar a caixa de diálogo **Configurar perfil executar** , clique em **Concluir**.

Para cada um dos cinco perfis, siga estas etapas para cada domínio **removido** :

1. Selecione o perfil de execução.
2. Se o **valor** do atributo **partição** é um GUID, selecione a etapa executar e clique em **Excluir etapa**.  
![Conector executar perfis](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

O resultado deve ser que cada domínio que você deseja sincronizar deve estar listado como uma etapa em cada perfil de execução.

Para fechar a caixa de diálogo **Configurar perfis executar** , clique em **Okey**.

- Para concluir a configuração, [Aplicar e verifique alterações](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtragem de baseado em unidade organizacional
A melhor maneira de alterar a filtragem baseada em unidade Organizacional está executando a instalação assistente e alterar o [domínio e organizacionais filtragem](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). O Assistente de instalação é automatizar todas as tarefas documentadas neste tópico.

Você só deve seguir estas etapas se por algum motivo não for possível executar o Assistente de instalação.

**Para configurar a filtragem de unidade – baseada na organização, siga estas etapas:**

1. Entrar no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Serviço de sincronização** do menu Iniciar.
3. Selecione **conectores** e na lista de **conectores** , selecione o conector com o tipo de **Serviços de domínio Active Directory**. Em **ações**, selecione **Propriedades**.  
![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Clique em **Configurar partições de diretório**, selecione o domínio que você deseja configurar e clique em **contêineres**.
5. Quando solicitado, forneça as credenciais com acesso de leitura ao Active Directory local. Ele não precisa ser o usuário que estiver preenchido na caixa de diálogo.
6. Na caixa de diálogo **Selecionar contêineres** , desmarque as unidades organizacionais que você não deseja sincronizar com o diretório de nuvem e, em seguida, clique em **Okey**.  
![UNIDADE ORGANIZACIONAL](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - O contêiner de **computadores** deve estar selecionado para seus computadores Windows 10 para ser sincronizado com o Azure AD. Se seus computadores de domínio associado estiverem localizados em outras unidades organizacionais, verifique se que aqueles selecionados.
  - O contêiner de **ForeignSecurityPrincipals** deve ser selecionado se você tiver várias florestas com relações de confiança. Este contêiner permite que as associações de grupo de segurança de cruz floresta ser resolvido.
  - A unidade Organizacional **RegisteredDevices** deve ser selecionada se você tiver habilitado o recurso de write-back do dispositivo. Se você usar outro recurso de write-back, como write-back de grupo, verifique se que esses locais são selecionados.
  - Selecione qualquer outra unidade Organizacional onde estão localizados os usuários, iNetOrgPersons, grupos, contatos e computadores. Na imagem, todos esses estão localizados na OU ManagedObjects.
7. Quando terminar, feche a caixa de diálogo de **Propriedades** , clicando em **Okey**.
8. Para concluir a configuração, [Aplicar e verifique alterações](#apply-and-verify-changes).

## <a name="attribute-based-filtering"></a>Filtragem baseada em atributo
Verifique se você estiverem a novembro de 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) ou posterior construir para essas etapas trabalhar.

A filtragem de atributo com base é a maneira mais flexível de objetos de filtro. Você pode usar o poder dos [provisionamento declarativa](active-directory-aadconnectsync-understanding-declarative-provisioning.md) para controlar quase todos os aspectos da quando um objeto deve ser sincronizado no Azure AD.

Filtragem pode ser aplicada ambos na [entrada](#inbound-filtering) do Active Directory para o metaverso e [saída](#outbound-filtering) do metaverso ao Azure AD. É recomendável para aplicar o filtro na entrada já que é mais fácil de manter. Filtragem de saída só deve ser usada se for necessário para ingressar objetos de mais de uma floresta antes da avaliação pode ocorrer.

### <a name="inbound-filtering"></a>Filtragem de entrada
Filtragem com base entrada está usando a configuração padrão onde objetos indo ao Azure AD devem ter o cloudFiltered de atributo metaverso não definido como um valor a ser sincronizada. Se este valor de atributo estiver definido como **True**, o objeto não está sincronizado. Ele não deve ser definido como **False** por design. Para garantir que outras regras têm a capacidade de um valor de contribuição, esse atributo só deve ter os valores **verdadeiro** ou **Nulo** (ausente).

Na filtragem de entrada, você pode usar a potência do **escopo** para determinar quais objetos devem ou não deverão ser sincronizados. Isso é onde você pode fazer ajustes para caber próprias exigências de sua organização. O módulo de escopo tem **grupo** e **cláusula** para determinar se uma regra de sincronização deve estar no escopo. Um **grupo** contém um ou vários **cláusula**. Não há um e lógico entre várias cláusulas e uma lógica ou entre vários grupos.

Vamos examine um exemplo:  
![Escopo](./media/active-directory-aadconnectsync-configure-filtering/scope.png) isso deve ser lido como **(departamento = IT) ou (departamento = Vendas e c = US)**.

Nos exemplos e etapas abaixo, você usa o objeto de usuário como exemplo, mas você pode usar isso para todos os tipos de objeto.

Os exemplos abaixo, o valor de precedência começa com 500. Esse valor garante que essas regras são avaliadas após as regras de prontos (precedência menor, maior valor numérico).

#### <a name="negative-filtering-do-not-sync-these"></a>Negativo filtragem, "não sincronizar esses"
No exemplo a seguir, você filtra (não sincronizam) onde **extensionAttribute15** tenha o valor **NoSync**de todos os usuários.

1. Entrar no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** do menu Iniciar.
3. Certifique-se de **que entrada** está selecionado e clique em **Adicionar nova regra**.
4. Dar a regra um nome descritivo, como "*do AD – usuário DoNotSyncFilter*". Selecione a floresta correta, o **usuário** como o **tipo de objeto de CS**e **pessoa** como o **tipo de objeto MV**. Como o **Tipo de vínculo**, selecione **ingressar** na precedência, digite um valor atualmente não usado por outra regra de sincronização (por exemplo 500) e clique em **Avançar**.  
![Entrada 1 Descrição](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. Em **filtro de escopo**, clique em **Adicionar grupo**, clique em **Adicionar cláusula**e no atributo selecione **ExtensionAttribute15**. Verifique se que o operador estiver definido como **igual** e digite o valor **NoSync** na caixa valor. Clique em **Avançar**.  
![Entrada escopo 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Deixe as regras **ingressar** vazia e clique em **Avançar**.
7. Clique em **Adicionar transformação**, selecione a **FlowType** a **constante**, o atributo de destino **cloudFiltered** e na caixa de texto de origem, digite **verdadeiro**. Clique em **Adicionar** para salvar a regra.  
![Entrada transformação 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Para concluir a configuração, [Aplicar e verifique alterações](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positivo filtragem, "só sincronizar esses"
Expressar filtragem positivo pode ser mais desafiador porque você deve considerar também objetos que não são óbvios a serem sincronizados, como salas de conferência.

A opção de filtragem positiva requer duas regras de sincronização. Um (ou vários) com o escopo correto de objetos para sincronizar e um segundo filtro check-out de todos os objetos que ainda não foram identificados como um objeto que deve ser sincronizado de regra de sincronização de variável-tudo.

No exemplo a seguir, você sincronizar apenas objetos de usuário onde o atributo de departamento tem o valor de **vendas**.

1. Entrar no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** do menu Iniciar.
3. Certifique-se de **que entrada** está selecionado e clique em **Adicionar nova regra**.
4. Dê um nome descritivo, como "*do AD – vendas de usuário Sincronizar*" para a regra. Selecione a floresta correta, o **usuário** como o **tipo de objeto de CS**e **pessoa** como o **tipo de objeto MV**. Como o **Tipo de vínculo**, selecione **ingressar** na precedência, digite um valor atualmente não usado por outra regra de sincronização (por exemplo 501) e clique em **Avançar**.  
![Descrição de 4 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. Em **filtro de escopo**, clique em **Adicionar grupo**, clique em **Adicionar cláusula**e no atributo selecione **departamento**. Verifique se que o operador estiver definido como **igual** e digite o valor **das vendas** na caixa valor. Clique em **Avançar**.  
![5 escopo de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Deixe as regras **ingressar** vazia e clique em **Avançar**.
7. Clique em **Adicionar transformação**, selecione a **FlowType** a **constante**, o atributo de destino **cloudFiltered** e na caixa de texto de origem, digite **False**. Clique em **Adicionar** para salvar a regra.  
![Entrada transformação de 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
Este é um caso especial onde você definir cloudFiltered explicitamente como False.

    Agora, temos que criar a regra de sincronização de variável-tudo.

8. Dar a regra um nome descritivo, como "*do AD – filtro de usuário variável-all*". Selecione a floresta correta, o **usuário** como o **tipo de objeto de CS**e **pessoa** como o **tipo de objeto MV**. Como o **Tipo de vínculo**, selecione **ingressar** e na precedência, digite um valor atualmente não usado por outra regra de sincronização (por exemplo 600). Você tem selecionado precedência valor mais alto (menor precedência) que a regra de sincronização anterior, mas também à esquerda alguns sala, portanto, podemos adicionar mais regras de sincronização de filtragem mais tarde quando você deseja iniciar a sincronização departamentos adicionais. Clique em **Avançar**.  
![Entrada descrição 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Deixe o **filtro de escopo** vazia e clique em **Avançar**. Um filtro vazio indica que a regra deve ser aplicada a todos os objetos.
10. Deixe as regras **ingressar** vazia e clique em **Avançar**.
11. Clique em **Adicionar transformação**, selecione a **FlowType** a **constante**, o atributo de destino **cloudFiltered** e na caixa de texto de origem, digite **verdadeiro**. Clique em **Adicionar** para salvar a regra.  
![Entrada transformação 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Para concluir a configuração, [Aplicar e verifique alterações](#apply-and-verify-changes).

Se for necessário, você pode criar mais regras do primeiro tipo onde você incluir mais objetos em nossa sincronização.

### <a name="outbound-filtering"></a>Filtragem de saída
Em alguns casos, é necessário fazer a filtragem somente depois que os objetos ingressaram no metaverso. Por exemplo, poderia, seja necessário examinar o atributo de email da floresta do recurso e o atributo userPrincipalName da floresta conta para determinar se um objeto deve ser sincronizado. Nesses casos, você criar a filtragem na regra de saída.

Neste exemplo, você alterar os usuários de modo que somente filtragem onde o email e userPrincipalName terminam com @contoso.com são sincronizados:

1. Entrar no servidor que está executando a sincronização do Azure AD Connect usando uma conta que seja um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** do menu Iniciar.
3. Em **Tipo de regras**, clique em **saída**.
4. Localize a regra chamada **Out para AAD – SOAInAD de participar do usuário**. Clique em **Editar**.
5. No pop-up, responda **Sim** para criar uma cópia da regra.
6. Na página de **Descrição** , altere a precedência para um valor não utilizado, por exemplo 50.
7. Clique em **filtro de escopo** na navegação à esquerda. Clique em **Adicionar cláusula**, no atributo select **mail**, no operador select **ENDSWITH**e no tipo de valor **@contoso.com**. Clique em **Adicionar cláusula**, na seleção de atributo **userPrincipalName**, no operador select **ENDSWITH**e no tipo de valor **@contoso.com**.
8. Clique em **Salvar**.
9. Para concluir a configuração, [Aplicar e verifique alterações](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Aplicar e verificar as alterações
Depois de fazer suas alterações de configuração, eles devem ser aplicados aos objetos já presentes no sistema. Também é possível que objetos não esteja atualmente no mecanismo de sincronização devem ser processados e o mecanismo de sincronização precisa ler o sistema de origem novamente para verificar seu conteúdo.

Se você alterou a configuração usando a filtragem de **unidade organizacional** ou **domínio** , você precisa fazer **importação completa** seguido de **sincronização Delta**.

Se você alterou a configuração usando o **atributo** filtragem, você precisa fazer a **sincronização completa**.

Siga estas etapas:

1. Inicie o **Serviço de sincronização** do menu Iniciar.
2. Selecione **conectores** e na lista de **conectores** , selecione o conector onde você fez uma configuração alterar anteriormente. Em **ações**, selecione **Executar**.  
![Conector executar](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. Na **Executar perfis**, selecione a operação mencionada na seção anterior. Se você precisar executar duas ações, execute o segundo após a conclusão da primeira (coluna **estado** está **ocioso** por conector selecionado).

Após a sincronização, todas as alterações são transferidas para ser exportada. Antes de realmente fazer as alterações no Azure AD, você deseja verificar se todas essas alterações estão corretas.

1. Inicie um prompt cmd e vá para`%Program Files%\Microsoft Azure AD Sync\bin`
2. Executar:`csexport "Name of Connector" %temp%\export.xml /f:x`  
O nome do conector pode ser encontrado no serviço de sincronização. Ela tem um nome semelhante a "contoso.com – AAD" do Azure AD.
3. Executar:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Agora você tem um arquivo em % temp % denominada export.csv que podem ser examinados no Microsoft Excel. Este arquivo contém todas as alterações que estão prestes a ser exportado.
5. Faça as alterações necessárias para os dados ou a configuração e execute estas etapas novamente (importação, sincronizar e verificar) até que as alterações que estão prestes a ser exportado são esperadas.

Quando estiver satisfeito, exporte as alterações ao Azure AD.

1. Selecione **conectores** e na lista de **conectores** , selecione o conector de AD do Azure. Em **ações**, selecione **Executar**.
2. Na **Executar perfis**, selecione **Exportar**.
3. Se as alterações na configuração excluir vários objetos, em seguida, você verá um erro à exportação quando o número for maior que o limite configurado (por padrão 500). Se você vir esse erro, você precisa desativar temporariamente o recurso [impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Agora é hora de habilitar o Agendador novamente.

1. Inicie o **Agendador de tarefas** no menu Iniciar.
2. Diretamente em **Biblioteca do Agendador de tarefas**, localize a tarefa chamada **Agendador do Azure AD Sync**, o botão direito e selecione **Ativar**.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
