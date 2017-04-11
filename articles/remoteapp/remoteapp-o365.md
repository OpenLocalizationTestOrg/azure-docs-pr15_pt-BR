
<properties
    pageTitle="Usar o Office com o Azure RemoteApp | Microsoft Azure" 
    description="Saiba como o Office e o Azure RemoteApp funcionam juntos"
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

# <a name="using-office-with-azure-remoteapp"></a>Usar o Office com o Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Você tem duas opções para hospedar aplicativos do Office no Azure RemoteApp: Office 365 ProPlus ou a versão de avaliação do Office 2013 Professional Plus.

**Olá, você sabia que temos um novo melhor artigo que breve substituirá isso? Confira [como usar sua assinatura do Office 365 com o Azure RemoteApp](remoteapp-officesubscription.md). Ele abrange todas as informações que necessárias para usar o Office 365 + RemoteApp Azure.**

## <a name="office-365-proplus"></a>O Office 365 ProPlus
Você pode criar uma coleção de RemoteApp usando a Office 365 ProPlus imagem de modelo. Essa opção permite que você estenda o seu serviço do Office 365 para RemoteApp. Você deve ter um plano de assinatura existente e seus usuários devem ser licenciados para o serviço Office 365 ProPlus, qualquer autônomo ou planos de serviço por meio do Office 365.

RemoteApp é compatível com a ativação de computador compartilhado do Office 365. Quando você habilitar a ativação compartilhados do computador e use a [ferramenta de implantação do Office](http://www.microsoft.com/download/details.aspx?id=36778) para a instalação, Office 365 ProPlus instala sem ser ativado. Quando um usuário assina em uma coleção que contém o Office 365, o Office verifica se o usuário tiver sido configurado para o Office 365 ProPlus. Se afirmativo, Office temporariamente ativação do Office 365 ProPlus - essa ativação persiste até que sinais de usuários cancelar o serviço.

Para usar a ativação de computador compartilhado do Office 365, você precisa criar um [modelo personalizado](remoteapp-create-custom-image.md) e instalar o Office 365 ProPlus, seguindo [estas instruções](https://technet.microsoft.com/library/dn782858.aspx).

Você pode gerenciar licenças do Office 365 de seus usuários no [Portal de administração do Office 365](https://portal.office365.com/). Leia mais informações sobre [planos de serviço do Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  


## <a name="office-2013-professional-plus-trial"></a>Versão de avaliação do Office 2013 Professional Plus
Durante uma avaliação de 30 dias do RemoteApp, você pode usar a imagem de modelo Professional Plus (avaliação) do Office 2013 para criar uma coleção de RemoteApp. Você pode atribuir usuários a esse conjunto de avaliação usando suas contas de trabalho do Azure Active Directory ou Microsoft. Nenhuma assinatura adicional é necessária.

Essa é uma ótima opção Iniciar os pneus e obter uma boa sensação do Office no RemoteApp. No entanto, essa opção é pretendido para avaliação e teste somente. Coleções de RemoteApp criado usando a imagem de modelo Professional Plus (avaliação) do Office 2013 não podem ser que fizeram a transição para o modo de produção e serão desabilitadas no final do período de avaliação.

## <a name="switching-from-trial-to-production"></a>Migrando do avaliação para produção
Quando você começa sua avaliação gratuita de 30 dias, uma anotação na seção RemoteApp do portal informará por quanto tempo falta da avaliação antes que você precise fazer a transição para uma conta paga. Você pode ativar a sua conta e alternar para modo de produção usando o link nesta nota.

Quando você ativa a sua conta, isso afetará todas as coleções de RemoteApp em sua conta.

- Coleções que estejam em execução com as Office 365 ProPlus imagens de modelo ou o Windows Server 2012 R2 passará a produção perfeitamente. Todos os dados de usuário e configurações, incluindo sessões em andamento, permanecerão intactas.
- Se você carregou imagens de modelo personalizado, coleções usando essas imagens também passará diretamente.
- A imagem de modelo Professional Plus (avaliação) do Office 2013 destina-se somente para avaliação. Coleções executando com esta imagem de modelo não podem ser fizeram a transição para produção. Eles serão colocados em estado "desativado".


Se você não fazer a transição para o modo de produção pela expiração da sua versão de avaliação, seus conjuntos de RemoteApp serão desabilitados. Não se preocupe - suas configurações e dados de usuários são salvos por outra 90 dias, para que possa ativar seu serviço e alternar para modo de produção sem qualquer perda de dados.
