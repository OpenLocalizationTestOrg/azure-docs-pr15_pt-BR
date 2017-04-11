<properties
   pageTitle="Publicar aplicativos para usuários individuais em uma coleção de RemoteApp Azure (visualização) | Microsoft Azure"
   description="Saiba como você pode publicar aplicativos para usuários individuais, em vez de dependendo de grupos, no Azure RemoteApp."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Publicar aplicativos para usuários individuais em uma coleção de RemoteApp Azure (visualização)

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Este artigo explica como publicar aplicativos para usuários individuais em uma coleção de RemoteApp do Azure. Isso é nova funcionalidade no Azure RemoteApp, em "privada preview" no momento e disponível somente para selecionar usuários iniciais para fins de avaliação.

Originalmente Azure RemoteApp habilitado apenas uma maneira de aplicativos "publicação": o administrador poderá publicar aplicativos da imagem e elas estarão visíveis para todos os usuários na coleção.

Um cenário comum é incluir muitos aplicativos em uma única imagem e implantar uma coleção para reduzir custos de gerenciamento. Muitas vezes, nem todos os aplicativos são relevantes para todos os usuários – administradores prefere publicar aplicativos para usuários individuais para que eles não vir aplicativos desnecessários em seus aplicativos feed.

Isso agora é possível no Azure RemoteApp – atualmente como um recurso de visualização limitada. Aqui está um resumo das novas funcionalidades:

1. Uma coleção pode ser definida em um dos dois modos:
 
  - o original "conjunto modo", onde podem ver todos os usuários em um conjunto de todos os aplicativos publicados. Este é o modo padrão.
  - o novo "aplicativo modo", onde os usuários veem apenas aplicativos que foram explicitamente atribuídos a eles

2. No momento o modo de aplicativo só pode ser habilitado usando cmdlets do PowerShell do Azure RemoteApp.

  - Quando definido para o modo de aplicativo, atribuição de usuário na coleção não pode ser gerenciada por meio do portal Azure. Atribuição de usuário deve ser gerenciado por meio de cmdlets do PowerShell.

3. Os usuários só verão os aplicativos publicados diretamente a eles. No entanto, ainda é possível que um usuário iniciar os outros aplicativos disponíveis na imagem pelo acessá-los diretamente no sistema operacional.
  - Este recurso não fornecem um bloqueio seguro de aplicativos; apenas limita a visibilidade no aplicativo do feed.
  - Se for necessário isolar usuários de aplicativos, você precisará usar coleções separadas para isso.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Como obter cmdlets do PowerShell do Azure RemoteApp

Para experimentar a nova funcionalidade de visualização, você precisará usar cmdlets do PowerShell do Azure. Atualmente não é possível usar o portal de gerenciamento do Azure para habilitar o novo modo de publicação de aplicativo.

Primeiro, verifique se que você tem o [módulo do PowerShell do Azure](../powershell-install-configure.md) instalado.

Em seguida, inicie o console do PowerShell no modo de administrador e execute o seguinte cmdlet:

        Add-AzureAccount

Ele solicitará que você o Azure nome de usuário e senha. Uma vez conectado, você poderá executar cmdlets do Azure RemoteApp contra suas assinaturas Azure.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>Como verificar qual modo uma coleção está em

Execute o seguinte cmdlet:

        Get-AzureRemoteAppCollection <collectionName>

![Verificar o modo de conjunto](./media/remoteapp-perapp/araacllelvel.png)

A propriedade AclLevel pode ter os seguintes valores:

- Conjunto: o publicação modo original. Todos os usuários ver que todos os aplicativos de publicados.
- Aplicativo: o novo publicação modo. Os usuários verão apenas os aplicativos publicados diretamente a eles.

## <a name="how-to-switch-to-application-publishing-mode"></a>Como alternar para modo de publicação de aplicativo

Execute o seguinte cmdlet:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

Estado de publicação de aplicativo será preservado: inicialmente todos os usuários verão todos os aplicativos publicados originais.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>Como os usuários de lista quem pode ver um aplicativo específico

Execute o seguinte cmdlet:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Lista todos os usuários quem podem ver o aplicativo.

Observação: Você pode ver os aliases de aplicativo (chamados de "alias de aplicativo" na sintaxe acima) executando Get-AzureRemoteAppProgram - NomeDaColeta <collectionName>.

## <a name="how-to-assign-an-application-to-a-user"></a>Como atribuir um aplicativo a um usuário

Execute o seguinte cmdlet:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

O usuário agora verá o aplicativo no cliente do Azure RemoteApp e será capaz de se conectar a ela.

## <a name="how-to-remove-an-application-from-a-user"></a>Como remover um aplicativo de um usuário

Execute o seguinte cmdlet:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>Fornecer comentários
Agradecemos os seus comentários e sugestões sobre este recurso de visualização. Preencha a [pesquisa](http://www.instant.ly/s/FDdrb) para nos informar sua opinião.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>Ainda não teve a oportunidade de experimentar o recurso Visualização?
Se você não participaram na visualização ainda, use esta [pesquisa](http://www.instant.ly/s/AY83p) para solicitar acesso.
