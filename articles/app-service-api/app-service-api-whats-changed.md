<properties
    pageTitle="Serviço de aplicativo API Apps - o que mudou | Microsoft Azure"
    description="Saiba o que há de novo para os aplicativos da API do serviço de aplicativo do Azure."
    services="app-service\api"
    documentationCenter=".net"
    authors="mohitsriv"
    manager="wpickett"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps---whats-changed"></a>Serviço de aplicativo API Apps - o que mudou

O evento Connect () em novembro de 2015, um número de aprimoramentos no serviço de aplicativo do Azure eram [anunciado](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/). Esses aprimoramentos incluem alterações subjacentes aos aplicativos de API para melhor alinhar com Mobile e Web Apps, reduzir a contagem de conceito e melhorar o desempenho de tempo de execução e implantação. Iniciando novos aplicativos de API 30 de novembro de 2015, você cria usando o portal de gerenciamento Azure ou as ferramentas mais recentes refletirão essas alterações. Este artigo descreve essas alterações, bem como reimplantar aplicativos existentes para tirar proveito dos recursos.

## <a name="feature-changes"></a>Alterações de recurso
Principais recursos do API aplicativos – autenticação, metadados CORS e API – foram movidos diretamente no aplicativo de serviço. Com essa alteração, os recursos estão disponíveis em Web, móveis e aplicativos de API. Na verdade, todos os três compartilham o mesmo tipo de recurso de **Microsoft.Web/sites** no Gerenciador de recursos. O gateway de aplicativos de API já não é necessária ou oferecido com aplicativos de API. Isso também torna mais fácil usar o gerenciamento de API do Azure como haverá apenas o gateway de gerenciamento de API único.

![Visão geral da API aplicativos](./media/app-service-api-whats-changed/api-apps-overview.png)

O princípio de design de chave com a atualização de aplicativos da API é permitem que você exiba sua API como está, no idioma de sua escolha.  Se sua API já está implantado como um aplicativo Web ou aplicativo móvel, você não tenha reimplantar seu aplicativo para aproveitar as vantagens dos novos recursos. Se você estiver atualmente na visualização de aplicativos de API, a orientação de migração é detalhada abaixo.

### <a name="authentication"></a>Autenticação
Os recursos de autenticação API aplicativos, serviços/aplicativos Mobile e aplicativos Web de completas existentes tiveram sido unificados e estão disponíveis em uma única lâmina de autenticação do serviço de aplicativo do Azure no portal de gerenciamento. Para obter uma introdução aos serviços de autenticação no serviço de aplicativo, consulte [autenticação do serviço de aplicativo de expansão / autorização](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Cenários de API, há um número de novos recursos relevantes:

- **Suporte ao uso do Active Directory do Azure diretamente**, sem ter de trocar o token AAD para um token de sessão de código do cliente: seu cliente pode incluir apenas os tokens AAD no cabeçalho de autorização, acordo com a especificação de token de portador. Isso também significa que nenhuma SDK específico do serviço App é necessário do lado do cliente ou do servidor. 
- **Acesso de "Internos" ou ao serviço**: se você tiver um processo de daemon ou outro cliente que precisam de acesso às APIs sem uma interface, você pode solicitar um token usando um capital de serviço AAD e passar ao aplicativo de serviço para autenticação com seu aplicativo.
- **Autorização adiada**: muitos aplicativos têm restrições de acesso variados para diferentes partes do aplicativo. Talvez você queira algumas APIs sejam publicamente disponível, enquanto outras exigem entrar. O recurso de autenticação/autorização original foi tudo ou nada, com a todo o site que exigem logon. Esta opção ainda existe, mas você também pode permitir que o código do seu aplicativo renderizar decisões de acesso depois que o aplicativo de serviço foi autenticada do usuário.
 
Para obter mais informações sobre os novos recursos de autenticação, consulte [autenticação e a autorização para os aplicativos da API do serviço de aplicativo do Azure](app-service-api-authentication.md). Para obter informações sobre como migrar aplicativos de API existentes do modelo de aplicativos de API anterior para a nova, consulte [Migrando existente API aplicativos](#migrating-existing-api-apps) posteriormente neste artigo.
 
### <a name="cors"></a>CORS
Em vez de uma configuração de aplicativo de **MS_CrossDomainOrigins** que delimitado por vírgulas, agora há um blade no portal de gerenciamento Azure para configurar CORS. Como alternativa, ele pode ser configurado usando o Gerenciador de recursos de ferramentas como Azure PowerShell, CLI ou [Resource Explorer](https://resources.azure.com/). Defina a propriedade **cors** no tipo de recurso **Microsoft.Web/sites/config** para sua ** &lt;nome do site&gt;/web** recurso. Por exemplo:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### <a name="api-metadata"></a>API de metadados
A lâmina de definição de API agora está disponível em Web, móveis e aplicativos de API. No portal de gerenciamento, você pode especificar uma url relativa ou uma url absoluta apontando para um ponto de extremidade que representação de hosts um 2.0 Swagger de sua API. Como alternativa, ele pode ser configurado usando ferramentas do Gerenciador de recursos. Defina a propriedade **apiDefinition** no tipo de recurso **Microsoft.Web/sites/config** para sua ** &lt;nome do site&gt;/web** recurso. Por exemplo:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

No momento, o ponto de extremidade de metadados precisa estar acessível publicamente sem autenticação para muitos clientes downstream (por exemplo, Visual Studio API REST geração de cliente e PowerApps "Adicionar API" fluxo) consumi-lo. Isso significa que se você estiver usando a autenticação do serviço de aplicativo e deseja expor a definição de API de dentro de seu próprio aplicativo, você precisará usar a opção de autenticação adiada descrita anteriormente para que a rota para metadados de sua Swagger é pública.

## <a name="management-portal"></a>Portal de gerenciamento
Selecionando **Novo > Web + Mobile > API App** no portal irá criar aplicativos de API que reflitam os novos recursos descritos no artigo. **Procurar > aplicativos da API** será apenas mostrará esses novos aplicativos de API. Depois que você navegue para um aplicativo de API, a lâmina compartilha o mesmo layout e recursos como aqueles da Web e aplicativos Mobile. As únicas diferenças são ordenação das configurações e conteúdo de início rápido.

Existente API aplicativos (ou Marketplace API criado a partir de lógica de aplicativos) com a visualização anterior recursos ainda estarão visíveis no designer de aplicativos de lógica e quando todos os recursos em um grupo de recursos de navegação.

## <a name="visual-studio"></a>O Visual Studio

A maioria dos aplicativos Web ferramentas funcionará com novos aplicativos de API pois compartilham o mesmo tipo de recurso **Microsoft.Web/sites** subjacente. O Visual Studio Azure ferramentas, no entanto, deve ser atualizado para a versão 2.8.1 ou posterior desde que ele expõe uma série de recursos específicos para APIs. Baixe o SDK na [página downloads Azure](https://azure.microsoft.com/downloads/).

Com a racionalização dos tipos de serviço de aplicativo, publicar também é unificada em **Publicar > serviço de aplicativo do Microsoft Azure**:

![Publicar aplicativos de API](./media/app-service-api-whats-changed/api-apps-publish.png)

Para saber mais sobre o SDK 2.8.1, leia o comunicado [postagem de blog](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Como alternativa, você pode importar manualmente o perfil de publicação a partir do portal de gerenciamento para habilitar a publicação. No entanto, seleção de aplicativo de API/criação, geração de código e nuvem Explorer exigirá SDK 2.8.1 ou superior.

## <a name="migrating-existing-api-apps"></a>Migrando aplicativos API existentes
Se sua API personalizado for implantado para a versão anterior de visualização de aplicativos de API, solicitamos que você migrar para o novo modelo para aplicativos de API até 31 de dezembro de 2015. Como tanto o modelo antigo e novo é baseado nas APIs da Web hospedado em um serviço de aplicativo, a maioria dos código existente pode ser reutilizada.

### <a name="hosting-and-redeployment"></a>Hospedagem e reimplantação
As etapas para reimplantar são a mesma que Implantando qualquer API Web existente para o serviço de aplicativo. Etapas:

1. Crie um aplicativo de API vazio. Isso pode ser feito no portal com New > App API, no Visual Studio de publicar ou de ferramentas do Gerenciador de recursos. Se usar ferramentas do Gerenciador de recursos ou modelos, defina o valor de **tipo** à **api** no tipo de recurso **Microsoft.Web/sites** ter as configurações e guias de início rápido no portal de gerenciamento a usá-lo em direção a cenários de API.
2. Conectar e implantar o seu projeto para o aplicativo de API vazio usando qualquer um dos mecanismos de implantação suportados pelo serviço de aplicativo. Leia a [documentação de implantação do serviço de aplicativo do Azure](../app-service-web/web-sites-deploy.md) para saber mais. 
  
### <a name="authentication"></a>Autenticação
Os serviços de autenticação do serviço de aplicativo suportam os mesmos recursos que estavam disponíveis com o modelo de aplicativos de API anterior. Se você estiver usando tokens de sessão e exige SDKs, use o SDK do cliente e servidor seguinte:

- Cliente: [Azure SDK de cliente móvel](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Extensão do Microsoft Azure aplicativo móvel .NET autenticação](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) do servidor: 

Se estivesse usando a alfa de serviço de aplicativo SDKs em vez disso, elas estão agora obsoleta:

- [Microsoft Azure AppService SDK](http://www.nuget.org/packages/Microsoft.Azure.AppService) do cliente:
- Servidor: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

Em particular com o Azure Active Directory, no entanto, nenhum específico do serviço App é necessário se você estiver usando o token AAD diretamente.

### <a name="internal-access"></a>Acesso interno
O modelo de aplicativos de API anterior incluído um nível de acesso interno interno. Isso necessário usar do SDK para solicitações de assinatura. Conforme descrito anteriormente, com o novo modelo de aplicativos de API, objetos de serviço AAD podem ser usados como uma alternativa para autenticação de serviço para sem a necessidade de um específico do serviço App SDK. Saiba mais do [serviço de autenticação principal para aplicativos de API do serviço de aplicativo do Azure](app-service-api-dotnet-service-principal-auth.md).

### <a name="discovery"></a>Descoberta
O modelo de aplicativos de API anterior tinha APIs para descobrir outros aplicativos de API em tempo de execução no mesmo grupo de recursos atrás do mesmo gateway. Isto é especialmente útil em arquiteturas que implementam microservice padrões. Enquanto isso não é suportado diretamente, várias opções estão disponíveis:

1. Use a API do Azure Gerenciador de recursos para descoberta.
2. Coloca o gerenciamento de API do Azure na frente de sua APIs hospedado no aplicativo de serviço. Gerenciamento de API Azure serve como uma fachada e pode fornecer uma url de opostas externos estável mesmo se altera de topologia interno.
3. Construir seu próprio aplicativo de API de descoberta e tiver outros aplicativos de API registrar com o aplicativo de descoberta na inicialização.
4. No momento da implantação preencha as configurações de aplicativo de todos os aplicativos de API (e clientes) com os pontos de extremidade de outros aplicativos do API. Isso é viável em implantações de modelo e como aplicativos de API agora oferecem controle da url.

## <a name="using-api-apps-with-logic-apps"></a>Usando aplicativos de API com aplicativos de lógica

O novo modelo de aplicativos de API funciona bem com [Aplicativos de lógica de versão do esquema de 2015-08-01](../app-service-logic/app-service-logic-schema-2015-08-01.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, leia os artigos na [seção documentação de aplicativos de API](https://azure.microsoft.com/documentation/services/app-service/api/). Eles foram atualizados para refletir o novo modelo para aplicativos de API. Além disso, comunique-se nos fóruns para obter detalhes adicionais ou orientação sobre migração:

- [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
- [Estouro de pilha](http://stackoverflow.com/questions/tagged/azure-api-apps)
