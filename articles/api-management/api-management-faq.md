<properties
    pageTitle="Perguntas Frequentes de gerenciamento de API Azure | Microsoft Azure"
    description="Aprenda as respostas para perguntas comuns, padrões e práticas recomendadas de gerenciamento de API do Azure."
    services="api-management"
    documentationCenter=""
    authors="miaojiang"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="mijiang"/>

# <a name="azure-api-management-faqs"></a>Perguntas frequentes de gerenciamento de API do Azure

Obtenha respostas para perguntas comuns, padrões e práticas recomendadas para gerenciamento de API do Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

-   [Como fazer uma pergunta da equipe de gerenciamento de API do Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)
-   [O que significa quando um recurso está na visualização?](#what-does-it-mean-when-a-feature-is-in-preview)
-   [Como posso proteger a conexão entre o gateway de gerenciamento de API e meus serviços de back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
-   [Como copiar minha instância de serviço de gerenciamento de API para uma nova instância?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
-   [Para gerenciar minha instância de gerenciamento de API programaticamente?](#can-i-manage-my-api-management-instance-programmatically)
-   [Como posso adicionar um usuário ao grupo Administradores?](#how-do-i-add-a-user-to-the-administrators-group)
-   [Por que a política que eu quero adicionar disponível no editor de política?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
-   [Como usar o controle de versão da API no gerenciamento de API?](#how-do-i-use-api-versioning-in-api-management)
-   [Como configurar vários ambientes em uma única API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
-   [Pode usar SOAP com gerenciamento de API?](#can-i-use-soap-with-api-management)
-   [É a constante de endereço IP de gateway de gerenciamento de API? Pode usá-lo nas regras de firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-   [Pode configurar um servidor de autorização OAuth 2.0 com segurança do AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-   [Qual método de roteamento de gerenciamento de API usar nas implantações em vários locais geográficos?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
-   [Pode usar um modelo do Gerenciador de recursos do Azure para criar uma instância de serviço de gerenciamento de API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
-   [Pode usar um certificado SSL autoassinado para um back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
-   [Por que recebo uma falha de autenticação ao tentar clonar um repositório GITO?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
-   [Gerenciamento de API funciona com rota expressa do Azure?](#does-api-management-work-with-azure-expressroute)
-   [Posso mudar um serviço de gerenciamento de API de uma assinatura para outro?](#can-i-move-an-api-management-service-from-one-subscription-to-another)


### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Como fazer uma pergunta da equipe de gerenciamento de API do Microsoft Azure?

Você pode entrar em contato conosco usando uma destas opções:

-   Poste suas perguntas em nosso [Fórum do MSDN de gerenciamento de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-   Enviar um email para <apimgmt@microsoft.com>.
-   Envie-em uma solicitação de recurso no [Fórum de comentários Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>O que significa quando um recurso está na visualização?

Quando um recurso está no modo de visualização, significa que estamos ativamente estiver buscando comentários sobre como o recurso está trabalhando para você. Um recurso no modo de visualização é funcionalidade completo, mas é possível que que vamos fazer uma quebra alterado em resposta a comentários do cliente. Recomendamos que você não depende de um recurso que está no modo de visualização em seu ambiente de produção. Se você tiver quaisquer comentários sobre os recursos de visualização, informe-por meio de uma das opções de contato em [como eu faça a equipe de gerenciamento de API do Microsoft Azure uma pergunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Como posso proteger a conexão entre o gateway de gerenciamento de API e meus serviços de back-end?

Você tem várias opções para proteger a conexão entre o gateway de gerenciamento de API e seus serviços de back-end. É possível:

-   Use autenticação básica HTTP. Para obter mais informações, consulte [configurações de API de configurar](api-management-howto-create-apis.md#configure-api-settings).
- Use autenticação comum SSL conforme descrito em [como proteger serviços de back-end usando autenticação de certificado de cliente no gerenciamento de API do Azure](api-management-howto-mutual-certificates.md).
- Use a lista de exceções IP no seu serviço de back-end. Se você tiver uma instância de gerenciamento de API de nível Standard ou Premium, o endereço IP do gateway permanece constante. Você pode definir sua lista branca para permitir que este endereço IP. Você pode obter o endereço IP da sua instância de gerenciamento de API no painel no portal do Azure.
- Conecte sua instância de gerenciamento de API a uma rede Virtual do Azure. Para obter mais informações, consulte [como configurar conexões VPN no gerenciamento de API do Azure](api-management-howto-setup-vpn.md).

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Como copiar minha instância de serviço de gerenciamento de API para uma nova instância?

Você tem várias opções se desejar copiar uma instância de gerenciamento de API para uma nova instância. É possível:

-   Use o backup e restauração função no gerenciamento de API. Para obter mais informações, consulte [como implementar a recuperação de dados usando o serviço backup e restauração no gerenciamento de API do Azure](api-management-howto-disaster-recovery-backup-restore.md).
-   Criar seu próprio backup e restaurar o recurso usando a [API do resto de gerenciamento](https://msdn.microsoft.com/library/azure/dn776326.aspx). Use a API REST para salvar e restaurar as entidades da instância do serviço desejado.
-   Baixe a configuração do serviço usando gito e carregue-o em uma nova instância. Para obter mais informações, consulte [como salvar e configurar a configuração do serviço de gerenciamento de API usando gito](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Para gerenciar minha instância de gerenciamento de API programaticamente?

Sim, você pode gerenciar o gerenciamento de API programaticamente usando:

-   O [gerenciamento de API API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx).
-   A [biblioteca de gerenciamento de serviço de ApiManagement do Microsoft Azure SDK](http://aka.ms/apimsdk).
-   Os cmdlets do PowerShell do [gerenciamento de serviços](https://msdn.microsoft.com/library/mt613507.aspx) e [implantação de serviço](https://msdn.microsoft.com/library/mt619282.aspx) .

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Como posso adicionar um usuário ao grupo Administradores?

Aqui está como você pode adicionar um usuário ao grupo Administradores:

1. Entrar no [portal do Azure](https://portal.azure.com).
2. Vá para o grupo de recursos com a instância de gerenciamento de API que você deseja atualizar.
3. Em gerenciamento de API, atribua a função de **Colaborador de gerenciamento de Api** para o usuário.

Agora o recém-adicionado colaborador pode usar [cmdlets](https://msdn.microsoft.com/library/mt613507.aspx)do PowerShell do Azure. Veja aqui como entrar como um administrador:

1. Use o `Login-AzureRmAccount` cmdlet para entrar.
2. Definir o contexto para a assinatura com o serviço usando `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Obtenha uma URL de logon única usando `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Use a URL para acessar o portal de administração.


### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Por que a política que eu quero adicionar disponível no editor de política?

Se a política que você deseja adicionar aparece esmaecido ou sombreado no editor de política, certifique-se de que você está no escopo correto para a política. Cada instrução de política foi projetada para ser usado nas seções de política e escopos específicos. Para revisar as seções de política e escopos para uma política, consulte a seção de uso da política em [políticas de gerenciamento de API](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### <a name="how-do-i-use-api-versioning-in-api-management"></a>Como usar o controle de versão da API no gerenciamento de API?

Você tem algumas opções para usar o controle de versão da API no gerenciamento de API:

-   Em gerenciamento de API, você pode configurar APIs para representar diferentes versões. Por exemplo, você pode ter duas APIs diferentes, MyAPIv1 e MyAPIv2. Um desenvolvedor pode escolher a versão que o desenvolvedor que deseja usar.
-   Você também pode configurar sua API com uma URL de serviço que não inclui um segmento de versão, por exemplo, https://my.api. Em seguida, configure um segmento de versão no modelo de [Reconfiguração de URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) da cada operação. Por exemplo, você pode ter uma operação com um [modelo de URL](api-management-howto-add-operations.md#url-template) chamado /resource e um modelo de [Reconfiguração de URL](api-management-howto-add-operations.md#rewrite-url-template) chamado /v1/Resource. Você pode alterar o valor de segmento versão separadamente para cada operação.
-   Se você quiser manter um segmento de versão "padrão" na URL de serviço da API em operações de selecionada, defina uma política que usa a política de [Configurar o serviço de back-end](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) para alterar o caminho da solicitação de back-end.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Como configurar vários ambientes em uma única API?

Para configurar vários ambientes, por exemplo, um ambiente de teste e um ambiente de produção, em uma única API, você tem duas opções. É possível:

-   Host APIs diferentes no mesmo locatário.
-   Hospede APIs da mesmas locatários diferentes.

### <a name="can-i-use-soap-with-api-management"></a>Pode usar SOAP com gerenciamento de API?

Suporte de [passagem SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) agora está disponível. Os administradores podem importar WSDL do seu serviço de SOAP e gerenciamento de API do Azure criará um front-end SOAP. Documentação de portal do desenvolvedor, console de teste, políticas e análise está todos disponível para serviços SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>É a constante de endereço IP de gateway de gerenciamento de API? Pode usá-lo nas regras de firewall?

Nas camadas Standard e Premium, o endereço IP público (VIP) do locatário gerenciamento de API é estático para o tempo de vida do locatário, com algumas exceções. Alterações no endereço IP nessas circunstâncias:

-   O serviço é excluído e depois recriado.
-   A assinatura do serviço está suspenso (por exemplo, para nonpayment) e, em seguida, restaurada.
-   Adicionar ou remover uma rede Virtual Azure (você pode usar uma rede Virtual apenas na camada Premium).

Para implantações de várias regiões, o endereço regional mudar se a região for vagas e, em seguida, restaurada (você pode usar várias regiões implantação apenas na camada Premium).

Locatários de nível Premium que estão configurados para implantação de várias regiões são atribuídos a um endereço IP público por região.

Você pode obter o endereço IP (ou endereços, em uma implantação de várias regiões) na página locatários no portal do Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Pode configurar um servidor de autorização OAuth 2.0 com segurança do AD FS?

Para saber como configurar um servidor de autorização de OAuth 2.0 com segurança de serviços de Federação do Active Directory (AD FS), consulte [Usando ADFS no gerenciamento de API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Qual método de roteamento de gerenciamento de API usar nas implantações em vários locais geográficos?

Gerenciamento de API usa o [método de roteamento de tráfego de desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method) em implantações em vários locais geográficos. Tráfego de entrada é roteado para o gateway de API mais próximo. Se uma região ficar offline, o tráfego de entrada é roteado automaticamente para o próximo gateway mais próximo. Saiba mais sobre os métodos de roteamento no [Gerenciador de tráfego roteamento métodos](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Pode usar um modelo do Gerenciador de recursos do Azure para criar uma instância de serviço de gerenciamento de API?

Sim. Consulte os modelos de início rápido do [Serviço de gerenciamento de API do Azure](http://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Pode usar um certificado SSL autoassinado para um back-end?

Sim. Veja aqui como usar um certificado auto-assinado do Secure Sockets Layer (SSL) para um back-end:

1. Crie uma entidade de [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) usando o gerenciamento de API.
2. Defina a propriedade **skipCertificateChainValidation** como **true**.
3. Se você não quiser mais permitir que os certificados autoassinados, exclua a entidade de back-end ou defina a propriedade **skipCertificateChainValidation** como **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Por que recebo uma falha de autenticação ao tentar clonar um repositório gito?

Se você usar o Gerenciador de credenciais gito, ou se você estiver tentando clonar um repositório gito usando Visual Studio, você poderá se deparar com um problema conhecido com a caixa de diálogo de credenciais do Windows. A caixa de diálogo limita o comprimento da senha para 127 caracteres e ele trunca a senha gerados pelo Microsoft. Estamos trabalhando em reduzindo a senha. Por agora, use gito Bash clonar seu repositório gito.

### <a name="does-api-management-work-with-azure-expressroute"></a>Gerenciamento de API funciona com rota expressa do Azure?

Sim. Gerenciamento de API funciona com rota expressa do Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Posso mudar um serviço de gerenciamento de API de uma assinatura para outro?

Sim. Para saber como, consulte [Mover recursos para um novo grupo de recursos ou assinatura](../resource-group-move-resources.md).
