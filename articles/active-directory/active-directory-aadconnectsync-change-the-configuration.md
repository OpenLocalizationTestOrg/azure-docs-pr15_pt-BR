<properties
    pageTitle="Sincronização do Azure AD Connect: como alterar a configuração padrão | Microsoft Azure"
    description="Explica como fazer uma alteração na configuração na sincronização do Azure AD Connect."
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
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Sincronização do Azure AD Connect: como alterar a configuração padrão
O objetivo deste tópico é para orientá-lo como fazer alterações na configuração padrão na sincronização do Azure AD Connect. Ele fornece etapas para alguns cenários comuns. Com esse conhecimento, você deve ser capaz de fazer alterações simples sua própria configuração com base em suas próprias regras de negócios.

## <a name="synchronization-rules-editor"></a>Editor de regras de sincronização
O Editor de regras de sincronização é usado para ver e alterar a configuração padrão. Você pode encontrá-lo no Menu Iniciar em grupo **Azure AD Connect** .  
![Menu Iniciar com o Editor de regra de sincronização](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Quando você abri-lo, você vê as regras de prontos de padrão.

![Editor de regras de sincronização](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegando no editor
As listas suspensas na parte superior do editor permite que você localize rapidamente uma determinada regra. Por exemplo, se você quiser ver as regras onde o atributo proxyAddresses está incluído, você poderia alterar os menus suspensos ao seguinte:  
![Filtragem de SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Para redefinir a filtragem e carregar uma nova configuração, pressione **F5** no teclado.

A parte superior direita, você tem um botão **Adicionar nova regra**. Este botão é usado para criar sua própria regra personalizada.

Na parte inferior, você tem botões para agir em uma regra de sincronização selecionado. **Editar** e **Excluir** fazem o que você espera. **Exportar** produz um script PowerShell para recriar a regra de sincronização. Este procedimento permite que você mover uma regra de sincronização de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Criar sua primeira regra personalizada
A alteração mais comuns é alterações para os fluxos de atributo. Os dados no seu diretório de origem não podem ser como Azure AD. No exemplo nesta seção, você deseja certificar-se de que o nome fornecido de um usuário é sempre primeira letra em **maiuscula**.

### <a name="disable-the-scheduler"></a>Desabilitar o Agendador
O [Agendador](active-directory-aadconnectsync-feature-scheduler.md) é executado a cada 30 minutos por padrão. Você deseja certificar-se de que ele não está iniciando enquanto você está fazendo alterações e solucionar problemas de suas novas regras. Para desativar temporariamente o Agendador, iniciar o PowerShell e executar`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Desabilitar o Agendador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Crie a regra

1. Clique em **Adicionar nova regra**.
2. Na página de **Descrição** , digite o seguinte:  
![Filtragem de regra de entrada](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
    - Nome: Atribua à regra um nome descritivo.
    - Descrição: Alguns explicação para que outra pessoa possa entender o que é a regra para.
    - Sistema conectado: O sistema de objeto pode ser encontrado na. Nesse caso, selecionamos o conector do Active Directory.
    - Tipo de objeto do sistema/metaverso conectado: Selecione **usuário** e **pessoa** , respectivamente.
    - Tipo de vínculo: Altere esse valor para **ingressar**.
    - Precedência: Fornece um valor que seja exclusivo no sistema. Um valor numérico inferior indica precedência.
    - Marca: Deixe em branco. Somente prontos de regras da Microsoft devem ter esta caixa preenchida com um valor.
3. Na página de **filtro de escopo** , insira **givenName ISNOTNULL**.  
![Regra de filtro de escopo de entrada](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
Esta seção é usada para definir quais objetos que a regra deve ser aplicada. Se vazia, a regra seria aplicada a todos os objetos de usuário. Mas que incluiria salas de conferência, contas de serviço e outros objetos de usuário não pessoas.
4. Sobre as **regras de ingressar**, deixe vazio.
5. Na página **transformações** , altere o FlowType a **expressão**. Selecione o atributo de destino **givenName**e na fonte insira `PCase([givenName])`.
![Regra de entrada transformações](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
O mecanismo de sincronização diferencia maiusculas de minúsculas tanto em nome da função e o nome do atributo. Se você digitar algo errado, você verá um aviso quando você adiciona a regra. O editor permite salvar e continuar, portanto, é necessário reabrir a regra e corrija as regras.
6. Clique em **Adicionar** para salvar a regra.

Sua nova regra personalizada deve estar visível com as outras regras de sincronização no sistema.

### <a name="verify-the-change"></a>Verifique a alteração
Com essa alteração nova, você deseja certificar-se de que ele está funcionando como esperado e não é lançar quaisquer erros. Dependendo do número de objetos que tiver, há duas maneiras diferentes para concluir esta etapa.

1. Executar uma sincronização completa em todos os objetos
2. Executar uma visualização e sincronização completa em um único objeto

Inicie o **Serviço de sincronização** do menu Iniciar. As etapas desta seção ficam nesta ferramenta.

1. **Sincronização completa em todos os objetos**  
Selecione **conectores** na parte superior. Identifique o conector que você fez uma alteração na seção anterior, nesse caso, os serviços de domínio Active Directory, e selecioná-lo. Selecione **Executar** ações e selecione **Sincronização completa** e **Okey**.
![Sincronização completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
Agora, os objetos são atualizados no metaverso. Agora que você deseja examinar o objeto no metaverso.

2. **Visualizar e sincronização completa em um único objeto**  
Selecione **conectores** na parte superior. Identifique o conector que você fez uma alteração na seção anterior, nesse caso, os serviços de domínio Active Directory, e selecioná-lo. Selecione o **espaço de pesquisa de conector**. Use o escopo para encontrar um objeto que você deseja usar para testar a alteração. Selecione o objeto e clique em **Visualizar**. Na tela novo, selecione **Confirmar Preview**.
![Confirmar preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
A alteração está agora confirmada para o metaverso.

**Examine o objeto no metaverso**  
Agora, você quer escolher alguns objetos de exemplo para certificar-se de que o valor esperado e que a regra aplicada. Selecione **Pesquisa metaversa** da parte superior. Adicione qualquer filtro para encontrar os objetos relevantes. Os resultados de pesquisa, abra um objeto. Examine os valores de atributo e também verificar na coluna **Regras de sincronização** que a regra aplicada como esperada.  
![Pesquisa metaversa](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  
### <a name="enable-the-scheduler"></a>Habilitar o Agendador
Se tudo estiver conforme esperado, você pode habilitar o Agendador novamente. A partir do PowerShell, execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações de fluxo de atributo comuns
A seção anterior descrita como para fazer alterações em um fluxo de atributo. Nesta seção, alguns exemplos adicionais são fornecidos. As etapas sobre como criar a regra de sincronização é abreviado, mas você pode encontrar as etapas completas na seção anterior.

### <a name="use-another-attribute-than-the-default"></a>Use outro atributo do padrão
Na Fabrikam, não há uma floresta onde o alfabeto local é usado para determinado nome, sobrenome e nome para exibição. A representação de caracteres latinos desses atributos pode ser encontrada dos atributos de extensão. Ao criar a lista de endereços global no Azure AD e Office 365, a organização quer esses atributos a ser usado em vez disso.

Com uma configuração padrão, um objeto da floresta local tem esta aparência:  
![Fluxo de atributo 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributo, faça o seguinte:

- Inicie o **Editor de regra de sincronização** do menu Iniciar.
- Com **entrada** ainda selecionado para a esquerda, clique no botão **Adicionar nova regra**.
- Dê a regra um nome e descrição. Selecione o Active Directory local e os tipos de objeto relevante.  Em **Tipo de vínculo**, selecione **ingressar**. Precedência, escolha um número que não é usado por outra regra. As regras de prontos comece com 100, para que o valor 50 possa ser usado neste exemplo.
![Fluxo de atributo 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Deixe o escopo vazio (ou seja, deverá se aplicar a todos os objetos de usuário na floresta).
- Deixar as regras de junção vazio (ou seja, permitir que a regra de prontos lidar com quaisquer associações).
- Em transformações, crie os seguintes fluxos:  
![Fluxo de atributo 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Clique em **Adicionar** para salvar a regra.
- Vá para **O Gerenciador de serviço de sincronização**. Em **conectores**, selecione o conector onde adicionamos a regra. Selecione **Executar**e **sincronização completa**. Uma sincronização completa recalcula todos os objetos usando as regras atuais.

Este é o resultado para o mesmo objeto com essa regra personalizada:  
![Fluxo de atributo 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento de atributos
Atributos de cadeia de caracteres são por padrão definida para ser indexáveis e o comprimento máximo é 448 caracteres. Se você estiver trabalhando com atributos de cadeia de caracteres que podem conter mais, em seguida, verifique se incluir o seguinte no fluxo de atributo:  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Alterando o userPrincipalSuffix
O atributo userPrincipalName no Active Directory não é sempre conhecido pelos usuários e talvez não seja adequado como a ID de entrada. O Azure AD Connect Assistente de instalação de sincronização permite escolher um atributo diferente, por exemplo de email. Mas em alguns casos o atributo deve ser calculado. Por exemplo, a empresa Contoso tem dois diretórios Azure AD, um para produção e outro para teste. Eles desejam que os usuários em seu locatário de teste para usar outro sufixo na ID de entrada.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Nesta expressão, levar tudo à esquerda do primeiro @-sign (Word) e concatenar com uma cadeia de caracteres fixa.

### <a name="convert-a-multi-value-to-a-single-value"></a>Converter um valor de vários para um único valor
Alguns atributos do Active Directory são múltiplos no esquema apesar de aparência simples com valor no Active Directory Users e computadores. Um exemplo é o atributo de descrição.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Nesta expressão caso o atributo tem um valor, podemos tirar o primeiro item (Item) no atributo, remover esquerda e à direita espaços (Cortar) e manter os caracteres primeiro 448 (à esquerda) na cadeia.

### <a name="do-not-flow-an-attribute"></a>Não fluem um atributo
Para o plano de fundo no cenário desta seção, consulte [controlar o processo de fluxo de atributo](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Há duas maneiras de fluxo não um atributo. O primeiro está disponível no Assistente de instalação e permite que você remover [atributos selecionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Esta opção funciona se você nunca tiver sincronizado o atributo antes. No entanto, se você tiver começado sincronizar esse atributo e posterior removê-lo com esse recurso, as paradas de mecanismo de sincronização Gerenciando o atributo e os valores existentes permanecem no Azure AD.

Se você quiser remover o valor de um atributo e verifique se que ele não flui no futuro, você precisa criar uma regra personalizada em vez disso.

Na Fabrikam, podemos tiver realizado que alguns dos atributos que podemos sincronizar na nuvem não devem ser lá. Queremos certificar-se de que esses atributos são removidos do Azure AD.  
![Atributos de extensão incorreta](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Criar uma nova regra de sincronização de entrada e preencher a descrição ![descrições](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Crie fluxos de atributo de tipo de **expressão** e com a fonte **AuthoritativeNull**. O literal **AuthoritativeNull** indica que o valor deve ser vazio na MV mesmo se uma regra de sincronização de precedência inferior tenta preencher o valor.
![Transformação de atributos de extensão](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Salve a regra de sincronização. Iniciar o **Serviço de sincronização**, encontre o conector, selecione **Executar**e **Sincronização completa**. Esta etapa recalcula todos os fluxos de atributo.
- Verifique se as alterações pretendidas estão prestes a ser exportado pesquisando o espaço de conector.
![Excluir em estágios](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre o modelo de configuração em [Noções básicas sobre declarativa provisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativa](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
