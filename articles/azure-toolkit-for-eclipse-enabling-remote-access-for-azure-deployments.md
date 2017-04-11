<properties
    pageTitle="Habilitar o acesso remoto para implantações Azure em Eclipse"
    description="Saiba como habilitar o acesso remoto para implantações Azure usando o Kit de ferramentas do Azure para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Habilitar o acesso remoto para implantações Azure em Eclipse

Para ajudar a solucionar problemas de suas implantações, você pode ativar e usar o acesso remoto para se conectar à máquina virtual sua implantação de hospedagem. A funcionalidade de acesso remoto se baseia no protocolo de área de trabalho remota (RDP). Você pode configurar o acesso remoto para a sua implantação depois de você publicá-lo no Azure, ou se você estiver usando o Eclipse com um sistema operacional Windows, você pode configurar o acesso remoto antes de publicar Azure. Observe que você precisará de um cliente de desktop remoto que seja compatível com seu sistema operacional para conectar-se a máquina virtual da sua implantação do Azure.

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Como habilitar o acesso remoto antes de implantar no Azure

> [AZURE.NOTE] Para habilitar o acesso remoto antes de implantar seu aplicativo no Azure, você precisa estar executando o Eclipse no Windows.

A imagem a seguir mostra as propriedades de **Acesso remoto a** caixa de diálogo usada para habilitar o acesso remoto.

![][ic719494]

Há duas maneiras de exibir a caixa de diálogo de propriedades de **Acesso remoto** :

* Clique no link **Avançado** na seção **Acesso remoto** da caixa de diálogo **Publicar Azure** .
* Abra a caixa de diálogo de **Propriedades** do seu projeto Azure.

Quando você cria um novo projeto de implantação do Azure, o projeto não terão acesso remoto habilitado por padrão. No entanto, você pode facilmente habilitar acesso remoto, especificando o nome de usuário e senha na caixa de diálogo **Publicar Azure** . A senha de acesso remoto está criptografada usando certificados x. 509. Se você não usar fornecer seu próprio certificado, a criptografia depende de um certificado auto-assinado fornecido com o plug-in do Azure para Eclipse. Este certificado auto-assinado é na pasta de **certificado** do seu projeto Azure, armazenado como um arquivo de certificado pública (SampleRemoteAccessPublic.cer) e um arquivo de certificado do Exchange de informações pessoais (PFX) (SampleRemoteAccessPrivate.pfx). O último contém a chave particular para o certificado e tem uma senha de padrão, **Senha1**. No entanto, como essa senha é conhecimento público, o certificado padrão deve ser usado somente para fins de aprendizado, não para uma implantação de produção. Portanto diferente para fins de aprendizado, quando quiser habilitado sessões remotas para suas implantações, você deve clicar no link **Avançado** na caixa de diálogo **Publicar Azure** para especificar seu próprio certificado. Observe que você precisará carregar a versão PFX do certificado no seu serviço hospedado dentro do Portal de gerenciamento do Azure, para que Azure pode descriptografar a senha do usuário.

O resto do tutorial mostra como habilitar o acesso remoto para um projeto de implantação Azure inicialmente criado com acesso remoto desativado. Para fins deste tutorial, vamos criar um novo certificado auto-assinado e seu arquivo. pfx terá uma senha de sua escolha. Você também tem a opção de usar um certificado emitido por uma autoridade de certificação.

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Como habilitar o acesso remoto após você ter implantado no Azure

Para habilitar o acesso remoto após você ter implantado no Azure, use as seguintes etapas:

1. Faça logon no portal de gerenciamento Azure usando sua conta do Azure
1. Em sua lista de **Serviços**de nuvem, selecione o serviço de nuvem implantado
1. Na página da web de serviço de nuvem, clique no link **Configurar**
1. Na parte inferior da página de configuração, clique no link **remoto**
1. Quando a caixa de diálogo pop-up aparecer:
    * Especifique a função para a qual você deseja habilitar o acesso remoto
    * Clique para selecionar a caixa de seleção **Habilitar a área de trabalho remota**
    * Especifique um nome de usuário e senha que você deseja usar para acesso remoto
    * Selecione o certificado para usar
1. Clique em **Okey** 

Você verá uma mensagem informando que a alteração da configuração está em andamento, o que pode levar alguns minutos para ser concluída. Após a alteração de configuração, siga as etapas na seção **para efetuar login remotamente** neste artigo.
    
## <a name="how-to-enable-remote-access-in-your-package"></a>Como habilitar o acesso remoto no pacote

