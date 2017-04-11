<properties
    pageTitle="Práticas recomendadas: Gerenciamento de senha do Azure AD | Microsoft Azure"
    description="Práticas recomendadas de implantação e uso, documentação de usuário final de exemplo e guias de treinamento para o gerenciamento de senha no Active Directory do Azure."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="deploying-password-management-and-training-users-to-use-it"></a>Implantação do gerenciamento de senha e treinar usuários usá-lo

> [AZURE.IMPORTANT] **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).

Depois de redefinição de senha de ativação, a próxima etapa, que você precisa fazer é obter usuários usando o serviço de sua organização. Para fazer isso, você precisará certificar-se de que os usuários estão configurados para usar o serviço corretamente e também que os usuários tenham o treinamento que eles precisam ser bem-sucedido gerenciar suas próprias senhas. Este artigo explica a você os conceitos a seguir:

* [**Como obter seus usuários configurados para gerenciamento de senha**](#how-to-get-users-configured-for-password-reset)
  * [O que faz uma conta configurada para redefinição de senha](#what-makes-an-account-configured)
  * [Métodos para preencher dados de autenticação por conta própria](#ways-to-populate-authentication-data)
* [**As melhores maneiras de implementar o senha Redefinir para sua organização**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Distribuição baseada em email + amostra comunicações de email](#email-based-rollout)
  * [Criar seu próprio portal de gerenciamento de senha personalizada para seus usuários](#creating-your-own-password-portal)
  * [Como usar o registro imposto forçar os usuários a registrar arroba no](#using-enforced-registration)
  * [Como carregar dados de autenticação para contas de usuário](#uploading-data-yourself)
* [**Usuário de amostra e materiais de treinamento de suporte (em breve!)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>Como obter os usuários configurados para redefinição de senha
Esta seção descreve a você vários métodos pelos quais você pode garantir que todos os usuários em sua organização podem usar a senha de autoatendimento redefinir efetivamente caso eles esquecerem sua senha.

### <a name="what-makes-an-account-configured"></a>O que faz uma conta configurada
Antes de um usuário pode usar redefinição de senha, **todas** as seguintes condições devem ser atendidas:

1.  Redefinição de senha deve estar habilitada no diretório.  Saiba como habilitar redefinição lendo [Permitir que os usuários redefinam suas senhas AD Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) ou [Permitir que os usuários reconfigure ou altere suas senhas AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) de senha
2.  O usuário deve ser licenciado.
 - Para usuários de nuvem, o usuário deve ter **qualquer licença paga do Office 365**, ou um **AAD básica** ou **AAD Premium licença** atribuída.
 - Para local usuários (federado ou hash sincronizado), o usuário **deve ter uma licença de AAD Premium atribuída**.
3.  O usuário deve ter o **conjunto mínimo de dados de autenticação definidos** de acordo com a política de redefinição de senha atual.
 - Dados de autenticação são considerados definido se o campo correspondente no diretório contém dados válidos.
 - Um conjunto mínimo de dados de autenticação é definido como pelo **menos um** das opções de autenticação habilitado se uma política de um gate estiver configurada, ou em **pelo menos duas** das opções de autenticação habilitado se uma política de dois gate estiver configurada.
4.  Se o usuário estiver usando uma conta local, em seguida, [Senha write-back](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) deve ser habilitado e ativado

### <a name="ways-to-populate-authentication-data"></a>Maneiras de preencher os dados de autenticação
Você tem várias opções sobre como especificar dados para usuários em sua organização a ser usado para redefinição de senha.

- Editar usuários no [Portal de gerenciamento do Azure](https://manage.windowsazure.com) ou o [Portal de administração do Office 365](https://portal.microsoftonline.com)
- Usar a sincronização do Azure AD para sincronizar propriedades do usuário no Azure AD de um domínio do Active Directory local
- Use o Windows PowerShell para editar as propriedades de usuário seguindo [as etapas aqui](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Permitir que usuários se registrar seus próprios dados, orientando-los para o portal de registro em [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Exigir que os usuários registrar para redefinir quando eles entrar à sua conta do Azure AD definindo a senha do [**exigir que os usuários registrar quando entrar?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) opção de configuração para **Sim**.

Os usuários não precisam registrar para redefinir para o sistema funcionar a senha.  Por exemplo, se você tiver o celular existente ou números de telefone do office em seu diretório local, você pode sincronizá-las no Azure AD e usaremos-los para redefinir automaticamente a senha.

Você também pode ler mais sobre [como os dados são usados por senha redefinir](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) e [como você pode preencher campos individuais de autenticação com o PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>O que é a melhor maneira de implementar o redefinição para usuários de senha?
A seguir estão as etapas de implementação geral para redefinição de senha:

1.  Habilite senha Redefinir no seu diretório indo para a guia **Configurar** no [Portal de gerenciamento do Azure](https://manage.windowsazure.com) e selecionando **Sim** para a opção **Os usuários habilitados para redefinição de senha** .
2.  Atribuir licenças apropriadas para cada usuário a quem você gostaria de oferecer senha redefinir na, indo para a guia **licenças** no [Portal de gerenciamento do Azure](https://manage.windowsazure.com).
3.  Opcionalmente restringir senha Redefinir para um grupo de usuários para implementar o recurso lentamente ao longo do tempo definindo o botão de alternância **Restringir acesso para redefinição de senha** para **Sim** e selecionando um grupo de segurança para habilitar para redefinição de senha (Observe todos esses usuários devem ter licenças atribuídas a eles).
4.  Instrua os usuários a usar senha redefinir qualquer enviando-os um email com instruções para registrar, permitindo que impostas registro no painel de acesso ou carregando os dados de autenticação adequado para esses usuários via DirSync, PowerShell ou o [Portal de gerenciamento do Azure](https://manage.windowsazure.com).  Mais detalhes são apresentados abaixo.
5.  Ao longo do tempo, examine os usuários registrando navegando até a guia relatórios e exibindo o relatório de [**Atividade de registro de redefinição de senha**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) .
6.  Após ter registrado um bom número de usuários, assista usar senha redefinir navegando até a guia relatórios e exibindo o relatório de [**Atividade de redefinição de senha**](active-directory-passwords-get-insights.md#view-password-reset-activity) .

Há várias maneiras para informar aos usuários que eles podem se inscrever e usar senha redefinir em sua organização.  Eles são detalhados abaixo.

### <a name="email-based-rollout"></a>Distribuição baseada em email
Talvez a abordagem mais simples para informar seus usuários para se inscrever ou usar senha redefinir está enviando um email com instruções-los para fazê-lo.  Abaixo está um modelo que você pode usar para fazer isso.  Fique à vontade para substituir as cores / logotipos com os seus próprios escolhendo personalizá-lo para atender às suas necessidades.

  ![][001]

Você pode [baixar o modelo de email](http://1drv.ms/1xWFtQM).

### <a name="creating-your-own-password-portal"></a>Criar seu próprio portal de senha
Uma estratégia que funciona bem para clientes maiores Implantando recursos de gerenciamento de senha é criar um único "portal de senha" que seus usuários podem usar para gerenciar todas as coisas relacionados a suas senhas em um único local.  

Muitos de nossos clientes maior escolha para criar uma entrada DNS, raiz como https://passwords.contoso.com com links para a senha do Azure AD redefinir portal, portal de registro de redefinição de senha e páginas de alteração de senha.  Dessa forma, em qualquer comunicações por email ou folhetos que você enviar, você pode incluir uma única memorável, URL que os usuários podem ir para quando eles têm um segundo para começar a usar o serviço.

Para obter indo aqui, estamos criou uma página simple que usa os mais recentes responde UI design paradigmas e funcionará em todos os navegadores e dispositivos móveis.

  ![][007]

Você pode [baixar o modelo de site aqui](https://github.com/kenhoff/password-reset-page).  Recomendamos Personalizando o logotipo e cores para as necessidades da sua organização.

### <a name="using-enforced-registration"></a>Usando o registro imposto
Se quiser que seus usuários para registrar para redefinir próprios sua senha, você também pode forçá-los para registrar quando eles entrarem no painel de acesso em [http://myapps.microsoft.com](http://myapps.microsoft.com).  Você pode habilitar essa opção do guia de **configuração** do seu diretório habilitando a opção **Exigir aos usuários registro ao entrar no painel de acesso** .  

Você também pode definir se ele serão solicitados a registrar novamente após um período de tempo configurável modificando a opção de **número de dias antes que os usuários devem confirmar seus dados de contato** como um valor diferente de zero. Consulte [Personalizar o comportamento de gerenciamento de senha do usuário](active-directory-passwords-customize.md#password-management-behavior) para obter mais informações.

  ![][002]

Depois de habilitar essa opção, quando os usuários entram no painel de acesso, elas verão um pop-up informando que o administrador necessário-los para verificar suas informações de contato. Eles podem usá-lo para redefinir sua senha se eles nunca perderam o acesso à sua conta.

  ![][003]

Clicando em **Verificar agora** traz-los para o **portal de registro de redefinição de senha** em [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) e requer registro.  Registro por esse método pode ser descartado clicando no botão **Cancelar** ou fechar a janela, mas os usuários são lembrados toda vez entrar se eles não registrar.

  ![][004]

### <a name="uploading-data-yourself"></a>Carregando dados por conta própria
Se você deseja carregar dados autenticação por conta própria, os usuários não precisam registrar para redefinição de senha antes de poder redefinir suas senhas.  Como os usuários têm os dados de autenticação definidos na sua conta que atende a senha redefinir a política que você definiu, em seguida, esses usuários poderão redefinir suas senhas.

Para saber quais propriedades que você pode definir via AAD conectar ou o Windows PowerShell, consulte [quais dados são usados pela redefinição de senha](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Você pode carregar os dados de autenticação por meio do [Portal de gerenciamento do Azure](https://manage.windowsazure.com) seguindo as etapas abaixo:

1.  Navegue até o diretório na **extensão do Active Directory** no [Portal de gerenciamento do Azure](https://manage.windowsazure.com).
2.  Clique na guia **usuários** .
3.  Selecione o usuário que você está interessado na lista.
4.  Na primeira guia, você encontrará **Email alternativo**, que pode ser usado como uma propriedade para habilitar redefinição de senha.

    ![][005]

5.  Clique na guia **Informações de trabalho** .
6.  Nesta página, você encontrará **Telefone comercial**, **telefone celular**, **Telefone de autenticação**e **Autenticação de Email**.  Essas propriedades também podem ser definidas para permitir que um usuário redefinir sua senha.

    ![][006]

Ver [quais dados são usados pela redefinição de senha](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) para ver como cada uma dessas propriedades pode ser usada.

Consulte [como acessar senha redefinição de dados para os usuários do PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) para ver como você pode ler e configurar esses dados com o PowerShell.

## <a name="sample-training-materials"></a>Materiais de treinamento de amostra
Estamos trabalhando em materiais de treinamento de amostra que você pode usar para sua organização de TI e seus usuários a acelerar rapidamente em como implantar e usar redefinição de senha.  Fique atento!


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Links para senha redefinir documentação
A seguir são links para todas as páginas de documentação de redefinição de senha do Azure AD:

* **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).
* [**Como ele funciona**](active-directory-passwords-how-it-works.md) - Saiba mais sobre os diferentes seis componentes do serviço e o que cada
* [**Introdução**](active-directory-passwords-getting-started.md) - Aprenda a permitem aos usuários reiniciar e alterar suas senhas de nuvem ou local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar a aparência e a aparência e o comportamento do serviço às necessidades da sua organização
* [**Obter ideias**](active-directory-passwords-get-insights.md) - Saiba mais sobre nossos recursos de geração de relatórios integrados
* [**Perguntas Frequentes**](active-directory-passwords-faq.md) - Obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - aprender rapidamente solucionar problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) - vá profunda os detalhes técnicos de como funciona o serviço



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"
