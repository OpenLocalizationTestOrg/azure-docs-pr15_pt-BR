<properties
   pageTitle="Gerenciar o diretório para sua assinatura do Office 365 no Azure | Microsoft Azure"
   description="Gerenciando um diretório de assinatura do Office 365 usando o Active Directory do Azure e o Azure portal clássico"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Gerenciar o diretório para sua assinatura do Office 365 no Azure

Este artigo descreve como gerenciar um diretório que foi criado para uma assinatura do Office 365, usando o portal de clássico Azure. Você deve ser administrador do serviço ou um administrador de colegas de uma assinatura do Azure para entrar no portal do Azure clássico. Se você ainda não tiver uma assinatura do Azure, você pode se inscrever em uma [versão de avaliação de 30 dias gratuita](https://azure.microsoft.com/trial/get-started-active-directory/) hoje e implantar sua primeira solução de nuvem em menos de 5 minutos, usando esse link. Certifique-se de usar a conta corporativa ou de estudante que você usa para entrar no Office 365.

Depois de concluir a assinatura do Azure, você pode entrar portal do Azure clássico e acessar os serviços do Azure. Clique na extensão do Active Directory para gerenciar o mesmo diretório que autentica os usuários do Office 365.

Se você já tiver uma assinatura do Azure, o processo para gerenciar um diretório adicional também é simples. Por exemplo, Michael Smith pode ter uma assinatura do Office 365 para Contoso.com. Ele também tem uma assinatura do Azure que ele assinou usando sua conta da Microsoft, msmith@hotmail.com. Nesse caso, ele gerencia dois diretórios.

  Assinatura |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  Nome para exibição |  Contoso  |     Diretório do Azure Active Directory (AD Azure) padrão
  Nome de domínio  |  Contoso.com  | msmithhotmail.onmicrosoft.com

Ele deseja gerenciar identidades de usuário no diretório Contoso enquanto ele está conectado ao Azure usando sua conta da Microsoft, para que ele pode habilitar recursos do Azure AD como autenticação multifator. O diagrama a seguir pode ajudar a ilustrar o processo.

![Diagrama de gerenciar dois diretórios independentes](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

Nesse caso, os dois diretórios são independentes uns dos outros.

## <a name="to-manage-two-independent-directories"></a>Gerenciar dois diretórios independentes
Em ordem para Michael Smith para gerenciar ambos os diretórios enquanto ele está conectado ao Azure como msmith@hotmail.com, ele deve concluir as etapas a seguir:

> [AZURE.NOTE]
> Essas etapas podem ser concluídas somente quando um usuário estiver conectado com uma conta da Microsoft. Se o usuário estiver conectado a um trabalho ou conta de escola, a opção para **usar o diretório existente** não está disponível. Uma conta corporativa ou escolar pode ser autenticada apenas por seu diretório inicial (ou seja, o diretório onde a conta corporativa ou escolar está armazenada e que a empresa ou instituição possui).

1.  Entre [portal de clássico Azure](https://manage.windowsazure.com) como msmith@hotmail.com.
2.  Clique em **novo** > **os serviços de aplicativo** > **Do Active Directory** > **diretório** > **Criar personalizada**.
3.  Clique em usar o diretório existente e marque a caixa de seleção **estou pronto para ser assinado agora** .
4.  Entre no portal do Azure clássico como administrador global de Contoso.onmicrosoft.com (por exemplo, msmith@contoso.com).
5.  Quando solicitado a **usar o diretório de Contoso com o Azure?**, clique em **continuar**.
6.  Clique em **sair agora**.
7.  Entre portal do Azure clássico como msmith@hotmail.com. O diretório de Contoso e o diretório padrão aparecem na extensão do Active Directory.

Após concluir essas etapas, msmith@hotmail.com é um administrador global no diretório de Contoso.

## <a name="to-administer-resources-as-the-global-admin"></a>Administrar recursos como o administrador global
Agora vamos supor que Maria ninguém precisa administrar recursos de sites e banco de dados que estão associados a assinatura do Azure para msmith@hotmail.com. Antes que ela pode ser feito, Michael Smith precisa concluir estas etapas adicionais:

1.  Entre [portal de clássico Azure](https://manage.windowsazure.com) usando a conta de administrador de serviço para a assinatura Azure (neste exemplo, msmith@hotmail.com).
2.  Transferir a inscrição no diretório de Contoso: clique em **configurações** > **assinaturas** > selecione a assinatura > **Editar diretório** > selecione **Contoso (Contoso.com)**. Como parte de transferência, qualquer trabalho ou escola contas que estão coadministradores da assinatura são removidas.
3.  Adicionar Maria ninguém como administrador colegas da assinatura: clique em **configurações** > **administradores** > selecione a assinatura > **Adicionar** > tipo **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a relação entre assinaturas e pastas, consulte [como uma assinatura está associada um diretório](active-directory-how-subscriptions-associated-directory.md).
