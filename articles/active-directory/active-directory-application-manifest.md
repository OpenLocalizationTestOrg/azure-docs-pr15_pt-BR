<properties
   pageTitle="Noções básicas sobre o manifesto de aplicativo do Active Directory do Azure | Microsoft Azure"
   description="Cobertura detalhada de manifesto do aplicativo Active Directory do Azure, que representa a configuração de identidade de um aplicativo em um locatário do Azure AD e é usado para facilitar a autorização OAuth, experiência de consentimento e muito mais."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="dkershaw;bryanla"/>

# <a name="understanding-the-azure-active-directory-application-manifest"></a>Noções básicas sobre o manifesto de aplicativo do Active Directory do Azure

Aplicativos que se integram com o Azure Active Directory (AD) devem ser registrados com um locatário do Azure AD, fornecendo uma configuração de identidade persistente para o aplicativo. Esta configuração é consultada em tempo de execução, a habilitação dos cenários que permitem que um aplicativo terceirizado e atuar como intermediário de autenticação/autorização por meio do Azure AD. Para obter mais informações sobre o modelo de aplicativo do Azure AD, consulte o [adicionando, atualizando e removendo um aplicativo] [ ADD-UPD-RMV-APP] artigo.

## <a name="updating-an-applications-identity-configuration"></a>Atualizando configuração de identidade de um aplicativo

Há realmente várias opções disponíveis para atualizar as propriedades na configuração de identidade de um aplicativo, que variam em recursos e graus de dificuldade, incluindo o seguinte:

- O ** [Azure portal clássico] [ AZURE-CLASSIC-PORTAL] interface de usuário do Web** permite que você atualize as propriedades mais comuns de um aplicativo. Esta é a maneira de sujeito a erro mais rápida e menos de atualização propriedades do seu aplicativo, mas não dá acesso completo a todas as propriedades, como os próximos dois métodos.
- Para cenários mais avançados onde você precisa atualizar as propriedades que não estão expostas no portal de clássico do Azure, você pode modificar o **manifesto do aplicativo**. Este é o foco deste artigo e é discutido mais detalhadamente, começando na próxima seção.
- Também é possível **escrever um aplicativo que usa a [API de gráfico] [ GRAPH-API] ** para atualizar seu aplicativo, que exige mais esforço. Isso pode ser uma opção atraente que, se você estiver escrevendo o software de gerenciamento ou precisa atualizar propriedades de aplicativo regularmente de forma automática.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Usando o manifesto do aplicativo para atualizar a configuração de identidade de um aplicativo
Por meio do [portal clássico Azure][AZURE-CLASSIC-PORTAL], você pode gerenciar a configuração da identidade do seu aplicativo, baixando e carregando um JSON arquivo representação, que é chamada de um manifesto de aplicativo. Nenhum arquivo real é armazenado no diretório. Manifesto do aplicativo é simplesmente uma operação GET HTTP na entidade de aplicativo de API do Azure AD gráfico e o carregamento é uma operação HTTP PATCH na entidade de aplicativo.

Como resultado, para compreender o formato e propriedades de manifesto do aplicativo, você precisará fazer referência a API do gráfico [entidade de aplicativo] [ APPLICATION-ENTITY] documentação. Exemplos de atualizações que podem ser executadas apesar de carregamento de manifesto do aplicativo:

