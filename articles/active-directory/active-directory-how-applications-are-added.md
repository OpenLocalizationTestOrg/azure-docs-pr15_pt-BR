<properties
   pageTitle="Como os aplicativos são adicionados ao Azure Active Directory."
   description="Este artigo descreve como os aplicativos são adicionados a uma instância do Active Directory do Azure."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e por que aplicativos são adicionados ao Azure AD

Uma das coisas inicialmente confusos ao exibir uma lista de aplicativos em sua instância do Active Directory do Azure é entender onde os aplicativos vêm e por que eles estão lá.  Este artigo fornecerá uma visão geral de alto nível de como os aplicativos são representados no diretório e fornecem contexto que ajudará Noções básicas sobre como um aplicativo veio até ser no diretório.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Quais serviços Azure AD oferece aos aplicativos?

Aplicativos são adicionados ao Azure AD para aproveitar um ou mais dos serviços que ele fornece.  Esses serviços incluem:

* Autorização e autenticação de aplicativo
* Autorização e autenticação do usuário
* Logon único (SSO) usando federação ou senha
* Provisionamento do usuário & sincronização
* Controle de acesso baseado em função; Use o diretório para definir funções de aplicativo para executar funções com base em verificações de autorização em um aplicativo.
* Serviços de autorização oAuth (usados pelo Office 365 e outros aplicativos do Microsoft para autorizar acesso a APIs/recursos.)
* Publicação de aplicativo & proxy; Publicar um aplicativo de uma rede privada na Internet

## <a name="how-are-applications-represented-in-the-directory"></a>Como os aplicativos são representados no diretório?

Aplicativos são representados no Azure AD usando 2 objetos: um objeto de aplicativo e um objeto de serviço.  Há um objeto de aplicativo, registrado em uma "casa" / "proprietário" ou "publicação" diretório e um ou mais objetos que representa o aplicativo em todas as pastas em que ele atua de serviço.  

O objeto de aplicativo descreve o aplicativo para o Azure AD (o serviço de vários Locatário) e pode incluir qualquer um dos seguintes: (*Observação*: isso não é uma lista completa.)

* Nome, logotipo e Publisher
* Segredos (chaves simétricas e/ou assimétricos, usados para autenticar o aplicativo)
* Dependências de API (oAuth)
* APIs/recursos/escopos publicados (oAuth)
* Funções de aplicativo (RBAC)
* Metadados SSO e configuração (SSO)
* Provisionamento de metadados e configuração do usuário
* Configuração e metadados de proxy

O princípio de serviço é um registro do aplicativo em cada diretório, onde o aplicativo age incluindo seu diretório inicial.  O serviço principal:

* Refere-se novamente a um objeto de aplicativo por meio da propriedade de id de aplicativo
* Usuário local de registros e atribuições de função de aplicativo de grupo
* Permissões de usuário e administrador locais de registros concedido ao aplicativo
    * Por exemplo: permissão para o aplicativo acessar um email de usuários específicos
* Diretivas locais registros, incluindo a política de acesso condicional
* Registros local alternativas configurações locais para um aplicativo
    * Regras de transformação de declarações
    * Mapeamentos de atributo (provisionamento de usuário)
    * Funções de aplicativo específico de inquilinos (se o aplicativo dá suporte a funções personalizadas)
    * Nome/logotipo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Um diagrama de objetos de aplicativo e objetos de serviço em diretórios

![Um diagrama que ilustra como o aplicativo objetos e objetos existentes com instâncias do Azure AD do serviço.][apps_service_principals_directory]

Como você pode ver do diagrama acima.  A Microsoft mantém dois diretórios internamente (à esquerda), ele usa para publicar aplicativos.

* Uma para Microsoft Apps (diretório de serviços da Microsoft)
* Uma para os aplicativos de terceiros 3º previamente integrados (diretório de galeria de aplicativos)

Fornecedores/fornecedores de aplicativo que integrar com o Azure AD são necessários para ter um diretório de publicação.  (Alguns diretório SAAS).

Os aplicativos que você adicionar você mesmo incluem:

* Aplicativos que você desenvolveu (integrados com AAD)
* Aplicativos conectados para logon único
* Aplicativos que você publicou usando o proxy de aplicativo do Azure AD.

### <a name="a-couple-of-notes-and-exceptions"></a>Algumas exceções e anotações

* Nem todos os objetos de serviço apontem para objetos de aplicativo.  Não é? Quando Azure AD foi originalmente criado os serviços fornecidos a aplicativos eram muito mais limitados e o capital de serviço foi suficiente para estabelecer uma identidade de aplicativo.  O serviço original principal foi detalhado na forma para a conta de serviço do Active Directory do Windows Server.  Por esse motivo, é possível criar objetos de serviço usando o PowerShell do Azure AD sem primeiro criar um objeto de aplicativo.  A API de gráfico requer um objeto de aplicativo antes de criar um serviço principal.
* Atualmente, não de todas as informações descritas acima é exposto por programação.  A seguir está disponível apenas na interface de usuário:
    * Regras de transformação de declarações
    * Mapeamentos de atributo (provisionamento de usuário)
