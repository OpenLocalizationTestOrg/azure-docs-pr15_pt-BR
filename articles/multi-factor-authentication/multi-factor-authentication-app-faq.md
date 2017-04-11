<properties
    pageTitle="Aplicativo Microsoft autenticador perguntas Frequentes"
    description="Fornece uma lista de perguntas frequentes e respostas relacionadas ao aplicativo Microsoft Authentication e autenticação multifator do Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar, librown"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator-application-faq"></a>Perguntas Frequentes do aplicativo Microsoft Authenticator

O aplicativo Microsoft Authenticator substituídos o aplicativo do Azure autenticador e é o aplicativo recomendado quando você usa autenticação multifator do Azure. Este aplicativo está disponível para iOS, Android e Windows Phone.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

- **O que aconteceu com o Azure autenticador, autenticação multifator e aplicativos de conta da Microsoft?**

    O aplicativo Microsoft Authenticator substitui a si esses aplicativos. Autenticador Azure atualizado para o Microsoft Authenticator. Se você usar a autenticação multifator ou aplicativos de conta da Microsoft, instale o Microsoft Authenticator e adicione suas contas novamente. Verifique se você terminar de adicionar suas contas para o novo aplicativo antes de excluir os antigos.

- **Já estou usando o aplicativo Microsoft Authenticator para códigos de verificação. Como alternar para notificações de envio de um único clique?**  

    Aprovar uma entrar por meio de notificação de envio está disponível somente para contas da Microsoft, não para contas de terceiros como Google ou Facebook. Para contas do Microsoft corporativas ou escolar, sua organização pode optar por desativar essa opção, apesar.

    Se você usa uma conta da Microsoft para sua conta pessoal e quiser alternar para notificações por push, é necessário adicionar sua conta novamente. Registrar novamente o dispositivo com sua conta e configurar notificações por push.  

    Se sua conta não tiver a verificação habilitada, consulte [sobre verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) para decidir se ele é adequado para você.  

- **Quando eu poderão usar notificações de envio de um único clique no iPhone ou iPad?**  

    Este recurso está na versão beta até o final de agosto, quando ela se tornar amplamente disponível para contas da Microsoft. Se você quiser participar de nosso programa beta, envie um email para msauthenticator@microsoft.com. Inclua seu nome, sobrenome e ID Apple na sua mensagem.  

- **Notificações de envio de um único clique funcionam para contas não sejam da Microsoft?**  

    Não, notificações por push só funcionam com contas da Microsoft e contas do Active Directory do Azure. Se seu trabalho ou escola usa contas do Azure AD, eles podem desativar esse recurso.  

- **Posso restaurado meu dispositivo de um backup e Meus códigos de conta estão faltando ou não está funcionando. O que aconteceu?**  

    Para fins de segurança, nós não restaurar contas de backups de aplicativo. Se você restaurar o aplicativo iOS a partir de um backup, suas contas ainda são exibidas, mas não funcionam para receber as verificações de entrada ou gerar códigos de segurança. Depois de restaurar o aplicativo, exclua suas contas e adicioná-los novamente.

- **Eu obtive um novo dispositivo. Como remover o aplicativo Microsoft Authenticator de meu dispositivo antigo e mover para a nova?**

    Adicionar o aplicativo Microsoft Authenticator a um novo dispositivo não removê-lo automaticamente de quaisquer outros dispositivos. Para gerenciar os dispositivos que estão configurados para a sua conta, visite o site mesmo que você usa para gerenciar a verificação e escolha remover aplicativos antigos.

    Para contas pessoais do Microsoft, este site é sua página de [segurança da conta](https://account.microsoft.com/security) . Para trabalhos ou da escola contas da Microsoft, este site pode ser [Meus aplicativos](https://myapps.microsoft.com) ou um portal personalizado que sua organização tiver configurado.

## <a name="contact-us"></a>Fale conosco

Se sua dúvida não foi respondida aqui, deixe um comentário na parte inferior da página. Ou, [contate o suporte](https://support.microsoft.com/contactus) e podemos será responder para o seu problema, assim que possível.

Se você contatar o suporte, inclua quanto as seguintes informações como você pode:

- **ID do usuário** – o que é o endereço de email que você tentou entrar?
- **Descrição geral do erro** – qual mensagem de erro exata faziam você ver?  Caso não exista nenhuma mensagem de erro, descreva o comportamento inesperado notado, em detalhes.
- **Página** – qual página você estava em quando você viu o erro (inclua a URL)?
- **Código de erro** - o código de erro específico que você está recebendo.
- **Identificação de sessão** - a id de sessão específica que está recebendo.
- **ID de correlação** – qual foi o código de id de correlação gerado quando o usuário viu o erro.
- **Carimbo de hora** – qual foi a precisa data e hora que você viu o erro (incluem o fuso horário)?

Muitas dessas informações podem ser encontrada na sua página de entrada. Quando você não verificar sua entrar em tempo, selecione **Exibir detalhes**.

![Entrar em detalhes do erro](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

Incluindo estas informações nos ajuda a solucionar o problema assim que possível.

## <a name="related-topics"></a>Tópicos relacionados

- [Perguntas Frequentes do Azure autenticação multifator](multi-factor-authentication-faq.md)  
- [Sobre a verificação](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) de contas da Microsoft
- [Está tendo problemas com a verificação?](multi-factor-authentication-end-user-troubleshoot.md)
