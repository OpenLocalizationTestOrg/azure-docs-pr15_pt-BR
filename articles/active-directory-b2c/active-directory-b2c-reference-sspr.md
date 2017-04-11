<properties
    pageTitle="Azure B2C diretório ativo: Redefinição de senha de autoatendimento | Microsoft Azure"
    description="Um tópico demonstram como configurar a senha de autoatendimento Redefinir para seus clientes no Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure B2C diretório ativo: Configurar o autoatendimento para redefinição para seus consumidores de senhas

Com o recurso de redefinição de senha de autoatendimento, seus consumidores (que se inscreveram para contas locais) podem redefinir suas senhas por conta própria. Isso reduz significativamente a carga em sua equipe de suporte, especialmente se seu aplicativo tem milhões de consumidores usá-lo regularmente. Atualmente, só há suporte para usar um endereço de email verificado como um método de recuperação. Adicionaremos métodos de recuperação adicionais (número de telefone verificado, perguntas de segurança, etc.) no futuro.

> [AZURE.NOTE]
Este artigo se aplica a senha de autoatendimento redefinir usado no contexto de uma política de entrada. Se você precisar totalmente personalizável de redefinição de senha políticas chamadas de seu aplicativo, consulte [Este artigo](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy).

Por padrão, o diretório não terão senha de autoatendimento redefinir ativado. Use as etapas a seguir para ativá-lo:

1. Entrar no [portal clássico Azure](https://manage.windowsazure.com/) como o administrador de assinatura. Este é o mesmo trabalho ou conta de escola ou a mesma conta da Microsoft que você usou para criar o diretório.
2. Navegue até a extensão do Active Directory na barra de navegação no lado esquerdo.
3. Encontre seu diretório na guia **diretório** e clique nele.
4. Clique na guia **Configurar** .
5. Role até a seção **política de redefinição de senha do usuário** e ative a opção **usuários habilitados para senha redefinir** para **Sim**. Observe que a opção de **Endereço de Email alternativo** está marcada; Deixe-a como está.

    ![Redefinição de senha de autoatendimento](./media/active-directory-b2c-reference-sspr/sspr.png)

6. Clique em **Salvar** na parte inferior da página. Pronto!

Para testar, use o recurso "Executar agora" em qualquer política entrar com contas locais como um provedor de identidade. Na conta local sinal do página (onde você inserir um endereço de email e senha, ou um nome de usuário e senha), clique em **não é possível acessar sua conta?** para verificar a experiência do consumidor.

> [AZURE.NOTE]
As páginas de redefinição de senha de autoatendimento podem ser personalizadas usando o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md).
