<properties
    pageTitle="Como funciona: gerenciamento de senha do Azure AD | Microsoft Azure"
    description="Saiba mais sobre os diferentes componentes do gerenciamento de senha do Azure AD, incluindo onde os usuários registrar, redefinir e alterarem suas senhas e onde os administradores configurar, relatar e habilitar o gerenciamento de senhas do Active Directory local."
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

# <a name="how-password-management-works"></a>Como funciona o gerenciamento de senha

> [AZURE.IMPORTANT] **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).

Gerenciamento de senha do Azure Active Directory é composto de vários componentes lógicos que são descritos abaixo.  Clique em cada link para saber mais sobre esse componente.

- [**Portal de configuração de gerenciamento de senha**](#password-management-configuration-portal) – os administradores podem controlar diferentes aspectos de como as senhas são gerenciadas em seus locatários navegando para a guia de configurar do seu diretório no [Portal de gerenciamento do Azure](https://manage.windowsazure.com).
- [**Portal de registro do usuário**](#user-registration-portal) – os usuários podem registrar para redefinir pelo portal web sua senha.
- [**Portal de redefinição de senha do usuário**](#user-password-reset-portal) – os usuários podem redefinir suas próprias senhas usando um número de desafios diferentes de acordo com a política de redefinição de senha controladas pelo administrador
- [**Portal de mudar de senha do usuário**](#user-password-change-portal) – os usuários podem alterar suas próprias senhas a qualquer momento inserindo sua senha antiga e selecionando uma nova senha usando este portal da web
- [**Relatórios de gerenciamento de senha**](#password-management-reports) – os administradores podem exibir e analisar atividade de registro e redefinição de senha em seu locatário por meio de navegação para a seção "Relatórios de atividade" da guia de "Relatórios" do seu diretório no [Portal de gerenciamento do Azure](https://manage.windowsazure.com)
- [**Componente de write-back de senha do Azure AD Connect**](#password-writeback-component-of-azure-ad-connect) – administradores, opcionalmente, podem habilitar o recurso de write-back de senha ao instalar o Azure AD Connect para habilitar o gerenciamento de federado ou senha sincronizada senhas de usuário da nuvem.

## <a name="password-management-configuration-portal"></a>Portal de configuração de gerenciamento de senha
Você pode configurar políticas de gerenciamento de senha para uma pasta específica usando o [Portal de gerenciamento do Azure](https://manage.windowsazure.com) , navegando até a seção de **Política de redefinição de senha do usuário** na guia de **configuração** do diretório.  Nesta página de configuração, você pode controlar vários aspectos de como as senhas são gerenciadas em sua organização, incluindo:

- Ativando e desativando redefinição para todos os usuários em um diretório de senha
- Definindo o número de desafios (um ou dois) um usuário deve percorrer para redefinir sua senha
- Definindo os tipos específicos de desafios que você deseja habilitar para os usuários em sua organização nas opções abaixo:
 - Telefone celular (um código de verificação por meio de texto ou uma chamada de voz)
 - Telefone comercial (uma chamada de voz)
 - Email alternativo (um código de verificação por email)
 - Perguntas de segurança (autenticação baseada em conhecimento)
- Definir o número de perguntas um usuário deve registrar para poder usar o método de autenticação de perguntas de segurança (visível somente se perguntas de segurança habilitadas)
- Definir o número de perguntas um usuário deve fornecer durante redefinição para usar o método de autenticação de perguntas de segurança (visível somente se perguntas de segurança habilitadas)
- Usar perguntas de segurança pré-configurado localizados, que um usuário pode optar por usar ao registrar senha redefinir (visível somente se perguntas de segurança habilitadas)
- Definir as perguntas de segurança personalizada que um usuário pode optar por usar ao registrar senha redefinir (visível somente se perguntas de segurança habilitadas)
- Exigir que os usuários para se registrar no quando eles ir para o painel de acesso do aplicativo em [http://myapps.microsoft.com](http://myapps.microsoft.com)de redefinição de senha.
- Os usuários que requerem Reconfirme seus dados anteriormente registrados após um número de dias configurável passaram (visíveis somente se registro imposto está habilitado)
- Fornecendo um email de suporte técnico personalizado ou uma URL que será mostrada aos usuários caso eles tenham algum problema redefinir suas senhas
- Ativar ou desativar o recurso de senha write-back (quando write-back senha tenha sido implantado usando a conexão de AAD)
- Exibir o status do agente de write-back senha (quando write-back senha tenha sido implantado usando a conexão de AAD)
- Ativar notificações por email aos usuários quando sua própria senha for redefinida (encontrado na seção **notificações** do [Portal de gerenciamento do Azure](https://manage.windowsazure.com))
- Ativar notificações de email para administradores quando outros administradores redefinam suas próprias senhas (encontradas na seção **notificações** do [Portal de gerenciamento do Azure](https://manage.windowsazure.com)
- Identidade visual a senha do usuário Redefinir portal e emails com o logotipo da sua organização e o nome de redefinição de senha usando o recurso de personalização (encontrado na seção **Propriedades do diretório** do [Portal de gerenciamento do Azure](https://manage.windowsazure.com) de identidade visual de locatário

Para saber mais sobre como configurar o gerenciamento de senha em sua organização, consulte [Introdução: gerenciamento de senha do Azure AD](active-directory-passwords-getting-started.md).

##<a name="user-registration-portal"></a>Portal de registro do usuário
Antes que os usuários são capazes de usar redefinição de senha, suas contas de usuário de nuvem devem ser atualizadas com os dados de autenticação correta para garantir que eles podem passar pelo número apropriado de desafios de redefinição de senha definidos por seu administrador.  Os administradores também podem definir essas informações de autenticação em nome do seu usuário usando os Azure ou Office web portais, DirSync / Azure AD Connect, ou o Windows PowerShell.

No entanto, se você prefere que seus usuários registrar seus próprios dados, também fornecemos uma página da web que os usuários podem ir para fornecer essas informações.  Esta página permitirá que os usuários especifiquem informações de autenticação de acordo com a senha redefinir políticas que tenham sido habilitadas em sua organização.  Depois que esses dados são verificados, ele é armazenado em sua conta de usuário de nuvem a ser usado para recuperação de conta mais tarde. Veja aqui o que o registro portal aparência semelhante a:

  ![][001]

Para obter mais informações, consulte [Introdução: gerenciamento de senha do Azure AD](active-directory-passwords-getting-started.md) e [práticas recomendadas: gerenciamento de senha do Azure AD](active-directory-passwords-best-practices.md).

##<a name="user-password-reset-portal"></a>Portal de redefinição de senha do usuário
Após ter ativado senha de autoatendimento redefinir, configurar a política de redefinição de senha de autoatendimento da sua organização e verificado que seus usuários tiverem os dados de contato apropriados no diretório, os usuários de sua organização poderão redefinir suas próprias senhas automaticamente a partir de qualquer página da web que usa uma conta de trabalho ou escola para entrar (como [portal.microsoftonline.com](https://portal.microsoftonline.com)). Nas páginas como esses, os usuários verão um **não é possível acessar sua conta?** link.

  ![][002]

Clicando no link iniciará o portal de redefinição de senha de autoatendimento.

  ![][003]

Para saber mais sobre como os usuários podem redefinir suas próprias senhas, consulte [Introdução: gerenciamento de senha do Azure AD](active-directory-passwords-getting-started.md).

##<a name="user-password-change-portal"></a>Portal de alteração de senha do usuário
Se os usuários desejarem alterar suas próprias senhas, eles podem fazer isso usando o portal de alteração de senha a qualquer momento.  Os usuários podem acessar o portal de alteração de senha por meio de página de perfil do painel de acesso ou clicando no link "Alterar senha" de dentro de aplicativos do Office 365.  No caso de quando suas senhas expiram, os usuários também deverá alterá-las automaticamente quando entrar.

  ![][004]

Em ambos os casos, se Write-back senha foi habilitada e o usuário ou federado ou seria de sincronização de senha, essas senhas alteradas são gravadas novamente seu Active Directory local. Aqui está o a aparência de portal de alteração de senha:

  ![][005]

Para saber mais sobre como os usuários podem alterar suas próprias senhas do Active Directory local, consulte [Introdução: gerenciamento de senha do Azure AD](active-directory-passwords-getting-started.md).

##<a name="password-management-reports"></a>Relatórios de gerenciamento de senha
Navegando até a guia **relatórios** e procurando sob a seção de **Logs de atividades** , você verá dois relatórios de gerenciamento de senha: **atividade de redefinição de senha** e a **atividade de registro de redefinição de senha**.  Usando esses dois relatórios, você pode obter um modo de exibição de usuários inscrever e usar senha redefinir em sua organização. Aqui está a aparência desses relatórios no [Portal de gerenciamento do Azure](https://manage.windowsazure.com):

  ![][006]

Para obter mais informações, consulte [obter ideias: relatórios de gerenciamento de senha do Azure AD](active-directory-passwords-get-insights.md).

##<a name="password-writeback-component-of-azure-ad-connect"></a>Componente de write-back de senha do Azure AD Connect
Se as senhas dos usuários da sua organização se originam de seu ambiente local (seja via federação ou sincronização de senha), você pode instalar a versão mais recente do Azure AD Connect para ativar a atualização essas senhas diretamente da nuvem.  Isso significa que quando os usuários esquecer ou deseja modificar a senha dele AD, ele pode fazer portanto diretamente da web.  Veja aqui onde encontrar write-back senha no Assistente de instalação Azure AD Connect:

  ![][007]

Para obter mais informações sobre o Azure AD Connect, consulte [Introdução: Azure AD Connect](active-directory-aadconnect.md). Para obter mais informações sobre write-back de senha, consulte [Introdução: gerenciamento de senha do Azure AD](active-directory-passwords-getting-started.md).


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Links para senha redefinir documentação
A seguir são links para todas as páginas de documentação de redefinição de senha do Azure AD:

* **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).
* [**Introdução**](active-directory-passwords-getting-started.md) - Aprenda a permitem aos usuários reiniciar e alterar suas senhas de nuvem ou local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar a aparência e a aparência e o comportamento do serviço às necessidades da sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) – Saiba como implantar rapidamente e efetivamente gerenciar senhas em sua organização
* [**Obter ideias**](active-directory-passwords-get-insights.md) - Saiba mais sobre nossos recursos de geração de relatórios integrados
* [**Perguntas Frequentes**](active-directory-passwords-faq.md) - Obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - aprender rapidamente solucionar problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) - vá profunda os detalhes técnicos de como funciona o serviço



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
