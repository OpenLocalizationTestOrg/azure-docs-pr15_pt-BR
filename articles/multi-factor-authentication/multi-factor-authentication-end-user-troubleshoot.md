<properties
    pageTitle="Solucionar problemas de verificação em duas etapas | Microsoft Azure"
    description="Este documento fornecerá usuários informações sobre o que fazer se eles tenha um problema com a autenticação multifator Azure."
    services="multi-factor-authentication"
    keywords = "cliente de autenticação multifator, problemas de autenticação, ID de correlação"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="having-trouble-with-two-step-verification"></a>Está tendo problemas com a verificação

Este artigo descreve alguns dos problemas que podem ocorrer com a verificação. Se o problema que você está tendo não é incluído aqui, forneça comentários detalhados na seção de comentários para que possamos melhorar.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Perdi meu telefone ou ele foi roubado

Há duas maneiras para voltar sua conta. A primeira é entrar usando seu número de telefone alternativo de autenticação, se você tiver configurado uma. O segundo é peça ao seu administrador para limpar suas configurações.

Se seu telefone foi perdido ou roubado, também é recomendável que você tem o administrador redefinir as senhas de aplicativo e desmarque qualquer lembrado dispositivos. Se seu administrador não certeza de como fazer isso, aponte para este artigo: [Gerenciar usuários e dispositivos](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords).


### <a name="use-an-alternate-phone-number"></a>Usar um número de telefone alternativo

Se você tiver configurado várias opções de verificação, incluindo um número de telefone secundário ou em um aplicativo de autenticador em um dispositivo diferente, você pode usar um destes procedimentos para entrar.

Para entrar usando o número de telefone alternativo, siga estas etapas:

1. Entrar como você faria normalmente.
2. Quando solicitado a verificar ainda mais sua conta, escolha **usar uma opção de verificação diferente**.

    ![Verificação diferente](./media/multi-factor-authentication-end-user-manage/differentverification.png)

3. Selecione o número de telefone que você tem acesso a.

    ![Telefone alternativos](./media/multi-factor-authentication-end-user-manage/altphone2.png)

4. Depois que você volta em sua conta, [Gerenciar suas configurações](multi-factor-authentication-end-user-manage-settings.md) alterar o número do seu telefone de autenticação.

>[AZURE.IMPORTANT]
>É importante configurar um número de telefone de autenticação secundária. Se seu número de telefone primário e seu aplicativo móvel estiverem no mesmo telefone, você precisa de uma terceira opção se seu telefone é perdido ou roubado.

### <a name="clear-your-settings"></a>Desmarque as configurações

Se você não tiver configurado um número de telefone de autenticação secundária, será necessário entrar em contato com seu administrador para obter ajuda. Peça desmarcar suas configurações para que na próxima vez que você entrar, você será solicitado a [configurar sua conta](multi-factor-authentication-end-user-first-time.md) novamente.


## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Eu não estou recebendo um texto ou chamada em meu telefone

Há vários motivos por que você pode tentar entrar, mas não receberá o texto ou uma chamada telefônica. Se você com êxito recebeu textos ou telefonemas para o seu telefone no passado, em seguida, isso provavelmente é um problema com o provedor de telefone, não sua conta. Certifique-se de que você tenha o sinal de célula boa, e se você está tentando receber uma mensagem de texto Certifique-se de que seu plano de telefone e serviço oferece suporte para mensagens de texto.

Se você tiver demorou alguns minutos para que um texto ou uma chamada, a maneira mais rápida de obter para sua conta é tentar uma opção diferente.

1. Selecione **usar uma opção de verificação diferente** na página que está aguardando sua verificação.

    ![Verificação diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

2. Selecione o método de número ou de entrega de telefone que você deseja usar.

    Se você recebeu vários códigos de verificação, funciona apenas aquele mais recente.

Se você não tiver outro método configurado, contate seu administrador e peça-lhe para desmarcar suas configurações. Na próxima vez que você entrar, você será solicitado a [Configurar a autenticação multifator](multi-factor-authentication-end-user-first-time.md) novamente.


Se você tiver muitas vezes atrasos devido a sinal de célula incorreta, recomendamos que você use o [aplicativo Microsoft autenticador](multi-factor-authentication-microsoft-authenticator.md) no smartphone. O aplicativo pode gerar códigos de segurança aleatória que você usa para entrar e esses códigos não exigem qualquer conexão de internet ou de sinal de célula.


## <a name="app-passwords-are-not-working"></a>Senhas de aplicativos não estão funcionando

Primeiro, certifique-se de que você digitou a senha do aplicativo corretamente.  Se ele ainda não estiver funcionando, tente entrar no e [criar uma nova senha de aplicativo](multi-factor-authentication-end-user-app-passwords.md).  Se isso não funcionar, contate o administrador e tê-los [excluir suas senhas existentes do aplicativo](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords) e, em seguida, você pode criar um novo.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Eu não encontrar uma resposta para o meu problema.

Se você já tentou fazer essas etapas de solução de problemas, mas ainda está em execução com problemas, contate o administrador ou a pessoa que configurou a autenticação multifator para você. Eles devem ser capazes de ajudá-lo.

Além disso, você pode postar uma pergunta no [Azure AD fóruns](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou [contate o suporte](https://support.microsoft.com/contactus) e podemos deverá responder para o seu problema, assim como podemos.

Se você contatar o suporte, inclua as seguintes informações:

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
- [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)  
- [Perguntas Frequentes do aplicativo Microsoft Authenticator](multi-factor-authentication-app-faq.md)
