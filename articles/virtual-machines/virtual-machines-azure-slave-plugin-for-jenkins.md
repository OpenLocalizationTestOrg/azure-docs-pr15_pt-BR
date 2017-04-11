<properties
    pageTitle="Como usar o Azure auxiliar plug-in com integração contínua Jenkins | Microsoft Azure"
    description="Descreve como usar o Azure auxiliar plug-in com integração contínua Jenkins."
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

# <a name="how-to-use-the-azure-slave-plug-in-with-jenkins-continuous-integration"></a>Como usar o Azure auxiliar plug-in com integração contínua Jenkins

Você pode usar o Azure auxiliar plug-in para Jenkins para provisionar auxiliar nós no Azure quando executando distribuído construções.

## <a name="install-the-azure-slave-plug-in"></a>Instalar o plug-in de auxiliar Azure

1. No painel Jenkins, clique em **Gerenciar Jenkins**.

1. Na página **Gerenciar Jenkins** , clique em **Gerenciar plug-ins**.

1. Clique na guia **disponível** .

1. No campo de filtro acima da lista de plug-ins disponíveis, digite **Azure** para limitar a lista para plug-ins relevantes.

    Se você optar por percorra a lista de plug-ins disponíveis, você encontrará o Azure auxiliar plug-in na seção **gerenciamento e Cluster distribuído construir** .

1. Marque a caixa de seleção de **Plug-in do Azure auxiliar** .

1. Clique em **instalar sem reiniciar** ou **Baixar agora e instalar após a reinicialização**.

Agora que o plug-in estiver instalado, as próximas etapas são para configurar o plug-in com seu perfil de assinatura Azure e criar um modelo que será usado na criação de máquina virtual para o nó auxiliar.


## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurar o Azure auxiliar plug-in com o seu perfil de assinatura

Um perfil de assinatura, também conhecido como publicar configurações, é um arquivo XML que contém proteger credenciais e algumas informações adicionais, que você precisará trabalhar com o Azure em seu ambiente de desenvolvimento. Para configurar o Azure auxiliar plug-in, você precisa:

* Sua id de assinatura
* Um certificado de gerenciamento de sua assinatura

Esses podem ser encontrados no seu [perfil de assinatura]. Abaixo está um exemplo de um perfil de assinatura.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID value>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Depois que seu perfil de assinatura, siga estas etapas para configurar o Azure auxiliar plug-in:

1. No painel Jenkins, clique em **Gerenciar Jenkins**.

1. Clique em **Configurar sistema**.

1. Role para baixo a página para encontrar a seção de **nuvem** .

1. Clique em **Adicionar novo nuvem > Microsoft Azure**.

    ![seção de nuvem][cloud section]

    Isso mostrará os campos onde você precisa digitar detalhes da sua assinatura.

    ![configuração de assinatura][subscription configuration]

1. Copie os valores de certificado de gerenciamento e id da assinatura do seu perfil de assinatura e colá-los nos campos apropriados.

    Ao copiar o certificado de gerenciamento e id da assinatura, não inclua as aspas que delimitam os valores.

1. Clique em **Verificar a configuração**.

1. Quando a configuração é verificada estar correta, clique em **Salvar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurar um modelo de máquina virtual para o Azure auxiliar plug-in

Um modelo de máquina virtual define os parâmetros que o plug-in será usado para criar um nó auxiliar no Azure. Nas etapas a seguir, vamos criar um modelo para uma máquina virtual de Ubuntu.

1. No painel Jenkins, clique em **Gerenciar Jenkins**.

1. Clique em **Configurar sistema**.

1. Role para baixo a página para encontrar a seção de **nuvem** .

1. Na seção de **nuvem** , localize o **Modelo de máquina Virtual do Azure adicionar**e clique em **Adicionar**.

    ![Adicionar modelo de máquina virtual][add vm template]

    Isso mostrará os campos onde você inserir detalhes sobre o modelo que você está criando.

    ![configuração geral em branco][blank general configuration]

1. Na caixa **nome** , digite um nome de serviço de nuvem Azure. Se o nome inserido se refere a um serviço de nuvem existente, a máquina virtual será configurada nesse serviço. Caso contrário, o Azure criará um novo.

1. Na caixa **Descrição** , digite o texto que descreve o modelo que você está criando. Este é somente para os seus registros e não é usada em uma máquina virtual de provisionamento.

1. A caixa **rótulos** é usada para identificar o modelo que você está criando e subsequentemente é usada para consultar o modelo ao criar um trabalho Jenkins. Para nosso objetivo, insira **linux** nessa caixa.

1. Na lista de **região** , clique na região onde a máquina virtual será criada.

