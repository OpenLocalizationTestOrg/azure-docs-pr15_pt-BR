<properties 
    pageTitle="Como autorizar contas de desenvolvedor usando o Active Directory do Azure no gerenciamento de API do Azure" 
    description="Saiba como autorizar usuários usando o Active Directory do Azure no gerenciamento de API." 
    services="api-management" 
    documentationCenter="API Management" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando o Active Directory do Azure no gerenciamento de API do Azure


## <a name="overview"></a>Visão geral
Este guia mostra como habilitar o acesso ao portal do desenvolvedor para todos os usuários em um ou mais diretórios Active Azure. Este guia também mostra como gerenciar grupos de usuários do Active Directory do Azure, adicionando grupos externos que contêm os usuários do Azure Active Directory.

>Para concluir as etapas neste guia primeiro você deve ter um Active Directory do Azure para criar um aplicativo.

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Como autorizar contas de desenvolvedor usando o Active Directory do Azure

Para começar, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Isso o leva para o portal de gerenciamento de API do publisher.

![Portal do Publisher][api-management-management-console]

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

Clique em **segurança** no menu de **Gerenciamento de API** à esquerda e clique em **Identidades externas**.

![Identidades externas][api-management-security-external-identities]

Clique em **Active Directory do Azure**. Anote a **URL de redirecionamento** e alternar ao Azure Active Directory no Portal de clássico do Azure.

![Identidades externas][api-management-security-aad-new]

Clique no botão **Adicionar** para criar um novo aplicativo do Active Directory do Azure e escolha **Adicionar um aplicativo minha organização está desenvolvendo**.

![Adicionar novo aplicativo do Active Directory do Azure][api-management-new-aad-application-menu]

Insira um nome para o aplicativo, selecione o **aplicativo da Web e/ou Web API**e clique no botão Avançar.

![Novo aplicativo do Active Directory do Azure][api-management-new-aad-application-1]

Para **logon URL**, digite a URL de logon de seu portal do desenvolvedor. Neste exemplo, a **URL de logon** é `https://aad03.portal.current.int-azure-api.net/signin`. 

Para a **URL de ID do aplicativo**, digite o domínio padrão ou um domínio personalizado para o Active Directory do Azure e acrescentar uma cadeia de caracteres exclusiva a ele. Neste exemplo, o domínio padrão do **https://contoso5api.onmicrosoft.com** é usado com o sufixo do **/api** especificado.

![Novas propriedades de aplicativo do Active Directory do Azure][api-management-new-aad-application-2]

Clique no botão de seleção para salvar e criar o novo aplicativo e alternar para a guia de **Configurar** para configurar o novo aplicativo.

![Criada de novo aplicativo de Active Directory do Azure][api-management-new-aad-app-created]

Se vários diretórios ativa do Azure estiverem indo para ser usado para este aplicativo, clique em **Sim** para **aplicativo está vários locatário**. O padrão é **Nenhum**.

![Aplicativo é vários locatário][api-management-aad-app-multi-tenant]

Copie a **URL de redirecionamento** da seção **Azure Active Directory** da guia **Identidades externo** no portal do publisher e colá-lo na caixa de texto **URL de resposta** . 

![URL de resposta][api-management-aad-reply-url]

Role até o final da guia Configurar, selecione na lista suspensa **Permissões de aplicativo** e verificar os **dados de diretório de leitura**.

![Permissões de aplicativo][api-management-aad-app-permissions]

Selecione na lista suspensa **Permissões de representante** e marque **Habilitar logon e ler os perfis dos usuários**.

![Permissões de delegado][api-management-aad-delegated-permissions]

>Para obter mais informações sobre o aplicativo e permissões delegadas, consulte [acessando a API do gráfico][].

Copie a **Id do cliente** para a área de transferência.

![Id do cliente][api-management-aad-app-client-id]

Alterne para o portal do publisher e cole o **Código do cliente** copiado da configuração de aplicativo do Active Directory do Azure.

![Id do cliente][api-management-client-id]

Alterne para a configuração do Active Directory do Azure e clique em **Selecionar duração** suspensa na seção de **chaves** e especificar um intervalo. Neste exemplo, **1 ano** é usado.

![Chave][api-management-aad-key-before-save]

Clique em **Salvar** para salvar a configuração e exibir a chave. Copie a chave para a área de transferência.

>Anote esta tecla. Depois que você fecha a janela de configuração do Active Directory do Azure, a chave não pode ser exibida novamente.

![Chave][api-management-aad-key-after-save]

Alterne para o portal do publisher e colar a chave na caixa de texto **Segredo cliente** .

![Secreta do cliente][api-management-client-secret]

