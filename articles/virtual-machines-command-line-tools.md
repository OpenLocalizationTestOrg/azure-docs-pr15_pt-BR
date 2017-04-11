<properties
    pageTitle="Azure comandos no modo de gerenciamento de serviço | Microsoft Azure"
    description="Comandos do Azure comando interface de linha () no modo de gerenciamento de serviço para gerenciar implantações no modelo clássico de implantação"
    services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="danlep"/>

# <a name="azure-cli-commands-in-azure-service-management-asm-mode"></a>Azure comandos no modo de gerenciamento de serviço do Azure (asm)

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)]Você também pode [ler sobre todos os comandos de modelo do Gerenciador de recursos](virtual-machines/azure-cli-arm-commands.md)e usar a CLI para [migrar recursos](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md) do clássico para o modelo do Gerenciador de recursos.

Este artigo fornece opções para Azure comandos que normalmente você usaria para criar e gerenciar recursos Azure no modelo clássico de implantação e sintaxe. Você pode acessar esses comandos executando a CLI no modo de gerenciamento de serviço do Azure (asm). Isso não é uma referência completa e sua versão CLI pode mostrar parâmetros ou comandos ligeiramente diferentes. 

Para começar a, primeiro [instalar o CLI do Azure](xplat-cli-install.md) e [se conectar à sua assinatura do Azure](xplat-cli-connect.md).

Sintaxe de comando atual e as opções na linha de comando, digite `azure help` ou, para exibir a Ajuda para um comando específico, `azure help [command]`. Também encontre exemplos CLI na documentação para criar e gerenciar serviços Azure específicos.

Parâmetros opcionais são mostrados entre colchetes (por exemplo, `[parameter]`). Todos os outros parâmetros são necessários.

Além de parâmetros opcionais do comando específico documentados aqui, há três parâmetros opcionais que podem ser usados para exibir a saída detalhada como opções de solicitação e códigos de status. O `-v` parâmetro fornece a saída detalhada e o `-vv` parâmetro fornece ainda mais detalhadas saída detalhada. O `--json` opção apresenta o resultado no formato json bruto.

## <a name="setting-asm-mode"></a>Definindo o modo de asm

Use o seguinte comando para ativar os comandos de modo de gerenciamento de serviço de CLI do Azure.

    azure config mode asm

>[AZURE.NOTE] Modo de Gerenciador de recursos do Azure e gerenciamento de serviço do Azure a CLI são mutuamente exclusivos. Isto é, criados em um modo de recursos não podem ser gerenciados do outro modo.

## <a name="manage-your-account-information-and-publish-settings"></a>Gerenciar informações da sua conta e configurações de publicação
Uma maneira que CLI pode se conectar à sua conta é usando as informações de assinatura Azure. (Consulte [conectar-se a uma assinatura do Azure da CLI do Azure](xplat-cli-connect.md) para obter outras opções.) Essas informações podem ser obtidas a partir do portal clássico Azure em um arquivo de configurações de publicar conforme descrito aqui. Você pode importar o arquivo de configurações de publicar como persistente local definição de configuração que a CLI usa para operações subsequentes. Você só precisa importar suas configurações de publicação uma vez.

**download de conta [Opções]**

Este comando inicia um navegador para baixar o arquivo. publishsettings do Azure portal clássico.

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**conta importação [Opções] &lt;arquivo >**


Este comando importa um arquivo de publishsettings ou certificado para que ele pode ser usado pela ferramenta no futuro.

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

> [AZURE.NOTE] O arquivo publishsettings pode conter detalhes (ou seja, o nome da assinatura e ID) sobre mais de uma assinatura. Quando você importa o arquivo publishsettings, a primeira assinatura é usada como descrição padrão. Para usar uma assinatura diferente, execute o seguinte comando:
<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**conta desmarque [Opções]**

Este comando remove o publishsettings armazenados que foram importados. Use este comando se você tiver concluído usando a ferramenta neste computador e deseja garantir que a ferramenta não pode ser usada com sua conta no futuro.

    ~$ azure account clear
    Clearing account info.
    info:   OK

**lista de contas [Opções]**

Lista as assinaturas importadas

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
    -----  -------
    data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**conta definida [Opções] &lt;assinatura&gt;**

Definir a assinatura atual

###<a name="commands-to-manage-your-affinity-groups"></a>Comandos para gerenciar seus grupos de afinidade

**lista de grupos de afinidade de conta [Opções]**

Esse comando lista seus grupos de afinidade Azure.

Grupos de afinidade pode ser definidos quando um grupo de máquinas virtuais abrange várias máquinas físicas. O grupo de afinidade Especifica que o máquinas físicas devem ser mais próximo uns aos outros possível, para reduzir a latência de rede.

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**grupo de afinidade conta criar [Opções] &lt;nome&gt;**

Esse comando cria um grupo de afinidade

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**grupo de afinidade conta mostrar [Opções] &lt;nome&gt;**

Esse comando mostra os detalhes do grupo de afinidade

    ~$ azure account affinity-group show opentec
    info:    Executing command account affinity-group show
    + Getting affinity groups
    data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
    data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
    data:    Name "opentec"
    data:    Label "b3BlbnRlYw=="
    data:    Description $ i:nil "true"
    data:    Location "West US"
    data:    HostedServices ""
    data:    StorageServices ""
    data:    Capabilities Capability 0 "PersistentVMRole"
    data:    Capabilities Capability 1 "HighMemory"
    info:    account affinity-group show command OK

**conta do grupo de afinidade excluir [Opções] &lt;nome&gt;**

Este comando exclui o grupo de afinidade especificado

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

###<a name="commands-to-manage-your-account-environment"></a>Comandos para gerenciar seu ambiente de conta

**lista de contas de envelope [Opções]**

Lista os ambientes de conta

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**conta env Mostrar [Opções] [ambiente]**

Mostrar detalhes do ambiente de conta

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
    info:    account env show command OK

**conta env adicionar [Opções] [ambiente]**

Este comando adiciona um ambiente para a conta

**conta env definir [Opções] [ambiente]**

Este comando define o ambiente de conta

**conta env excluir [Opções] [ambiente]**

Este comando exclui o ambiente especificado da conta

## <a name="commands-to-manage-your-classic-virtual-machines"></a>Comandos para gerenciar suas máquinas virtuais clássicas
O diagrama a seguir mostra como clássico Azure máquinas virtuais são hospedadas no ambiente de implantação de produção de um serviço de nuvem Azure.