1. Na lista **Tamanho da máquina Virtual** , clique no tamanho apropriado.

1. Na caixa **Nome da conta de armazenamento** , especifique uma conta de armazenamento onde a máquina virtual será criada. Verifique se ele está na mesma região como o serviço de nuvem que você usará. Se desejar que o novo armazenamento a ser criado, você pode deixar essa caixa em branco.

1. Tempo de retenção Especifica o número de minutos antes Jenkins exclui um auxiliar ocioso. Deixe o valor padrão de 60. Você também pode optar por desligar o auxiliar em vez de excluí-lo quando está ocioso. Para fazer isso, marque a caixa de seleção **Somente desligamento (não exclua) após o tempo de retenção** .

1. Na lista de **uso** , clique na condição apropriada quando este nó auxiliar será usado. Por agora, clique em **usa esse nó tanto quanto possível**.

    Neste ponto, seu formulário deve ser um pouco semelhante a esta:

    ![configuração de modelo gerais de ponto de verificação][checkpoint general template config]

    A próxima etapa é fornecer detalhes sobre a imagem de sistema operacional que deseja que seu auxiliar ser criados em.

1. Na caixa de ferramentas **familiares de imagem ou Id** , você precisa especificar qual imagem do sistema será instalada na máquina virtual. Você pode selecionar de uma lista de famílias de imagem ou especifique uma imagem personalizada.

    Se você quiser selecionar em uma lista das famílias de imagem, insira o primeiro caractere (diferencia maiusculas de minúsculas) do nome da família da imagem. Por exemplo, digitar **U** exibirá uma lista das famílias de servidor Ubuntu. Depois de selecionar na lista, Jenkins usará a versão mais recente dessa imagem de sistema do que família ao provisionar sua máquina virtual.

    ![Exemplo de lista de imagem de sistema operacional][OS Image list sample]

    Se você tiver uma imagem personalizada que você deseja usar em vez disso, insira o nome da imagem personalizada. Nomes de imagem personalizada não são mostrados em uma lista, para que você tenha que garantir que o nome foi digitado corretamente.

    Para este tutorial, digite **U** para exibir uma lista de imagens de Ubuntu e clique em **Ubuntu servidor 14.04 LTS**.

1. Na lista **Método de início** , clique em **SSH**.

1. Copie o script abaixo e cole-a na caixa **Script de inicialização** .

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

    O script de inicialização será executado após a máquina virtual é criada. Neste exemplo, o script instala Java, gito e ant.

1. Nas caixas **nome de usuário** e **senha** , insira valores de sua preferência para a conta de administrador que será criada em sua máquina virtual.

1. Clique em **Verificar o modelo** para verificar se os parâmetros especificados são válidos.

1. Clique em **Salvar**.


## <a name="create-a-jenkins-job-that-runs-on-a-slave-node-on-azure"></a>Criar um trabalho Jenkins que é executado em um nó de auxiliar no Azure

Nesta seção, você criará uma tarefa Jenkins que será executado em um nó de auxiliar no Azure. Você precisará ter seu próprio projeto até no GitHub para acompanhar.

1. No painel Jenkins, clique em **Novo Item**.

1. Insira um nome para a tarefa que você está criando.

1. Para o tipo de projeto, clique em **projeto Freestyle**.

1. Clique em **Okey**.

1. Na página de configuração de tarefa, selecione **restringir onde esse projeto pode ser executado**.

1. Na caixa de **Expressão de rótulo** , digite **linux**. Na seção anterior, criamos um modelo de auxiliar que nomeamos **linux**, que é o que estamos especificando aqui.

1. Na seção **criar** , clique em **Adicionar etapa de construção** e selecione **Executar shell**.

1. Edite o script a seguir, substituindo **(seu nome de conta GitHub)**, **(seu nome de projeto)**e **(seu diretório de projeto)** com valores apropriados e cole o script editado na área de texto que aparece.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e (your project directory) ]; then

            cd (your project directory)

            git pull origin master

        else

            git clone https://github.com/(your GitHub account name)/(your project name).git

        fi

        # change directory to project

        cd $currentDir/(your project directory)

        #Execute build task

        ant

1. Clique em **Salvar**.

1. No painel Jenkins, passe o mouse sobre a tarefa que você acabou de criar e clique na seta suspensa para exibir as opções de tarefa.

1. Clique em **criar agora**.

Jenkins será, em seguida, criar um nó auxiliar usando o modelo criado na seção anterior e execute o script que você especificou na etapa construir para essa tarefa.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Azure com Java, consulte o [Azure Java Developer Center].

<!-- URL List -->

[Central de desenvolvedores do Azure Java]: https://azure.microsoft.com/develop/java/
[perfil de assinatura]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png