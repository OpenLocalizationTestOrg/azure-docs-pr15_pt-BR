<properties
   pageTitle="Configurar o ambiente de desenvolvimento no Linux | Microsoft Azure"
   description="Instale o SDK e o tempo de execução e criar um cluster de desenvolvimento local no Linux. Depois de concluir essa configuração, você estará pronto para criar aplicativos."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# <a name="prepare-your-development-environment-on-linux"></a>Preparar o ambiente de desenvolvimento no Linux


> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Para implantar e executar [aplicativos do Azure serviço tecidos](service-fabric-application-model.md) em sua máquina de desenvolvimento do Linux, instale o tempo de execução e SDK comuns. Você também pode instalar SDKs opcionais para Java e .NET Core.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistema operacional compatível
As seguintes versões de sistema operacional têm suporte para desenvolvimento:

- Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>Atualizar suas fontes chance

Para instalar o SDK e o pacote de runtime associados via chance-get, você deve primeiro atualizar suas fontes chance.

1. Abra um terminal.
2. Adicione o repo de serviço tecidos à sua lista de fontes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Adicione a nova chave GPG a sua chaveiro chance.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Atualize suas listas de pacote com base em repositórios recém-adicionado.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Instalar e configurar o SDK

Depois que suas fontes são atualizados, você pode instalar o SDK.

1. Instale o pacote de serviço tecidos SDK. Você será solicitado a confirmar a instalação e aceitar um contrato de licença.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Execute o script de instalação do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>Configurar o Azure CLI entre plataformas

O [Azure CLI entre plataformas] [ azure-xplat-cli-github] inclui comandos para interagir com entidades de serviço tecidos, incluindo clusters e aplicativos. Ele se baseia Node então [Certifique-se de que você instalou o nó] [ install-node] antes de prosseguir com as instruções abaixo.

1. Clone o repo github à sua máquina de desenvolvimento.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Alternar para o repo clonado e instale dependências da ILC usando o Gerenciador de pacote de nó (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Crie um symlink da pasta bin/azure do repo clonado para /usr/bin/azure para que ele é adicionado ao caminho e comandos estão disponíveis em qualquer pasta.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Por fim, habilite comandos de tecidos de serviço de preenchimento automático.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Configurar um cluster local

Se tudo tiver instalado com êxito, você deve ser capaz de iniciar um cluster local.

1. Execute o script de configuração de cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Abra um navegador da web e navegue até http://localhost:19080/Explorer. Se o cluster iniciou, você deve ver o painel de serviço tecidos Explorer.

    ![Serviço tecidos Explorer no Linux][sfx-linux]

Neste ponto, você é capazes de implantar pacotes de aplicativos de serviço tecidos pré-criados ou novos com base em contêineres de convidado ou executáveis de convidado. Para criar novos serviços usando o Java ou .NET Core SDKs, siga as etapas de instalação opcional abaixo.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Instalar o plugin Java SDK e Eclipse Neon (opcional)

O SDK Java fornece as bibliotecas e modelos necessários para criar serviços de serviço tecidos usando Java.

1. Instale o pacote Java SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Execute o script de instalação do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Você pode instalar o plug-in Eclipse tecidos de serviço de dentro do IDE de Neon Eclipse.

1. No Eclipse, certifique-se de que você tenha Buildship versão 1.0.17 ou posterior instalado. Você pode verificar as versões dos componentes instalados, escolhendo **Ajuda > detalhes da instalação**. Você pode atualizar Buildship usando as instruções [aqui][buildship-update].

2. Para instalar o plug-in tecidos de serviço, escolha **Ajuda > instalar novo Software …**

3. Na caixa de texto "Trabalhar com", digite: http://dl.windowsazure.com/eclipse/servicefabric

4. Clique em Adicionar.

    ![Plug-in Eclipse][sf-eclipse-plugin]

5. Escolha o plug-in tecidos de serviço e clique em Avançar.

6. Continue com a instalação e aceite o contrato de licença de usuário final.

## <a name="install-the-net-core-sdk-optional"></a>Instalar o SDK do .NET Core (opcional)

O .NET Core SDK fornece as bibliotecas e modelos necessários para criar serviços de serviço tecidos usando núcleo de .NET entre plataformas.

1. Instale o pacote de .NET Core SDK.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Execute o script de instalação do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro aplicativo Java no Linux](service-fabric-create-your-first-linux-application-with-java.md)

- [Preparar o ambiente de desenvolvimento em OSX](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
