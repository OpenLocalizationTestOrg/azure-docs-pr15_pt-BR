<properties
    pageTitle="Azure B2C diretório ativo: Ferramenta de auxiliar de personalização Page UI | Microsoft Azure"
    description="Uma ferramenta de auxiliar usada para demonstrar o recurso de personalização de interface do usuário de página do Azure Active Directory B2C"
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
    ms.date="07/22/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure B2C diretório ativo: Uma ferramenta de auxiliar usada para demonstrar o recurso de personalização de interface de usuário de página

Este artigo é um complemento para o [artigo de personalização de interface do usuário principal](active-directory-b2c-reference-ui-customization.md) no B2C Azure Active Directory (AD Azure). As etapas a seguir descrevem como exercício o recurso de personalização de interface do usuário de página usando o conteúdo HTML e CSS de amostra que fornecemos.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um locatário do Azure AD B2C

Antes de poder personalizar nada, você precisará [obter um locatário do Azure AD B2C](active-directory-b2c-get-started.md) se ainda não tiver uma.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou entrar

O conteúdo de amostra fornecemos pode ser usados para imagem duas páginas em uma [política de inscrição ou entrar](active-directory-b2c-reference-policies.md): a [página de entrada unificada](active-directory-b2c-reference-ui-customization.md) e o [certificado confirmada página atributos](active-directory-b2c-reference-ui-customization.md). Ao [criar sua política de inscrição ou entrar](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), adicione a conta Local (endereço de email), Facebook, Google e Microsoft como **provedores de identidade**. Estas são as IDPs apenas que nosso conteúdo HTML de amostra aceitará.  Você também pode adicionar um subconjunto desses IDPs se desejar.

## <a name="register-an-application"></a>Registrar um aplicativo

Você precisará [registrar um aplicativo](active-directory-b2c-app-registration.md) em seu locatário B2C que pode ser usado para executar sua política. Depois de registrar seu aplicativo, você tem algumas opções que você pode usar para realmente executar sua política de inscrição:

