<properties
    pageTitle="Integrar uma conta de armazenamento com CDN | Microsoft Azure"
    description="Saiba como usar a rede de entrega de conteúdo (CDN) do Azure para fornecer conteúdo de largura de banda alta armazenando blobs do armazenamento do Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="integrate-a-storage-account-with-cdn"></a>Integrar uma conta de armazenamento com CDN

CDN pode ser habilitado em cache o conteúdo de seu armazenamento do Azure. Ele oferece aos desenvolvedores uma solução global para fornecer o conteúdo de banda larga armazenando blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul.


## <a name="step-1-create-a-storage-account"></a>Etapa 1: Criar uma conta de armazenamento

Use o procedimento a seguir para criar uma nova conta de armazenamento para uma assinatura Azure. Uma conta de armazenamento fornece acesso a serviços de armazenamento do Azure. A conta de armazenamento representa o nível mais alto do namespace para acessar todos os componentes de serviço de armazenamento do Azure: Blob serviços, serviços de fila e os serviços de tabela. Para obter mais informações, consulte [Introdução ao Microsoft Azure armazenamento](../storage/storage-introduction.md).

Para criar uma conta de armazenamento, você deve ser administrador do serviço ou um administrador de colegas para a assinatura associado.

> [AZURE.NOTE] Há vários métodos que você pode usar para criar uma conta de armazenamento, incluindo o Portal do Azure e Powershell.  Para este tutorial, usaremos o Portal do Azure.  

**Para criar uma conta de armazenamento para uma assinatura do Azure**

