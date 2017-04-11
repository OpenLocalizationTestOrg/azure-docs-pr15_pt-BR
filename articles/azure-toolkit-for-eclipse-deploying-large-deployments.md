<properties
    pageTitle="Implantando grandes implantações"
    description="Saiba como implantar grandes implantações usando o Kit de ferramentas do Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# <a name="deploying-large-deployments"></a>Implantando grandes implantações #

Se sua implantação é muito grande para ser contido na pasta de approot padrão, você pode usar um recurso de armazenamento local como a pasta raiz de implantação para o seu JDK e servidor de aplicativos.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Para usar um recurso de armazenamento local como a pasta raiz de implantação para grandes implantações ##

1. Crie um novo recurso de armazenamento local. O nome do recurso não importa. Recursos de armazenamento são definidos no nível de função. A maneira mais rápida para acessar a caixa de diálogo de configuração de armazenamento local, do qual você pode criar um novo recurso de armazenamento local, é usando as seguintes etapas: a função no modo de exibição **Explorador de projeto** de atalho (expanda o nó do seu projeto Azure se você não vir a função), clique em **Azure**e clique em **Armazenamento Local**. Caixa de diálogo **Armazenamento Local** , clique em **Adicionar** para criar um novo recurso de armazenamento local.
1. Defina o tamanho desejado pelo menos 2048 MB (nada menos pode causar o mesmo arquivo tamanho problemas como você faria encontrar no approot).
1. Certifique-se de que **Limpar o conteúdo quando a instância de função é reciclada** está marcada; Isso ajudará a impedir a execução em conflitos com arquivos preexistentes no recurso quando a instância da função é reciclada de lógica de inicialização da implantação.
1. Certifique-se de que o valor de **variável de ambiente armazenando o caminho do diretório do recurso após a implantação** é definido como a cadeia de caracteres **DEPLOYROOT**. Sua caixa de diálogo de recursos de armazenamento local terá aparência semelhante à seguinte.
    ![][ic667943]

Como alternativa, se você usar **DEPLOYROOT** como o *nome* do recurso local e você não altere o nome de variável de ambiente gerado automaticamente (que será definido como **DEPLOYROOT_PATH** nesse caso), que é adequada para seu aplicativo também.

Informações adicionais sobre a criação de um recurso de armazenamento local podem ser encontradas propriedades de [armazenamento Local][].

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
[Propriedades de armazenamento local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png
