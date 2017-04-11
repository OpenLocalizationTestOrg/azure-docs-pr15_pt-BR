<properties
    pageTitle="Como usar o Azure auxiliar plug-in com integração contínua Hudson | Microsoft Azure"
    description="Descreve como usar o Azure auxiliar plug-in com integração contínua Hudson."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Como usar o Azure auxiliar plug-in com integração contínua Hudson

O Azure auxiliar plug-in para Hudson permite provisionar nós auxiliar no Azure quando executando distribuído construções.

## <a name="install-the-azure-slave-plug-in"></a>Instalar o plug-in de auxiliar do Azure

1. No painel Hudson, clique em **Gerenciar Hudson**.

1. Na página **Gerenciar Hudson** , clique em **Gerenciar plug-ins**.

1. Clique na guia **disponível** .

1. Clique em **Pesquisar** e digite **Azure** para limitar a lista para plug-ins relevantes.

    Se você optar por percorra a lista de plug-ins disponíveis, você encontrará o Azure auxiliar plug-in na seção **gerenciamento e Cluster distribuído construir** na guia **outros** .

1. Selecione a caixa de seleção para **Plug-in do Azure auxiliar**.

1. Clique em **instalar**.

1. Reinicie Hudson.

Agora que o plug-in estiver instalado, as próximas etapas seria configurar o plug-in com o seu perfil de inscrição do Azure e criar um modelo que será usado na criação da máquina virtual para o nó auxiliar.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurar o plug-in de auxiliar Azure com seu perfil de assinatura

Um perfil de assinatura, também conhecido como publicar configurações, é um arquivo XML que contém proteger credenciais e algumas informações adicionais, que você precisará trabalhar com o Azure em seu ambiente de desenvolvimento. Para configurar o Azure auxiliar plug-in, você precisa:

* Sua id de assinatura
* Um certificado de gerenciamento de sua assinatura

Esses podem ser encontrados no seu [perfil de assinatura]. Abaixo está um exemplo de um perfil de assinatura.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Quando você tiver seu perfil de assinatura, siga estas etapas para configurar o Azure auxiliar plug-in.

1. No painel Hudson, clique em **Gerenciar Hudson**.

1. Clique em **Configurar sistema**.

1. Role para baixo a página para encontrar a seção de **nuvem** .

1. Clique em **Adicionar novo nuvem > Microsoft Azure**.

    ![Adicionar novo nuvem][add new cloud]

    Isso mostrará os campos onde você precisa digitar detalhes da sua assinatura.

    ![Configurar perfil][configure profile]

1. Copie o certificado de id e gerenciamento de assinatura do seu perfil de assinatura e colá-los nos campos apropriados.

    Ao copiar o certificado de gerenciamento e id da assinatura, **não** inclua as aspas que delimitam os valores.

1. Clique em **Verificar configuração**.

1. Quando a configuração é verificada com êxito, clique em **Salvar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurar um modelo de máquina virtual para auxiliar o Azure plug-in

Um modelo de máquina virtual define os parâmetros que o plug-in será usado para criar um nó auxiliar no Azure. Nas etapas a seguir podemos criará modelo para uma VM Ubuntu.

1. No painel Hudson, clique em **Gerenciar Hudson**.

1. Clique em **Configurar sistema**.

1. Role para baixo a página para encontrar a seção de **nuvem** .

1. Dentro da seção de **nuvem** , localize o **Modelo de máquina Virtual do Azure adicionar** e clique no botão **Adicionar** .

    ![Adicionar modelo de máquina virtual][add vm template]

1. Especifique um nome de serviço de nuvem no campo **nome** . Se o nome que você especificar se refere a um serviço de nuvem existente, a máquina virtual será configurada nesse serviço. Caso contrário, o Azure criará um novo.

1. No campo **Descrição** , insira o texto que descreve o modelo que você está criando. Esta informação é somente para fins de documentário e não é usada em uma máquina virtual de provisionamento.

1. No campo de **etiquetas** , insira **linux**. Este rótulo é usado para identificar o modelo que você está criando e subsequentemente é usado para consultar o modelo ao criar um trabalho de Hudson.