![Diagrama técnico Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**Criar novo** cria a unidade no armazenamento de blob (ou seja, e:\ no diagrama); **Anexar** anexa um disco já criado, mas desanexado a uma máquina virtual.

**máquina virtual criar [Opções] &lt;nome de dns > &lt;imagem > &lt;nome de usuário > [senha]**

Esse comando cria uma máquina virtual Azure. Por padrão, cada máquina virtual (vm) é criada em seu próprio serviço de nuvem. Você pode especificar que uma máquina virtual deve ser adicionada a um serviço de nuvem existente por meio do uso da opção - c como documentadas aqui.

A máquina virtual criar comando, como o portal de clássico do Azure, só cria máquinas virtuais no ambiente de implantação de produção. Não há nenhuma opção para criar uma máquina virtual no ambiente de implantação de preparação de um serviço de nuvem. Se sua assinatura não tiver uma conta existente do armazenamento do Azure, o comando cria uma.

Você pode especificar um local por meio de parâmetro-local, ou você pode especificar um grupo de afinidade por meio-parâmetro de grupo de afinidade. Se não for fornecido, você é solicitado a fornecer um de uma lista de locais válidos.

A senha fornecida deve ser 123 de 8 caracteres e atender aos requisitos de complexidade de senha do sistema operacional que você está usando para essa máquina virtual.

Se você planeja usar SSH para gerenciar um computador de virtual Linux implantado (conforme é geralmente o caso), você deve ativar SSH via a opção -e quando você cria a máquina virtual. Não é possível ativar SSH após a máquina virtual foi criada.

Máquinas virtuais do Windows pode ativar RDP posteriormente, adicionando porta 3389 como um ponto de extremidade.

Os seguintes parâmetros opcionais são suportados para este comando:

**- c, – conecte** criar a máquina virtual dentro de uma implantação já criada em um serviço de hospedagem. Se - vmname não é usada com essa opção, o nome da nova máquina virtual é gerado automaticamente.<br />
**-n, - máquina virtual-nome** Especifique o nome da máquina virtual. Esse parâmetro usa o nome do serviço de hospedagem por padrão. Se - vmname não for especificada, o nome para a nova máquina virtual será gerado como &lt;nome do serviço >&lt;id >, onde &lt;id > é o número de máquinas virtuais existentes no serviço mais 1. Por exemplo, se você usar este comando para adicionar uma máquina virtual a um serviço de hospedagem MyService que tem uma máquina virtual existente, a nova máquina virtual é denominada MyService2.<br />
**-u, --url de blob** Especifique a URL de armazenamento de blob de destino na qual deseja criar o disco de sistema de máquina virtual. <br />
**-z, tamanho-máquina virtual** Especifique o tamanho da máquina virtual. Os valores válidos são: "ExtraSmall", "Pequena", "Medium", "Grande", "ExtraLarge", "A5", "A6", "A7", "A8", "A9", "A10", "A11", "Basic_A0", "Basic_A1", "Basic_A2", "Basic_A3", "Basic_A4", "Standard_D1", "Standard_D2", "Standard_D3", "Standard_D4", "Standard_D11", "Standard_D12", "Standard_D13", "Standard_D14", "Standard_DS1", "Standard_DS2", "Standard_DS3", "Standard_DS4", "Standard_DS11", "Standard_DS12", "Standard_DS13", "Standard_DS14", "Standard_G1", "Standard_G2" , "Standard_G3", "Standard_G4", "Standard_G55". O valor padrão é "Pequeno". <br />
**-r** Adiciona conectividade RDP a uma máquina virtual Windows. <br />
**-e -e-ssh** Adiciona conectividade SSH para uma máquina virtual Windows. <br />
**-t, --ssh certificado** Especifica o certificado SSH. <br />
**-s** A assinatura <br />
**-o,-comunidade** A imagem especificada é uma imagem de comunidade <br />
**-w** O nome de rede virtual <br/>
**-l, - local** Especifica o local (por exemplo, "América do Norte Central EUA"). <br />
**-a, --grupo de afinidade** Especifica o grupo de afinidade.<br />
**-w, --nome de rede virtual** Especifique a rede virtual no qual você deseja adicionar a nova máquina virtual. Redes virtuais podem ser configuradas e gerenciadas do Azure portal clássico do.<br />
**-b, – nomes de sub-rede** Especifica os nomes de sub-rede para atribuir a máquina virtual.

Neste exemplo, MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB é uma imagem fornecida pela plataforma. Para obter mais informações sobre imagens do sistema operacional, consulte a lista de imagens de máquina virtual.

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************
    info:   vm create command OK

**Criar da máquina virtual &lt;nome de dns > &lt;arquivo de função >**

Esse comando cria uma máquina virtual Azure de um arquivo de função JSON.

    ~$ azure vm create-from my-vm example.json
    info:   OK

**lista de máquina virtual [Opções]**

Esse comando lista Azure máquinas virtuais. A opção – json Especifica que os resultados são retornados em formato JSON bruto.

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**lista de locais de máquina virtual [Opções]**

Esse comando lista todos os locais de conta do Microsoft Azure disponíveis.

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name
    data:   ---------------------  ------------
    data:   Azure Preview  West US
    info:   account location list command OK

**máquina virtual Mostrar [Opções] &lt;nome >**

Este comando mostra detalhes sobre uma máquina virtual Azure. A opção – json Especifica que os resultados são retornados em formato JSON bruto.

    ~$ azure vm show my-vm
    info:   Executing command vm show
    data:   {
    data:       InstanceSize: 'Small',
    data:       InstanceStatus: 'ReadyRole',
    data:       DataDisks: [],
    data:       IPAddress: '10.26.192.206',
    data:       DNSName: 'my-vm.cloudapp.net',
    data:       InstanceStateDetails: {},
    data:       VMName: 'my-vm',
    data:       Network: {
    data:           Endpoints: [
    data:               {
    data:                   Protocol: 'tcp',
    data:                   Vip: '65.52.250.250',
    data:                   Port: '63238' ,
    data:                   LocalPort: '3389',
    data:                   Name: 'RemoteDesktop'
    data:               }
    data:           ]
    data:       },
    data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
    data:   }
    info:   vm show command OK

**máquina virtual excluir [Opções] &lt;nome >**

Este comando exclui uma máquina virtual Azure. Por padrão, este comando não exclui o Azure blob do qual o disco de sistema operacional e os dados são criados. Para excluir o blob e máquina virtual no qual ele se baseia, especifique a opção -b.

    ~$ azure vm delete my-vm
    info:   Executing command vm delete
    info:   vm delete command OK

**máquina virtual iniciar [Opções] &lt;nome >**

Este comando inicia uma máquina virtual Azure.

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**máquina virtual reiniciar [Opções] &lt;nome >**

Este comando reinicia uma máquina virtual Azure.

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**desligamento de máquina virtual [Opções] &lt;nome >**

Este comando desliga uma máquina virtual Azure. Você pode usar a opção -p para especificar que o recurso de computação não são lançados no desligamento.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**captura de máquina virtual &lt;nome da máquina virtual > &lt;nome da imagem de destino >**

Este comando captura uma imagem do Azure máquina virtual.

Uma imagem de máquina virtual só pode ser capturada se o estado da máquina virtual é **interrompido**. Desligar a máquina virtual antes de continuar.

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**exportação de máquina virtual [Opções] &lt;nome da máquina virtual > &lt;caminho do arquivo >**

Este comando exporta uma imagem do Azure máquina virtual para um arquivo

    ~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

##  <a name="commands-to-manage-your-azure-virtual-machine-endpoints"></a>Comandos para gerenciar seus pontos de extremidade do Azure máquina virtual
O diagrama a seguir mostra a arquitetura de uma implantação típica de várias instâncias de uma máquina virtual clássica. Neste exemplo, a porta 3389 está aberta em cada máquina virtual (para acesso RDP). Há também um endereço IP interno (por exemplo, 168.55.11.1) em cada máquina virtual que é usada pelo Balanceador de carga para rotear o tráfego na máquina virtual. Este endereço IP interno também pode ser usado para a comunicação entre máquinas virtuais.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Solicitações externas para máquinas virtuais passam por um balanceador de carga. Por isso, solicitações não podem ser especificadas em relação a uma máquina virtual específica em implantações com várias máquinas virtuais. Para implantações com várias máquinas virtuais, mapeamento de porta deverá ser configurado entre as máquinas virtuais (porta de máquina virtual) e o balanceador de carga (porta lb).

**ponto de extremidade de máquina virtual criar &lt;nome da máquina virtual > &lt;lb porta > [máquina virtual-porta]**

Esse comando cria um ponto de extremidade de máquina virtual. Você também pode usar -u ou --habilitar direct-server-retorno para especificar se para habilitar o servidor direta retornar nesse ponto de extremidade, desabilitado por padrão.

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**máquina virtual ponto de extremidade criar várias [Opções] &lt;nome da máquina virtual > &lt;lb porta > [:&lt;porta de máquina virtual > [:&lt;protocolo > [:&lt;ativar direct-server-retorno > [:&lt;lb-set-name > [:&lt;protocolo de teste > [:&lt;porta de teste > [:&lt;caminho de teste > [:&lt;nome interno lb >]]] {1 -*}**

Crie vários pontos de extremidade de máquina virtual.

**ponto de extremidade de máquina virtual excluir [Opções] &lt;nome da máquina virtual > &lt;-nome do ponto de extremidade >**

Este comando exclui um ponto de extremidade de máquina virtual.

    ~$ azure vm endpoint delete my-vm http
    azure vm endpoint delete my-vm http
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**lista de ponto de extremidade de máquina virtual &lt;nome da máquina virtual >**

Esse comando lista todos os pontos de extremidade de máquina virtual. A opção – json Especifica que os resultados são retornados em formato JSON bruto.

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port
    data:   ----  -------------  ----------
    data:   ssh   22             22

**atualização de ponto de extremidade de máquina virtual [Opções] &lt;nome da máquina virtual > &lt;-nome do ponto de extremidade >**

Este comando atualiza um ponto de extremidade de máquina virtual para novos valores usando as seguintes opções.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**ponto de extremidade de máquina virtual Mostrar [Opções] &lt;nome da máquina virtual >**

Esse comando mostra os detalhes dos pontos de extremidade em uma máquina virtual

    ~$ azure vm endpoint show "mycouchvm"
    info:    Executing command vm endpoint show
    + Getting virtual machines
    data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
    data:    Network Endpoints 0 LocalPort "5984"
    data:    Network Endpoints 0 Name "CouchDB_EP"
    data:    Network Endpoints 0 Port "5984"
    data:    Network Endpoints 0 Protocol "tcp"
    data:    Network Endpoints 0 Vip "168.61.9.97"
    data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
    data:    Network Endpoints 1 LocalPort "2020"
    data:    Network Endpoints 1 Name "CouchEP_2"
    data:    Network Endpoints 1 Port "2020"
    data:    Network Endpoints 1 Protocol "tcp"
    data:    Network Endpoints 1 Vip "168.61.9.97"
    data:    Network Endpoints 2 LocalPort "3389"
    data:    Network Endpoints 2 Name "RemoteDesktop"
    data:    Network Endpoints 2 Port "3389"
    data:    Network Endpoints 2 Protocol "tcp"
    data:    Network Endpoints 2 Vip "168.61.9.97"
    info:    vm endpoint show command OK

## <a name="commands-to-manage-your-azure-virtual-machine-images"></a>Comandos para gerenciar suas imagens Azure máquina virtual

Imagens de máquinas virtuais estão capturas de máquinas virtuais já configuradas que poderão ser replicadas conforme necessário.

**lista de imagens de máquina virtual [Opções]**

Esse comando obtém uma lista de imagens de máquina virtual. Há três tipos de imagens: imagens criadas pela Microsoft, que são o prefixo "MSFT", imagens criadas por terceiros, que são precedidos com o nome do fornecedor e imagens que você criar. Para criar imagens, você pode capturar uma máquina virtual existente ou criar uma imagem a partir de um VHD personalizado carregados para armazenamento de blob. Para obter mais informações sobre como usar um VHD personalizado, consulte imagem de máquina virtual criar.
A opção – json Especifica que os resultados são retornados em formato JSON bruto.

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK

**apresentação de imagem de máquina virtual [Opções] &lt;nome >**

Esse comando mostra os detalhes de uma imagem de máquina virtual.

    ~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
    + Fetching VM image
    info:   Executing command vm image show
    data:   {
    data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
    data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
    data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
    data:       Category: 'Microsoft',
    data:       OS: 'Windows',
    data:       Eula: 'http://www.microsoft.com',
    data:       LogicalSizeInGB: '30'
    data:   }
    info:   vm image show command OK

**exclusão de imagem de máquina virtual [Opções] &lt;nome >**

Este comando exclui uma imagem de máquina virtual.

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image
    info:   vm image delete command OK

**imagem de máquina virtual criar &lt;nome > [caminho de origem]**

Esse comando cria uma imagem de máquina virtual. Seus arquivos. vhd personalizados são carregados para armazenamento de blob e, em seguida, a imagem de máquina virtual é criada a partir daí. Você use esta imagem de máquina virtual para criar uma máquina virtual. O local e OS parâmetros são necessários.

>[AZURE.NOTE]Atualmente esse comando suporta apenas carregando arquivos. vhd fixo. Para carregar um arquivo. VHD dinâmicos, use os [utilitários do Azure VHD para ir](https://github.com/Microsoft/azure-vhd-utils-for-go).

Alguns sistemas impõem limites de descritor de arquivo de cada processo. Se esse limite for excedido, a ferramenta exibe um erro de limite de descritor de arquivo. Você pode executar o comando novamente usando o -p &lt;número > parâmetro para reduzir o número máximo de carregamentos paralelos. O número máximo padrão de carregamentos paralelos é 96.

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

## <a name="commands-to-manage-your-azure-virtual-machine-data-disks"></a>Comandos para gerenciar os seus discos de dados do Azure máquina virtual

Discos de dados são arquivos. vhd no armazenamento de blob que podem ser usados por uma máquina virtual. Para obter mais informações sobre como discos de dados são implantados para armazenamento de blob, consulte o diagrama técnico Azure mostrado anteriormente.

Os comandos para anexar discos de dados (disco de máquina virtual azure anexar e máquina virtual azure disco anexar novo) atribuir um número de unidade lógica (LUN) para o disco de dados anexados, conforme o necessário pelo protocolo SCSI. O primeiro disco de dados anexado a uma máquina virtual é atribuído LUN 0, o próximo é atribuído LUN 1 e assim por diante.

Quando você desanexar um disco de dados com o disco de máquina virtual azure desanexar comando, use o &lt;lun&gt; parâmetro para indicar quais disco desanexar.

>[AZURE.NOTE] Você sempre deve desanexar discos de dados na ordem inversa, começando com o LUN número mais alto que foi atribuído. A camada de Linux SCSI não dá suporte a desanexação um LUN numerados inferiores enquanto um LUN numerados superiores ainda estiver conectado. Por exemplo, você não deve desanexar LUN 0 se LUN 1 ainda estiver conectado.

**apresentação de disco de máquina virtual [Opções] &lt;nome >**

Este comando mostra detalhes sobre um disco Azure.

    ~$ azure vm disk show anucentos-anucentos-0-20120524070008
    info:   Executing command vm disk show
    data:   AttachedTo DeploymentName "mycentos"
    data:   AttachedTo HostedServiceName "myanucentos"
    data:   AttachedTo RoleName "myanucentos"
    data:   OS "Linux"
    data:   Location "Azure Preview"
    data:   LogicalDiskSizeInGB "30"
    data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
    data:   Name "mycentos-mycentos-0-20120524070008"
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**lista de disco de máquina virtual [Opções] [nome da máquina virtual]**

Este comando listas discos Azure ou discos anexados a uma máquina virtual especificada. Se for executado com um parâmetro de nome de máquina virtual, ela retorna todos os discos anexados à máquina virtual. LUN 1 é criado com a máquina virtual e qualquer outro disco listado é anexado separadamente.

    ~$ azure vm disk list mycentos
    info:   Executing command vm disk list
    data:   Lun  Size(GB)  Blob-Name
    data:   ---  --------  --------------------------------
    data:   1    30        mycentos-cb39b8223b01f95c.vhd
    data:   2    10        mycentos-e3f0d717950bb78d.vhd
    info:   vm disk list command OK

Executar esse comando sem um parâmetro de nome de máquina virtual retorna todos os discos.

    ~$ azure vm disk list
    data:   Name                                        OS
    data:   ------------------------------------------  -------
    data:   mycentos-mycentos-0-20120524070008          Linux
    data:   mycentos-mycentos-2-20120525055052
    data:   mywindows-winvm-20120522223119              Windows
    info:   vm disk list command OK

**exclusão de disco de máquina virtual [Opções] &lt;nome >**

Este comando exclui um disco Azure um repositório pessoal. O disco deve ser desanexado na máquina virtual antes de serem excluído.

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052
    info:   vm disk delete command OK

**Criar disco de máquina virtual &lt;nome > [caminho de origem]**

Este comando carrega e registra um disco Azure. – blob-url, - local ou --grupo de afinidade deve ser especificado. Se você usar esse comando com [caminho de origem], o arquivo. vhd especificado é carregado e uma imagem é criada. Você pode anexar esta imagem a uma máquina virtual usando o disco de máquina virtual anexar.

Alguns sistemas impõem limites de descritor de arquivo de cada processo. Se esse limite for excedido, a ferramenta exibe um erro de limite de descritor de arquivo. Você pode executar o comando novamente usando o -p &lt;número > parâmetro para reduzir o número máximo de carregamentos paralelos. O número máximo padrão de carregamentos paralelos é 96.

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**carregamento de disco de máquina virtual [Opções] &lt;caminho de origem > &lt;blob-url > &lt;chave da conta de armazenamento >**

Este comando permite carregar um disco de máquina virtual

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**Anexar de disco de máquina virtual &lt;nome da máquina virtual > &lt;nome da imagem de disco >**

Este comando anexa um disco existente no armazenamento de blob a uma máquina virtual existente implantada em um serviço na nuvem.

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**máquina virtual disco anexar novo &lt;nome da máquina virtual > &lt;gb de tamanho > [blob-url]**

Este comando anexa um disco de dados a uma máquina virtual Azure. Neste exemplo, 20 é o tamanho do disco novato, em gigabytes, será anexado. Opcionalmente, você pode usar uma URL de blob como o último argumento para especificar explicitamente o blob de destino para criar. Se você não especificar uma URL de blob, um objeto de blob é gerado automaticamente.

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**disco de máquina virtual desanexar &lt;nome da máquina virtual > &lt;lun >**

Este comando desconecta um disco de dados conectado a uma máquina virtual Azure. &lt;LUN > identifica o disco para ser desanexado. Para obter uma lista de discos associadas com um disco antes de desconectá-lo, use o disco-lista de máquina virtual &lt;nome da máquina virtual >.

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

## <a name="commands-to-manage-your-azure-cloud-services"></a>Comandos para gerenciar seus serviços de nuvem do Azure

Serviços de nuvem Azure são aplicativos e serviços hospedados em funções da web e de trabalho. Os seguintes comandos podem ser usados para gerenciar serviços de nuvem Azure.

**serviço criar [Opções] &lt;serviceName >**

Esse comando cria um serviço na nuvem

    ~$ azure service create newservicemsopentech
    info:    Executing command service create
    + Getting locations
    help:    Location:
      1) East Asia
      2) Southeast Asia
      3) North Europe
      4) West Europe
      5) East US
      6) West US
      : 6
    + Creating cloud service
    data:    Cloud service name newservicemsopentech
    info:    service create command OK

**serviço Mostrar [Opções] &lt;serviceName >**

Esse comando mostra os detalhes de um serviço de nuvem do Azure

    ~$ azure service show newservicemsopentech
    info:    Executing command service show
    + Getting cloud service
    data:    Name newservicemsopentech
    data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
    data:    Properties location West US
    data:    Properties label newservicemsopentech
    data:    Properties status Created
    data:    Properties dateCreated
    data:    Properties dateLastModified
    info:    service show command OK

**lista de serviço [Opções]**

Esse comando lista Serviços de nuvem Azure.

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**serviço excluir [Opções] &lt;nome >**

Este comando exclui um serviço de nuvem Azure.

    ~$ azure service delete myservice
    info:   Executing command service delete myservice
    info:   cloud-service delete command OK

Para forçar a exclusão, use o `-q` parâmetro.


## <a name="commands-to-manage-your-azure-certificates"></a>Comandos para gerenciar seus certificados Azure

Os certificados de serviço Azure são certificados SSL conectados à sua conta do Azure. Para obter mais informações sobre certificados Azure, consulte [Gerenciar certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**lista de certificado de serviço [Opções]**

Este comando lista certificados Azure.

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services
    + Fetching certificates
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
    info:   service cert list command OK

**Criar certificado de serviço &lt;prefixo de dns > &lt;arquivo > [senha]**

Este comando carrega um certificado. Deixe o prompt de senha em branco para certificados que não são protegidas por senha.

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password:
    + Creating certificate
    info:   service cert create command OK

**exclusão de certificado de serviço [Opções] &lt;impressão digital >**

Este comando exclui um certificado.

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

## <a name="commands-to-manage-your-web-apps"></a>Comandos para gerenciar seus aplicativos web

Aplicativo web Azure é uma configuração de web acessível pelo URI. Aplicativos Web são hospedados em máquinas virtuais, mas você não precisa pensar sobre os detalhes de criação e implantação da máquina virtual. Esses detalhes são tratados para você pelo Azure.

**lista de sites [Opções]**

Esse comando lista seus aplicativos web.

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**conjunto de sites [Opções] [nome]**

Este comando define opções de configuração para seu aplicativo web do [nome]

    ~$ azure site set
    info:    Executing command site set
    Web site name: mydemosite
    + Getting sites
    + Updating site config information
    info:    site set command OK

**site deploymentscript [Opções]**

Este comando gera um script de implantação personalizada

    ~$ azure site deploymentscript --node
    info:    Executing command site deploymentscript
    info:    Generating deployment script for node.js Web Site
    info:    Generated deployment script files
    info:    site deploymentscript command OK

**site criar [Opções] [nome]**

Este comando cria um aplicativo web e o diretório local.

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

> [AZURE.NOTE] O nome do site deve ser exclusivo. Você não pode criar um site com o mesmo nome DNS de um site existente.

**procurar sites [Opções] [nome]**

Esse comando abre seu aplicativo web em um navegador.

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**apresentação de site [Opções] [nome]**

Esse comando mostra detalhes para um aplicativo web.

    ~$ azure site show mysite
    info:   Executing command site show
    info:   Showing details for site
    data:   Site AdminEnabled true
    data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
    data:   Site Name mysite
    data:   Site Owner 00060000814EDDEE
    data:   Site RepositorySiteName mysite
    data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
    data:   Site State Running
    data:   Site UsageState Normal
    data:   Site WebSpace northeuropewebspace
    data:   Config AppSettings
    data:   Config ConnectionStrings
    data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
    data:   Config DetailedErrorLoggingEnabled false
    data:   Config HttpLoggingEnabled false
    data:   Config Metadata
    data:   Config NetFrameworkVersion v4.0
    data:   Config NumberOfWorkers 1
    data:   Config PhpVersion 5.3
    data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
    data:   Config RequestTracingEnabled false
    data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
    info:   site show command OK

**exclusão de site [Opções] [nome]**

Este comando exclui um aplicativo web.

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

 **troca de site [Opções] [nome]**

Este comando troca dois slots de aplicativo web.

Este comando suporta as seguintes opções adicionais:

**- q ou **-silencioso * *: não solicitar confirmação. Use esta opção em scripts automatizados.


**início do site [Opções] [nome]**

Este comando inicia um aplicativo web.

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**parar de site [Opções] [nome]**

Este comando interrompe um aplicativo web.

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**reiniciar site [Opções] [nome]**

Este comando interrompe e inicia um aplicativo web especificado.

Este comando suporta as seguintes opções adicionais:

**-slot** &lt;slot >: O nome do slot reiniciar.


**lista de locais de site [Opções]**

Esse comando lista os locais de aplicativo web.

    ~$ azure site location list
    info:    Executing command site location list
    + Getting locations
    data:    Name
    data:    ----------------
    data:    West Europe
    data:    West US
    data:    North Central US
    data:    North Europe
    data:    East Asia
    data:    East US
    info:    site location list command OK

###<a name="commands-to-manage-your-web-app-application-settings"></a>Comandos para gerenciar as configurações do aplicativo web app

**lista de appsetting site [Opções] [nome]**

Esse comando lista a configuração de aplicativo adicionada para o aplicativo web.

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**site appsetting adicionar [Opções] &lt;keyvaluepair > [nome]**

Este comando adiciona uma configuração de aplicativo para o seu aplicativo web como um par de valor de chave.

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**site appsetting excluir [Opções] &lt;chave > [nome]**

Este comando exclui a configuração do aplicativo específico do aplicativo web.

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**site appsetting Mostrar [Opções] &lt;chave > [nome]**

Este comando exibe detalhes da configuração do aplicativo especificado

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

###<a name="commands-to-manage-your-web-app-certificates"></a>Comandos para gerenciar seus certificados de aplicativo web

**lista de certificado de site [Opções] [nome]**

Este comando exibe uma lista de certificados de aplicativo na web.

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**certificado de site adicionar [Opções] &lt;caminho do certificado > [nome]**

**exclusão de certificado de site [Opções] &lt;impressão digital > [nome]**

**apresentação de certificado [Opções] do site &lt;impressão digital > [nome]**

Esse comando mostra os detalhes do certificado

    ~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    Executing command site cert show
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Certificate hostNames 0=msopentech.azurewebsites.net
    data:    Certificate expirationDate
    data:    Certificate friendlyName msopentech.azurewebsites.net
    data:    Certificate issueDate
    data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
    data:    Certificate subjectName msopentech.azurewebsites.net
    data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    site cert show command OK

###<a name="commands-to-manage-your-web-app-connection-strings"></a>Comandos para gerenciar suas cadeias de caracteres de conexão do web app

**lista de connectionstring site [Opções] [nome]**

**site connectionstring adicionar [Opções] &lt;connectionname > &lt;valor > &lt;tipo > [nome]**

**site connectionstring excluir [Opções] &lt;connectionname > [nome]**

**site connectionstring Mostrar [Opções] &lt;connectionname > [nome]**

###<a name="commands-to-manage-your-web-app-default-documents"></a>Comandos para gerenciar seus documentos de padrão do aplicativo da web

**lista de defaultdocument de site [Opções] [nome]**

**site defaultdocument adicionar [Opções] &lt;documento > [nome]**

**site defaultdocument excluir [Opções] &lt;documento > [nome]**

###<a name="commands-to-manage-your-web-app-deployments"></a>Comandos para gerenciar as implantações de aplicativo web

**lista de implantação de site [Opções] [nome]**

**implantação do site Mostrar [Opções] &lt;commitId > [nome]**

**reimplantação de implantação de site [Opções] &lt;commitId > [nome]**

**site implantação github [Opções] [nome]**

**conjunto de usuários de implantação de site [Opções] [nomeusuário] [senha]**

###<a name="commands-to-manage-your-web-app-domains"></a>Comandos para gerenciar seus domínios de aplicativo web

**lista de domínios de site [Opções] [nome]**

**domínio de site adicionar [Opções] &lt;dn > [nome]**

**exclusão de domínio de site [Opções] &lt;dn > [nome]**

###<a name="commands-to-manage-your-web-app-handler-mappings"></a>Comandos para gerenciar os mapeamentos de manipulador de aplicativo web

**lista de manipulador de site [Opções] [nome]**

**manipulador de site adicionar [Opções] &lt;extensão > &lt;processador > [nome]**

**exclusão de manipulador de site [Opções] &lt;extensão > [nome]**

###<a name="commands-to-manage-your-web-jobs"></a>Comandos para gerenciar seus trabalhos de Web

**lista de trabalhos de site [Opções] [nome]**

Este comando Listar todos os trabalhos de web em um aplicativo web.

Este comando suporta as seguintes opções adicionais:

+ **-tipo de trabalho** &lt;tipo de trabalho >: opcional. O tipo da webjob. Valor válido é "disparadas" ou "contínuo". Por padrão retornam webjobs de todos os tipos.
+ **-slot** &lt;slot >: O nome do slot reiniciar.

**Mostrar trabalho [Opções] do site &lt;Nome_do_trabalho > &lt;jobType > [nome]**

Esse comando mostra os detalhes de um serviço web específico.

Este comando suporta as seguintes opções adicionais:

+ **-nome do trabalho** &lt;nome de trabalho >: obrigatório. O nome da webjob.
+ **-tipo de trabalho** &lt;tipo de trabalho >: obrigatório. O tipo da webjob. Valor válido é "disparadas" ou "contínuo".
+ **-slot** &lt;slot >: O nome do slot reiniciar.

**exclusão de trabalho de site [Opções] &lt;Nome_do_trabalho > &lt;jobType > [nome]**

Este comando exclui o trabalho de web especificada.

Este comando suporta as seguintes opções adicionais:

+ **-nome do trabalho** &lt;nome de trabalho > necessários. O nome da webjob.
+ **-tipo de trabalho** &lt;tipo de trabalho > necessários. O tipo da webjob. Valor válido é "disparadas" ou "contínuo".
+ **-p** ou **-silencioso**: não solicitar confirmação. Use esta opção em scripts automatizados.
+ **-slot** &lt;slot >: O nome do slot reiniciar.

**carregamento de trabalho de site [Opções] &lt;Nome_do_trabalho > &lt;jobType > <jobFile> [nome]**

Este comando exclui o trabalho de web especificada.

Este comando suporta as seguintes opções adicionais:

+ **-nome do trabalho** &lt;nome de trabalho >: obrigatório. O nome da webjob.
+ **-tipo de trabalho** &lt;tipo de trabalho >: obrigatório. O tipo da webjob. Valor válido é "disparadas" ou "contínuo".
+ **-arquivo de trabalho** &lt;arquivo de trabalho >: obrigatório. O arquivo de trabalho.
+ **-slot** &lt;slot >: O nome do slot reiniciar.

**início do trabalho [Opções] do site &lt;Nome_do_trabalho > &lt;jobType > [nome]**

Este comando inicia o trabalho de web especificada.

Este comando suporta as seguintes opções adicionais:

+ **-nome do trabalho** &lt;nome de trabalho >: obrigatório. O nome da webjob.
+ **-tipo de trabalho** &lt;tipo de trabalho >: obrigatório. O tipo da webjob. Valor válido é "disparadas" ou "contínuo".
+ **-slot** &lt;slot >: O nome do slot reiniciar.

**interrupção de trabalho de site [Opções] &lt;Nome_do_trabalho > &lt;jobType > [nome]**

Este comando interrompe o trabalho web especificada. Somente os trabalhos contínuos poderá ser interrompidos.

Este comando suporta as seguintes opções adicionais:

+ **-nome do trabalho** &lt;nome de trabalho >: obrigatório. O nome da webjob.
+ **-slot** &lt;slot >: O nome do slot reiniciar.

###<a name="commands-to-manage-your-web-jobs-history"></a>Comandos para gerenciar seu histórico de trabalhos de Web

**lista de histórico de trabalho de site [Opções] [nome_do_trabalho] [nome]**

Esse comando exibe um histórico das execuções do trabalho web especificada.

Este comando suporta as seguintes opções adicionais:

+ **-nome do trabalho** &lt;nome de trabalho >: obrigatório. O nome da webjob.
+ **-slot** &lt;slot >: O nome do slot reiniciar.

**histórico de trabalho de site Mostrar [Opções] [nome_do_trabalho] [runId] [nome]**

Esse comando obtém os detalhes do trabalho executado para o trabalho de web especificada.

Este comando suporta as seguintes opções adicionais:

+ **-nome do trabalho** &lt;nome de trabalho >: obrigatório. O nome da webjob.
+ **-id da execução** &lt;id da execução >: opcional. A id do histórico de execução. Se não especificado, mostre as últimas execução.
+ **-slot** &lt;slot >: O nome do slot reiniciar.

###<a name="commands-to-manage-your-web-app-diagnostics"></a>Comandos para gerenciar o diagnóstico de aplicativo web

**log de site baixar [Opções] [nome]**

Baixe um arquivo. zip que contém o diagnóstico do seu aplicativo web.

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**Laço de log de site [Opções] [nome]**

Esse comando conecta seu terminal no serviço de streaming de log.

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**log de site definido [Opções] [nome]**

Esse comando configura as opções de diagnósticos para seu aplicativo web.

    ~$ azure site log set -a
    info:    Executing command site log set
    + Getting output options
    help:    Output:
      1) file
      2) storage
      : 1
    Web site name: mydemosite
    + Getting locations
    + Getting sites
    + Getting site information
    + Getting diagnostic settings
    + Updating diagnostic settings
    info:    site log set command OK