- **Escopos de permissão Declare (oauth2Permissions)** expostos pela sua web API. Consulte o tópico "Expor APIs para outros aplicativos Web" em [Aplicativos de integração com o Active Directory do Azure] [ INTEGRATING-APPLICATIONS-AAD] para obter informações sobre como implementar a representação de usuário usar o oauth2Permissions delegada escopo de permissão. Conforme mencionado anteriormente, propriedades de entidade de aplicativo estão documentadas no gráfico API [entidade e tipo complexo] [ APPLICATION-ENTITY] artigo de referência, incluindo a propriedade oauth2Permissions que é uma coleção de tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Funções de aplicativo de Declare (appRoles) expostas por seu aplicativo**. Propriedade de appRoles da entidade aplicativo é uma coleção de tipo [AppRole][APPLICATION-ENTITY-APP-ROLE]. Consulte o [controle de acesso em aplicativos de nuvem usando o Azure AD baseado em função] [ RBAC-CLOUD-APPS-AZUREAD] artigo para obter um exemplo de implementação.
- **Declare conhecido cliente aplicativos (knownClientApplications)**, que permitem que você vincule logicamente o consentimento dos aplicativos cliente especificado para o recurso/web API.
- **Solicitar Azure AD para emitir reivindicar associações de grupo** para o usuário conectado (groupMembershipClaims).  Isso também pode ser configurado para emitir declarações sobre associações de funções de diretório do usuário. Consulte a [autorização em aplicativos de nuvem usando grupos AD] [ AAD-GROUPS-FOR-AUTHORIZATION] artigo para obter um exemplo de implementação.
- **Permitir que o aplicativo ofereça suporte OAuth 2.0 implícitos conceder** fluxos (oauth2AllowImplicitFlow). Esse tipo de fluxo de conceder é usado com páginas da web de JavaScript incorporado ou aplicativos de página única (SPA). Para mais informações sobre a concessão de autorização implícito, consulte [Noções básicas sobre o OAuth2 implícitos conceder fluxo no Active Directory do Azure][IMPLICIT-GRANT].
- **Habilitar o uso da X509 certificados como a chave secreta** (keyCredentials). Consulte [criar aplicativos de serviço e daemon no Office 365] [ O365-SERVICE-DAEMON-APPS] [Guia do desenvolvedor para auth com a API do Gerenciador de recursos do Azure] e[ DEV-GUIDE-TO-AUTH-WITH-ARM] artigos para obter exemplos de implementação.
- **Adicionar um novo URI de ID de aplicativo** para o seu aplicativo (identifierURIs[]). URIs de ID do aplicativo são usados para identificar exclusivamente um aplicativo em seu locatário do Azure AD (ou em vários locatários do Azure AD, para vários locatários cenários quando qualificado por meio de domínio personalizado verificado). Elas são usadas quando solicitando permissões para um aplicativo de recurso, ou ao adquirir um token de acesso para um aplicativo do recurso. Quando você atualiza esse elemento, a mesma atualização é feita para servicePrincipalNames conjunto de [] do principal do serviço correspondente, o que reside no locatário inicial do aplicativo.

Manifesto do aplicativo também fornece uma boa maneira de controlar o estado de seu registro de aplicativo. Porque ele está disponível no formato JSON, a representação de arquivo pode ser verificada para o controle de origem, juntamente com o código-fonte do seu aplicativo.

## <a name="step-by-step-example"></a>Etapa por exemplo de etapa
Agora, vamos percorrer as etapas necessárias para atualizar a configuração de identidade do seu aplicativo por meio de manifesto do aplicativo. Vamos destacar um dos exemplos anteriores, mostrando como declarar um novo escopo de permissão em um aplicativo de recurso:

1. Navegue até o [portal clássico Azure] [ AZURE-CLASSIC-PORTAL] e entre com uma conta que tem privilégios de administrador ou colegas de serviço.

2. Depois que você autenticado, role para baixo e selecione a extensão "Active Directory do Azure" no painel de navegação à esquerda (1) e clique em locatário Azure AD onde seu aplicativo é registrado (2).

    ![Selecione o locatário do Azure AD][SELECT-AZURE-AD-TENANT]

3. Quando a página diretório surge, clique em "Aplicativos" (1) na parte superior da página para ver uma lista de aplicativos registrados no locatário. Localize o aplicativo que você deseja atualizar na lista e clique nela (2).

    ![Selecione o locatário do Azure AD][SELECT-AZURE-AD-APP]

4. Agora que você selecionou página principal do aplicativo, observe o recurso "Gerenciar manifesto" na parte inferior da página (1). Se você clicar nesse link, você será solicitado a baixar ou carregar o arquivo de manifesto JSON. Clique em "Download manifesto" (2). Isso será seguido imediatamente com a caixa de diálogo de confirmação de download solicitando que você confirme clicando em "Baixar manifesto" (3), e em seguida, abra ou salve o arquivo localmente (4).

    ![Gerenciar o manifesto, opção de download][MANAGE-MANIFEST-DOWNLOAD]

    ![Baixar o manifesto][DOWNLOAD-MANIFEST]