1. Selecione uma região onde a máquina virtual será criada.

1. Selecione o tamanho de máquina virtual apropriado.

1. Especifica uma conta de armazenamento onde a máquina virtual será criada. Verifique se ele está na mesma região como o serviço de nuvem que você usará. Se desejar que o novo armazenamento a ser criado, você pode deixar este campo em branco.

1. Tempo de retenção Especifica o número de minutos antes de Hudson exclui um auxiliar ocioso. Deixe o valor padrão de 60.

1. Em **uso**, selecione a condição apropriada quando este nó auxiliar será usado. Por agora, selecione **usa esse nó tanto quanto possível**.

    Neste ponto, seu formulário seria um pouco semelhante a esta:

    ![configuração de modelo][template config]

1. **Família de imagem** ou Id, você precisa especificar qual imagem do sistema será instalada em sua máquina virtual. Você pode selecionar de uma lista de famílias de imagem ou especifique uma imagem personalizada.

    Se você quiser selecionar em uma lista das famílias de imagem, insira o primeiro caractere (diferencia maiusculas de minúsculas) do nome da família da imagem. Por exemplo, digitar **U** exibirá uma lista das famílias de servidor Ubuntu. Depois que você selecionar na lista, Jenkins usará a versão mais recente dessa imagem de sistema do que família ao provisionar sua máquina virtual.

    ![Lista de família de sistema operacional][OS family list]

    Se você tiver uma imagem personalizada que você deseja usar em vez disso, insira o nome da imagem personalizada. Nomes de imagem personalizada não são mostrados em uma lista para que você tenha que garantir que o nome foi digitado corretamente.    

    Neste tutorial, digite **U** para exibir uma lista de imagens de Ubuntu e selecione **Ubuntu servidor 14.04 LTS**.

1. Para **Iniciar o método**, selecione **SSH**.

1. Copie o script abaixo e cole no campo **script de inicialização** .

        # Install Java

        sudo apt-get -y update

        sudo apt-get install -y openjdk-7-jdk

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y openjdk-7-jdk

        # Install git

        sudo apt-get install -y git

        #Install ant

        sudo apt-get install -y ant

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y ant

    O **script de inicialização** será executada após a máquina virtual é criada. Neste exemplo, o script instala Java, gito e ant.

1. Nos campos **nome de usuário** e **senha** , insira valores de sua preferência para a conta de administrador que será criada em sua máquina virtual.

1. Clique em **Verificar o modelo** para verificar se os parâmetros especificados são válidos.

1. Clique em **Salvar**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Criar um trabalho de Hudson que é executado em um nó de auxiliar no Azure

Nesta seção, você criará uma tarefa de Hudson que será executado em um nó de auxiliar no Azure.

1. No painel Hudson, clique em **Nova tarefa**.

1. Insira um nome para o trabalho que você está criando.

1. Para o tipo de trabalho, selecione **criar um trabalho de software de estilo livre**.

1. Clique em **Okey**.

1. Na página de configuração de trabalho, selecione **restringir onde esse projeto pode ser executado**.

1. Selecione o **menu nó e rótulo** e **linux** (nós especificado este rótulo ao criar o modelo de máquina virtual na seção anterior).

1. Na seção **criar** , clique em **Adicionar etapa de construção** e selecione **Executar shell**.

1. Edite o script a seguir, substituindo **{seu nome de conta github}**, **{seu nome de projeto}**e **{seu diretório de projeto}** com valores apropriados e cole o script editado na área de texto que aparece.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e {your project directory} ]; then

            cd {your project directory}

            git pull origin master

        else

            git clone https://github.com/{your github account name}/{your project name}.git

        fi

        # change directory to project

        cd $currentDir/{your project directory}

        #Execute build task

        ant

1. Clique em **Salvar**.

1. No painel Hudson, encontre o trabalho que você acabou de criar e clique no ícone **Agendar uma compilação** .

Hudson será, em seguida, criar um nó auxiliar usando o modelo criado na seção anterior e execute o script que você especificou na etapa construir para essa tarefa.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: https://azure.microsoft.com/develop/java/
[perfil de assinatura]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

