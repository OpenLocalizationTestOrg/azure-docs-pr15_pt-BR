<properties
    pageTitle="Recursos de nuvem seguro com MFA do Azure e AD FS"
    description="Esta é a página de autenticação multifator do Azure que descreve como começar com o Azure MFA e AD FS na nuvem."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Protegendo recursos de nuvem com autenticação multifator do Azure e AD FS

Se sua organização for federada com Azure Active Directory, use a autenticação multifator Azure ou serviços de Federação do Active Directory para proteger os recursos que são acessados por Azure AD. Use os procedimentos a seguir para proteger os recursos do Active Directory do Azure com autenticação multifator do Azure ou serviços de Federação do Active Directory.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger os recursos do Azure AD usam AD FS

Para proteger seus recursos de nuvem, primeiro habilitar uma conta para usuários e configurar uma regra de declarações. Siga este procedimento para percorrer as etapas:

1. Use as etapas descritas em [autenticação multifator de Turn-on para os usuários](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) para habilitar uma conta.
2. Inicie o console de gerenciamento do AD FS.
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Navegue até **Confiar festa confia** e clique em confiar festa de confiança. Selecione **Editar regras de declaração...**
4. Clique em **Adicionar regra...**
5. Na lista suspensa, selecione **Enviar declarações usando uma regra personalizada** e clique em **Avançar**.
6. Insira um nome para a regra de declaração.
7. Em regra personalizada: Adicione o seguinte texto:

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Declaração correspondente:

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```

8. Clique em **Okey** e em seguida **Concluir**. Feche o console de gerenciamento do AD FS.

Os usuários podem conclua entrar usando o método de local (como cartão inteligente).

## <a name="trusted-ips-for-federated-users"></a>IPs confiáveis para usuários federados
IPs confiáveis permitem aos administradores verificação em duas etapas de desviar para endereços IP específicos, ou para os usuários federados que têm solicitações provenientes dentro de seu próprios intranet. As seções a seguir descrevem como configurar IPs confiáveis do Azure multifator autenticação com usuários federados e verificação em duas etapas de desviar quando uma solicitação origina dentro de uma intranet usuários federados. Isso é feito configurando o AD FS para usar uma passagem ou filtrar um modelo de declaração de entrada com o tipo de declaração de dentro da rede corporativa.

Este exemplo usa o Office 365 para nosso confiar festa confia.

### <a name="configure-the-ad-fs-claims-rules"></a>Configure as regras de declarações do AD FS

A primeira coisa que precisamos fazer é configurar as declarações do AD FS. Vamos criar regras de dois declarações, para o tipo de declaração de dentro da rede corporativa e um adicional para manter nossos usuários conectados.

1. Abra o gerenciamento do AD FS.
2. À esquerda, selecione **Contar festa confia**.
3. Clique com botão direito na **Plataforma de identidade do Microsoft Office 365** e selecione **Editar regras de declaração...** 
 ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. Em regras de transformação de emissão, clique em **Adicionar regra.** 
 ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Transformar declarar regra Assistente para adicionar, selecione **passagem ou filtrar uma declaração de entrada** da lista suspensa e clique em **Avançar**.
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Na caixa ao lado do nome da regra de declaração, Nomeie sua regra. Por exemplo: InsideCorpNet.
7. Na lista suspensa, ao lado de entrada, tipo de declaração, selecione **Dentro da rede corporativa**.
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Clique em **Concluir**.
9. Em regras de transformação de emissão, clique em **Adicionar regra**.
10. Em Adicionar transformar declarar regra assistente, selecione **Enviar declarações usando uma regra personalizada** na lista suspensa e clique em **Avançar**.
11. Na caixa em nome da regra de declaração: inserir *Manter usuários conectado em*.
12. Na caixa de regra personalizada, digite:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Clique em **Concluir**.
14. Clique em **Aplicar**.
15. Clique em **Okey**.
16. Feche o gerenciamento do AD FS.



### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurar o Azure multifator IPs com usuários federados de confiáveis de autenticação
Agora que as declarações estão no lugar, podemos configurar IPs confiáveis.

1. Entrar no [portal clássico Azure](https://manage.windowsazure.com).
2. À esquerda, clique em **Active Directory**.
3. Em diretório, selecione a pasta onde você deseja configurar IPs confiáveis.
4. No diretório que você selecionou, clique em **Configurar**.
5. Na seção autenticação multifator, clique em **Gerenciar configurações de serviço**.
6. Na página Configurações de serviço, em IPs confiáveis, selecione **Ignorar multi autenticação multifator para solicitações de usuários federados na minha intranet.** 
 ![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Clique em **Salvar**.
8. Depois que as atualizações foram aplicadas, clique em **Fechar**.


Pronto! Neste ponto, os usuários federados do Office 365 só devem ter que usar MFA quando uma declaração se originam de fora da intranet corporativa.