- Criar um dos B2C de AD Azure aplicativos de início rápido listados na seção "Introdução" da [entrar para cima e entre os consumidores em seus aplicativos](active-directory-b2c-overview.md#getting-started).
- Use o aplicativo do [Azure AD B2C espaço](https://aadb2cplayground.azurewebsites.net) previamente criado. Se você optar por usar o espaço, você deve registrar um aplicativo em seu locatário B2C usando a **Redirecionar URI** `https://aadb2cplayground.azurewebsites.net/`.
- Use o botão **Executar agora** na sua política no [portal do Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personalizar sua política

Para personalizar a aparência da sua política, é necessário primeiro criar arquivos HTML e CSS usando as convenções específicas do Azure AD B2C. Em seguida, você pode carregar seu conteúdo estático para um local publicamente disponível para que o Azure AD B2C possam acessá-lo. Isso pode ser seu próprio servidor web dedicado, armazenamento de Blob do Azure, rede de distribuição de conteúdo do Azure ou qualquer outro estático hospedagem recurso provedor. Os requisitos somente são que seu conteúdo está disponível em HTTPS e pode ser acessado usando CORS. Depois que você já exposto seu conteúdo estático na web, você pode editar sua política para apontar para esse local e apresentar conteúdo aos seus clientes. O [artigo de personalização de interface do usuário principal](active-directory-b2c-reference-ui-customization.md) descreve detalhadamente como funciona o recurso de personalização do Azure AD B2C.

Para os fins deste tutorial, nós já criou algum conteúdo de amostra e hospedado-lo no armazenamento de Blob do Azure. O conteúdo de amostra é muito básica personalização no tema de nossa empresa fictícia, "Wingtip Toys". Para testá-la em sua própria política, siga estas etapas:

1. Entrar em seu locatário no [portal do Azure](https://portal.azure.com/) e navegue até a lâmina de recursos B2C.
2. Clique em **políticas de inscrição ou entrar** em sua política (por exemplo, "b2c\_1\_entrada\_para cima\_entrada\_em").
3. Clique **personalização de IU de página** e **página de inscrição ou entrada unificada**.
4. Alterne a chave de **página personalizado de uso** para **Sim**. No campo de **página personalizado URI** , insira `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Clique em **Okey**.
5. Clique em **página de inscrição de conta Local**. Alterne a chave de **usar modelo personalizado** para **Sim**. No campo de **página personalizado URI** , insira `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
5. Repita a etapa mesma para a **página de inscrição de conta Social**.
 Clique em **Okey** duas vezes para fechar as lâminas de personalização de interface do usuário.
6. Clique em **Salvar**.

Agora você pode experimentar sua política personalizada. Você pode usar seu próprio aplicativo ou o espaço do Azure AD B2C se desejar, mas você pode também simplesmente clicar no comando **Executar agora** na lâmina política. Selecione o aplicativo na caixa suspensa e escolha o URI de redirecionamento apropriado. Clique no botão **Executar agora** . Uma nova guia do navegador será aberta e você poderá executar por meio de experiência do usuário do se inscrever para o seu aplicativo com o novo conteúdo no lugar!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Carregar o conteúdo de amostra para o armazenamento de Blob do Azure

Se você quiser usar o armazenamento de Blob do Azure para hospedar seu conteúdo de página, você pode criar sua própria conta de armazenamento e usar nossa ferramenta de auxiliar B2C para carregar os arquivos.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

1. Entrar no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ novo** > **dados + armazenamento** > **conta de armazenamento**. Você precisará de uma assinatura do Azure para criar uma conta de armazenamento de Blob do Azure. Você pode se inscrever uma avaliação gratuita no [site Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Forneça um **nome** para a conta de armazenamento (por exemplo, "contoso") e escolha as seleções apropriadas para a **camada de preços**, **grupo de recursos** e **assinatura**. Certifique-se de que você tem a opção de **Pin para Startboard** marcada. Clique em **criar**.
4. Volte para o Startboard e clique na conta de armazenamento que você acabou de criar.
5. Na seção de **Resumo** , clique em **contêineres**e clique em **+ Add**.
6. Forneça um **nome** para o recipiente (por exemplo, "b2c") e selecione **Blob** como o **tipo de acesso**. Clique em **Okey**.
7. O recipiente que você criou aparecerão na lista na lâmina **Blobs** . Anote a URL do contêiner; Por exemplo, ele deve ser semelhante ao `https://contoso.blob.core.windows.net/b2c`. Feche a lâmina **Blobs** .
8. Na lâmina armazenamento conta, clique **nas teclas** e anote os valores dos campos **Nome de conta de armazenamento** e a **Chave primária de acesso** .

1. Entrar no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ novo** > **dados + armazenamento** > **conta de armazenamento**. Você precisará de uma assinatura do Azure para criar uma conta de armazenamento de Blob do Azure. Você pode se inscrever uma avaliação gratuita no [site Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Selecione o **Armazenamento de Blob** em **Tipo de conta**e deixe os outros valores como padrão.  Você pode editar o grupo de recursos & local se desejar.  Clique em **criar**.
4. Volte para o Startboard e clique na conta de armazenamento que você acabou de criar.
5. Na seção **Resumo** , clique em **+ contêiner**.
6. Forneça um **nome** para o recipiente (por exemplo, "b2c") e selecione **Blob** como o **tipo de acesso**. Clique em **Okey**.
7. Abra o contêiner **Propriedades**e anote a URL do contêiner; Por exemplo, ele deve ser semelhante ao `https://contoso.blob.core.windows.net/b2c`. Feche a lâmina de contêiner.
8. Na lâmina armazenamento conta, clique no **Ícone de chave** e anote os valores dos campos **Nome de conta de armazenamento** e **Chave primária de acesso** .

> [AZURE.NOTE]
    **Chave primária de acesso** é uma credencial de segurança importantes.

### <a name="download-the-helper-tool-and-sample-files"></a>Baixar os arquivos de ferramenta e amostras de auxiliar

Você pode baixar os [arquivos de ferramenta e amostras de auxiliar de armazenamento de Blob do Azure como um arquivo. zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou cloná-lo do GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Este repositório contém um `sample_templates\wingtip` diretório, que contém o exemplo HTML, CSS e imagens. Para esses modelos fazer referência a sua própria conta de armazenamento de Blob do Azure, você precisará editar os arquivos HTML. Abrir `unified.html` e `selfasserted.html` e substituir todas as instâncias de `https://localhost` com a URL do seu próprio recipiente que você anotou nas etapas anteriores. Você deve usar o caminho absoluto dos arquivos HTML porque nesse caso, o HTML será fornecido por Azure AD, sob o domínio `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Carregue os arquivos de amostra

No mesmo repositório, descompacte `B2CAzureStorageClient.zip` e executar o `B2CAzureStorageClient.exe` dentro do arquivo. Este programa simplesmente carregar todos os arquivos na pasta que você especificar à sua conta de armazenamento e habilitar o acesso CORS para esses arquivos. Se você seguiu as etapas acima, os arquivos HTML e CSS agora serão apontando para sua conta de armazenamento. Observe que o nome da sua conta de armazenamento é a parte que precede `blob.core.windows.net`; Por exemplo, `contoso`. Você pode confirmar que o conteúdo foi carregado corretamente por tentando acessar `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` em um navegador. Também use [http://test-cors.org/](http://test-cors.org/) para certificar-se de que o conteúdo é agora CORS habilitado. (Procure por "status XHR: 200" no resultado.)

### <a name="customize-your-policy-again"></a>Personalizar sua política, novamente

Agora que você carregou o conteúdo de amostra à sua própria conta de armazenamento, você deve editar sua política de inscrição para fazer referência a ela. Repita as etapas da seção ["Personalizar sua política"](#customize-your-policy) acima, desta vez usando URLs da sua própria conta de armazenamento. Por exemplo, o local do seu `unified.html` arquivo seria `<url-of-your-container>/wingtip/unified.html`.

Agora você pode usar o botão **Executar agora** ou seu próprio aplicativo para executar sua política novamente. O resultado deve quase exatamente a mesma aparência – você usou a mesma amostra de HTML e CSS em ambos os casos. Entretanto, suas políticas agora estão fazendo referência a sua própria instância do armazenamento de Blob do Azure e você está livre para editar e carregue os arquivos novamente durante a envie. Para obter mais informações sobre como personalizar o HTML e CSS, consulte o [artigo de personalização de interface do usuário principal](active-directory-b2c-reference-ui-customization.md).
