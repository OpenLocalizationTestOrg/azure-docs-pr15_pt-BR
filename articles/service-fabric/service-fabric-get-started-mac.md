<properties
   pageTitle="Configurar o ambiente de desenvolvimento no Mac OS X | Microsoft Azure"
   description="Instale o tempo de execução, SDK e ferramentas e criar um cluster de desenvolvimento local. Depois de concluir essa configuração, você estará pronto para criar aplicativos no Mac OS X."
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
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar o ambiente de desenvolvimento no Mac OS X

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

Você pode criar aplicativos de serviço tecidos para executar em clusters Linux usando o Mac OS X. Este artigo aborda como configurar seu Mac para desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Estrutura de serviço não é executado nativamente nos X. Para executar um cluster de serviço tecidos local, fornecemos uma máquina de virtual pré-configurado Ubuntu usando Vagrant e VirtualBox. Antes de começar, você precisa:

- [Vagrant (v1.8.4 ou posterior)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>Criar a máquina virtual local

Para criar a local máquina virtual contendo um cluster de estrutura de serviço de 5 nós, faça o seguinte:

1. Clonar a repo Vagrantfile

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Navegue até o local clonar do repo

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Opcional) Modificar as configurações de máquina virtual padrão

    Por padrão, a máquina virtual local é configurada da seguinte maneira:

    - 3 GB de memória alocada
    - Rede de host privada configurada no IP 192.168.50.50 habilitando passagem de tráfego de host de Mac

    Você pode alterar uma dessas configurações ou adicione outra configuração para a máquina virtual na Vagrantfile. Consulte a [documentação de Vagrant](http://www.vagrantup.com/docs) para a lista completa das opções de configuração.

4. Criar a máquina virtual

    ```bash
    vagrant up
    ```

    Esta etapa baixa a imagem de máquina virtual pré-configurado, inicialização-lo localmente e em seguida, configure uma estrutura de serviço local cluster nele. Você deve esperar isso poderá levar alguns minutos. Se a instalação for concluída com êxito, você verá uma mensagem na saída indicando que o cluster está iniciando.

    ![Cluster instalação iniciando acompanhando provisionamento de máquina virtual][cluster-setup-script]

5. Teste o cluster tiver sido configurado corretamente, navegando até o serviço tecidos Explorer em http://192.168.50.50:19080/Explorer (presumindo que você mantido o IP de rede privada padrão).

    ![Serviço tecidos Explorer visualizadas do host Mac][sfx-mac]


## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Instalar o plug-in tecidos de serviço para Eclipse Neon (opcional)

Serviço tecidos fornece um plug-in para o Eclipse Neon IDE que pode simplificar o processo de criação e implantação serviços Java.

1. No Eclipse, certifique-se de que você tenha Buildship versão 1.0.17 ou posterior instalado. Você pode verificar as versões dos componentes instalados, escolhendo **Ajuda > detalhes da instalação**. Você pode atualizar Buildship usando as instruções [aqui][buildship-update].

2. Para instalar o plug-in tecidos de serviço, escolha **Ajuda > instalar novo Software …**

3. Na caixa de texto "Trabalhar com", digite: http://dl.windowsazure.com/eclipse/servicefabric.

4. Clique em Adicionar.

    ![Plug-in Eclipse Neon tecidos de serviço][sf-eclipse-plugin-install]

5. Escolha o plug-in tecidos de serviço e clique em Avançar.

6. Continue com a instalação e aceite o contrato de licença de usuário final.

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro aplicativo de serviço tecidos para Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Criar um cluster de estrutura de serviço no portal do Azure](service-fabric-cluster-creation-via-portal.md)
- [Criar um cluster de estrutura de serviço usando o Gerenciador de recursos do Azure](service-fabric-cluster-creation-via-arm.md)
- [Entender o modelo de aplicativo de serviço tecidos](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
