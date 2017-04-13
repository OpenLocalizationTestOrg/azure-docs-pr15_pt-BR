<properties
   pageTitle="Implantar um nó 3 Deis cluster | Microsoft Azure"
   description="Este artigo descreve como criar um nó 3 Deis cluster no Azure usando um modelo do Gerenciador de recursos do Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="HaishiBai"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# <a name="deploy-a-3-node-deis-cluster"></a>Implantar um nó 3 Deis cluster

Este artigo conduz você por meio de provisionamento um [Deis](http://deis.io/) cluster no Azure. Ele abrange todas as etapas de criar os certificados necessários para implantar e a escala de um aplicativo de amostra, **vá** no cluster recentemente provisionado.

O diagrama a seguir mostra a arquitetura do sistema implantado. Um administrador do sistema gerencia o cluster usando Deis ferramentas tais como **deis** e **deisctl**. Conexões são estabelecidas por meio de um balanceador de carga Azure, que encaminha as conexões para uma do membro nós no cluster. O acesso de clientes implantado aplicativos por meio de Balanceador de carga. Nesse caso, o balanceador de carga encaminha o tráfego para um Deis malha de roteador, o que routs ainda mais o tráfego para o correspondentes contêineres de Docker hospedados no cluster.

  ![Diagrama de arquitetura de cluster Desis implantado](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

Para executar as seguintes etapas, você precisará:

 * Uma assinatura ativa do Azure. Se você não tiver um, você pode obter uma trilha livre em [azure.com](https://azure.microsoft.com/).
 * Uma identificação de trabalho ou da escola para usar grupos de recursos Azure. Se você tiver uma conta pessoal e faça logon com uma id da Microsoft, você precisa [criar uma id de trabalho do seu pessoal](virtual-machines-windows-create-aad-work-id.md).
 * Seja - dependendo do sistema operacional cliente – o [Azure PowerShell](../powershell-install-configure.md) ou da [CLI do Azure para Mac, Linux e Windows](../xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL é usada para gerar os certificados necessários.
 * Um cliente gito como [Gito Bash](https://git-scm.com/).
 * Para testar o aplicativo de exemplo, também será necessário um servidor DNS. Você pode usar qualquer servidores DNS ou serviços que oferecem suporte a registros curinga.
 * Um computador para executar Deis ferramentas de cliente. Você pode usar um computador local ou uma máquina virtual. Você pode executar essas ferramentas em praticamente qualquer distribuição Linux, mas as instruções a seguir usam Ubuntu.

## <a name="provision-the-cluster"></a>Provisionar o cluster

Nesta seção, você vai usar um modelo de [Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md) o repositório de abrir origem [modelos de início rápido do azure](https://github.com/Azure/azure-quickstart-templates). Primeiro, você copiará para baixo o modelo. Em seguida, você vai criar um novo par de chaves SSH para autenticação. E, em seguida, você vai configurar um novo identificador para você cluster. E finalmente, você usará o script Shell ou o script do PowerShell para provisionar o cluster.

1. Clonar repositório: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Vá para a pasta de modelo:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Crie um novo par de chaves de SSH usando ssh keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Gere um certificado usando a chave privada acima:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Vá para [https://discovery.etcd.io/new](https://discovery.etcd.io/new) para gerar um novo token de cluster, com aparência algo como:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />
Cada cluster CoreOS precisa ter um token exclusivo desse serviço gratuito. Consulte a [documentação de CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) para obter mais detalhes.

6. Modificar o arquivo de **config.yaml de nuvem** para substituir o token de **Descoberta** existente com o novo token:

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Modificar o arquivo **azuredeploy-parameters.json** : Abra o certificado que você criou na etapa 4 em um editor de texto. Copiar todo o texto entre `----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----` no parâmetro **sshKeyData** (você precisará remover todos os caracteres de nova linha).

8. Modificar o parâmetro **newStorageAccountName** . Esta é a conta de armazenamento para discos de sistema operacional da máquina virtual. Este nome de conta deve ser exclusivo.

9. Modificar o parâmetro **publicDomainName** . Isso tornará parte do nome do DNS associado o IP público de Balanceador de carga. O FQDN final terá o formato de _[valor desse parâmetro]_. _[Região]_. cloudapp.azure.com. Por exemplo, se você especifica o nome como deishbai32 e o grupo de recursos é implantado para a região Oeste EUA, o FQDN final ao seu balanceador de carga será deishbai32.westus.cloudapp.azure.com.

10. Salve o arquivo de parâmetro. E, em seguida, você pode provisionar o cluster usando Azure PowerShell:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  ou CLI Azure:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Depois de grupo de recursos é configurado, você pode ver todos os recursos no grupo no Azure portal clássico. Como mostrado na seguinte captura de tela, o grupo de recursos contém uma rede virtual com três VMs, que fazem parte do mesmo conjunto de disponibilidade. O grupo também contém um balanceador de carga, que tem um IP público associado.

  ![O grupo de recursos provisionados no portal clássico Azure](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>Instalar o cliente

Você precisa **deisctl** para controlar sua Deis cluster. Embora deisctl é instalado automaticamente em todos os nós de cluster, é recomendável usar deisctl em uma máquina administrativa separada. Além disso, como todos os nós são configurados com apenas os endereços IP particulares, você precisará usar SSH túnel por meio de Balanceador de carga, que tem um IP público, para conectar-se às máquinas nó. A seguir estão as etapas de configuração de deisctl em um separada Ubuntu físico ou máquina virtual.

1. Instalação deisctl:mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Adicione sua chave particular ssh agente:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Configure deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

O modelo define as regras de entrada NAT que mapeiam 2223 à instância 1, 2224 à instância 2 e 2225 a instância 3. Isso fornece redundância para usar a ferramenta de deisctl. Você pode examinar essas regras no Azure portal clássico:

![Regras NAT no balanceador de carga](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [AZURE.NOTE] Atualmente o modelo suporta apenas 3 nós clusters. Isso é devido a uma limitação no Gerenciador de recursos do Azure modelo definição de regra NAT, que não dá suporte a sintaxe de loop.

## <a name="install-and-start-the-deis-platform"></a>Instale e inicie o Deis plataforma

Agora você pode usar deisctl para instalar e iniciar o Deis plataforma:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] Iniciando a plataforma leva algum tempo (máximo de 10 minutos). Especialmente, iniciando o serviço de construtor pode levar muito tempo. E algumas vezes demora alguns tenta bem-sucedida: se a operação parece travar, tente digitar `ctrl+c` por interromper a execução do comando e tente novamente.

Você pode usar `deisctl list` para verificar se todos os serviços estão em execução:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Parabéns! Agora você tem uma execução Deis clsuter no Azure! Em seguida, vamos implantar uma amostra de aplicativo de ir para ver o cluster em ação.

## <a name="deploy-and-scale-a-hello-world-application"></a>Implantar e dimensionar um aplicativo Hello World

As etapas a seguir mostram como distribuir um "Olá, mundo" Vá aplicativo ao cluster. As etapas são baseadas em [Deis documentação](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Para a malha roteamento funcione corretamente, você precisará ter um registro de curinga r para seu domínio apontando para o IP público de Balanceador de carga. A captura de tela a seguir mostra o registro para um registro de domínio de amostra no GoDaddy:

    ![Registro de GoDaddy A](media/virtual-machines-linux-deis-cluster/go-daddy.png)
<p />
2. Instalar deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Criar uma nova chave SSH e adicione a chave pública GitHub (claro, também é possível reutilizar suas chaves existentes). Para criar um novo par de chaves de SSH, use:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Adicione id_rsa.pub ou a chave pública de sua escolha, a GitHub. Você pode fazer isso usando o adicionar SSH chave botão em sua tela de configuração de chaves SSH:

  ![Chave GitHub](media/virtual-machines-linux-deis-cluster/github-key.png)
<p />
5. Registre um novo usuário:

        deis register http://deis.[your domain]
<p />
6. Adicione a chave SSH:

        deis keys:add [path to your SSH public key]
  <p />      
7. Crie um aplicativo.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8.O envio de gito acionará imagens de Docker para ser criado e implantado, que vai demorar alguns minutos. Na minha experiência, ocasionalmente, pode travar etapa 10 (Pushing imagem ao repositório particular). Quando isso acontece, você pode interromper o processo, remova o aplicativo usando ' deis aplicativos: destroy – um <application name> ` to remove the application and try again. You can use `deis apps:list' para descobrir o nome do seu aplicativo. Se tudo funciona check-out, você verá algo semelhante ao seguinte no final do comando saídas:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Verifique se o aplicativo está funcionando:

        curl -S http://[your application name].[your domain]
  Você deverá ver:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Dimensione o aplicativo em 3 instâncias:

        deis scale cmd=3
<p />
11. Opcionalmente, você pode usar deis informações para examinar detalhes de seu aplicativo. As seguintes saídas estão no meu implantação do aplicativo:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>Próximas etapas

Este artigo percorrido todas as etapas para provisionar um novo Deis cluster no Azure usando um modelo do Gerenciador de recursos do Azure. O modelo suporta redundância em ferramentas conexões, bem como balanceamento de carga para aplicativos implantados. O modelo também evita usando IPs público em nós de membro, que salva preciosos recursos IP públicos e fornece um ambiente mais seguro para aplicativos de host. Para saber mais, consulte os seguintes artigos:

[Visão geral do Gerenciador de recursos do Azure] [resource-group-overview]  
[Como usar a CLI do Azure] [azure-command-line-tools]  
[Usando o PowerShell Azure com o Azure Resource Manager] [powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md
