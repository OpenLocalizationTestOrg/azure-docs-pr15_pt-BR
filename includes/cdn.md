# <a name="using-cdn-for-azure"></a>Usando o CDN do Azure

A rede de entrega de conteúdo (CDN) do Azure oferece aos desenvolvedores uma solução global para fornecer o conteúdo de banda larga armazenando blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul. Para obter uma lista atual dos locais de nó CDN, consulte [Azure CDN nó locais].

Esta tarefa inclui as seguintes etapas:

* [Etapa 1: Criar uma conta de armazenamento](#Step1)
* [Etapa 2: Criar um novo ponto de extremidade CDN para sua conta de armazenamento](#Step2)
* [Etapa 3: Acessar seu conteúdo CDN](#Step3)
* [Etapa 4: Remover o conteúdo CDN](#Step4)

Os benefícios de usar CDN em cache dados Azure incluem:

-   Melhor desempenho e experiência de usuário para usuários finais que não são uma fonte de conteúdo e estiver usando aplicativos onde muitos 'viagens de internet' são necessárias para carregar o conteúdo
-   Grande escala distribuída para lidar melhor com alta carga instantânea, digamos, no início de um evento como um lançamento do produto

Clientes CDN existentes agora podem usar o Azure CDN no [Azure portal clássico]. A CDN é um recurso de complemento à sua assinatura e tem um [plano de cobrança]de separado.

<a id="Step1"> </a>
<h2>Etapa 1: Criar uma conta de armazenamento</h2>

Use o procedimento a seguir para criar uma nova conta de armazenamento para uma assinatura Azure. Uma conta de armazenamento fornece acesso a serviços de armazenamento do Azure. A conta de armazenamento representa o nível mais alto do namespace para acessar todos os componentes de serviço de armazenamento do Azure: Blob serviços, serviços de fila e os serviços de tabela. Para obter mais informações sobre os serviços de armazenamento do Azure, consulte [usando os serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Para criar uma conta de armazenamento, você deve ser administrador do serviço ou um administrador de colegas para a assinatura associado.

> [AZURE.NOTE] Para obter informações sobre como executar essa operação usando a API de gerenciamento de serviço do Azure, consulte o tópico de referência de [Criar a conta de armazenamento](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) .

**Para criar uma conta de armazenamento para uma assinatura do Azure**

1.  Faça logon no [portal de clássico Azure].
2.  No canto inferior esquerdo, clique em **novo**. Na caixa de diálogo **novo** , selecione **Os serviços de dados**, clique em **armazenamento**, então **Criar rápida**.

    Aparece a caixa de diálogo **Criar conta de armazenamento** .

    ![Criar conta de armazenamento][create-new-storage-account]

4. No campo **URL** , digite um nome de subdomínio. Essa entrada pode conter de 3-24 letras minúsculas e números.

    Esse valor se torna o nome de host dentro o URI que é usado para tratar Blob, fila ou tabela de recursos para a assinatura. Para resolver um recurso de contêiner no serviço Blob, você usaria um URI no seguinte formato, onde * &lt;StorageAccountLabel&gt; * refere-se ao valor digitado em **Digite uma URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importante:** O rótulo de URL o subdomínio da conta de armazenamento URI de formulários e deve ser exclusivo entre todos os serviços hospedados no Azure.

    Este valor também é usado como o nome dessa conta de armazenamento no portal ou ao acessar essa conta programaticamente.

5.  Na lista suspensa **Região/afinidade grupo** , selecione uma região ou grupo de afinidade para a conta de armazenamento. Selecione um grupo de afinidade em vez de uma região se quiser que seus serviços de armazenamento estejam no mesmo data center com outros serviços do Windows Azure que você está usando. Isso pode melhorar o desempenho e sem encargos são incorridos pelo egresso.  

    **Observação:** Para criar um grupo de afinidade, abra a área **configurações** do Portal de gerenciamento, clique em **grupos de afinidade**e clique em **Adicionar um grupo de afinidade** ou **Adicionar**. Você também pode criar e gerenciar grupos de afinidade usando a API de gerenciamento de serviço do Windows Azure. Para obter mais informações, consulte [operações em grupos de afinidade].

6. Na lista suspensa de **assinatura** , selecione a assinatura que será usada com a conta de armazenamento.
7.  Clique em **Criar conta de armazenamento**. O processo de criação da conta de armazenamento pode demorar vários minutos para concluir.
8.  Para verificar que a conta de armazenamento foi criada com êxito, verifique se a conta aparece na itens relacionados para **armazenamento** com um status **Online**.

<a id="Step2"> </a>
<h2>Etapa 2: Criar um novo ponto de extremidade CDN para sua conta de armazenamento</h2>

Depois de habilitar o acesso CDN a uma conta de armazenamento ou serviço hospedado, todos os objetos publicamente disponíveis são qualificados para CDN cache de borda. Se você modificar um objeto que é atualmente armazenadas em cache a CDN, o novo conteúdo não estará disponível por meio da CDN até que a CDN atualiza seu conteúdo quando o período de tempo de vida conteúdo em cache expira.

**Para criar um novo ponto de extremidade CDN para sua conta de armazenamento**

1. No [portal do Azure clássico], no painel de navegação, clique em **CDN**.

2. Na faixa de opções, clique em **novo**. Na caixa de diálogo **novo** , selecione **Os serviços de aplicativo**, e em seguida, **CDN**, **Criar rápida**.

3. Na lista suspensa de **Domínio de origem** , selecione a conta de armazenamento que você criou na seção anterior na lista de suas contas de armazenamento disponível. 

4. Clique no botão **criar** para criar o novo ponto de extremidade.

5. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade para a assinatura. O modo de exibição de lista mostra o URL usar para acessar o conteúdo em cache, bem como o domínio de origem. 

    O domínio de origem é o local do qual a CDN armazena conteúdo em cache. O domínio de origem pode ser uma conta de armazenamento ou um serviço de nuvem; uma conta de armazenamento é usada para os fins deste exemplo. Conteúdo do armazenamento é armazenadas em cache para os servidores de borda para uma configuração de controle de cache que você especificar, ou a heurística padrão da rede cache de acordo com. 


    > [AZURE.NOTE] A configuração criada para o ponto de extremidade não estará disponível imediatamente; pode levar até 60 minutos para o registro a se propagar através da rede CDN. Os usuários que tentarem usar o nome de domínio CDN imediatamente podem receber o código de status 400 (solicitação incorreta) até que o conteúdo está disponível via a CDN.

<a id="Step3"> </a>
<h2>Etapa 3: Access CDN conteúdo</h2> 

Para acessar o conteúdo em cache na CDN, use a URL de CDN fornecida no portal. O endereço para um blob em cache será semelhante ao seguinte:

http://<;*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Etapa 4: Remover o conteúdo da CDN</h2>

Se você não deseja mais cache um objeto na rede de entrega de conteúdo o Azure (CDN), você poderá executar uma das seguintes etapas:

-   Para um blob Azure, você pode excluir o blob do contêiner público.
-   Você pode fazer o contêiner particular em vez de público. Consulte [Restringir o acesso aos contêineres e Blobs](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) para obter mais informações.
-   Você pode desabilitar ou excluir o ponto de extremidade CDN usando o Portal de gerenciamento.
-   Você pode modificar seu serviço hospedado para já não responder às solicitações para o objeto.

Um objeto que já estão armazenado na CDN permanecerá em cache até que o período de tempo de vida do objeto expirar. Quando o período de tempo de vida expira, a CDN verificará se o ponto de extremidade CDN ainda estiver válido e o objeto ainda podem ser acessado anonimamente. Se não estiver, o objeto não será cache.

A capacidade de limpar imediatamente o conteúdo não é suportada atualmente no Portal de gerenciamento do Azure. Contate o [suporte do Azure](https://azure.microsoft.com/support/options/) se você precisar imediatamente Limpar conteúdo. 

## <a name="additional-resources"></a>Recursos adicionais

-   [Como criar um grupo de afinidade no Azure]
-   [Como: gerenciar contas de armazenamento para uma assinatura do Azure]
-   [Sobre a API de gerenciamento de serviço]
-   [Como mapear CDN conteúdo para um domínio personalizado]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Locais de nó do Azure CDN]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Azure portal clássico]: https://manage.windowsazure.com/
  [plano de cobrança]: /pricing/calculator/?scenario=full
  [Como criar um grupo de afinidade no Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Sobre a API de gerenciamento de serviço]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Como mapear CDN conteúdo para um domínio personalizado]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
