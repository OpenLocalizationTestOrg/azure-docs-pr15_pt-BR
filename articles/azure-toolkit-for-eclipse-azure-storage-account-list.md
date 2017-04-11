<properties
    pageTitle="Lista de contas de armazenamento do Azure"
    description="Gerenciar suas configurações de conta de armazenamento usando o Kit de ferramentas do Azure para Eclipse"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# <a name="azure-storage-account-list"></a>Lista de contas de armazenamento do Azure #

As contas de armazenamento do Azure permitem locais de download ser usado para seu JDK, servidor de aplicativos e componentes aleatório, bem como para armazenar o estado ao usar o armazenamento em cache. Eclipse mantém uma lista de contas de armazenamento conhecidos que estão disponíveis para seus projetos em seu espaço de trabalho do Eclipse. Para abrir a caixa de diálogo **Contas de armazenamento** , que é usada para gerenciar dessa lista, em Eclipse, clique em **janela**, clique em **Preferências**, expanda **Azure**e, em seguida, clique em **Contas de armazenamento**.

A seguir mostra a caixa de diálogo **Contas de armazenamento** .

![][ic719496]

Esta caixa de diálogo também pode ser aberta em um link de **contas** em caixas de diálogo que usam contas de armazenamento, como o seguinte:

* A guia **JDK** da caixa de diálogo **Configuração do servidor** .
* Na guia **servidor** da caixa de diálogo **Configuração do servidor** .
* Caixa de diálogo **Add Component** .
* A caixa de diálogo de propriedades do **cache** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Para importar suas contas de armazenamento utilizando um arquivo de configurações de publicação ##

1. Caixa de diálogo **Contas de armazenamento** , clique em **Importar de arquivo de configurações de publicação**.
2. (Pule esta etapa se você já salvou um arquivo de configurações de publicação para sua máquina local). Na caixa de diálogo **Importar informações de assinatura** , clique em **Baixar o arquivo de configurações de publicação**. Se você ainda não conectado para sua conta do Azure, você será solicitado a fazer logon no. Em seguida, você será solicitado a salvar um Azure Publicar arquivo de configurações. (Você pode ignorar as instruções resultantes mostradas nas páginas de logon - elas são fornecidos pelo portal do Azure e destinam-se para usuários do Visual Studio.) Salvá-lo no computador local.
3. Ainda na caixa de diálogo **Importar informações de assinatura** , clique no botão **Procurar** , selecione o arquivo de configurações de publicação que salvo localmente anteriormente e clique em **Abrir**.
4. Clique **Okey** para fechar a caixa de diálogo **Importar informações de assinatura** .

## <a name="to-create-a-new-storage-account"></a>Para criar uma nova conta de armazenamento ##

1. Caixa de diálogo **Contas de armazenamento** , clique em **Adicionar**.
2. Na caixa de diálogo **Adicionar conta de armazenamento** , clique em **novo**.
3. Na caixa de diálogo **Nova conta de armazenamento** , especifique valores para o seguinte:
    * Nome de conta de armazenamento.
    * Local da conta de armazenamento.
    * Descrição da conta de armazenamento.
    * A assinatura à qual pertence a conta de armazenamento.
4. Clique **Okey** para fechar a caixa de diálogo de **Nova conta de armazenamento** .

Ele pode levar alguns minutos para que sua conta de armazenamento a ser criado. Após sua criação, clique **Okey** para fechar a caixa de diálogo **Adicionar conta de armazenamento** , e sua nova conta de armazenamento será adicionada à lista de contas de armazenamento disponível.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Para adicionar uma conta existente do armazenamento à lista ##

1. Se você ainda não tiver uma conta de armazenamento do Azure, criar uma seguindo as etapas indicadas na seção **para criar uma nova seção de conta de armazenamento** acima. (Como alternativa, você pode criar uma nova conta de armazenamento no [Portal de gerenciamento do Azure][]).
2. Caixa de diálogo **Contas de armazenamento** , clique em **Adicionar**.
3. Na caixa de diálogo **Adicionar conta de armazenamento** , insira valores para o **nome** e a **Chave de acesso**. A chave de acesso e nome da conta deve ser para uma conta existente do armazenamento do Azure. Use a seção de **armazenamento** do [Portal de gerenciamento do Azure][] para exibir os nomes de conta de armazenamento e chaves. Sua caixa de diálogo **Adicionar conta de armazenamento** terá aparência semelhante à seguinte.

    ![][ic719497]

4. Clique **Okey** para fechar a caixa de diálogo **Adicionar conta de armazenamento** .

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Para modificar uma conta de armazenamento para usar uma nova chave de acesso ##

1. Na caixa de diálogo **Contas de armazenamento** , clique na conta de armazenamento que você deseja editar e clique em **Editar**.
2. Na caixa de diálogo **Editar tecla de acesso de conta de armazenamento** , modifique o valor de **Chave de acesso** .
3. Clique **Okey** para fechar a caixa de diálogo **Editar tecla de acesso de conta de armazenamento** .

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Para remover uma conta de armazenamento da lista mantida no Eclipse ##

1. Na caixa de diálogo **Contas de armazenamento** , clique na conta de armazenamento que você deseja editar e clique em **Remover**.
2. Clique em **Okey** quando solicitado para remover a conta de armazenamento.

>[AZURE.NOTE] Remover a conta de armazenamento por meio da caixa de diálogo **Contas de armazenamento** apenas remove da lista de contas de armazenamento pode ser visualizadas dentro Eclipse. Ele não remove a conta de armazenamento de sua assinatura Azure. Além disso, a conta de armazenamento pode aparecer novamente na sua lista, depois Eclipse carrega novamente os detalhes da sua assinatura.

## <a name="see-also"></a>Consulte também ##

[Kit de ferramentas de Azure para Eclipse][]

[Instalando o Kit de ferramentas do Azure para Eclipse][] 

[Criando um aplicativo Hello World para Azure no Eclipse][]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center][].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Portal de gerenciamento do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Criando um aplicativo Hello World para Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png
