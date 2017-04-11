<properties
    pageTitle="Azure B2C diretório ativo: Atributos personalizados | Microsoft Azure"
    description="Como usar atributos personalizados no Azure Active Directory B2C para coletar informações sobre seus clientes"
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
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

#  <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure B2C diretório ativo: Usar atributos personalizados para coletar informações sobre seus clientes

Seu diretório B2C Azure Active Directory (AD Azure) vem com um conjunto interno de informações (atributos): nome, sobrenome, cidade, CEP e outros atributos. Entretanto, todos os aplicativos voltados para o consumidor tem requisitos exclusivos em quais atributos para reunir de consumidores. Com o Azure AD B2C, você pode estender o conjunto de atributos armazenados em cada conta de cliente. Você pode criar atributos personalizados no [portal do Azure](https://portal.azure.com/) e usá-lo em suas políticas de inscrição, conforme mostrado abaixo. Você também pode ler e gravar esses atributos usando a [API do Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]
Atributos personalizados usam [extensões de esquema do Azure AD Graph API Directory](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **atributos de usuário**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. Forneça um **nome** para o atributo personalizado (por exemplo, "ShoeSize") e, opcionalmente, uma **Descrição**. Clique em **criar**.

    > [AZURE.NOTE]
    Somente o "Cadeia" **Tipo de dados** está disponível atualmente.

O atributo personalizado agora está disponível na lista de **atributos**de usuário e para uso em suas políticas de inscrição.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Usar um atributo personalizado em sua política de inscrição

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de inscrição**.
3. Clique em sua política de inscrição (por exemplo, "B2C_1_SiUp") para abri-lo. Clique em **Editar** na parte superior da lâmina.
4. Clique em **atributos de inscrição** e selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **Okey**.
5. Clique em **declarações de aplicativo** e selecione o atributo personalizado. Clique em **Okey**.
6. Clique em **Salvar** na parte superior da lâmina.

Você pode usar o recurso de "Executar agora" na política para verificar a experiência do consumidor. Agora você deve ver "ShoeSize" na lista de atributos coletados durante a inscrição do consumidor e vê-la no token enviado de volta para seu aplicativo.

## <a name="notes"></a>Anotações

- Juntamente com as diretivas de inscrição, atributos personalizados também podem ser usados em políticas ou de inscrição entrar e políticas de edição de perfil.
- Não há uma limitação conhecida de atributos personalizados. Ele só é criado na primeira vez em que ele é usado em qualquer política, e não quando você adicioná-lo à lista de **atributos de usuário**.
