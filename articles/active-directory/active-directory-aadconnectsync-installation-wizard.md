<properties
    pageTitle="Sincronização do Azure AD Connect: executar o Assistente de instalação uma segunda vez | Microsoft Azure"
    description="Explica como o Assistente de instalação funciona a segunda vez que você executá-lo."
    keywords="O Assistente de instalação do Azure AD Connect permite definir configurações de manutenção na segunda vez em que você executá-lo"
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


# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronização do Azure AD Connect: executar o Assistente de instalação uma segunda vez
Na primeira vez que você executar o Assistente de instalação do Azure AD Connect, ele orienta como configurar sua instalação. Se você executar o Assistente de instalação novamente, ele oferece opções para manutenção.

Você pode encontrar o Assistente para instalação no menu Iniciar, chamado **Azure AD Connect**.

![Menu Iniciar](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Quando você iniciar o Assistente de instalação, você verá uma página com estas opções:

![Página com uma lista de tarefas adicionais](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Se você tiver instalado o AD FS com o Azure AD Connect, há ainda mais opções. As opções adicionais que você tem para ADFS estão documentadas no [Gerenciamento do ADFS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Selecione uma das tarefas e clique em **Avançar** para continuar.

> [AZURE.IMPORTANT] Enquanto o Assistente de instalação estiver aberto, todas as operações do mecanismo de sincronização estão suspensos. Verifique se que você fechar o Assistente de instalação, assim que você concluiu as alterações na configuração.

## <a name="view-current-configuration"></a>Configuração de modo de exibição atual
Esta opção fornece uma exibição rápida de suas opções de configurado no momento.

![Página com uma lista de todas as opções e seu estado](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Clique em **anterior** para voltar. Se você selecionar **Sair**, fechar o Assistente de instalação.

## <a name="customize-synchronization-options"></a>Personalizar as opções de sincronização
Essa opção é usada para fazer alterações na configuração de sincronização. Você verá um subconjunto de opções do caminho de instalação do configuração personalizada. Você verá esta opção, mesmo se você usou a instalação expressa inicialmente.

- [Adicionar mais pastas](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Para remover um diretório, consulte [Excluir um conector](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Alterar domínio e filtragem de unidade Organizacional](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Remova a filtragem de grupo.
- [Recursos opcionais de alteração](active-directory-aadconnect-get-started-custom.md#optional-features).

As outras opções da instalação inicial não podem ser alteradas e não estão disponíveis. Essas opções são:

- Altere o atributo usar para userPrincipalName e sourceAnchor.
- Altere o método de ingresso para objetos de floresta diferente.
- Habilite filtragem baseada em grupo.

## <a name="refresh-directory-schema"></a>Atualizar esquema de diretório
Esta opção é usada se você alterou o esquema em um dos seus locais florestas do AD DS. Por exemplo, você pode ter instalado o Exchange ou atualizado para um esquema do Windows Server 2012 com objetos de dispositivo. Nesse caso, você precisa instruir o Azure AD Connect para ler o esquema novamente do AD DS e atualizar seu cache. Esta ação também gera novamente as regras de sincronização. Se você adicionar o esquema do Exchange, como exemplo, as regras de sincronização para o Exchange são adicionadas à configuração.

Quando você selecionar essa opção, todas as pastas em sua configuração são listadas. Você pode manter a configuração padrão e, em seguida, atualizar todas as florestas ou desmarcar algumas delas.

![Página com uma lista de todas as pastas no ambiente](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurar o modo de teste
Essa opção permite habilitar e desabilitar o modo de preparação no servidor. Mais informações sobre temporários modo e como ele é usado podem ser encontradas em [operações](active-directory-aadconnectsync-operations.md#staging-mode).

A opção mostra se o teste está habilitada ou desabilitada no momento:  
![Opção que também está mostrando o estado atual do modo de teste](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione essa opção e marcar ou desmarcar a caixa de seleção.  
![Opção que também está mostrando o estado atual do modo de teste](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Alterar o acesso do usuário
Essa opção permite que você altere de sincronização de senha para federação ou o contrário. Você não pode alterar **não**configurar.

Para obter mais informações sobre essa opção, consulte [acesso do usuário](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o modelo de configuração usado pela sincronização do Azure AD Connect na [Compreensão declarativa provisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
