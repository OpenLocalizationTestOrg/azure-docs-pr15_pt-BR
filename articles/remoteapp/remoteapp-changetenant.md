
<properties
    pageTitle="Alterar o locatário do Azure Active Directory do Azure RemoteApp | Microsoft Azure"
    description="Saiba como alterar o locatário do Active Directory do Azure associado RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>Alterar o locatário do Azure Active Directory do Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

RemoteApp Azure usa Azure Active Directory (AD Azure) para permitir o acesso do usuário. O locatário somente Azure AD que você pode usar no Azure RemoteApp é aquele associado a assinatura do Azure. Você pode exibir a assinatura associada na página **configurações** no portal. Examine a coluna de **diretório** na guia **assinaturas** .

> [AZURE.NOTE] Para que essa alteração tenha êxito, primeiro remova todos os usuários do locatário existente do Active Directory do Azure de todos os conjuntos de RemoteApp do Azure. Para fazer isso, acesse o Portal do Azure, vá para a guia **RemoteApp do Azure** e abra cada coleção de RemoteApp do Azure. Vá para a guia de **usuários** e remover usuários que fazem parte de seu locatário atual do Active Directory do Azure. Repita para todos os conjuntos de Azure RemoteApp existentes. Sem fazer isso, você não poderá criar ou conjuntos de patches.

Se você quiser usar um locatário diferente, use estas etapas para alterar a associação com sua assinatura:

1. No portal do, remova usuários Azure AD ao qual conceder acesso para conjuntos de RemoteApp do Azure. (Consulte a observação acima para obter as etapas sobre como fazer isso.)


2. Configure uma conta da Microsoft (anteriormente chamada de um Live ID) como o administrador do serviço. (Não sabe se você já for o administrador do serviço? Você pode descobrir clicando em **Configurações -> administradores**.) Agora, veja aqui como você alterá-lo:
    1. Clique no usuário no canto superior direito e, em seguida, clique em **Exibir minha fatura**.
    2. Clique na assinatura. Em seguida, na página novo, role para baixo e clique em **Editar detalhes da assinatura** no lado direito. (Classificação da parte inferior intermediária à direita, se isso ajuda você a encontrá-lo.)
    3. Digite a conta da Microsoft para o usuário que deve ser administrador do serviço.

3. Agora, saia do portal e entre novamente a conta da Microsoft que você especificou na etapa anterior.


4. Clique em **Novo -> aplicativo Serviços -> Active Directory -> diretório -> personalizado criar**.
5. Em **diretório**, escolha **usar o diretório existente**. Vamos ter ao sair você o portal agora, portanto escolha **que estou pronto para ser assinado agora**.
6. Entrar novamente no portal do como um administrador global do diretório que você deseja adicionar. (Se você não tiver um administrador global, você será após um round de entrar e, em seguida, sair.)
7. Você será solicitado quando entrar se você deseja usar seu locatário existente do AD com sua assinatura. Clique em **continuar**e, em seguida, clique em **sair agora**.
5. Entrar novamente e voltar para **Configurações -> assinaturas**. Selecione a assinatura e, em seguida, clique em **Editar no diretório**. Selecione o locatário do Azure AD que você deseja usar.



Você pode locatário de usar o novo anúncio Azure agora para controlar o acesso à assinatura Azure e configurar o acesso do usuário no Azure RemoteApp.