###<a name="commands-to-manage-your-web-app-repositories"></a>Comandos para gerenciar seus repositórios de aplicativo web

**ramificação de repositório de site [Opções] &lt;ramificação > [nome]**

**exclusão de repositório de site [Opções] [nome]**

**sincronização de repositório de site [Opções] [nome]**

###<a name="commands-to-manage-your-web-app-scaling"></a>Comandos para gerenciar seu dimensionamento de aplicativo web

**modo de escala de site [Opções] &lt;modo > [nome]**

**escala de site instâncias [Opções] &lt;instâncias > [nome]**


## <a name="commands-to-manage-azure-mobile-services"></a>Comandos para gerenciar serviços de celular do Azure

Os serviços do Azure Mobile reúne um conjunto de serviços Azure que permitem recursos de back-end para seus aplicativos. Comandos de serviços móveis são divididos em categorias a seguir:

+ [Comandos para gerenciar as instâncias de serviço móvel](#Mobile_Services)
+ [Comandos para gerenciar a configuração de serviço móvel](#Mobile_Configuration)
+ [Comandos para gerenciar as tabelas de serviço móvel](#Mobile_Tables)
+ [Comandos para gerenciar scripts de serviço móvel](#Mobile_Scripts)
+ [Comandos para gerenciar tarefas agendadas](#Mobile_Jobs)
+ [Comandos para dimensionar um serviço móvel](#Mobile_Scale)

As opções a seguir se aplicam a maioria dos comandos de serviços de celular:

+ **-h** ou **– Ajuda**: informações sobre o uso de saída de exibição.
+ **-s `<id>` ** ou **-assinatura `<id>` **: usar uma assinatura específica, especificada como `<id>`.
+ **-v** ou **-detalhado**: escrever saída detalhada.
+ **– json**: saída JSON escrever.

### <a name="Mobile_Services"></a>Comandos para gerenciar as instâncias de serviço móvel

**locais móveis [Opções]**

Esse comando lista locais geográficos suportados pelos serviços de celular.

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US
    info:    North Europe

**Mobile criar [Opções] [nome_do_serviço] [sqlAdminUsername] [sqlAdminPassword]**

Esse comando cria um serviço móvel juntamente com um banco de dados SQL e um servidor.

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

Este comando suporta as seguintes opções adicionais:

+ **- r `<sqlServer>` ** ou **– SQL Server `<sqlServer>` **: usar um servidor de banco de dados SQL existente, especificado como `<sqlServer>`.
+ **-d `<sqlDb>` ** ou **- sqlDb `<sqlDb>` **: usar o banco de dados SQL existente, especificado como `<sqlDb>`.
+ **-l `<location>` ** ou **-local `<location>` **: criar o serviço em um local específico, especificado como `<location>`. Execute o azure locais móveis para obter locais disponíveis.
+ **- sqlLocation `<location>` **: criar o SQL server em uma determinada `<location>`; padrões para o local do serviço móvel.

**Excluir móvel [Opções] [nome_do_serviço]**

Este comando exclui um serviço móvel junto com seu banco de dados SQL e o servidor.

    ~$ azure mobile delete todolist -a -q
    info:    Executing command mobile delete
    data:    Mobile service todolist
    data:    SQL database todolistAwrhcL60azo1C401
    data:    SQL server fh1kvbc7la
    + Deleting mobile service
    info:    Deleted mobile service
    + Deleting SQL server
    info:    Deleted SQL server
    + Deleting mobile application
    info:    Deleted mobile application
    info:    mobile delete command OK

Este comando suporta as seguintes opções adicionais:

+ **-d** ou **– deleteData**: excluir todos os dados desse serviço móvel do banco de dados.
+ **-** ou **- deleteAll**: excluir o banco de dados SQL e o servidor.
+ **-p** ou **-silencioso**: não solicitar confirmação. Use esta opção em scripts automatizados.

**lista móvel [Opções]**

Esse comando lista seus serviços móveis.

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**Mostrar móvel [Opções] [nome_do_serviço]**

Esse comando exibe detalhes sobre um serviço móvel.

    ~$ azure mobile show todolist
    info:    Executing command mobile show
    + Getting information
    info:    Mobile application
    data:    status Healthy
    data:    Mobile service name todolist
    data:    Mobile service status ProvisionConfigured
    data:    SQL database name todolistAwrhcL60azo1C401
    data:    SQL database status Linked
    data:    SQL server name fh1kvbc7la
    data:    SQL server status Linked
    info:    Mobile service
    data:    name todolist
    data:    state Ready
    data:    applicationUrl https://todolist.azure-mobile.net/
    data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    webspace WESTUSWEBSPACE
    data:    region West US
    data:    tables TodoItem
    info:    mobile show command OK

**reiniciar móvel [Opções] [nome_do_serviço]**

Este comando reinicia uma instância de serviço móvel.

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**log móvel [Opções] [nome_do_serviço]**

Esse comando retorna logs de serviço móvel, filtrando todos os tipos de log, mas `error`.

    ~$ azure mobile log todolist -t error
    info:    Executing command mobile log
    data:
    data:    timeCreated 2013-01-07T16:04:43.351Z
    data:    type error
    data:    source /scheduler/TestingLogs.js
    data:    message This is an error.
    data:
    info:    mobile log command OK

Este comando suporta as seguintes opções adicionais:

+ **- r `<query>` ** ou **-consulta `<query>` **: executa a consulta de log especificado.
+ **-t `<type>` ** ou **-tipo `<type>` **: filtrar os registros retornados pela entrada `<type>`, que pode ser `information`, `warning`, ou `error`.
+ **-k `<skip>` ** ou **-Ignorar `<skip>` **: ignora o número de linhas especificado pelo `<skip>`.
+ **-p `<top>` ** ou **-início `<top>` **: retorna um número específico de linhas, especificado pelo `<top>`.

> [AZURE.NOTE] O **-consulta** parâmetro tem precedência sobre **-tipo**, **-Ignorar**, e **-início**.

**recuperar móvel [Opções] [unhealthyservicename] [healthyservicename]**

Este comando recupera um serviço móvel não íntegro movendo-o para um serviço móvel Íntegro em uma região diferente.

Este comando suporta as seguintes opções adicionais:

**-p** ou **-silencioso**: suprimir o prompt de confirmação de recuperação.

**chave móvel gerar [Opções] [nome_do_serviço] [tipo]**

Este comando gera novamente a tecla de aplicativo de serviço móvel.

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

Tipos de chave são `master` e `application`.

> [AZURE.NOTE] Quando você gerar chaves, os clientes que usam a chave antiga talvez não seja possível acessar o serviço móvel. Quando você gerar novamente a chave de aplicativo, você deverá atualizar seu aplicativo com o novo valor de chave.

**conjunto de chaves móvel [Opções] [nome_do_serviço] [tipo] [valor]**

Este comando define a chave de serviço móvel a um valor específico.


### <a name="Mobile_Configuration"></a>Comandos para gerenciar a configuração de serviço móvel

**lista de configuração móvel [Opções] [nome_do_serviço]**

Esse comando lista Opções de configuração para um serviço móvel.

    ~$ azure mobile config list todolist
    info:    Executing command mobile config list
    + Getting mobile service configuration
    data:    dynamicSchemaEnabled true
    data:    microsoftAccountClientSecret Not configured
    data:    microsoftAccountClientId Not configured
    data:    microsoftAccountPackageSID Not configured
    data:    facebookClientId Not configured
    data:    facebookClientSecret Not configured
    data:    twitterClientId Not configured
    data:    twitterClientSecret Not configured
    data:    googleClientId Not configured
    data:    googleClientSecret Not configured
    data:    apnsMode none
    data:    apnsPassword Not configured
    data:    apnsCertifcate Not configured
    info:    mobile config list command OK

**configuração móvel obter [Opções] [nome_do_serviço] [chave]**

Esse comando obtém uma opção de configuração específicos para um serviço móvel, nesse caso dinâmico esquema.

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**configuração móvel definir [Opções] [nome_do_serviço] [chave] [valor]**

Este comando define uma opção de configuração específica para um serviço móvel, nesse caso dinâmico esquema.

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK


### <a name="Mobile_Tables"></a>Comandos para gerenciar as tabelas de serviço móvel

**lista de tabelas móvel [Opções] [nome_do_serviço]**

Esse comando lista todas as tabelas no seu serviço móvel.

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**Mostrar tabela móvel [Opções] [nome_do_serviço] [tablename]**

Esse comando mostra retorna detalhes sobre uma tabela específica.

    ~$azure mobile table show todolist
    info:    Executing command mobile table show
    + Getting table information
    info:    Table statistics:
    data:    Number of records 5
    info:    Table operations:
    data:    Operation  Script       Permissions
    data:    ---------  -----------  -----------
    data:    insert     1900 bytes   user
    data:    read       Not defined  user
    data:    update     Not defined  user
    data:    delete     Not defined  user
    info:    Table columns:
    data:    Name  Type           Indexed
    data:    ----  -------------  -------
    data:    id    bigint(MSSQL)  Yes
    data:    text      string
    data:    complete  boolean
    info:    mobile table show command OK

**tabela móvel criar [Opções] [nome_do_serviço] [tablename]**

Esse comando cria uma tabela.

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

Este comando suporta as seguintes opções adicionais:

+ **-p `&lt;permissions>` ** ou **– permissões `&lt;permissions>` **: lista delimitada por vírgulas de `<operation>` = `<permission>` pares, onde `<operation>` é `insert`, `read`, `update`, ou `delete` e `&lt;permissions>` é `public`, `application` (padrão), `user`, ou `admin`.

**dados móveis ler [Opções] [nome_do_serviço] [tablename] [query]**

Este comando lê dados de uma tabela.

    ~$azure mobile data read todolist TodoItem
    info:    Executing command mobile data read
    data:    id  text     complete
    data:    --  -------  --------
    data:    1   item #1  false
    data:    2   item #2  true
    data:    3   item #3  false
    data:    4   item #4  true
    info:    mobile data read command OK

Este comando suporta as seguintes opções adicionais:

+ **-k `<skip>` ** ou **-Ignorar `<skip>` **: ignora o número de linhas especificado pelo `<skip>`.
+ **-t `<top>` ** ou **-início `<top>` **: retorna um número específico de linhas, especificado pelo `<top>`.
+ **-l** ou **– lista**: retorna dados em um formato de lista.

**atualização de tabela móvel [Opções] [nome_do_serviço] [tablename]**

Este comando altera as permissões de exclusão em uma tabela somente para administradores.

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

Este comando suporta as seguintes opções adicionais:

+ **-p `&lt;permissions>` ** ou **– permissões `&lt;permissions>` **: lista delimitada por vírgulas de `<operation>` = `<permission>` pares, onde `<operation>` está `insert`, `read`, `update`, ou `delete` e `&lt;permissions>` está `public`, `application` (padrão), `user`, ou `admin`.
+ **- deleteColumn `<columns>` **: lista delimitada por vírgulas de colunas para excluir, como `<columns>`.
+ **-p** ou **-silencioso**: Exclui colunas sem solicitar confirmação.
+ **- addIndex `<columns>` **: lista delimitada por vírgulas de colunas a incluir no índice.
+ **- deleteIndex `<columns>` **: lista delimitada por vírgulas de colunas a excluir do índice.

**tabela móvel excluir [Opções] [nome_do_serviço] [tablename]**

Este comando exclui uma tabela.

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

Especifique o parâmetro - q para excluir a tabela sem confirmação. Siga este procedimento para evitar o bloqueio de scripts de automação.

**dados móveis truncar [Opções] [nome_do_serviço] [tablename]**

Este comando remove todas as linhas de dados da tabela.

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>Comandos para gerenciar scripts

Comandos nessa seção são usados para gerenciar os scripts de servidor que fazem parte de um serviço móvel. Para obter mais informações, consulte [trabalhar com scripts de servidor nos serviços do Mobile](https://github.com/Azure/azure-mobile-services/blob/master/docs/mobile-services-how-to-use-server-scripts.md).

**lista de script móvel [Opções] [nome_do_serviço]**

Esse comando lista scripts registrados, incluindo scripts de tabela e Agendador.

    ~$azure mobile script list todolist
    info:    Executing command mobile script list
    + Getting script information
    info:    Table scripts
    data:    Name                   Size
    data:    ---------------------  ----
    data:    table/TodoItem.delete  256
    data:    table/Devices.insert   1660
    error:   Unable to get shared scripts
    info:    Scheduler scripts
    data:    Name                 Status     Interval   Last run   Next run
    data:    -------------------  ---------  ---------  ---------  ---------
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    info:    mobile script list command OK

**download de script móvel [Opções] [nome_do_serviço] [scriptname]**

Este comando baixa o script de inserção da tabela TodoItem em um arquivo denominado `todoitem.insert.js` no `table` subpasta.

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

Este comando suporta as seguintes opções adicionais:

+ **-p `<path>` ** ou **-caminho `<path>` **: O local no arquivo em que deseja salvar o script, onde a pasta de trabalho atual é o padrão.
+ **-f `<file>` ** ou **-arquivo `<file>` **: O nome do arquivo no qual deseja salvar o script.
+ **-o** ou **-Substituir**: substituir um arquivo existente.
+ **-c** ou **-console**: escrever o script para o console em vez de um arquivo.

**carregamento de script móvel [Opções] [nome_do_serviço] [scriptname]**

Este comando carrega um script chamado `todoitem.insert.js` do `table` subpasta.

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

O nome do arquivo deve ser composto dos nomes de tabela e a operação. Ele deve estar localizado na subpasta tabela relativo no local onde o comando é executado. Você também pode usar o **-f `<file>` ** ou **-arquivo `<file>` ** parâmetro para especificar um nome de arquivo diferente e um caminho para o arquivo que contém o script para registrar.


**script móvel excluir [Opções] [nome_do_serviço] [scriptname]**

Este comando remove o script inserir existente da tabela TodoItem.

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>Comandos para gerenciar tarefas agendadas

Comandos nesta seção são usados para gerenciar tarefas agendadas que fazem parte de um serviço móvel. Para obter mais informações, consulte [Agendar trabalhos](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**lista de trabalhos móvel [Opções] [nome_do_serviço]**

Esse comando lista trabalhos agendados.

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**trabalho móvel criar [Opções] [nome_do_serviço] [nome_do_trabalho]**

Esse comando cria um trabalho denominado `getUpdates` que está agendada para executar a cada hora.

    ~$azure mobile job create -i 1 -u hour todolist getUpdates
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

Este comando suporta as seguintes opções adicionais:

+ **-i `<number>` ** ou **– intervalo `<number>` **: O intervalo de trabalho, como um inteiro. O valor padrão é `15`.
+ **-u `<unit>` ** ou **- intervalUnit `<unit>` **: A unidade de _intervalo_, que pode ser um dos seguintes valores:
    + **minuto** (padrão)
    + **hora**
    + **dia**
    + **mês**
    + **Nenhum** (sob demanda trabalhos)
+ **-t `<time>`** **a hora de início - `<time>` ** A hora de início da primeira execução para o script, no formato ISO. O valor padrão é `now`.

> [AZURE.NOTE] Novos trabalhos são criados em um estado desabilitado porque um script ainda deve ser carregado. Use o comando de **script móvel carregar** para carregar um script e o comando **Atualizar trabalho móvel** para habilitar o trabalho.

**atualização de trabalho móvel [Opções] [nome_do_serviço] [nome_do_trabalho]**

O comando a seguir habilita a desabilitada `getUpdates` trabalho.

    ~$azure mobile job update -a enabled todolist getUpdates
    info:    Executing command mobile job update
    info:    mobile job update command OK

Este comando suporta as seguintes opções adicionais:

+ **-i `<number>` ** ou **– intervalo `<number>` **: O intervalo de trabalho, como um inteiro. O valor padrão é `15`.
+ **-u `<unit>` ** ou **- intervalUnit `<unit>` **: A unidade de _intervalo_, que pode ser um dos seguintes valores:
    + **minuto** (padrão)
    + **hora**
    + **dia**
    + **mês**
    + **Nenhum** (sob demanda trabalhos)
+ **-t `<time>`** **a hora de início - `<time>` ** A hora de início da primeira execução para o script, no formato ISO. O valor padrão é `now`.
+ **- `<status>` ** ou **-status `<status>` **: O status do trabalho, que poderá ser `enabled` ou `disabled`.

**exclusão de trabalho móvel [Opções] [nome_do_serviço] [nome_do_trabalho]**

Este comando remove o trabalho agendado getUpdates do servidor de lista de tarefas pendentes.

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

> [AZURE.NOTE] Excluir um trabalho também exclui o script carregado.

### <a name="Mobile_Scale"></a>Comandos para dimensionar um serviço móvel

Comandos nessa seção são usados para dimensionar um serviço móvel. Para obter mais informações, consulte [escala com um serviço móvel](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**escala móvel Mostrar [Opções] [nome_do_serviço]**

Este comando exibe informações de escala, incluindo o modo de computação atual e o número de instâncias.

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**escala móvel alterar [Opções] [nome_do_serviço]**

Este comando altera a escala do serviço móvel do gratuito para o modo de premium.

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

Este comando suporta as seguintes opções adicionais:

+ **- c `<mode>` ** ou **- computeMode `<mode>` **: O modo de computação deve ser um `Free` ou `Reserved`.
+ **-i `<count>` ** ou **- numberOfInstances `<count>` **: O número de instâncias usado durante a execução no modo reservado.

> [AZURE.NOTE] Quando você configurar o modo de cálculo para `Reserved`, todos os seus serviços móveis na mesma região executado no modo de premium.


###<a name="commands-to-enable-preview-features-for-your-mobile-service"></a>Comandos para habilitar os recursos de visualização para o serviço Mobile

**lista de visualização móvel [Opções] [nome_do_serviço]**

Este comando exibe os recursos de visualização disponíveis sobre o serviço especificado e se elas são habilitadas.

    ~$ azure mobile preview list mysite
    info:    Executing command mobile preview list
    + Getting preview features
    data:    Preview feature  Enabled
    data:    ---------------  -------
    data:    SourceControl    No
    data:    Users            No
    info:    You can enable preview features using the 'azure mobile preview enable' command.
    info:    mobile preview list command OK

**Habilitar a visualização móvel [Opções] [nome_do_serviço] [nome do recurso]**

Este comando ativa o recurso de visualização especificado para um serviço móvel. Uma vez habilitado, os recursos de visualização não podem ser desabilitados para um serviço móvel.

###<a name="commands-to-manage-your-mobile-service-apis"></a>Comandos para gerenciar o serviço móvel APIs

**lista de api móvel [Opções] [nome_do_serviço]**

Este comando exibe uma lista móvel APIs personalizado que você criou para o serviço móvel do serviço.

    ~$ azure mobile api list mysite
    info:    Executing command mobile api list
    + Retrieving list of APIs
    info:    APIs
    data:    Name                  Get          Put          Post         Patch        Delete
    data:    --------------------  -----------  -----------  -----------  -----------  -----------
    data:    myCustomRetrieveAPI   application  application  application  application  application
    info:    You can manipulate API scripts using the 'azure mobile script' command.
    info:    mobile api list command OK

**api móvel criar [Opções] [nome_do_serviço] [apiname]**

Cria uma API personalizado do serviço móvel

    ~$ azure mobile api create mysite myCustomRetrieveAPI
    info:    Executing command mobile api create
    + Creating custom API: 'myCustomRetrieveAPI'
    info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
    info:    mobile api create command OK

Este comando suporta as seguintes opções adicionais:

**-p** ou **– permissões** &lt;permissões >: uma lista delimitada por vírgulas de &lt;método > =&lt;permissão > pares.

**atualização de api móvel [Opções] [nome_do_serviço] [apiname]**

Este comando atualiza a API personalizado do serviço móvel especificado.

Este comando suporta as seguintes opções adicionais:

Este comando suporta as seguintes opções adicionais:

+ **-p** ou **– permissões** &lt;permissões >: uma lista delimitada por vírgulas de &lt;método > =&lt;permissão > pares.
+ **-f** ou **-Forçar**: substitui quaisquer alterações personalizadas para o arquivo de metadados de permissões.

**api móvel excluir [Opções] [nome_do_serviço] [apiname]**

    ~$ azure mobile api delete mysite myCustomRetrieveAPI
    info:    Executing command mobile api delete
    + Deleting API: 'myCustomRetrieveAPI'
    info:    mobile api delete command OK

Este comando exclui a API personalizado do serviço móvel especificado.

###<a name="commands-to-manage-your-mobile-application-app-settings"></a>Comandos para gerenciar as configurações de aplicativo do aplicativo móvel

**lista de appsetting móvel [Opções] [nome_do_serviço]**

Este comando exibe as configurações de aplicativo de aplicativos móveis para o serviço especificado.

    ~$ azure mobile appsetting list mysite
    info:    Executing command mobile appsetting list
    + Retrieving app settings
    data:    Name               Value
    data:    -----------------  -----
    data:    enablebetacontent  true
    info:    mobile appsetting list command OK

**appsetting móvel adicionar [Opções] [nome_do_serviço] [nome] [valor]**

Este comando adiciona uma configuração de aplicativo personalizado para o serviço móvel.

    ~$ azure mobile appsetting add mysite enablebetacontent true
    info:    Executing command mobile appsetting add
    + Retrieving app settings
    + Adding app setting
    info:    mobile appsetting add command OK

**Excluir appsetting móvel [Opções] [nome_do_serviço] [nome]**

Este comando remove a configuração do aplicativo especificado para o serviço móvel.

    ~$ azure mobile appsetting delete mysite enablebetacontent
    info:    Executing command mobile appsetting delete
    + Retrieving app settings
    + Removing app setting 'enablebetacontent'
    info:    mobile appsetting delete command OK

**Mostrar appsetting móvel [Opções] [nome_do_serviço] [nome]**

Este comando remove a configuração do aplicativo especificado para o serviço móvel.

    ~$ azure mobile appsetting show mysite enablebetacontent
    info:    Executing command mobile appsetting show
    + Retrieving app settings
    info:    enablebetacontent: true
    info:    mobile appsetting show command OK

## <a name="manage-tool-local-settings"></a>Gerenciar configurações de local de ferramenta

Configurações locais são sua ID de assinatura e o nome de conta de armazenamento padrão.

**lista de configuração [Opções]**

Este comando exibe as configurações.

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**conjunto de configurações [Opções] &lt;nome&gt;,&lt;valor&gt;**

Este comando altera uma configuração de config.

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

## <a name="commands-to-manage-service-bus"></a>Comandos para gerenciar barramento de serviço

Use estes comandos para gerenciar sua conta do barramento de serviço

**seleção de namespace SB [Opções] &lt;nome >**

Verifique se um namespace de barramento de serviço está disponível e legal.

**Criar SB namespace &lt;nome > &lt;local >**

Cria um namespace barramento de serviço.

    ~$ azure sb namespace create mysbnamespacea-test "West US"
    info:    Executing command sb namespace create
    + Creating namespace mysbnamespacea-test in region West US
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Activating
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    _: [object Object]
    info:    sb namespace create command OK


**Excluir SB namespace &lt;nome >**

Remova um namespace.

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**lista de namespace SB**

Liste todos os namespaces criados para sua conta.

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK


**lista de locais de namespace SB**

Exiba uma lista de todos os locais de namespace disponíveis.

    ~$ azure sb namespace location list
    info:    Executing command sb namespace location list
    + Getting locations
    data:    Name              Code
    data:    ----------------  ----------------
    data:    East Asia         East Asia
    data:    West Europe       West Europe
    data:    North Europe      North Europe
    data:    East US           East US
    data:    Southeast Asia    Southeast Asia
    data:    North Central US  North Central US
    data:    West US           West US
    data:    South Central US  South Central US
    info:    sb namespace location list command OK

**SB namespace Mostrar &lt;nome >**

Exibir detalhes sobre um namespace específico.

    ~$ azure sb namespace show mysbnamespacea-test
    info:    Executing command sb namespace show
    + Getting namespace
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Active
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    UpdatedAt: 2013-11-14T16:25:37.85Z
    info:    sb namespace show command OK

**verificar o namespace SB &lt;nome >**

Verifique se o namespace está disponível.

## <a name="commands-to-manage-your-storage-objects"></a>Comandos para gerenciar os objetos de armazenamento

###<a name="commands-to-manage-your-storage-accounts"></a>Comandos para gerenciar suas contas de armazenamento

**lista de contas de armazenamento [Opções]**

Este comando exibe as contas de armazenamento em sua assinatura.

    ~$ azure storage account list
    info:    Executing command storage account list
    + Getting storage accounts
    data:    Name             Label  Location
    data:    ---------------  -----  --------
    data:    mybasestorage           West US
    info:    storage account list command OK

**apresentação de conta de armazenamento [Opções]<name>**

Este comando exibe informações sobre a conta de armazenamento especificado, incluindo as propriedades URI e conta.

**conta de armazenamento criar [Opções]<name>**

Esse comando cria uma conta de armazenamento com base nas opções fornecidas.

    ~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK

Este comando suporta as seguintes opções adicionais:

+ **-eletrônico** ou **-rótulo** &lt;rótulo >: O rótulo para a conta de armazenamento.
+ **-d** ou **-Descrição** &lt;descrição >: A conta de armazenamento de descrição.
+ **-l** ou **-local** &lt;nome >: A região geográfica no qual deseja criar a conta de armazenamento.
+ **-a** ou **-grupo de afinidade** &lt;nome >: O grupo de afinidade ao qual deseja associar a conta de armazenamento. 
+ **-tipo**: indica o tipo de conta para criar: o armazenamento padrão com opção de redundância (LRS/ZRS/GRS/RAGRS) ou Premium armazenamento (PLRS).

**conta de armazenamento definida [Opções]<name>**

Este comando atualiza a conta de armazenamento especificado.

    ~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
    info:    Executing command storage account set
    + Updating storage account
    info:    storage account set command OK

Este comando suporta as seguintes opções adicionais:

+ **-eletrônico** ou **-rótulo** &lt;rótulo >: O rótulo para a conta de armazenamento.
+ **-d** ou **-Descrição** &lt;descrição >: A conta de armazenamento de descrição.
+ **-l** ou **-local** &lt;nome >: A região geográfica no qual deseja criar a conta de armazenamento.
+ **-tipo**: indica o novo tipo de conta: o armazenamento padrão com opção de redundância (LRS/ZRS/GRS/RAGRS) ou Premium armazenamento (PLRS).

**Excluir conta de armazenamento [Opções]<name>**

Este comando exclui a conta de armazenamento especificado.

Este comando suporta as seguintes opções adicionais:

**-p** ou **-silencioso**: não solicitar confirmação. Use esta opção em scripts automatizados.

###<a name="commands-to-manage-your-storage-account-keys"></a>Comandos para gerenciar suas chaves de conta de armazenamento

**teclas de conta de armazenamento listam [Opções]<name>**

Este comando lista as chaves primárias e secundárias da conta de armazenamento especificado.

**teclas de conta de armazenamento renovar [Opções]<name>**

###<a name="commands-to-manage-your-storage-container"></a>Comandos para gerenciar seu contêiner de armazenamento

**lista de contêiner de armazenamento [Opções] [prefixo]**

Este comando exibe a lista de contêiner de armazenamento para uma conta de armazenamento especificado. A conta de armazenamento está especificada pela cadeia de conexão ou a chave de nome e uma conta de conta do armazenamento.

Este comando suporta as seguintes opções adicionais:

+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contêiner de armazenamento.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento.
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento.
+ **-Depurar**: executa o comando de armazenamento no modo de depuração.

**apresentação de contêiner de armazenamento [Opções] [contêiner]**
**contêiner de armazenamento criar [Opções] [contêiner]**

Esse comando cria um contêiner de armazenamento para a conta de armazenamento especificado. A conta de armazenamento é especificada pela cadeia de conexão ou a chave de nome e uma conta de conta do armazenamento.

Este comando suporta as seguintes opções adicionais:

+ **– contêiner** &lt;contêiner >: O nome do contêiner de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contêiner de armazenamento.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento
+ **-Depurar**: executa o comando de armazenamento no modo de depuração.

**Excluir contêiner de armazenamento [Opções] [contêiner]**

Este comando exclui o contêiner de armazenamento especificado. A conta de armazenamento é especificada pela cadeia de conexão ou a chave de nome e uma conta de conta do armazenamento.

Este comando suporta as seguintes opções adicionais:

+ **– contêiner** &lt;contêiner >: O nome do contêiner de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contêiner de armazenamento.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento.
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento.
+ **-Depurar**: executa o comando de armazenamento no modo de depuração.

**conjunto de contêiner de armazenamento [Opções] [contêiner]**

Este comando define a lista de controle de acesso para o contêiner de armazenamento. A conta de armazenamento é especificada pela cadeia de conexão ou a chave de nome e uma conta de conta do armazenamento.

Este comando suporta as seguintes opções adicionais:

+ **– contêiner** &lt;contêiner >: O nome do contêiner de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contêiner de armazenamento.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento.
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento.
+ **-Depurar**: executa o comando de armazenamento no modo de depuração.

###<a name="commands-to-manage-your-storage-blob"></a>Comandos para gerenciar seu blob Storage

**lista armazenamento de blob [Opções] [contêiner] [prefixo]**

Este comando retorna uma lista dos blobs armazenamento no contêiner de armazenamento especificado.

Este comando suporta as seguintes opções adicionais:

+ **– contêiner** &lt;contêiner >: O nome do contêiner de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contêiner de armazenamento.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento.
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento.
+ **-Depurar**: executa o comando de armazenamento no modo de depuração.

**blob Storage Mostrar [Opções] [contêiner] [blob]**

Este comando exibe os detalhes do blob armazenamento especificado.

Este comando suporta as seguintes opções adicionais:

+ **– contêiner** &lt;contêiner >: O nome do contêiner de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contêiner de armazenamento.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento.
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento.
+ **-Depurar**: executa o comando de armazenamento em depuração.

**blob Storage excluir [Opções] [contêiner] [blob]**

Este comando suporta as seguintes opções adicionais:

+ **– contêiner** &lt;contêiner >: O nome do contêiner de armazenamento para criar.
+ **-b** ou **-blob** &lt;blobName >: O nome do blob storage excluir.
+ **-p** ou **-silencioso**: remover o blob Storage especificado sem confirmação.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento.
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento.
+ **-Depurar**: executa o comando de armazenamento em depuração.

**carregamento de blob Storage [Opções] [arquivo] [contêiner] [blob]**

Este comando carregar o arquivo especificado para o blob de armazenamento specified\.

Este comando suporta as seguintes opções adicionais:

+ **– contêiner** &lt;contêiner >: O nome do contêiner de armazenamento para criar.
+ **-b** ou **-blob** &lt;blobName >: O nome do blob storage para carregar.
+ **-t** ou **- blobtype** &lt;blobtype >: O tipo de armazenamento do blob: página ou bloco.
+ **-p** ou **-Propriedades** &lt;Propriedades >: as propriedades de blob do armazenamento de arquivo carregado. Propriedades são chave = valor par s e separados com semicolon(;). Propriedades disponíveis são contentType, contentEncoding, contentLanguage e cacheControl.
+ **-m** ou **- metadados** &lt;metadados >: os metadados de blob do armazenamento de arquivo carregado. Metadados são fundamentais = pares de valor um d separada por ponto e vírgula (;).
+ **-concurrenttaskcount** &lt;concurrenttaskcount >: O número máximo de solicitações simultâneas de carregamento.
+ **-p** ou **-silencioso**: substituir o blob Storage especificado sem confirmação.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento.
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento.
+ **-Depurar**: executa o comando de armazenamento em depuração.

**download de blob Storage [Opções] [contêiner] [blob] [destino]**

Este comando baixa o blob de armazenamento especificado.

Este comando suporta as seguintes opções adicionais:

+ **– contêiner** &lt;contêiner >: O nome do contêiner de armazenamento para criar.
+ **-b** ou **-blob** &lt;blobName >: O nome de blob de armazenamento.
+ **-d** ou **-destino** [destino]: O caminho de arquivo ou pasta de destino do download.
+ **-m** ou **– checkmd5**: md5sum a seleção para o arquivo baixado.
+ **-concurrenttaskcount** &lt;concurrenttaskcount > o número máximo de solicitações simultâneas de carregamento
+ **-p** ou **-silencioso**: substituir o arquivo de destino sem confirmação.
+ **-a** ou **-nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **k** ou **-chave da conta** &lt;accountKey >: A chave da conta de armazenamento.
+ **-c** ou **– cadeia de conexão** &lt;connectionString >: A cadeia de conexão de armazenamento.
+ **-Depurar**: executa o comando de armazenamento em depuração.

## <a name="commands-to-manage-sql-databases"></a>Comandos para gerenciar bancos de dados SQL

Use esses comandos para gerenciar seus bancos de dados do SQL Azure

###<a name="commands-to-manage-sql-servers"></a>Comandos para gerenciar servidores SQL.

Use esses comandos para gerenciar seus servidores SQL

**SQL server criar &lt;administratorLogin > &lt;administratorPassword > &lt;local >**

Criar um servidor de banco de dados

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**Mostrar do SQL server &lt;nome >**

Exibir detalhes do servidor.

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**lista do SQL server**

Obter a lista de servidores.

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**exclusão do SQL server &lt;nome >**

Exclui um servidor

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

###<a name="commands-to-manage-sql-databases"></a>Comandos para gerenciar bancos de dados SQL

Use esses comandos para gerenciar seus bancos de dados do SQL.

**banco de dados do SQL criar [Opções] &lt;NomeServidor > &lt;databaseName > &lt;administratorPassword >**

Cria uma instância de banco de dados

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**banco de dados do SQL Mostrar [Opções] &lt;NomeServidor > &lt;databaseName > &lt;administratorPassword >**

Exibir detalhes do banco de dados.

    C:\windows\system32>azure sql db show fr8aelne00 newdb test
    info:    Executing command sql db show
    Administrator password: ********
    + Getting SQL server databases
    data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
    ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
    m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
    icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
    ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
    ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
    /Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
    ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
    Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
    services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
    tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
    om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
    pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
    d=2013-11-18T19:48:27Z, name=
    data:    Database Id 4
    data:    Database Name newdb
    data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database ServiceObjectiveAssignmentState 1
    data:    Database ServiceObjectiveAssignmentStateDescription Complete
    data:    Database ServiceObjectiveAssignmentErrorCode
    data:    Database ServiceObjectiveAssignmentErrorDescription
    data:    Database ServiceObjectiveAssignmentSuccessDate
    data:    Database Edition Web
    data:    Database MaxSizeGB 1
    data:    Database MaxSizeBytes 1073741824
    data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
    data:    Database CreationDate
    data:    Database RecoveryPeriodStartDate
    data:    Database IsSystemObject
    data:    Database Status 1
    data:    Database IsFederationRoot
    data:    Database SizeMB -1
    data:    Database IsRecursiveTriggersOn
    data:    Database IsReadOnly
    data:    Database IsFederationMember
    data:    Database IsQueryStoreOn
    data:    Database IsQueryStoreReadOnly
    data:    Database QueryStoreMaxSizeMB
    data:    Database QueryStoreFlushPeriodSeconds
    data:    Database QueryStoreIntervalLengthMinutes
    data:    Database QueryStoreClearAll
    data:    Database QueryStoreStaleQueryThresholdDays
    info:    sql db show command OK

**lista de banco de dados do SQL [Opções] &lt;NomeServidor > &lt;administratorPassword >**

Liste os bancos de dados.

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**banco de dados do SQL excluir [Opções] &lt;NomeServidor > &lt;databaseName > &lt;administratorPassword >**

Exclui um banco de dados.

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

###<a name="commands-to-manage-your-sql-server-firewall-rules"></a>Comandos para gerenciar suas regras de firewall do SQL Server

Use estes comandos para gerenciar suas regras de firewall do SQL Server

**SQL firewallrule criar [Opções] &lt;NomeServidor > &lt;nome_da_regra > &lt;startIPAddress > &lt;endIPAddress >**

Crie uma regra de firewall para um SQL Server.

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**SQL firewallrule Mostrar [Opções] &lt;NomeServidor > &lt;nome_da_regra >**

Mostrar detalhes da regra de firewall.

    ~$ azure sql firewallrule show fr8aelne00 allowed
    info:    Executing command sql firewallrule show
    + Getting firewall rule
    data:    Firewall rule Name allowed
    data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
    data:    Firewall rule State Normal
    data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
    5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
    data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
    055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
    data:    Firewall rule StartIPAddress 131.107.0.0
    data:    Firewall rule EndIPAddress 131.107.255.255
    info:    sql firewallrule show command OK

**lista de firewallrule SQL [Opções] &lt;NomeServidor >**

Liste as regras de firewall.

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**SQL firewallrule excluir [Opções] &lt;NomeServidor > &lt;nome_da_regra >**

Este comando exclui uma regra de firewall.

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

## <a name="commands-to-manage-your-virtual-networks"></a>Comandos para gerenciar as redes virtuais

Use estes comandos para gerenciar as redes virtuais

**rede vnet criar [Opções] &lt;local >**

Crie uma rede Virtual.

    ~$ azure network vnet create vnet1 --location "West US" -v
    info:    Executing command network vnet create
    info:    Using default address space start IP: 10.0.0.0
    info:    Using default address space cidr: 8
    info:    Using default subnet start IP: 10.0.0.0
    info:    Using default subnet cidr: 11
    verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
    verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
    verbose: Fetching Network Configuration
    verbose: Fetching or creating affinity group
    verbose: Fetching Affinity Groups
    verbose: Fetching Locations
    verbose: Creating new affinity group AG1
    info:    Using affinity group AG1
    verbose: Updating Network Configuration
    info:    network vnet create command OK

**Mostrar vnet de rede &lt;nome >**

Mostra detalhes de uma rede Virtual.

    ~$ azure network vnet show vnet1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "vnet1"
    data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
    data:    AffinityGroup "AG1"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
    data:    Subnets 0 Name "subnet-1"
    data:    Subnets 0 AddressPrefix "10.0.0.0/11"
    info:    network vnet show command OK

**lista de vnet de rede**

Lista todas as redes virtuais existentes.

    ~$ azure network vnet list
    info:    Executing command network vnet list
    + Fetching Virtual Networks
    data:    Name        Status   AffinityGroup
    data:    ----------  -------  -------------
    data:    vnet1      Created  AG1
    data:    vnet2      Created  AG1
    data:    vnet3      Created  AG1
    data:    vnet4      Created  AG1
    info:    network vnet list command OK


**Excluir rede vnet &lt;nome >**

Exclui a rede Virtual especificada.

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ?  (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**exportação de rede [caminho do arquivo]**

Para configuração de rede avançadas, você pode exportar sua configuração de rede localmente. A configuração de rede exportado inclui configurações do servidor DNS, configurações de rede virtual, configurações de site de rede local e outras configurações.

**Importar de rede [caminho do arquivo]**

Importe uma configuração de rede local.

**rede ServidorDNS registrar [Opções] &lt;dnsIP >**

Registre um servidor DNS que você planeja usar para resolução de nome em sua configuração de rede.

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**lista de ServidorDNS de rede**

Liste todos os servidores DNS registrados em sua configuração de rede.

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**rede ServidorDNS cancelar o Registro [Opções] &lt;dnsIP >**

Remove uma entrada de servidor DNS a configuração de rede.

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK

