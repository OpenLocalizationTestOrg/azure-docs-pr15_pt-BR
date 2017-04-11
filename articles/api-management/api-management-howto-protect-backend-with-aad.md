<properties
    pageTitle="Como proteger um back-end API Web com o Active Directory do Azure e gerenciamento de API"
    description="Aprenda a proteger um back-end API Web com o Active Directory do Azure e gerenciamento de API." 
    services="api-management"
    documentationCenter=""
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

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Como proteger um back-end API Web com o Active Directory do Azure e gerenciamento de API

O vídeo a seguir mostra como criar um back-end Web API e protegê-la usando o protocolo OAuth 2.0 com o Active Directory do Azure e gerenciamento de API.  Este artigo fornece uma visão geral e informações adicionais para as etapas no vídeo. Este vídeo 24 minutos mostra como para:

-   Construir um back-end Web API e protegê-lo com AAD - começando em 1:30
-   Importar a API para gerenciamento de API - começando na 7:10
-   Configurar o portal do desenvolvedor para chamar a API - começando em 9:09
-   Configurar um aplicativo da área de trabalho para chamar a API - começando em 18:08
-   Configurar uma regra de validação JWT para autorizar previamente solicitações - começando em 20:47

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>Criar um diretório de Azure AD

Para proteger sua API Web feito usando o Active Directory do Azure primeiro você deve ter um um locatário AAD. Neste vídeo um locatário chamado **APIMDemo** é usado. Para criar um locatário AAD, entrar no [Portal de clássico do Azure](https://manage.windowsazure.com) e clique em **novo**->**Os serviços de aplicativo**->**Do Active Directory**->**diretório**->**Criar personalizada**. 

![Active Directory do Azure][api-management-create-aad-menu]

Neste exemplo, um diretório chamado **APIMDemo** é criado com um domínio padrão denominado **DemoAPIM.onmicrosoft.com**. Este diretório é usado em todo o vídeo.

![Active Directory do Azure][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Criar um serviço Web API protegido pelo Active Directory do Azure

Nesta etapa, um back-end API Web é criado usando o Visual Studio 2013. Esta etapa do vídeo começa em 1:30. Criar Web API projeto de back-end no Visual Studio clique em **arquivo**->**novo**->**projeto**e escolha o **Aplicativo Web ASP.NET** da lista de modelos da **Web** . Neste vídeo, o projeto é denominado **APIMAADDemo**. Clique em **Okey** para criar o projeto. 

![O Visual Studio][api-management-new-web-app]

Clique em **Web API** de **Selecionar uma lista de modelo** para criar um projeto de API da Web. Para configurar a autenticação de Directory do Azure clique **Autenticação de alteração**.

![Novo projeto][api-management-new-project]

Clique em **Contas organizacionais**e especifique o **domínio** do seu locatário AAD. Neste exemplo, o domínio é **DemoAPIM.onmicrosoft.com**. O domínio de seu diretório pode ser obtido na guia **domínios** do seu diretório.

![Domínios][api-management-aad-domains]

Configurar as configurações desejadas na caixa de diálogo **Alterar autenticação** e clique em **Okey**.

![Autenticação de alteração][api-management-change-authentication]

Quando você clica em **Okey** Visual Studio tentará registrar seu aplicativo com o diretório do Azure AD e você pode ser solicitado a entrar pelo Visual Studio. Entre usando uma conta administrativa para seu diretório.

![Entrar no Visual Studio][api-management-sign-in-vidual-studio]

Para configurar esse projeto como uma verificação de API do Azure Web a caixa do **Host na nuvem** e, em seguida, clique em **Okey**.

![Novo projeto][api-management-new-project-cloud]

Você pode ser solicitado a entrar no Azure e, em seguida, você pode configurar o aplicativo Web.

![Configurar][api-management-configure-web-app]

Neste exemplo, um novo **plano de serviço de aplicativo** chamado **APIMAADDemo** é especificado.

Clique em **Okey** para configurar o aplicativo da Web e crie o projeto.

## <a name="add-the-code-to-the-web-api-project"></a>Adicionar o código para o projeto de API da Web

A próxima etapa no vídeo adiciona o código para o projeto de API da Web. Esta etapa começa em 4:35.

A API Web neste exemplo implementa um serviço de calculadora básica usando um modelo e um controlador. Para adicionar o modelo do serviço, clique com botão direito **modelos** no **Solution Explorer** e escolha **Adicionar**, **classe**. Nomeie a classe `CalcInput` e clique em **Adicionar**.

Adicione o seguinte `using` instrução na parte superior da `CalcInput.cs` arquivo.

    using Newtonsoft.Json;

 Substitua a classe gerada pelo código a seguir.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Clique com botão direito **controladores** no **Solution Explorer** e escolha **Adicionar**->**controlador**. Escolha **Web API 2 controlador - vazio** e clique em **Adicionar**. Digite **CalcController** para o nome do controlador e clique em **Adicionar**.

![Adicionar controlador][api-management-add-controller]

Adicione o seguinte `using` instrução na parte superior da `CalcController.cs` arquivo.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Substitua a classe de controlador gerado com o seguinte código. Esse código implementa a `Add`, `Subtract`, `Multiply`, e `Divide` operações da API do Calculadora básica.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

Pressione **F6** para criar e verificar a solução.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Nesta etapa do Visual Studio projeto é publicado no Azure. Esta etapa do vídeo começa em 5:45.

Para publicar o projeto do Azure, clique com botão direito do projeto **APIMAADDemo** no Visual Studio e escolha **Publicar**. Manter as configurações padrão na caixa de diálogo **Publicar Web** e clique em **Publicar**.

![Publicação na Internet][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Conceder permissões para o aplicativo de serviço de back-end do Azure AD

Um novo aplicativo para o serviço de back-end é criado no diretório Azure AD como parte do processo de publicação e configuração do seu projeto de API da Web. Nesta etapa do vídeo, começando na 6:13, as permissões são concedidas a API Web back-end.

![Aplicativo][api-management-aad-backend-app]

Clique no nome do aplicativo para configurar as permissões necessárias. Navegue até a guia **Configurar** e role até a seção **permissões para outros aplicativos** . Clique nas **Permissões do aplicativo** suspensa ao lado do **Windows** **Azure Active Directory**, marque a caixa de **dados do diretório de leitura**e clique em **Salvar**.

![Adicionar permissões][api-management-aad-add-permissions]

>[AZURE.NOTE] Se o **Windows** **Azure Active Directory** não estiver listado em permissões para outros aplicativos, clique em **Adicionar aplicativo** e adicioná-lo na lista.

Anote o **URI de Id do aplicativo** para uso em uma etapa posterior quando um aplicativo do Azure AD está configurado para o portal de gerenciamento de API do desenvolvedor.

![Id de aplicativo URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importar Web API para gerenciamento de API

APIs são configurados no portal de fornecedor API, que é acessado por meio do Portal de clássico do Azure. Para acessar o portal do publisher, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Gerenciar sua primeira API][] .

![Portal do Publisher][api-management-management-console]

Operações podem ser [adicionadas manualmente para APIs](api-management-howto-add-operations.md)ou eles podem ser importados. Neste vídeo, operações serão importadas no formato de Swagger começando com 6:40.

Crie um arquivo denominado `calcapi.json` com seguinte conteúdo e salvá-lo com seu computador. Certifique-se de que o `host` atributo pontos à sua back-end API da Web. Neste exemplo `"host": "apimaaddemo.azurewebsites.net"` é usado.

{"swagger": "2.0", "informações": {"título": "Calculadora", "Descrição": "Aritmética sobre HTTP!", "versão": "1.0"}, "host": "apimaaddemo.azurewebsites.net", "basePath": "/ api", "esquemas": ["http"], "caminhos": {"/ adicionar? uma = {a} & b = {b}": {"obter": {"Descrição": "Responde com uma soma de dois números.", "operationId": "Adicionar dois inteiros", "parâmetros": [{"nome": "a", "em": "consulta", "Descrição": "primeiro operando. Valor padrão é <code>51</code>. ","obrigatório": verdadeiro,"padrão":"51","enumeração": ["51"]}, {"nome":"b","em":"consulta","Descrição":"segundo operando. Valor padrão é <code>49</code>. ","obrigatório": verdadeiro,"padrão":"49","enumeração": ["49"]}],"respostas": {}}}," / sub?a = {a} & b = {b} ": {"obter": {"Descrição":"Responde com uma diferença entre dois números.","operationId":"Subtrair dois inteiros","parâmetros": [{"nome":"a","em":"consulta","Descrição":"primeiro operando. Valor padrão é <code>100</code>. ","obrigatório": verdadeiro,"padrão":"100","enumeração": ["100"]}, {"nome":"b","em":"consulta","Descrição":"segundo operando. Valor padrão é <code>50</code>. ","obrigatório": verdadeiro,"padrão":"50","enumeração": ["50"]}],"respostas": {}}}," / div?a = {a} & b = {b} ": {"obter": {"Descrição":"Responde com um quociente de dois números.","operationId":"Divide dois inteiros","parâmetros": [{"nome":"a","em":"consulta","Descrição":"primeiro operando. Valor padrão é <code>100</code>. ","obrigatório": verdadeiro,"padrão":"100","enumeração": ["100"]}, {"nome":"b","em":"consulta","Descrição":"segundo operando. Valor padrão é <code>20</code>. ","obrigatório": verdadeiro,"padrão":"20","enumeração": ["20"]}],"respostas": {}}}," / mul?a = {a} & b = {b} ": {"obter": {"Descrição":"Responde com um produto de dois números.","operationId":"Multiplicar dois inteiros","parâmetros": [{"nome":"a","em":"consulta","Descrição":"primeiro operando. Valor padrão é <code>20</code>. ","obrigatório": verdadeiro,"padrão":"20","enumeração": ["20"]}, {"nome":"b","em":"consulta","Descrição":"segundo operando. Valor padrão é <code>5</code>. ","obrigatório": verdadeiro,"padrão":"5","enumeração": ["5"]}],"respostas": {}}}}}

Para importar a Calculadora API, clique **APIs** no menu **Gerenciamento de API** à esquerda e, em seguida, clique em **Importar API**.

![Botão Importar API][api-management-import-api]

Execute as seguintes etapas para configurar a API de calculadora.

1. Clique em **do arquivo**, navegue até o `calculator.json` arquivo foi salvo e clique no botão de rádio **Swagger** .
2. Digite **cálculo** na caixa de texto **sufixo de Web API URL** .
3. Clique na caixa de **produtos (opcionais)** e escolha **Starter**.
4. Clique em **Salvar** para importar a API.

![Adicionar nova API][api-management-import-new-api]

Depois que a API é importada, a página de resumo para a API é exibida no portal do publisher.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Chamar a API sem sucesso do portal do desenvolvedor

Neste ponto, a API foi importada para o gerenciamento de API, mas não pode ainda ser chamada com êxito a partir do portal do desenvolvedor porque o serviço de back-end está protegido com a autenticação do Azure AD. Isso é demonstrou no vídeo começando com 7:40 usando as seguintes etapas.

Clique em **portal do desenvolvedor** do lado superior direito do portal do publisher.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** e a **Calculadora** API.

![Portal do desenvolvedor][api-management-dev-portal-apis]

Clique em **experimentá-lo**.

![Experimente][api-management-dev-portal-try-it]

Clique em **Enviar** e observe o status de resposta de **401 não autorizado**.

![Enviar][api-management-dev-portal-send-401]

A solicitação é não autorizada porque o API de back-end está protegido pelo Active Directory do Azure. Antes de chamar com êxito a API do desenvolvedor portal deverá ser configurado para permitir que os desenvolvedores usando OAuth 2.0. Esse processo é descrito nas seções a seguir.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registrar o portal do desenvolvedor como um aplicativo de AAD

A primeira etapa na configuração o portal do desenvolvedor para autorizar desenvolvedores usando OAuth 2.0 é registrar o portal do desenvolvedor como um aplicativo de AAD. Isso é demonstrou começando em 8:27 no vídeo.

Navegue até o locatário do Azure AD da primeira etapa neste vídeo, neste exemplo **APIMDemo** e navegue até a guia de **aplicativos** .

![Novo aplicativo][api-management-aad-new-application-devportal]

Clique no botão **Adicionar** para criar um novo aplicativo do Active Directory do Azure e escolha **Adicionar um aplicativo minha organização está desenvolvendo**.

![Novo aplicativo][api-management-new-aad-application-menu]

Escolha o **aplicativo da Web e/ou API da Web**, insira um nome e clique na seta próxima. Neste exemplo, **APIMDeveloperPortal** é usado.

![Novo aplicativo][api-management-aad-new-application-devportal-1]

Para **logon URL** insira a URL do seu serviço de gerenciamento de API e acrescentar `/signin`. Neste exemplo, **https://contoso5.portal.azure-api.net/signin **é usado.

**URL de Id de aplicativo** do insira a URL do seu serviço de gerenciamento de API e acrescentar alguns caracteres exclusivos. Elas podem ser quaisquer caracteres desejados e neste exemplo **https://contoso5.portal.azure-api.net/dp** é usado. Quando as **Propriedades de aplicativo** de desejadas são configurados, clique na marca de seleção para criar o aplicativo.

![Novo aplicativo][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Configurar um servidor de autorização de API gerenciamento OAuth 2.0

A próxima etapa é configurar um servidor de autorização OAuth 2.0 no gerenciamento de API. Esta etapa é demonstrou no vídeo começando em 9:43.

Clique em **segurança** no menu de gerenciamento de API à esquerda, clique **OAuth 2.0**e, em seguida, clique em **Adicionar autorização** server.

![Adicionar servidor de autorização][api-management-add-authorization-server]

Insira um nome e uma descrição opcional nos campos **nome** e **Descrição** . Esses campos são usados para identificar o servidor de autorização OAuth 2.0 dentro da instância do serviço de gerenciamento de API. Neste exemplo **demonstração de servidor de autorização** é usado. Mais tarde quando você especificar um servidor de OAuth 2.0 para ser usado para autenticação para uma API, você selecionará esse nome.

Para obter a **URL de página de registro de cliente** , insira um valor de espaço reservado, como `http://localhost`.  A **URL de página de registro de cliente** aponta para a página que os usuários podem usar para criar e configurar suas próprias contas para provedores de OAuth 2.0 que oferecem suporte a gerenciamento de contas de usuário. Neste exemplo usuários não criar e configurar suas próprias contas para que um espaço reservado seja usado.

![Adicionar servidor de autorização][api-management-add-authorization-server-1]

Em seguida, especifique **URL de ponto de extremidade de autorização** e a **URL de ponto de extremidade de Token**.

![Servidor de autorização][api-management-add-authorization-server-1a]

Esses valores podem ser recuperados na página **Pontos de extremidade do aplicativo** do aplicativo AAD criado para o portal do desenvolvedor. Para acessar os pontos de extremidade navegue até a guia **Configurar** para o aplicativo AAD em clique em **pontos de extremidade do modo de exibição**.

![Aplicativo][api-management-aad-devportal-application]

![Pontos de extremidade do modo de exibição][api-management-aad-view-endpoints]

Copie o **ponto de extremidade de autorização OAuth 2.0** e colá-lo na caixa de texto **URL de ponto de extremidade de autorização** .

![Adicionar servidor de autorização][api-management-add-authorization-server-2]

Copie o **ponto de extremidade token OAuth 2.0** e colá-lo na caixa de texto **URL de ponto de extremidade de Token** .

![Adicionar servidor de autorização][api-management-add-authorization-server-2a]

Além de colando o ponto de extremidade de token, adicione um parâmetro de corpo adicionais chamado **recurso** e o uso de valor o **URI de Id de aplicativo** do aplicativo AAD para o serviço de back-end que foi criado quando o projeto do Visual Studio foi publicado.

![Id de aplicativo URI][api-management-aad-sso-uri]

Em seguida, especifique as credenciais de cliente. Estas são as credenciais para o recurso que você deseja acessar, nesse caso, o serviço de back-end.

![Credenciais do cliente][api-management-client-credentials]

Para obter a **Id do cliente**, navegue até a guia de **configuração** do aplicativo AAD para o serviço de back-end e copie a **Id do cliente**.

Para obter o clique **Segredo cliente** **Selecione duração** suspensa na seção de **chaves** e especificar um intervalo. Neste exemplo, 1 ano é usado.

![ID do cliente][api-management-aad-client-id]

Clique em **Salvar** para salvar a configuração e exibir a chave. 

>[AZURE.IMPORTANT] Anote esta tecla. Depois que você fecha a janela de configuração do Active Directory do Azure, a chave não pode ser exibida novamente.

Copiar a chave para a área de transferência, alterne para o portal do publisher, cole a chave para a caixa de texto de **Segredo cliente** e clique em **Salvar**.

![Adicionar servidor de autorização][api-management-add-authorization-server-3]

Imediatamente após as credenciais do cliente é uma concessão de autorização de código. Copie este código de autorização e retorne para o seu aplicativo de portal de desenvolvedor do Azure AD Configurar página e colar a autorização conceder no campo **URL de resposta** e clique em **Salvar** novamente.

![URL de resposta][api-management-aad-reply-url]

A próxima etapa é configurar as permissões para o portal do desenvolvedor do aplicativo AAD. Clique em **Permissões de aplicativo** e marque a caixa de **dados do diretório de leitura**. Clique em **Salvar** para salvar essa alteração e clique em **Adicionar aplicativo**.

![Adicionar permissões][api-management-add-devportal-permissions]

Clique no ícone de pesquisa, digite **APIM** em Iniciar com caixa, selecione **APIMAADDemo**e clique na marca de seleção para salvar.

![Adicionar permissões][api-management-aad-add-app-permissions]

Clique em **Permissões delegada** **APIMAADDemo** e marque a caixa de **APIMAADDemo de acesso**e clique em **Salvar**. Isso permite que o desenvolvedor do aplicativo de portal para acessar o serviço de back-end.

![Adicionar permissões][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Ativar a autorização de usuário OAuth 2.0 para a API de Calculadora

Agora que o servidor OAuth 2.0 estiver configurado, você pode especificá-lo nas configurações de segurança para sua API. Esta etapa é demonstrou no vídeo a partir de 14:30.

Clique em **APIs** no menu à esquerda e clique em **Calculadora** para exibir e configurar suas configurações.

![Calculadora de API][api-management-calc-api]

Navegue até a guia **segurança** , marque a caixa de seleção **OAuth 2.0** , selecione o servidor de autorização desejado na lista suspensa do **servidor de autorização** e clique em **Salvar**.

![Calculadora de API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Chamar a API de calculadora com êxito a partir do portal do desenvolvedor

Agora que a autorização OAuth 2.0 está configurada no API, suas operações podem ser chamadas com êxito do developer center. Esta etapa é demonstrou no vídeo começando em 15:00.

Navegue de volta para a operação de **Adicionar dois inteiros** do serviço Calculadora no portal do desenvolvedor e clique em **experimentá-lo**. Observe o novo item na seção **autorização** correspondente ao servidor de autorização que você acabou de adicionar.

![Calculadora de API][api-management-calc-authorization-server]

Selecione o **código de autorização** na lista suspensa de autorização e insira as credenciais de conta a ser usada. Se você já estiver conectado com a conta que não pode ser solicitado.

![Calculadora de API][api-management-devportal-authorization-code]

Clique em **Enviar** e observe o **status de resposta** de **200 Okey** e os resultados da operação no conteúdo resposta.

![Calculadora de API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Configurar um aplicativo da área de trabalho para chamar a API

O procedimento a seguir no vídeo começa em 16:30 e configura um aplicativo de área de trabalho simples para chamar a API. A primeira etapa é registrar o aplicativo da área de trabalho no Azure AD e dar acesso ao diretório e ao serviço de back-end. 18:25 haverá uma demonstração do aplicativo da área de trabalho chamar uma operação na API de calculadora.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurar uma regra de validação JWT para autorizar previamente solicitações

O procedimento final no vídeo começa em 20:48 e mostra como usar a política de [Validar JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) para autorizar previamente solicitações Validando os tokens de acesso de cada solicitação de entrada. Se a solicitação não for validada pela política JWT validar, a solicitação é bloqueada pelo gerenciamento de API e não é passada para o back-end.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Para outra demonstração de Configurando e usando essa política, consulte [nuvem cobrir episódio 177: mais recursos de gerenciamento de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e avanço para 13:50. Avançar rapidamente para 15:00 para ver as diretivas configuradas no editor de política e, em seguida, 18:50 para ver uma demonstração de chamar uma operação de portal do desenvolvedor com e sem o token de autorização necessária.

## <a name="next-steps"></a>Próximas etapas
-   Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre o gerenciamento de API.
-   Para outras maneiras de proteger seu serviço de back-end, consulte [autenticação de certificado comum](api-management-howto-mutual-certificates.md) e [Conectar via VPN ou rota expressa](api-management-howto-setup-vpn.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance
[Gerenciar sua primeira API]: api-management-get-started.md
