<properties
    pageTitle="Active Directory do Azure B2C: Visão geral | Microsoft Azure"
    description="Desenvolver aplicativos voltados para o consumidor com o Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure B2C diretório ativo: Inscrever-se e entre os consumidores em seus aplicativos

Azure B2C diretório ativo é uma solução de gerenciamento de identidade de nuvem abrangente para seu web voltados para o consumidor e aplicativos móveis. É um serviço global altamente disponível que se expande para centenas de milhões de identidades. Criado em uma plataforma de seguro de nível empresarial, o Azure Active Directory B2C mantém seus aplicativos, sua empresa e seus consumidores protegidos.

No passado, os desenvolvedores de aplicativo que desejam inscrever-se e entre os consumidores em seus aplicativos teria gravado seu próprio código. E eles têm usados sistemas ou bancos de dados local para armazenar os nomes de usuário e senhas. Azure B2C diretório ativo oferece aos desenvolvedores uma melhor maneira de integrar o gerenciamento de identidades do consumidor em seus aplicativos com a Ajuda de uma plataforma segura e baseada em padrões e um conjunto sofisticado de políticas extensíveis. Quando você usa o Azure Active Directory B2C, seus consumidores Inscreva seus aplicativos utilizando suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de email e senha, ou nome de usuário e senha); Chamamos as último "contas locais".

## <a name="get-started"></a>Introdução

Para criar um aplicativo que aceita inscrição do consumidor e entrar, você deverá primeiro preciso registrar o aplicativo com um Azure Active Directory B2C de locatários. Obter seu próprio locatário usando as etapas descritas em [criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md).

Você pode escrever seu aplicativo contra o serviço do Azure Active Directory B2C escolhendo enviar mensagens de protocolo diretamente, usando [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) ou [Abrir ID conectar](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), ou usando nossas bibliotecas para fazer o trabalho para você. Escolha a sua plataforma favorita na tabela a seguir e começar.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Quais são as novidades

Verifique novamente aqui com frequência para saber mais sobre futuras alterações o Azure Active Directory B2C. Nós também será tweet sobre todas as atualizações usando @AzureAD.

- Saiba sobre nossa [estrutura de políticas extensível](active-directory-b2c-reference-policies.md) e sobre os tipos de políticas que você pode criar e usar em seus aplicativos.
- Inserir um indicador no nosso [blog do serviço](https://blogs.msdn.microsoft.com/azureadb2c/) de notificações sobre problemas de serviço secundárias, atualizações, status e reduções. Continue a monitorar o [Painel de status Azure](https://azure.microsoft.com/status/) também.
- Atual [restrições, restrições e limitações do serviço](active-directory-b2c-limitations.md).
- Finalmente, uma [amostra de código](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) usando Azure AD B2C & ASP.NET Core.

## <a name="how-to-articles"></a>Artigos de instruções

Saiba como usar os recursos específicos do Azure Active Directory B2C:

- Configure contas de [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [conta da Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md)e [LinkedIn](active-directory-b2c-setup-li-app.md) para uso em seus aplicativos voltados para o consumidor.
- [Atributos personalizados de uso para coletar informações sobre seus clientes](active-directory-b2c-reference-custom-attr.md).
- [Habilitar a autenticação multifator do Azure em seus aplicativos voltados para o consumidor](active-directory-b2c-reference-mfa.md).
- [Configurar a senha de autoatendimento Redefinir para seus clientes](active-directory-b2c-reference-sspr.md).
- [Personalizar a aparência de inscrição, entrar e outras páginas voltados para o consumidor](active-directory-b2c-reference-ui-customization.md) servidas por Azure Active Directory B2C.
- [Usar a API do gráfico Azure Active Directory para programaticamente criar, ler, atualizar e excluir os consumidores](active-directory-b2c-devquickstarts-graph-dotnet.md) em seu locatário do Azure Active Directory B2C.

## <a name="next-steps"></a>Próximas etapas

Esses links será útil para explorar o serviço em profundidade:

- Consulte as [informações sobre preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Obter ajuda sobre estouro de pilha usando o [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal) marcas.
- Envie-nos seus pensamentos usando [Voz do usuário](https://feedback.azure.com/forums/169401-azure-active-directory/)– queremos ouvi-los! Use a frase "AzureADB2C:" no título da sua postagem para que possamos encontrá-lo.
- Examine a [referência de protocolo do Azure AD B2C](active-directory-b2c-reference-protocols.md).
- Examine a [referência de Token do Azure AD B2C](active-directory-b2c-reference-tokens.md).
- Leia as [Perguntas frequentes do Active Directory do Azure B2C](active-directory-b2c-faqs.md).
- [Arquivo solicitações de Azure Active Directory B2C de suporte](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos

Recomendamos que você obtenha as notificações de quando ocorrências de segurança ocorrerem visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinar alertas de segurança comunicado.