* Para obter mais informações detalhadas sobre o capital de serviço e objetos de aplicativo, consultem a documentação de referência da API REST de Azure AD Graph.  *Dica*: documentação da API do gráfico de AD o Azure é a mais próxima a uma referência de esquema para anúncio Azure que está disponível no momento.  
    * [Aplicativo](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Serviço de capital](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Como os aplicativos são adicionados ao meu instância do Azure AD?
Há várias maneiras de que um aplicativo pode ser adicionado ao Azure AD:

* Adicionar um aplicativo da [Galeria de aplicativo do Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Sinal superiores/em uma 3º festa aplicativo integrado ao Azure Active Directory (por exemplo: [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * Durante a inscrição para cima/em usuários será solicitado para fornecer permissão para o aplicativo para acessar o seu perfil e outras permissões.  A primeira pessoa dar consentimento faz com que uma entidade de serviço que representa o aplicativo a ser adicionado ao diretório.
* Entrar para cima/para serviços online da Microsoft como o [Office 365](http://products.office.com/)
    * Quando você se inscrever para o Office 365 ou começar uma avaliação um ou mais objetos de serviço são criados no diretório que representa os vários serviços que são usados para entregar todas as funcionalidades associada ao Office 365.
    * Alguns serviços do Office 365 como o SharePoint criar objetos de serviço de forma contínua para permitir a comunicação segura entre componentes, incluindo fluxos de trabalho.
* Adicionar um aplicativo que você está desenvolvendo na seção Consulte Portal de gerenciamento do Azure: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Adicione um aplicativo que você está desenvolvendo usando o Visual Studio consulte:
    * [Métodos de autenticação do ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Serviços conectados](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Adicionar um aplicativo usar para usar o [Proxy de aplicativo do Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Conectar um aplicativo para logon único usando SAML ou SSO de senha
* Várias outras incluindo várias experiências de desenvolvedor no Azure e/no Explorador de API experiências entre centros de desenvolvedor

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicativos a minha instância do Azure AD?

Somente os administradores globais podem:

* Adicionar aplicativos da Galeria de aplicativo do Azure AD (previamente 3º festa recurso aplicativos integrados)
* Publicar um aplicativo usando o Proxy de aplicativo Azure AD

Todos os usuários no seu diretório tem direitos para adicionar aplicativos que estão desenvolvendo e critério sobre quais aplicativos eles compartilhamento/conceder acesso aos seus dados organizacionais.  *Lembre-se de entrada do usuário para cima/em um aplicativo e conceder permissões pode resultar em uma entidade de serviço sendo criada.*

Este pode inicialmente som relacionadas, mas manter o seguinte em mente:

* Aplicativos foram capazes de aproveitar o Active Directory do Windows Server para autenticação do usuário para muitos anos sem exigir que o aplicativo seja registrado/registradas no diretório.  Agora a organização será aprimorados visibilidade exatamente quantos aplicativos estão usando o diretório e quais for.
* Não é necessário para administração controlado pelo processo de publicação/registro do aplicativo.  Com os serviços de Federação do Active Directory era provável que um administrador tinha que adicionar um aplicativo como parte confiante em nome de desenvolvedores.  Agora os desenvolvedores podem autoatendimento.
* Usuários entrando em/até aplicativos usando suas contas de organização para fins de negócios é uma boa coisa.  Se posteriormente deixam a organização que eles perderão o acesso à sua conta no aplicativo estivessem usando-o.
* Ter um registro das quais dados foi compartilhados com o qual o aplicativo é uma boa coisa.  Dados são mais transportáveis que nunca e ter um registro limpar das quem compartilhou quais dados com quais aplicativos é útil.
* Aplicativos que usam o Azure AD para oAuth decidir exatamente quais permissões que os usuários podem conceder aos aplicativos e que precisam de permissões de administrador para concordar.  Ele deve ir sem dizendo que somente administradores podem consentimento para escopos maiores e mais significativas permissões.
* Usuários adicionando e permitir que aplicativos acessem seus dados são auditados eventos para poder visualizar os relatórios de auditoria dentro do portal de gerenciamento do Azure para determinar como um aplicativo foi adicionado ao diretório.

**Observação:** *Própria Microsoft foi operacional usando a configuração padrão para muitos meses agora.*

Com todos que disseram é possível impedir que usuários no seu diretório adicionando aplicativos e de exercer critério sobre quais informações eles compartilhar com aplicativos modificando a configuração de diretório no portal de gerenciamento do Azure.  A seguinte configuração pode ser acessada dentro do portal de gerenciamento do Azure em Guia de "Configurar" do seu diretório.

![Uma captura de tela da interface do usuário para definir configurações de aplicativo integrado][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como adicionar aplicativos Azure AD e como configurar serviços para os aplicativos.

* Desenvolvedores: [Aprenda a integrar um aplicativo com AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Desenvolvedores: [código de exemplo de revisão para aplicativos integrados com o Azure Active Directory no Github](https://github.com/AzureADSamples)
* Os desenvolvedores e profissionais de TI: [Revisar a documentação da API REST para a API do Azure Active Directory Graph](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Profissionais de TI: [Saiba como usar aplicativos previamente integrados do Active Directory do Azure da Galeria de aplicativo](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Os profissionais de TI: [localizar tutoriais para configurar aplicativos previamente integrados específicos](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Profissionais de TI: [Aprenda como publicar um aplicativo usando o Proxy de aplicativo do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Consulte também

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
