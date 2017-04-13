<properties
    pageTitle="Introdução ao Gerenciador de armazenamento (visualização) | Microsoft Azure"
    description="Gerenciar recursos de armazenamento do Azure com o Gerenciador de armazenamento (prévia)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="getting-started-with-storage-explorer-preview"></a>Introdução ao Gerenciador de armazenamento (prévia)

## <a name="overview"></a>Visão geral 

Gerenciador de armazenamento do Microsoft Azure (visualização) é um aplicativo autônomo que permite que você facilmente trabalhar com dados de armazenamento do Azure no Windows, OS X e Linux. Neste artigo, você aprenderá as diversas maneiras de conectar e gerenciar suas contas de armazenamento do Azure.

![Gerenciador de armazenamento do Microsoft Azure (visualização)][15]

## <a name="prerequisites"></a>Pré-requisitos

- [Baixe e instale o Gerenciador de armazenamento (prévia)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Conectar a uma conta de armazenamento ou serviço

Gerenciador de armazenamento (visualização) fornece uma inúmeras maneiras para se conectar à contas de armazenamento. Isso inclui conexão para contas de armazenamento associadas a suas assinaturas Azure, conexão com contas de armazenamento e serviços compartilhados de outras assinaturas Azure e até mesmo conectar e gerenciar armazenamento local usando o emulador de armazenamento do Azure:

- [Conectar a uma assinatura do Azure](#connect-to-an-azure-subscription) - gerenciar recursos de armazenamento pertencentes a sua assinatura do Azure.
- [Trabalhar com armazenamento de desenvolvimento local](#work-with-local-development-storage) - gerenciar armazenamento local usando o emulador de armazenamento do Azure. 
- [Anexar armazenamento externo](#attach-or-detach-an-external-storage-account) - gerenciar recursos de armazenamento pertencente a outra assinatura do Azure usando o nome da conta e chave a conta de armazenamento.
- [Conta de armazenamento de anexar usando SAS](#attach-storage-account-using-sas) - gerenciar recursos de armazenamento pertencente a outra assinatura do Azure usando uma SAS.
- [Serviço de anexar usando SAS](#attach-service-using-sas) - gerenciar um serviço de armazenamento específico (contêiner de blob, fila ou tabela) pertencentes a outra assinatura do Azure usando uma SAS.

## <a name="connect-to-an-azure-subscription"></a>Conectar a uma assinatura do Azure

> [AZURE.NOTE] Se você não tiver uma conta do Azure, você pode [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. No Explorador de armazenamento (visualização), selecione **configurações de conta do Azure**. 

    ![Configurações de conta do Azure][0]

1. No painel esquerdo agora exibirá todas as contas da Microsoft que você tiver conectado. Para se conectar a outra conta, selecione **Adicionar uma conta**e siga as caixas de diálogo para entrar com uma conta da Microsoft que está associada a pelo menos uma assinatura ativa do Azure.

    ![Adicionar uma conta][1]

1. Assim que você entrar com êxito em uma conta da Microsoft, no painel esquerdo preencherá com as assinaturas Azure associadas a essa conta. Selecione as assinaturas Azure com os quais você deseja trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **todas as assinaturas** alterna selecionando todos ou nenhum das assinaturas Azure listadas).

    ![Selecione assinaturas do Azure][3]

1. No painel esquerdo agora exibirão as contas de armazenamento associadas as assinaturas Azure selecionadas.

    ![Assinaturas do Azure selecionadas][4]

## <a name="work-with-local-development-storage"></a>Trabalhar com armazenamento local de desenvolvimento

Gerenciador de armazenamento (visualização) permite que você trabalhe em relação a armazenamento local usando o emulador de armazenamento do Azure. Isso permite escrever código contra e testar o armazenamento sem necessariamente ter uma conta de armazenamento implantada no Azure (desde que a conta de armazenamento está sendo emular pelo emulador de armazenamento do Azure).

>[AZURE.NOTE] O emulador de armazenamento do Azure é suportado atualmente somente para o Windows. 

1. No painel esquerdo do Gerenciador de armazenamento (visualização), expanda o **(Local e anexadas** > **Contas de armazenamento** > nó**(desenvolvimento)** .

    ![Nó de desenvolvimento local][21]

1. Se você ainda não tiver instalado o emulador de armazenamento do Azure, você será solicitado a fazer isso por meio de uma barra de informações. Se a barra de informações for exibida, selecione **Baixar a versão mais recente**e instale o emulador. 

    ![Baixe o prompt de emulador de armazenamento do Azure][22]

1. Quando o emulador estiver instalado, você terá a capacidade de criar e trabalhar com tabelas, filas e blobs locais. Para saber como trabalhar com cada tipo de conta de armazenamento, selecione o link apropriado abaixo:

    - [Gerenciar recursos de armazenamento de blob do Microsoft Azure](./vs-azure-tools-storage-explorer-blobs.md)
    - Gerenciar recursos de armazenamento de compartilhamento de arquivo Azure - *em breve*
    - Gerenciar recursos de armazenamento do Azure fila - *em breve*
    - Gerenciar recursos de armazenamento de tabela do Microsoft Azure - *em breve*

## <a name="attach-or-detach-an-external-storage-account"></a>Anexar ou desanexar uma conta de armazenamento externo

Gerenciador de armazenamento (visualização) fornece a capacidade de anexar a contas de armazenamento externo para que as contas de armazenamento podem ser facilmente compartilhadas. Esta seção explica como anexar a (e desanexar) contas de armazenamento externo.

### <a name="get-the-storage-account-credentials"></a>Obter as credenciais de conta de armazenamento

Para compartilhar uma conta de armazenamento externo, o proprietário da conta deve primeiro obter as credenciais - nome da conta e chave - para a conta e, em seguida, compartilhar informações com a pessoa que desejam anexar a essa conta (externa). Como obter as credenciais de conta de armazenamento pode ser feito por meio do portal do Azure seguindo estas etapas: 

1.  Entrar no [portal do Azure](https://portal.azure.com).
1.  Selecione **Procurar**.
1.  Selecione **contas de armazenamento**.
1.  Na lâmina **Contas de armazenamento** , selecione a conta de armazenamento desejada.
1.  Na lâmina **configurações** da conta de armazenamento selecionado, selecione **as teclas de acesso**.

    ![Opção de teclas de acesso][5]
    
1.  Na lâmina **teclas de acesso** , copie os valores de **Nome de conta de armazenamento** e **chave 1** para uso ao anexar à conta de armazenamento. 

    ![Teclas de acesso][6]

### <a name="attach-to-an-external-storage-account"></a>Anexar a uma conta de armazenamento externo
Para anexar a uma conta de armazenamento externo, você precisará do nome e a chave da conta. A seção *obter as credenciais de conta de armazenamento* explica como obter esses valores a partir do portal do Azure. No entanto, observe que, no portal do, a chave de conta é chamada "chave 1" Portanto onde o Storage Explorer (visualização) solicitar uma chave de conta, você será Insira (ou cole) o valor de "chave 1". 
 
1.  No Explorador de armazenamento (visualização), selecione **conectar ao armazenamento do Azure**.

    ![Conectar-se a opção de armazenamento do Azure][23]

1.  Na caixa de diálogo **conectar ao armazenamento do Azure** , especifique a chave de conta (valor do portal do Azure "chave 1") e selecione **Avançar**.

    ![Conectar-se a caixa de diálogo de armazenamento do Azure][24] 

1.  Na caixa de diálogo **Anexar armazenamento externo** , insira o nome da conta de armazenamento na caixa **nome da conta** , especifique outras configurações desejadas e selecione **Avançar** quando terminar. 

    ![Anexar caixa de diálogo de armazenamento externo][8]

1.  Na caixa de diálogo **Resumo de Conexão** , verifique se as informações. Se você quiser alterar alguma propriedade, selecione **Voltar** e digite novamente as configurações desejadas. Quando terminar, selecione **Conectar**.

1.  Uma vez conectado, a conta de armazenamento externo será exibida com o texto **(externo)** acrescentado ao nome da conta de armazenamento. 

    ![Resultado de conexão para uma conta de armazenamento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desconectar-se de uma conta de armazenamento externo

1.  Clique com botão direito na conta de armazenamento externo que você deseja desanexar e - no menu de contexto - selecione **Desanexar**.

    ![Desanexar da opção de armazenamento][10]

1.  Quando for exibida a caixa de mensagem de confirmação, selecione **Sim** para confirmar a desconexão da conta de armazenamento externo.

## <a name="attach-storage-account-using-sas"></a>Anexar a conta de armazenamento usando SAS

Uma [SAS (assinatura de acesso compartilhado)](storage/storage-dotnet-shared-access-signature-part-1.md) dá o administrador de uma assinatura do Azure a capacidade de conceder acesso a uma conta de armazenamento em base temporária sem precisar fornecer suas credenciais de assinatura Azure. 

Para ilustrar isso, digamos UserA é um administrador de uma assinatura do Azure e UserA deseja permitir UserB acessar uma conta de armazenamento por um tempo limitado com determinadas permissões:

1. UserA gera uma SAS (consiste a cadeia de conexão da conta de armazenamento) para um período específico e com as permissões desejadas.
1. UserA compartilha as associações de segurança com a pessoa que desejam acesso à conta armazenamento - UserB, em nosso exemplo.  
1. UserB usa armazenamento Explorer (visualização) para anexar à conta pertencentes a UserA usando as associações de segurança fornecidas. 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obter uma SAS para a conta que você deseja compartilhar

1.  No Explorador de armazenamento (visualização), clique com botão direito a conta de armazenamento que você deseja compartilhar e - no menu de contexto - selecione **Obter assinatura de acesso compartilhado**.

    ![Obter a opção de menu de contexto SAS][13]

1. Na caixa de diálogo **Assinatura de acesso compartilhado** , especifique o período de tempo e permissões que você deseja para a conta e selecione **criar**.

    ![Acessar a caixa de diálogo SAS][14]
 
1. Uma segunda caixa de diálogo de **Assinatura de acesso compartilhado** aparecerá exibindo as associações de segurança. Selecione **Copiar** ao lado da **Cadeia de Conexão** copiá-lo para a área de transferência. Selecione **Fechar** para fechar a caixa de diálogo.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Anexar à conta compartilhada usando as associações de segurança

1.  No Explorador de armazenamento (visualização), selecione **conectar ao armazenamento do Azure**.

    ![Conectar-se a opção de armazenamento do Azure][23]

1.  Na caixa de diálogo **conectar ao armazenamento do Azure** , especifique a cadeia de conexão e selecione **Avançar**.

    ![Conectar-se a caixa de diálogo de armazenamento do Azure][24] 

1.  Na caixa de diálogo **Resumo de Conexão** , verifique se as informações. Se você quiser alterar alguma propriedade, selecione **Voltar** e digite novamente as configurações desejadas. Quando terminar, selecione **Conectar**.

1.  Uma vez conectado, a conta de armazenamento será exibida com o texto (SAS) acrescentado ao nome da conta que você forneceu.

    ![O resultado da anexada a uma conta usando SAS][17]

## <a name="attach-service-using-sas"></a>Anexar usando SAS de serviço

Na seção [conta de armazenamento de anexar usando SAS](#attach-storage-account-using-sas) ilustra como um administrador de assinatura Azure concedendo acesso temporário a uma conta de armazenamento gerando (e compartilhamento) uma SAS para a conta de armazenamento. Da mesma forma, uma SAS podem ser gerada para um serviço específico (contêiner de blob, fila ou tabela) dentro de uma conta de armazenamento.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Gerar uma SAS para o serviço que você deseja compartilhar

Nesse contexto, um serviço pode ser um contêiner de blob, fila ou tabela. As seções a seguir explicam como gerar as associações de segurança para o serviço listado:

- [Obter as associações de segurança para um contêiner de blob](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Obter as associações de segurança para um compartilhamento de arquivo - *em breve*
- Obter as associações de segurança para uma fila - *em breve*
- Obter as associações de segurança de uma tabela - *em breve*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Anexar com o serviço de conta compartilhado usando as associações de segurança

1.  No Explorador de armazenamento (visualização), selecione **conectar ao armazenamento do Azure**.

    ![Conectar-se a opção de armazenamento do Azure][23]

1.  Na caixa de diálogo **conectar ao armazenamento do Azure** , especifique o URI SAS e selecione **Avançar**.

    ![Conectar-se a caixa de diálogo de armazenamento do Azure][24] 

1.  Na caixa de diálogo **Resumo de Conexão** , verifique se as informações. Se você quiser alterar alguma propriedade, selecione **Voltar** e digite novamente as configurações desejadas. Quando terminar, selecione **Conectar**.

1.  Uma vez conectado, o serviço recentemente anexado será exibido sob o nó de **Serviço SAS ()** .

    ![Resultado de conectar a um serviço compartilhado usando SAS][20]

## <a name="search-for-storage-accounts"></a>Procure por contas de armazenamento

Se você tiver uma lista longa de contas de armazenamento, uma maneira rápida para localizar uma conta de armazenamento particular é usar a caixa Pesquisar na parte superior do painel esquerdo. 

Enquanto você estiver digitando na caixa de pesquisa, no painel esquerdo exibirá apenas as contas de armazenamento que correspondem ao valor de pesquisa que você digitou até que apontam. A captura de tela a seguir ilustra um exemplo onde posso pesquisou para todas as contas de armazenamento onde o nome da conta de armazenamento contendo o texto "tarcher".

![Pesquisa de conta de armazenamento][11]
    
Para limpar a pesquisa, selecione o botão **x** na caixa de pesquisa.

## <a name="next-steps"></a>Próximas etapas
- [Gerenciar recursos de armazenamento de blob do Microsoft Azure com o Storage Explorer (prévia)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