5. Neste exemplo, podemos salvou o arquivo localmente, permitindo-nos aberto em um editor, fazer alterações o JSON e salve novamente. Veja o que a estrutura JSON como fica dentro do editor do Visual Studio JSON. Observe que ela segue o esquema para a [entidade de aplicativo] [ APPLICATION-ENTITY] como mencionado anteriormente:

    ![Atualizar o JSON manifesto][UPDATE-MANIFEST]

    Por exemplo, supondo que queremos implementar/expõem uma nova permissão chamada "Employees.Read.All" em nosso aplicativo de recurso (API), você simplesmente adicionaria um elemento de novo/segundo à coleção oauth2Permissions, ie:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    A entrada deve ser exclusiva e, portanto, você deve gerar um novo exclusivo identificação GUID () para o `"id"` propriedade. Nesse caso, porque podemos especificado `"type": "User"`, essa permissão pode ser consentiu por qualquer conta autenticada pelo Azure AD locatários no qual o aplicativo de recurso/API está registrado. Isso concede o cliente permissão de aplicativo para acessá-lo em nome da conta. As cadeias de caracteres de descrição e exibição de nome são usadas durante consentimento e para a exibição no portal de clássico do Azure.

6. Quando tiver terminado de atualizar o manifesto, retorne para a página de aplicativo do Azure AD no portal de clássico do Azure e clique no "Gerenciar manifesto" recurso novamente (1). Mas desta vez, selecione a opção "Carregar manifesto" (2). Semelhante ao download, você será exibido novamente com uma segunda caixa de diálogo, solicitando o local do arquivo JSON. Clique em "Procurar arquivo..." (3), em seguida, use a caixa de diálogo "Escolher arquivo para carregar" para selecionar o arquivo JSON (4) e pressione "Abrir". Depois que a caixa de diálogo desaparece, selecione a marca de seleção de "Okey" (5) e seu manifesto será carregado.  

    ![Gerenciar o manifesto, carregue opção][MANAGE-MANIFEST-UPLOAD]

    ![Carregar o JSON manifesto][UPLOAD-MANIFEST]

    ![Carregar o manifesto JSON - confirmação][UPLOAD-MANIFEST-CONFIRM]

Agora que o manifesto é salva, você pode dar um registrados acesso de aplicativo do cliente para a nova permissão adicionamos acima. Desta vez, você pode usar o Azure clássico do portal Web UI em vez de edição manifesto do aplicativo cliente:  

1. Primeiro vá para a página de "Configurar" do aplicativo cliente para o qual você deseja adicionar o acesso para a nova API e clique no botão "Adicionar aplicativo".
2. Em seguida, você verá a lista de aplicativos (APIs) do recurso registrado no locatário. Clique no sinal de adição / + símbolo ao lado do nome do aplicativo recurso para selecioná-la.  
3. Clique na marca de seleção no canto inferior direito.
4. Quando você retornar para a seção "Adicionar aplicativo" da página de configuração do seu cliente, você verá o novo aplicativo de recurso na lista. Quando você focaliza a seção "Delegada permissões" à direita da linha, você verá uma lista suspensa aparecem. Clique na lista e selecione a nova permissão para adicioná-lo à lista solicitada do cliente de permissões. Observação: essa permissão nova será armazenado na configuração de identidade do aplicativo cliente, na propriedade de coleção de "requiredResourceAccess".

![Permissões para outros aplicativos][PERMS-TO-OTHER-APPS]

![Permissões para outros aplicativos][PERMS-SELECT-APP]

![Permissões para outros aplicativos][PERMS-SELECT-PERMS]

É isso. Agora seus aplicativos serão executado com sua nova configuração de identidade.

## <a name="next-steps"></a>Próximas etapas
- Para obter mais detalhes sobre a relação entre objetos de aplicativo e serviço Principal de um aplicativo, consulte [aplicativo e objetos de serviço no Azure AD][AAD-APP-OBJECTS].
- Consulte o [Glossário de desenvolvedor do Azure AD] [ AAD-DEVELOPER-GLOSSARY] para obter definições de alguns dos conceitos centrais Azure Active Directory (AD) desenvolvedor.

Use a seção de comentários DISQUS abaixo para fornecer comentários e ajudar a refinar e nosso conteúdo de forma.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

