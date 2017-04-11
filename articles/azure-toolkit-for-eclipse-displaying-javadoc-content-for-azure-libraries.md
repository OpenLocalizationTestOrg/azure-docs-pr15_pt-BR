<properties
    pageTitle="Exibir o conteúdo de Javadoc em Eclipse para o pacote de bibliotecas Azure para Java"
    description="Como exibir o conteúdo de Javadoc para as bibliotecas do Azure no Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Exibir o conteúdo de Javadoc em Eclipse para o pacote de bibliotecas Azure para Java #

O conteúdo de Javadoc para as bibliotecas do Azure para Java pode ser exibido no seu ambiente Eclipse associando o conteúdo de Javadoc para as bibliotecas do Azure para Java. As etapas a seguir mostram como usar essa funcionalidade em Eclipse.

Este procedimento supõe que você já tenha adicionado a biblioteca do Azure para Java ao seu caminho de compilação.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Para exibir o conteúdo de Javadoc no Eclipse para as bibliotecas do Azure para Java ##

* No Explorador de projeto do Eclipse, na seção **Referenciados bibliotecas** do seu projeto, abra o menu de contexto para a biblioteca do Azure para Java JAR. Por exemplo, **microsoft-windowsazure-api-0.1.0.jar** (o número da versão pode ser diferente, depende de qual versão você instalou).
* Clique em **Propriedades**.
* Caixa de diálogo **Propriedades** , no painel esquerdo, clique em **Localização do Javadoc**. Caixa de diálogo **Javadoc local** é exibida.
* Você pode especificar uma **URL Javadoc**ou um **Javadoc no arquivo morto**.
    * Se você optar por especificar uma **URL Javadoc**, use as URLs como **http://dl.windowsazure.com/javadoc** ou **http://dl.windowsazure.com/storage/javadoc**.
    * Se você optar por usar **Javadoc no arquivo**, você pode especificar um arquivo externo ou um arquivo de espaço de trabalho.
    Faça sua escolha e procurar/validar conforme necessário. O exemplo a seguir associa as bibliotecas do Azure para Java com o correspondente Javadoc JAR que foi baixado localmente para uma pasta chamada **c:\MyAzureJARs**.
    ![][ic553487]
* *Opcional*: clique em **Validar**. Possíveis problemas com o Javadoc JAR poderiam ser exibidos aqui.
* Clique em **Okey**.

Uma vez associados a biblioteca, o conteúdo de Javadoc deve exibir seu IDE Eclipse. Por exemplo, se `blob` é definido do tipo `CloudBlockBlob` em seu código, a seguir é um exemplo de conteúdo de Javadoc que aparece quando você digita `blob.acquireLease` no código:

![][ic553488]

## <a name="see-also"></a>Consulte também ##

[Kit de ferramentas de Azure para Eclipse][]

[Criando um aplicativo Hello World para Azure no Eclipse][]

[Instalando o Kit de ferramentas do Azure para Eclipse][] 

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center][].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criando um aplicativo Hello World para Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png