<properties 
    pageTitle="Como proteger serviços de back-end usando o cliente de autenticação de certificado no gerenciamento de API do Azure" 
    description="Saiba como proteger serviços de back-end usando autenticação de certificado de cliente no gerenciamento de API do Azure." 
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

# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como proteger serviços de back-end usando o cliente de autenticação de certificado no gerenciamento de API do Azure

Gerenciamento de API fornece a capacidade para proteger o acesso ao serviço de back-end de uma API usando certificados de cliente. Este guia mostra como gerenciar certificados no portal do publisher API e como configurar uma API para usar um certificado para acessar seu serviço de back-end.

Para obter informações sobre o gerenciamento de certificados usando a API REST de gerenciamento, consulte [entidade de certificado de API REST do gerenciamento de API do Azure][].

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra como configurar sua instância de serviço de gerenciamento de API para usar autenticação de certificado do cliente para acessar o serviço de back-end para uma API. Antes de seguir as etapas neste tópico, você deve ter seu serviço de back-end configurado para autenticação de certificado de cliente ([para configurar a autenticação de certificado em sites do Azure consulte este artigo][]) e acessem o certificado e a senha para o certificado para carregar no portal de fornecedor de gerenciamento de API.

## <a name="step1"> </a>Carregar um certificado de cliente

Para começar, clique em **Gerenciar** no Portal de clássico do Azure para o serviço de gerenciamento de API. Isso o leva para o portal de fornecedor de gerenciamento de API.

![Portal de fornecedor de API][api-management-management-console]

>Se você ainda não tiver criado uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][] do tutorial de [Introdução ao gerenciamento de API do Azure][] .

Clique em **segurança** no menu de **Gerenciamento de API** à esquerda e clique em **certificados de cliente**.

![Certificados de cliente][api-management-security-client-certificates]

Para carregar um novo certificado, clique em **carregar certificado**.

![Carregar certificado][api-management-upload-certificate]

Navegue até seu certificado e, em seguida, digite a senha para o certificado.

>O certificado deve estar no formato **. pfx** . Certificados autoassinados são permitidos.

![Carregar certificado][api-management-upload-certificate-form]

Clique em **carregar** para carregar o certificado.

>A senha de certificado é validada neste momento. Se ela estiver incorreta, uma mensagem de erro é exibida.

![Certificado carregado][api-management-certificate-uploaded]

Depois que o certificado é carregado, ele aparece na guia **certificados de cliente** . Se você tiver vários certificados, fazer uma anotação do assunto ou os últimos quatro caracteres da impressão digital, que são usados para selecionar o certificado ao configurar uma API para usar certificados, conforme abordados na seção [Configurar uma API para usar um certificado de cliente para autenticação de gateway][] seguinte.

>Para desativar validação de cadeia de certificados ao usar, por exemplo, um certificado autoassinado, siga as etapas descritas neste FAQ [item](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Excluir um certificado de cliente

Para excluir um certificado, clique em **Excluir** ao lado do certificado desejado.

![Excluir certificado][api-management-certificate-delete]

Clique em **Sim, excluí-la** para confirmar.

![Confirmar a exclusão][api-management-confirm-delete]

Se o certificado está em uso por uma API, uma tela de aviso é exibida. Para excluir o certificado que você remova primeiro o certificado de qualquer APIs que são configurados para usá-lo.

![Confirmar a exclusão][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurar uma API para usar um certificado de cliente para autenticação de gateway

Clique em **APIs** no menu de **Gerenciamento de API** à esquerda, clique no nome da API desejado e clique na guia **segurança** .

![Segurança de API][api-management-api-security]

Selecione **certificados de cliente** na lista suspensa **com credenciais** .

![Certificados de cliente][api-management-mutual-certificates]

Selecione o certificado desejado na lista suspensa de **certificado de cliente** . Se houver vários certificados, que você pode examinar o assunto ou os últimos quatro caracteres da impressão digital conforme descrito na seção anterior para determinar o certificado correto.

![Selecionar certificado][api-management-select-certificate]

Clique em **Salvar** para salvar a alteração de configuração à API.

>Essa alteração tem efeita imediato e chamadas para operações dessa API usará o certificado para autenticar no servidor back-end.

![Salvar alterações de API][api-management-save-api]

>Quando um certificado é especificado para autenticação de gateway para o serviço de back-end de uma API, ele torna-se parte da política para essa API e pode ser visualizado no editor de política.

![Diretiva de certificado][api-management-certificate-policy]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre outras maneiras de proteger seu serviço de back-end, como HTTP autenticação básica ou compartilhada secreta, consulte o vídeo a seguir.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introdução ao gerenciamento de API do Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Criar uma instância de serviço de gerenciamento de API]: api-management-get-started.md#create-service-instance

[Azure entidade de certificado de API do resto de gerenciamento de API]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Para configurar a autenticação de certificado em sites do Azure consulte este artigo]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configurar uma API para usar um certificado de cliente para autenticação de gateway]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 
