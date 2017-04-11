<properties
   pageTitle="Diretrizes de identidade visual para aplicativos | Microsoft Azure"
   description="Um guia abrangente para recursos orientados a desenvolvedor para o Active Directory do Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# <a name="branding-guidelines-for-applications"></a>Diretrizes de identidade visual para aplicativos


Este tópico aborda as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Azure Active Directory (AD Azure). Estas diretrizes ajudará direcione seus clientes quando eles desejam usar sua conta corporativa ou escolar, gerenciada no Azure AD, ou seu pessoal conta para inscrição e entrar no seu aplicativo.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Contas pessoais versus trabalho ou escola da Microsoft

Microsoft gerencia dois tipos de contas de usuário:

- **Contas pessoais** (conhecido anteriormente como Windows Live ID). Essas contas representam a relação entre os usuários *individuais* e a Microsoft e são usadas para acessar os dispositivos do consumidor e serviços da Microsoft. Essas contas destinam-se para uso pessoal.

- **Contas corporativas ou de estudante.** Essas contas são gerenciadas pelo Microsoft em nome de organizações que usam o Active Directory do Azure. Essas contas são usadas para entrar no Office 365 e outros serviços de negócios da Microsoft.

Microsoft contas corporativas ou de estudante geralmente são atribuídas a usuários finais (funcionários, alunos e funcionários federais) por suas organizações (empresa, escola, órgão governamental). Essas contas são mastered diretamente na nuvem, na plataforma do Azure AD, ou sincronizadas com o Azure AD de um diretório local, como o Active Directory do Windows Server. Microsoft é o *dos responsáveis* das contas corporativas ou de estudante, mas as contas estão pertencente e controladas pela organização.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Fazendo referência a contas do Azure AD em seu aplicativo

Microsoft não expõe os usuários finais para o Azure ou os nomes de marca do Active Directory e nenhuma delas você deve.

- Depois que os usuários estiver conectados, você deve usar o nome da organização e o logotipo tanto quanto possível. Este é melhor que usar termos genéricos como "sua organização".

- Quando os usuários não estiver conectados, você deve se referir a suas contas como "trabalho ou escola contas" e usar o logotipo da Microsoft para transmitir que essas contas são gerenciadas pela Microsoft. Não use termos como "conta da empresa", "business conta" ou "conta corporativa" que criam confusão do usuário.

## <a name="user-account-pictogram"></a>Pictogram de conta de usuário
Em uma versão anterior dessas diretrizes, é recomendável usar um pictogram "crachá azul". Com base nos comentários de usuário e desenvolvedor, agora recomendamos o uso do logotipo do Microsoft em vez disso. Isso ajudará os usuários a entender o que eles podem reutilizar a conta usarem com o Office 365 ou outros Microsoft dos serviços corporativos para entrar em seu aplicativo.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Inscrição e entrar com Azure AD

Seu aplicativo pode apresentar caminhos separados para inscrição e entrar e as seguintes seções fornecem orientações visual para os dois cenários.

**Se seu aplicativo dá suporte a entrada do usuário final para cima (por exemplo, gratuita ao modelo de avaliação ou freemium)**: você pode mostrar um botão de **entrada** que permite que usuários acessem seu aplicativo com sua conta de trabalho ou sua conta pessoal. Azure AD mostrará um prompt de consentimento na primeira vez em que eles acessar seu aplicativo.

**Se seu aplicativo exigir permissões que somente administradores podem consentimento, ou se seu aplicativo requer licenciamento organizacional**: você deve separar aquisição de administração de usuário de entrada. O **botão "obter este aplicativo"** redirecionará administradores para entrar e peça-lhe conceder consentimento em nome de usuários na organização. Isso tem a vantagem de suprimir avisos de consentimento de usuários finais para seu aplicativo.

## <a name="visual-guidance-for-app-acquisition"></a>Orientação visual para aquisição de aplicativo

O link "obter o aplicativo" deve redirecionar o usuário para conceder acesso Azure AD (Autorizar) página, para permitir que o administrador da organização autorizar seu aplicativo tenham acesso aos dados da sua organização que estão hospedados pela Microsoft. Para obter detalhes sobre como solicitar acesso são discutidas no artigo [Aplicativos de integração com o Active Directory do Azure](active-directory-integrating-applications.md) .

Depois que os administradores consentimento para o seu aplicativo, eles podem optar por adicioná-lo à experiência aplicativo de iniciador do Office 365 dos seus usuários (acessível a partir do waffle quanto do [https://portal.office.com/myapps](https://portal.office.com/myapps)). Se você quiser anunciar esse recurso, você pode usar termos como "Adicionar este aplicativo à sua organização" e mostrar um botão assim:

![Tipos de aplicativos e cenários](./media/active-directory-branding-guidelines/add-to-my-org.png)

No entanto, recomendamos que você escreva um texto explicativo em vez de depender de botões. Por exemplo:
> *Se você já utiliza o Office 365 ou outro serviço de negócios da Microsoft, você pode simplesmente conceder < your_app_name > acesso aos dados da sua organização. Isso permitirá que seus usuários acessem < your_app_name > com suas contas de trabalho existente.*


## <a name="visual-guidance-for-sign-in"></a>Orientação visual para entrar
Seu aplicativo deve exibir uma entrada no botão que redireciona os usuários para a empresa entrada que corresponde ao protocolo que usar para integrar com o Azure AD. A seção a seguir fornece detalhes sobre o que deve ser a aparência de que o botão.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictogram e "Entrar com Microsoft"
É a associação do logotipo do Microsoft e os termos de "Sinal em com Microsoft" que representa com exclusividade Azure AD entre outros provedores de identidade seu aplicativo pode oferecer suporte. Se você não tem espaço suficiente para "Entrada em com Microsoft", é okey para diminua-a para "Entrada".

![Tipos de aplicativos e cenários](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Tipos de aplicativos e cenários](./media/active-directory-branding-guidelines/sign-in-light.png)

Você também pode usar um esquema de cor escura para os botões.

![Tipos de aplicativos e cenários](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Tipos de aplicativos e cenários](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Regras de identidade Visual

**Faça** usar "conta corporativa ou escolar" em combinação com o botão "Logon em com Microsoft" para fornecer uma explicação adicional para ajudar os usuários finais reconhecer se eles podem usá-lo. **Não** use outros termos como "conta da empresa", "conta comercial" ou "conta corporativa".

**Não** use "ID do Office 365" ou "ID do Azure". O Office 365 também é o nome de um consumidor oferta da Microsoft que não usa o Azure AD para autenticação.

**Não** altere o logotipo da Microsoft.

**Não** expõem os usuários finais para as marcas Azure ou do Active Directory. Okey Entretanto é usar esses termos com os desenvolvedores, os profissionais de TI e administradores.

## <a name="navigation-dos-and-donts"></a>Regras de navegação

**Fornecem uma maneira para os usuários sair e alternar para outra conta de usuário.** Embora a maioria das pessoas tenham uma única conta pessoal da Microsoft/Facebook/Google/Twitter, as pessoas costumam ser associadas com mais de uma organização. Suporte para vários usuários assinado é em breve.
