<properties
    pageTitle="Instalar a Interface de linha Azure | Microsoft Azure"
    description="Instalar a Interface de linha de Azure (CLI) para Mac, Linux e Windows começar a usar os serviços do Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"/>
    
# <a name="install-the-azure-cli"></a>Instalar o Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [CLI Azure](xplat-cli-install.md)

Instale rapidamente a Interface de linha do Azure (Azure comando) para usar um conjunto de código-fonte aberto baseado em shell comandos para criar e gerenciar recursos do Microsoft Azure. Você tem várias opções para instalar essas ferramentas entre plataformas em seu computador: 

* **pacote de npm** - executar npm (o Gerenciador de pacote para JavaScript) para instalar o pacote de CLI do Azure mais recente no sistema operacional ou distribuição Linux. Requer node e npm em seu computador.
* **Installer** - Download um instalador para facilitar a instalação no Mac ou Windows.
* **Contêiner de docker** - começar a usar o CLI mais recente em um contêiner de Docker pronto para executar. Requer o host Docker em seu computador.
    
Para obter mais opções e plano de fundo, consulte o repositório de projeto no [GitHub](https://github.com/azure/azure-xplat-cli). 

Depois que a CLI Azure é instalada, [conectá-la com sua assinatura do Azure](xplat-cli-connect.md) e executar os comandos do **azure** de sua interface de linha de comando (Bash, Terminal, prompt de comando e assim por diante) para trabalhar com seus recursos Azure.



## <a name="option-1-install-an-npm-package"></a>Opção 1: Instalar um pacote de npm

Para instalar a CLI de um pacote de npm, verifique se você tiver baixou e instalou o [node e npm mais recentes](https://nodejs.org/en/download/package-manager/). Em seguida, execute o **npm instalar** para instalar o pacote do azure-cli: 

    npm install -g azure-cli

Em distribuições Linux, você talvez precise usar **sudo** para executar com êxito o comando __npm__ , da seguinte maneira:

    sudo npm install -g azure-cli

> [AZURE.NOTE]Se você precisar instalar ou atualizar node e npm no sistema operacional ou distribuição Linux, recomendamos que você instale a versão mais recente do LTS Node (4. x). Se você usar uma versão mais antiga, você pode receber erros de instalação. 

Se preferir, baixe o mais recente do Linux [arquivo tar] [ linux-installer] para o pacote de npm localmente. Em seguida, instale o pacote de npm baixado da seguinte forma (em distribuições Linux que você talvez precise usar **sudo**):

    npm install -g <path to downloaded tar file>

## <a name="option-2-use-an-installer"></a>Opção 2: Use um instalador

Se você usar um computador Mac ou Windows, os seguintes instaladores CLI estão disponíveis para download:

* [Instalador do Mac OS X][mac-installer]

* [Windows MSI][windows-installer] 

>[AZURE.TIP]No Windows, você também pode baixar o [Web Platform Installer](https://go.microsoft.com/?linkid=9828653) para instalar a CLI. Este instalador fornece a opção para instalar o SDK do Azure adicionais e ferramentas de linha de comando após a instalação do CLI. 


## <a name="option-3-use-a-docker-container"></a>Opção 3: Usar um contêiner de Docker

Se você tiver configurado seu computador como um host de [Docker](https://docs.docker.com/engine/understanding-docker/) , você pode executar a CLI Azure mais recentes em um contêiner de Docker. Execute o seguinte comando (em distribuições Linux que você talvez precise usar **sudo**):

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>Executar comandos do Azure
Após a CLI Azure estiver instalada, execute o comando **azure** da sua interface de usuário de linha de comando (Bash, Terminal, prompt de comando e assim por diante). Por exemplo, para executar o comando de Ajuda, digite o seguinte:

```
azure help
```
> [AZURE.NOTE]Em algumas distribuições Linux, você pode receber um erro semelhante ao `/usr/bin/env: ‘node’: No such file or directory`. Esse erro é proveniente de instalações recentes do Node sendo instalado em /usr/bin/nodejs. Para corrigi-lo, crie um link simbólico para /usr/bin/node executando este comando:

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para ver a versão da CLI Azure você instalou, digite o seguinte:

```
azure --version
```

Agora você está pronto! Para acessar todos os comandos CLI para trabalhar com seus próprios recursos, [conectar-se a sua assinatura do Azure da CLI Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Quando você usa o Azure CLI primeiro, verá uma mensagem perguntando se você deseja permitir que a Microsoft colete informações de uso. Participação é voluntária. Se você optar por participar, você pode interromper a qualquer momento executando `azure telemetry --disable`. Para ativar a participação a qualquer momento, execute `azure telemetry --enable`.


## <a name="update-the-cli"></a>Atualizar a CLI

Frequentemente, a Microsoft libera versões atualizadas da CLI Azure. Reinstale o CLI usando o instalador para seu sistema operacional ou executar o contêiner Docker mais recente. Ou, se você tiver as últimas node e npm instalado, atualize digitando o seguinte (em distribuições Linux que você talvez precise usar **sudo**).

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Ativar a conclusão de tabulação

Conclusão do guia de comandos tem suporte para Mac e Linux.

Para habilitá-la em zsh, execute:

```
echo '. <(azure --completion)' >> .zshrc
```

Para habilitá-la em bash, execute:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Próximas etapas 

* [Conectar-se da CLI à sua assinatura do Azure](xplat-cli-connect.md) para criar e gerenciar recursos do Azure.

* Para saber mais sobre a CLI do Azure, baixe o código-fonte, relatar problemas ou contribuir para o projeto, visite [GitHub repositório para a CLI do Azure](https://github.com/azure/azure-xplat-cli).

* Se você tiver dúvidas sobre como usar o Azure CLI ou Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

* Se desejar, você também pode tentar baseado em Python [Azure CLI 2.0 Preview](https://github.com/azure/azure-cli).

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
