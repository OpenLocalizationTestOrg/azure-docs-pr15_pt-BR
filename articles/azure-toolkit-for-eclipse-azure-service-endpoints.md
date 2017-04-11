<properties
    pageTitle="Pontos de extremidade do serviço Azure"
    description="Descreve as configurações de ponto de extremidade de serviço do Azure no Kit de ferramentas do Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# <a name="azure-service-endpoints"></a>Pontos de extremidade do serviço Azure #

Pontos de extremidade do serviço Azure determinam que se seu aplicativo é implantado e gerenciado pela plataforma Windows Azure global, Azure operado pela 21Vianet na China, ou um particular plataforma Windows Azure. A caixa de diálogo de **Pontos de extremidade do serviço** permite especificar quais pontos de extremidade do serviço que você deseja usar. Para abrir a caixa de diálogo de **Pontos de extremidade do serviço** , dentro Eclipse, clique em **janela**, clique em **Preferências**, expanda **Azure**e clique em **Pontos de extremidade do serviço**. Configuração do campo **Ativo definir** determina quais pontos de extremidade do serviço Azure serão usados para os projetos Azure em seu espaço de trabalho atual.

A seguir mostra a caixa de diálogo de **Pontos de extremidade do serviço** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Definir os pontos de extremidade do serviço ##

Na caixa de diálogo **Pontos de extremidade do serviço** , execute uma das seguintes ações:

* Se você quiser usar a plataforma Windows Azure global, na lista suspensa **Definir ativa** , selecione **windowsazure.com** e clique **Okey**.
* Se você quiser usar o Azure operado pela 21Vianet na China, na lista suspensa **Definir ativa** , selecione **windowsazure.cn (China)** e clique em **Okey**.
* Se você quiser usar uma plataforma Windows Azure particular:
    1. Clique em **Editar**.
    2. Abre uma caixa de diálogo, informando que a caixa de diálogo de **Pontos de extremidade do serviço** será fechada, e o arquivo de conjuntos de preferência será aberto. Clique em **Okey**.
    3. No arquivo preferencesets.xml, crie um novo `preferenceset` elemento. Para esse novo elemento, crie `name`, `blob`, `management`, `portalURL` e `publishsettings` atributos e adicionar valores para eles que correspondem à sua plataforma Windows Azure particular. Você pode usar os valores fornecidos para o existente `preferenceset` elementos como modelos. **Observação**: O valor usado para o `blob` atributo deve conter o texto "blob" na URL.
    4. Salve e feche preferencesets.xml.
    5. Reabra a caixa de diálogo de **Pontos de extremidade do serviço** .
    6. Na lista suspensa **Definir ativo** , selecione o conjunto ativo que você criou e clique **Okey**.
    7. Depois de criar sua plataforma Windows Azure particular `preferenceset` elemento, você pode alterar os valores atribuídos a ele clicando no botão **Editar** na caixa de diálogo **Serviços de ponto de extremidade** . Você também pode criar vários plataforma Windows Azure particular `preferenceset` elementos, se desejar.

## <a name="see-also"></a>Consulte também ##

[Kit de ferramentas de Azure para Eclipse][]

[Instalando o Kit de ferramentas do Azure para Eclipse][] 

[Criando um aplicativo Hello World para Azure no Eclipse][]

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center][].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criando um aplicativo Hello World para Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png