**Permitido locatários** Especifica quais diretórios têm acesso às APIs da instância do serviço de gerenciamento de API. Especifique os domínios das instâncias do Azure Active Directory ao qual você deseja conceder acesso. Você pode separar vários domínios com novas linhas, espaços ou vírgulas.

![Locatários permitidos][api-management-client-allowed-tenants]

Vários domínios podem ser especificados na seção **Locatários permitidos** . Antes de qualquer usuário pode efetuar login de um domínio diferente do domínio original onde o aplicativo foi registrado, um administrador global do domínio diferente deve conceder permissão para o aplicativo para acessar os dados de diretório. Para conceder permissão, um administrador global deve efetuar login no aplicativo e clique em **Aceitar**. No exemplo a seguir `miaoaad.onmicrosoft.com` foi adicionada ao **Locatários permitido** e global administrador desse domínio estiver fazendo logon pela primeira vez.

![Permissões][api-management-permissions-form]

>Se um administrador não global tenta efetuar login antes de permissões são concedidas por um administrador global, a tentativa de login falhar e uma tela de erro é exibida.

Após a configuração desejada for especificada, clique em **Salvar**.

![Salvar][api-management-client-allowed-tenants-save]

Depois que as alterações são salvas, os usuários no Azure Active Directory especificado podem fazer logon no portal do desenvolvedor seguindo as etapas no [logon no portal do desenvolvedor usando uma conta do Active Directory do Azure][].

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Como adicionar um externos Azure grupo do Active Directory

Depois de habilitar o acesso de usuários de um Active Directory do Azure, você pode adicionar grupos do Active Directory do Azure em gerenciamento de API para gerenciar com mais facilidade a associação dos desenvolvedores no grupo com os produtos desejados.

> Para configurar um grupo do Active Directory do Azure externo, o Active Directory do Azure primeiro deve ser configurado na guia identidades seguindo o procedimento na seção anterior. 

Grupos do Active Directory do Azure externos são adicionados a partir da guia de **visibilidade** do produto para o qual você deseja conceder acesso ao grupo. Clique em **produtos**e clique no nome do produto desejado.

![Configurar o produto][api-management-configure-product]

Alternar para a guia de **visibilidade** e clique em **Adicionar grupos do Active Directory do Azure**.

![Adicionar grupos][api-management-add-groups]

Selecione o **Azure Active Directory locatário** na lista suspensa e digite o nome do grupo desejado nos **grupos** a ser adicionado a caixa de texto.

![Selecione o grupo][api-management-select-group]

Esse nome de grupo pode ser encontrada na lista de **grupos** para o Active Directory do Azure, conforme mostrado no exemplo a seguir.

![Lista de grupos do Active Directory do Azure][api-management-aad-groups-list]

Clique em **Adicionar** para validar o nome do grupo e adicione o grupo. Neste exemplo, os **Desenvolvedores de 5 Contoso** grupo externo é adicionado. 

![Grupo adicionado][api-management-aad-group-added]

Clique em **Salvar** para salvar a nova seleção de grupo.

Quando um grupo do Active Directory do Azure do Azure tiver sido configurado em um produto, ele ficará disponível para ser verificado na guia **visibilidade** para os outros produtos da instância do serviço de gerenciamento de API.

Para revisar e configurar as propriedades para grupos externos depois que eles foram adicionados, clique no nome do grupo na guia **grupos** .

![Gerenciar grupos][api-management-groups]

A partir daqui, você pode editar o **nome** e a **Descrição** do grupo.

![Editar grupo][api-management-edit-group]

Usuários do Azure Active Directory configurado podem fazer logon no portal do desenvolvedor e exibir e inscrever-se em todos os grupos aos quais tenham visibilidade seguindo as instruções na seção a seguir.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Como fazer logon portal do desenvolvedor do usando uma conta do Active Directory do Azure

Para fazer logon no portal do desenvolvedor usando uma conta do Active Directory do Azure configurada nas seções anteriores, abra uma nova janela de navegador usando a **URL de logon** de configuração de aplicativo do Active Directory e clique em **Active Directory do Azure**.

![Portal do desenvolvedor][api-management-dev-portal-signin]

Insira as credenciais de um dos usuários no Active Directory do Azure e clique em **entrar**.

![Entrar][api-management-aad-signin]

Você pode ser solicitado com um formulário de registro se qualquer informação adicional é necessária. Preencha o formulário de registro e clique em **inscrever-se**.

![Registro][api-management-complete-registration]

O usuário agora é registrado no portal do desenvolvedor para a sua instância de serviço de gerenciamento de API.

![Registro concluído][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Acessando o API do gráfico]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Faça logon no portal do desenvolvedor do usando uma conta do Active Directory do Azure]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