1.  Entre [Portal do Azure](https://portal.azure.com).
2.  No canto superior esquerdo, selecione **novo**. Na caixa de diálogo **novo** , selecione **dados + armazenamento**, clique em **conta de armazenamento**.

    A lâmina de **criar a conta de armazenamento** será exibida.

    ![Criar conta de armazenamento][create-new-storage-account]

4. No campo **nome** , digite um nome de subdomínio. Essa entrada pode conter 3-24 letras minúsculas e números.

    Esse valor se torna o nome de host dentro o URI que é usado para tratar Blob, fila ou tabela de recursos para a assinatura. Para resolver um recurso de contêiner no serviço Blob, você usaria um URI no seguinte formato, onde * &lt;StorageAccountLabel&gt; * refere-se ao valor digitado em **Digite uma URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importante:** O rótulo de URL o subdomínio da conta de armazenamento URI de formulários e deve ser exclusivo entre todos os serviços hospedados no Azure.

    Este valor também é usado como o nome dessa conta de armazenamento no portal ou ao acessar essa conta programaticamente.

5. Deixe os padrões para o **modelo de implantação**, **tipo de conta**, **desempenho**e **replicação**. 

6. Selecione a **assinatura** que será usada com a conta de armazenamento.

7. Selecione ou crie um **Grupo de recursos**.  Para obter mais informações sobre grupos de recursos, consulte [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

8. Selecione um local para a sua conta de armazenamento.

8. Clique em **criar**. O processo de criação da conta de armazenamento pode demorar vários minutos para concluir.


## <a name="step-2-create-a-new-cdn-profile"></a>Etapa 2: Criar um novo perfil CDN

Um perfil CDN é um conjunto de pontos de extremidade CDN.  Cada perfil contém um ou mais pontos de extremidade CDN.  Talvez você queira usar vários perfis para organizar seus pontos de extremidade CDN por domínio internet, aplicativo web ou algum outro critério.

> [AZURE.TIP] Se você já tiver um perfil CDN que você deseja usar para este tutorial, prossiga para a [etapa 3](#step-3-create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>Etapa 3: Criar um novo ponto de extremidade CDN

**Para criar um novo ponto de extremidade CDN para sua conta de armazenamento**

1. No [Portal de gerenciamento do Azure](https://portal.azure.com), navegue até seu perfil CDN.  Você pode ter fixada ao painel na etapa anterior.  Se você não, você pode encontrá-lo clicando em **Procurar**, em seguida **perfis CDN**e, em seguida, clicando no perfil que você planeja adicionar seu ponto de extremidade para.

    A lâmina de perfil CDN é exibida.

    ![Perfil CDN][cdn-profile-settings]

2. Clique no botão **Adicionar ponto de extremidade** .

    ![Adicionar botão de ponto de extremidade][cdn-new-endpoint-button]

    A lâmina de **Adicionar um ponto de extremidade** é exibida.

    ![Adicionar blade de ponto de extremidade][cdn-add-endpoint]

3. Insira um **nome** para este ponto de extremidade CDN.  Esse nome será usado para acessar os recursos armazenados em cache no domínio `<endpointname>.azureedge.net`.

4. Na lista suspensa **tipo de origem** , selecione *armazenamento*.  

5. Na lista suspensa **nome do host de origem** , selecione sua conta de armazenamento.

6. Deixe os padrões para o **caminho de origem**, **cabeçalho do host de origem**e **porta de protocolo/origem**.  Você deve especificar pelo menos um protocolo (HTTP ou HTTPS).

    > [AZURE.NOTE] Essa configuração permite que todos os seus contêineres publicamente visíveis na sua conta de armazenamento para armazenamento em cache na CDN.  Se você quiser limitar o escopo para um único contêiner, use o **caminho de origem**.  Observe que o contêiner deve ter sua visibilidade definida como pública.

7. Clique no botão **Adicionar** para criar o novo ponto de extremidade.

8. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade para o perfil. O modo de exibição de lista mostra o URL usar para acessar o conteúdo em cache, bem como o domínio de origem.

    ![Ponto de extremidade CDN][cdn-endpoint-success]

    > [AZURE.NOTE] O ponto de extremidade não estará disponível imediatamente para uso.  Pode levar até 90 minutos para o registro a se propagar através da rede CDN. Os usuários que tentarem usar o nome de domínio CDN imediatamente podem receber o código de status 404 até que o conteúdo está disponível via a CDN.


## <a name="step-4-access-cdn-content"></a>Etapa 4: Access CDN conteúdo

Para acessar o conteúdo em cache na CDN, use a URL de CDN fornecida no portal. O endereço para um blob em cache será semelhante ao seguinte:

http://<;*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [AZURE.NOTE] Depois de habilitar o acesso CDN a uma conta de armazenamento ou serviço hospedado, todos os objetos publicamente disponíveis são qualificados para CDN cache de borda. Se você modificar um objeto que é atualmente armazenadas em cache a CDN, o novo conteúdo não estará disponível por meio da CDN até que a CDN atualiza seu conteúdo quando o período de tempo de vida conteúdo em cache expira.

## <a name="step-5-remove-content-from-the-cdn"></a>Etapa 5: Remover o conteúdo da CDN

Se você não deseja mais cache um objeto na rede de entrega de conteúdo o Azure (CDN), você poderá executar uma das seguintes etapas:

-   Você pode fazer o contêiner particular em vez de público. Consulte [Gerenciar acesso anônimo de leitura para contêineres e blobs](../storage/storage-manage-access-to-resources.md) para obter mais informações.
-   Você pode desabilitar ou excluir o ponto de extremidade CDN usando o Portal de gerenciamento.
-   Você pode modificar seu serviço hospedado para já não responder às solicitações para o objeto.

Um objeto que já estão armazenado na CDN permanecerá em cache até que o período de tempo de vida do objeto expirar ou o ponto de extremidade é limpo. Quando o período de tempo de vida expira, a CDN verificará se o ponto de extremidade CDN ainda estiver válido e o objeto ainda podem ser acessado anonimamente. Se não estiver, o objeto não será cache.


## <a name="additional-resources"></a>Recursos adicionais

-   [Como mapear CDN conteúdo para um domínio personalizado](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png