1. No painel do Explorador de projeto do Eclipse, seu projeto Azure de atalho e clique em **Propriedades**.

1. Na caixa de diálogo **Propriedades** , expanda **Azure** no painel à esquerda e clique em **Acesso remoto**.

1. Na caixa de diálogo **Acesso remoto** , certifique-se de **que habilitar todas as funções aceitar conexões de área de trabalho remota com essas credenciais de login** está marcada.

1. Especifique um nome de usuário para a conexão de área de trabalho remota.

1. Especifique e confirme a senha do usuário. Os valores de nome e senha de usuário definidos nesta caixa de diálogo serão usados quando você faz uma conexão de área de trabalho remota. (Observe que se trata de uma senha separada da sua senha PFX.)

1. Especifique a data de expiração da conta de usuário.

1. Clique em **novo** para criar um novo certificado auto-assinado. (Como alternativa, você pode selecionar um certificado de seu sistema de arquivo ou espaço de trabalho por meio dos botões de **espaço de trabalho** ou **sistema de arquivos** , respectivamente, mas para os objetivos deste tutorial vamos criar um novo certificado.)

    * Na caixa de diálogo **Novo certificado** , especifique e confirme a senha que você usará para o arquivo PFX.

    * Aceite o valor fornecido para **CN (nome)**ou use um nome personalizado.

    * Especifique o nome do caminho e do arquivo onde o novo certificado, no formulário. cer, será salvo. Para essa etapa e a próxima etapa, você pode usar a pasta de **certificado** do seu projeto Azure, mas você está livre para escolher outro local. Para fins deste tutorial, usaremos **c:\mycert\mycert.cer**. (Criar a pasta de **c:\mycert** antes de prosseguir ou usar uma pasta existente, se desejar.)

    * Especifique o nome do caminho e do arquivo onde o novo certificado e sua chave particular, no formulário. pfx, serão salvo. Para fins deste tutorial, usaremos **c:\mycert\mycert.pfx**. Sua caixa de diálogo **Novo certificado** deve ser semelhante à seguinte (atualizar os caminhos de pasta se você não usou **c:\mycert**):

        ![][ic712275]

    * Clique **Okey** para fechar a caixa de diálogo **Novo certificado** .

1. Sua caixa de diálogo de **Acesso remoto** deve ser semelhante ao seguinte:</p>

    ![][ic719495]

1. Clique **Okey** para fechar a caixa de diálogo de **Acesso remoto** .
    
Recrie seu aplicativo, com a compilação definida para implantação na nuvem.

## <a name="to-log-in-remotely"></a>Para efetuar login remotamente

Depois que sua instância de função estiver pronta, você pode fazer logon remotamente máquina virtual que hospeda seu aplicativo.

* Se estiver usando o Eclipse no Windows e você tiver selecionado a opção de **implantar de área de trabalho remota no início** durante a implantação do Azure, será apresentada com uma tela de logon de Conexão de área de trabalho remota quando sua implantação é iniciado. Quando você for solicitado a fornecer o nome de usuário e senha, insira os valores que você especificou para o usuário remoto e serão capazes de fazer logon no.

* Outra maneira de fazer logon no remotamente é por meio do <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Portal de gerenciamento do Azure</a>:

    * No modo de exibição **Serviços de nuvem** do portal de gerenciamento do Azure, clique em seu serviço de nuvem, clique em **instâncias**, clique uma instância específica e, em seguida, clique no botão **Conectar** . Botão **Conectar** aparece como o seguinte na barra de comando:

        ![][ic659273]

    * Depois de clicar no botão **Conectar** , você será solicitado a abrir um arquivo RDP. Abra o arquivo e siga as instruções na tela. (Você pode também salvar este arquivo em seu computador local e, em seguida, execute o arquivo clicando duas vezes nele para efetuar login remoto em sua máquina virtual sem precisar acessar primeiro o portal de gerenciamento.)

    * Quando você for solicitado a fornecer o nome de usuário e senha, insira os valores que você especificou para o usuário remoto e serão capazes de fazer logon no.

> [AZURE.NOTE] Se você estiver em um sistema operacional não-Windows, você precisa usar um cliente de área de trabalho remota que seja compatível com seu sistema operacional e siga as etapas para configurar o cliente com as configurações no arquivo RDP que você baixou.

## <a name="see-also"></a>Consulte também

[Kit de ferramentas de Azure para Eclipse][]

[Criando um aplicativo Hello World para Azure no Eclipse][]

[Instalando o Kit de ferramentas do Azure para Eclipse][] 

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center][].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ferramentas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criando um aplicativo Hello World para Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalando o Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png
