<properties
   pageTitle="Implementando ADFS no Azure | Microsoft Azure"
   description="Como implementar uma arquitetura de rede de seguro híbrida com autorização de serviço de Federação do Active Directory no Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/13/2016"
   ms.author="telmos"/>

# <a name="implementing-active-directory-federation-services-adfs-in-azure"></a>Implementando serviços de Federação do Active Directory (AD FS) no Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as práticas recomendadas para implementação de uma rede de seguro híbrido que estende sua rede local para o Azure e que usa [Serviços de Federação do Active Directory (ADFS)] [ active-directory-federation-services] para executar autenticação federada e a autorização para componentes em execução na nuvem. Essa arquitetura estende a estrutura descrita por [Estendendo Active Directory do Azure][implementing-active-directory].

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

ADFS pode executar no local, mas em um cenário de híbrido onde os aplicativos estão localizados no Azure pode ser mais eficiente para implementar essa funcionalidade na nuvem. Casos de uso típico para essa arquitetura incluem:

- Aplicativos híbridos onde executar cargas de trabalho parcialmente local e parcialmente no Azure.

- Soluções que utilizam federada autorização para expor aplicativos da web para organizações de parceiros.

- Sistemas que permitem o acesso de navegadores da web em execução fora da firewall organizacional.

- Sistemas que permitem aos usuários acesso aos aplicativos web conectando-se autorizados dispositivos externos como computadores remotos, notebooks e outros dispositivos móveis. 

Para obter mais informações sobre o funcionamento do ADFS, consulte [Visão geral do Active Directory Federation Services][active-directory-federation-services-overview]. Além disso, o artigo de [implantação do ADFS no Azure] [ adfs-intro] contém uma introdução passo a passo detalhada para implementar o ADFS no Azure.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir destaca os componentes importantes nesta arquitetura (*clique para ampliar*). Para saber mais sobre qualquer elemento não relacionado ao ADFS, leia [implementar uma arquitetura de rede de seguro híbrido no Azure][implementing-a-secure-hybrid-network-architecture], [implementar uma arquitetura de rede de seguro híbrida com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]e [implementar uma arquitetura de rede de seguro híbrida com identidades do Active Directory no Azure][implementing-active-directory]:

[! [0]][0]

>[AZURE.NOTE] Este diagrama representa os seguintes casos de uso:
>
>- Código de aplicativo em execução dentro de uma organização parceira acessa um aplicativo web hospedado dentro de sua VNet do Azure.
>
>- Um usuário externo, registrado (com credenciais armazenadas dentro ADICIONA) acessando um aplicativo web hospedado dentro de sua VNet do Azure.
>
>- Um usuário se conectando ao seu VNet usando um dispositivo autorizado e executando um aplicativo web hospedado dentro de sua VNet do Azure.
>
>Além disso, essa arquitetura se concentra em federação passiva, onde os servidores de Federação decidir como e quando para autenticar um usuário. O usuário deve fornecer informações de entrada quando um aplicativo começa a ser executado. Este é o mecanismo mais comumente usado por navegadores da web e envolve um protocolo que redireciona o navegador para um site onde o usuário pode fornecer suas credenciais. ADFS também dá suporte à federação ativa por meio da qual um aplicativo assume responsabilidade para fornecer credenciais sem maior interação do usuário, mas neste caso está fora do escopo dessa arquitetura.

- **ADICIONA sub-rede.** Os servidores de ADICIONA estão contidos em sua própria sub-rede. Regras NSG ajudam a proteger os servidores de ADICIONA e podem fornecer um firewall contra tráfego de fontes inesperadas.

- **ADICIONA servidores.** Estas são controladores de domínio executando como VMs na nuvem. Esses servidores podem fornecer autenticação de identidades locais dentro do domínio.

- **Sub-rede do ADFS.** Os servidores ADFS podem ser localizados em sua própria sub-rede, com regras NSG atuando como um firewall.

- **Servidores do ADFS.** Os servidores ADFS fornecem autenticação e autorização federada. Nesta arquitetura, eles executarem as seguintes tarefas:

    - Eles podem receber tokens de segurança contendo declarações feitas por um servidor de Federação do parceiro em nome de um usuário de parceiro. ADFS pode verificar se esses tokens são válidos antes de passar as declarações ao aplicativo web em execução no Azure. O aplicativo da web corporativo (no Azure) pode usar essas declarações para autorizar solicitações. Neste cenário, o aplicativo da web corporativo é a *terceira parte confiável*e é responsabilidade do servidor de federação de parceiro para emitir declarações que são compreendidos pelo aplicativo web corporativo. Os servidores de Federação do parceiro são chamados de *parceiros de conta* porque eles enviarem solicitações de acesso em nome de contas autenticadas na organização do parceiro. Os servidores ADFS são chamados *parceiros de recurso* porque eles fornecem acesso aos recursos (aplicativos da web, nesse caso).

    - Eles podem autenticar (via ADICIONA e o [Serviço de registro de dispositivo do Active Directory][ADDRS]) e autorizar solicitações de entrada de usuários externos executando um navegador da web ou dispositivo que precise acessar seus aplicativos da web corporativo. 

    Os servidores ADFS são configurados como um farm, acessado por meio de um balanceador de carga Azure. Essa estrutura ajuda a melhorar a disponibilidade e escalabilidade. Além disso, observe que os servidores ADFS não estão expostos diretamente à Internet, em vez disso, todo o tráfego Internet é filtrado por meio de servidores de proxy de aplicativo do ADFS da web e DMZ.

- **Sub-rede de proxy do AD FS.** Os servidores de proxy do AD FS podem estar contidos em sua própria sub-rede, com regras NSG fornecendo proteção. Os servidores nesta sub-rede estão expostos na Internet por meio de um conjunto de dispositivos virtuais de rede que fornecem um firewall entre sua rede virtual Azure e a Internet.

- **Servidores proxy (WAP) do ADFS web application.** Esses computadores atuam como servidores ADFS para solicitações de entrada de organizações de parceiros e dispositivos externos. Os servidores WAP atuam como um filtro, protege os servidores ADFS do acesso direto da Internet pública. Como com os servidores ADFS, implantando WAP servidores em um farm com balanceamento de carga oferece maior disponibilidade e escalabilidade que a implantação de um conjunto de servidores autônomos.

    >[AZURE.NOTE] Para obter informações detalhadas sobre como instalar servidores WAP, consulte [instalar e configurar o servidor de Proxy de aplicativo Web][install_and_configure_the_web_application_proxy_server]

- **Organização de parceiro.** Este é um exemplo da organização do parceiro, que executa um aplicativo web que solicita acesso ao aplicativo da web em execução no Azure. O servidor de Federação da organização parceira autentica solicitações localmente e envia tokens de segurança contendo declarações para ADFS em execução no Azure. ADFS no Azure valida os tokens de segurança e se eles forem válidos, ele pode passar as declarações ao aplicativo da web em execução no Azure para autorizá-los.

    >[AZURE.NOTE] Você também pode configurar um túnel VPN usando o Gateway do Azure para fornecer acesso direto aos ADFS para parceiros confiáveis. Solicitações recebidas desses parceiros não passam os servidores WAP.

## <a name="recommendations"></a>Recomendações

Esta seção resume as recomendações para implementar ADFS no Azure, abrangendo:

- Recomendações de máquina virtual.

- Recomendações de rede.

- Recomendações de disponibilidade.

- Recomendações de segurança.

- Recomendações de instalação do ADFS.

- Recomendações de confiança do ADFS.

### <a name="vm-recommendations"></a>Recomendações de máquina virtual

Crie VMs com recursos suficientes para lidar com o volume esperado de tráfego. Use o tamanho das máquinas existentes ADFS no local como ponto de partida de hospedagem. Monitore a utilização de recursos. Você pode redimensionar as VMs e dimensionar para baixo se forem muito grandes.

Siga as recomendações listadas no [executando uma máquina de virtual do Windows no Azure][vm-recommendations].

### <a name="networking-recommendations"></a>Recomendações de rede

Configure a interface de rede para cada uma das VMs servidores ADFS e WAP com endereços IP particulares estáticos de hospedagem.

Não forneça os endereços IP públicos do ADFS VMs. Para obter mais informações, consulte [Considerações sobre segurança][security-considerations].

Defina o endereço IP dos servidores DNS preferenciais e secundários para as interfaces de rede para cada parte do ADFS e WAP VM referido VMs ADICIONA (que deve estar executando o DNS). Essa etapa é necessária para habilitar cada máquina virtual para ingressar no domínio.

### <a name="availability-recommendations"></a>Recomendações de disponibilidade

Crie um farm ADFS com pelo menos dois servidores para aumentar a disponibilidade do serviço ADFS.

Use contas de armazenamento diferentes para cada VM ADFS no farm. Essa abordagem ajuda a garantir que uma falha em uma conta de armazenamento único não faça toda a farm inacessível.

Crie conjuntos de disponibilidade Azure separada para o ADFS e WAP VMs. Certifique-se de que há pelo menos duas VMs em cada conjunto. Cada conjunto de disponibilidade deve ter pelo menos dois domínios de atualização e duas falhas.

Configure os balanceadores de carga para as VMs do ADFS e WAP VMs da seguinte maneira:

- Use um balanceador de carga Azure para fornecer acesso externo para VMs WAP e um balanceador de carga interno para distribuir a carga entre os servidores ADFS no farm ADFS.

- Só passe tráfego que aparecem na porta 443 (HTTPS) aos servidores do ADFS/WAP.

- Dê balanceador de carga de um endereço IP estático.

- Crie um teste de integridade usando o protocolo TCP em vez de HTTPS. Você pode executar ping porta 443 para verificar se um servidor ADFS está funcionando.

    >[AZURE.NOTE] Servidores ADFS usam o protocolo de indicação de nome de servidor (SNI), então a tentativa de teste usando um ponto de extremidade HTTPS da falha de Balanceador de carga.

- Adicione um registro DNS *A* para o domínio para o balanceador de carga do ADFS. 

    Especifique o endereço IP do balanceador de carga e dê um nome de domínio (como adfs.contoso.com). Este é o nome pelo qual clientes e os servidores WAP acessar o farm de servidores do ADFS.

### <a name="security-recommendations"></a>Recomendações de segurança

Evitar exposição direta dos servidores ADFS à Internet. Servidores ADFS são computadores domínio tem autorização completa para conceder tokens de segurança. Se um servidor ADFS for comprometido, um usuário mal-intencionado pode emitir tokens de acesso total a todos os aplicativos web e servidores de Federação protegidos por ADFS. Se seu sistema deve lidar com solicitações de usuários externos não necessariamente conectando dos sites de parceiros confiáveis, use os servidores WAP lidar com essas solicitações. Para obter mais informações, consulte [onde colocar um Proxy de servidor de Federação][where-to-place-an-fs-proxy].

Servidores local ADFS e servidores WAP sub-redes separadas com seus próprios firewalls. Você pode usar regras NSG para definir regras de firewall. Se você precisar de proteção mais abrangente que você pode implementar um perímetro de segurança adicional em torno de servidores usando um par de sub-redes e NVAs, conforme descrito pelo documento [implementar uma arquitetura de rede de seguro híbrida com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Observe que todos os firewalls devem permitir o tráfego na porta 443 (HTTPS).

Restringir o acesso de login direto aos servidores do ADFS e WAP. Somente DevOps equipe deve ser capaz de se conectar.

Os servidores WAP não ingresse no domínio.

### <a name="adfs-installation-recommendations"></a>Recomendações de instalação do ADFS

O artigo [Implantando um Farm de servidores de Federação] [ Deploying_a_federation_server_farm] fornece instruções detalhadas para instalar e configurar o ADFS. Execute as seguintes tarefas antes de configurar o primeiro servidor ADFS no farm:

1. Obter um certificado confiável publicamente para executar a autenticação de servidor. O *nome de assunto* deve conter o nome pelo qual clientes acessam o serviço de Federação. Isso pode ser o nome DNS registrado para o balanceador de carga, por exemplo, *adfs.contoso.com* (Evite usar nomes de curinga, como **. contoso.com*, por razões de segurança). Use o mesmo certificado em todas as VMs de servidor ADFS. Você pode adquirir um certificado de uma autoridade de certificação confiável, mas se sua organização usa os serviços de certificado do Active Directory que você pode criar seus próprios. 

    O *nome de entidade alternativo* é usado por o DRS para habilitar o acesso de dispositivos externos. Isso deve ser o formulário *enterpriseregistration.contoso.com*.

    Para obter mais informações, consulte [obter e configurar um certificado SSL para ADFS][adfs_certificates].

2. No controlador de domínio, gere uma nova chave raiz para o serviço de distribuição de chave. Defina a hora eficaz para ser a hora atual menos 10 horas (esta configuração reduz o atraso que pode ocorrer em distribuir e sincronizando chaves em todo o domínio). Essa etapa é necessária para dar suporte a criar a conta de serviço de grupo que são usados para executar o serviço do ADFS. O seguinte comando do Powershell mostra um exemplo de como fazer isso:

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. Adicione cada servidor ADFS máquina virtual para o domínio.

>[AZURE.NOTE] Para instalar o ADFS, controlador de domínio executando o emulador PDC função FSMO do domínio deve ser executadas e acessíveis a partir do VMs ADFS.

### <a name="adfs-trust-recommendations"></a>Recomendações de confiança do ADFS

Estabelece confiança de federação entre sua instalação do ADFS e os servidores de federação de qualquer organizações de parceiro. Configure as declarações filtragem e mapeamento obrigatório. Esse processo requer:

- DevOps equipe **cada organização parceira** para adicionar uma relação de confiança de terceiros confiante para os aplicativos web acessados por meio de seus servidores do ADFS.

- DevOps equipe **em sua organização** para configurar confiança de provedor de declarações para permitir que seus servidores ADFS confiar as declarações que fornecem organizações de parceiros.

- DevOps equipe **em sua organização** para configurar ADFS para passar declarações para aplicativos da web da sua organização.

    Para obter mais informações, consulte [Estabelecendo confiança de Federação][establishing-federation-trust].

Publicar aplicativos da web da sua organização e disponibilizá-los para parceiros externos usando autenticação prévia através dos servidores WAP. Para obter mais informações, consulte [publicar aplicativos usando ADFS pré-autenticação][publish_applications_using_AD_FS_preauthentication]

Observe que o ADFS dá suporte aumento e transformação token. Active Directory do Azure não oferece esse recurso. Com o ADFS, quando você configura as relações de confiança, você pode:

- Configure transformações de declaração para regras de autorização. Por exemplo, você pode mapear segurança de grupo de uma representação usada por uma organização de parceiro não sejam da Microsoft para algo que ADICIONA pode autorizar em sua organização.

- Transforme declarações de um formato para outro. Por exemplo, você poderá mapear do SAML 2.0 para SAML 1.1 se seu aplicativo suporta somente declarações de SAML 1.1. 

## <a name="availability-considerations"></a>Considerações de disponibilidade

Você pode usar o SQL Server ou o banco de dados interno de Windows (trabalho) para armazenar informações de configuração do ADFS. Trabalho fornece redundância básica. As alterações são gravadas diretamente apenas um banco de dados ADFS no cluster ADFS, enquanto outros servidores usam replicação de recepção para manter seus bancos de dados atualizados. Usando o SQL Server pode fornecer redundância de banco de dados completo e alta disponibilidade usando failover cluster ou espelhamento.

## <a name="security-considerations"></a>Considerações de segurança

ADFS utiliza o protocolo HTTPS, então certifique-se de que as regras NSG para a sub-rede que contém a web nível VMs solicitações HTTPS de permissão. Essas solicitações podem se originar da rede local, as sub-redes contendo a camada da web, camada de negócios, camada de dados, DMZ particular, DMZ público e a sub-rede que contém os servidores ADFS.

Considere usar um conjunto de dispositivos virtuais de rede que registra informações detalhadas sobre tráfego percorrer a borda da sua rede virtual para fins de auditoria.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

As seguintes considerações, resumidas do documento [planejar sua implantação do ADFS][plan-your-adfs-deployment], dar um ponto de partida para farms ADFS de dimensionamento:

- Se você tiver menos de 1.000 usuários, não crie servidores ADFS dedicados, mas em vez disso, instalar ADFS em cada um dos servidores ADICIONA na nuvem (certifique-se de que você tenha pelo menos dois servidores ADICIONA, para manter a disponibilidade). Crie um único servidor WAP.

- Se você tiver entre 1000 e 15000 usuários, crie dois servidores dedicados do ADFS e dois servidores WAP dedicados.

- Se você tiver entre 15000 e 60000 usuários, crie entre três e cinco servidores ADFS dedicados e pelo menos dois servidores WAP dedicados.

Esses números presumem que você está usando duplas VMs quatro-core (D4_v2 padrão, ou melhor) para hospedar os servidores no Azure.

Observe que, se você estiver usando o banco de dados interno do Windows para armazenar dados de configuração do ADFS, você está limitado a oito servidores ADFS no farm. Se você prevê precisar de mais, em seguida, use o SQL Server. Para obter mais informações, consulte [A função do banco de dados de configuração do ADFS][adfs-configuration-database].

## <a name="management-considerations"></a>Considerações de gerenciamento

DevOps equipe deve estar preparado para executar as seguintes tarefas:

- Gerenciando os servidores de Federação (Gerenciando o farm ADFS, gerenciar diretiva de confiança nos servidores de Federação e gerenciando os certificados usados pelos serviços de federação).

- Gerenciando os servidores WAP (Gerenciando o farm de WAP, gerenciando os certificados WAP).

- Gerenciando aplicativos da web (Configurando partes confiantes, métodos de autenticação e os mapeamentos de declarações).

- Fazer backup de componentes do ADFS.

## <a name="monitoring-considerations"></a>Considerações de monitoramento

O [Microsoft System Center Management Pack para o Active Directory Federation Services 2012 R2] [ oms-adfs-pack] fornece proativas e reativas monitoramento de sua implantação do ADFS para o servidor de Federação. Este pacote de gerenciamento monitora:

- Eventos que o ADFS serviço registros em logs de eventos do ADFS.

- Os dados de desempenho que coletam os contadores de desempenho do ADFS. 

- A integridade geral dos ADFS sistema e web aplicativos (partes confiáveis) e fornece alertas para problemas críticos e avisos.

## <a name="solution-components"></a>Componentes de solução

Um script de solução de amostra, [Implantar-ReferenceArchitecture.ps1][solution-script], está disponível que você pode usar para implementar a arquitetura que segue as recomendações descritas neste artigo. Esse script utiliza modelos do Gerenciador de recursos do Azure. Os modelos estão disponíveis como um conjunto de blocos de construção fundamentais, cada uma das quais executa uma ação específica como criar um VNet ou configurar um NSG. A finalidade do script é coordenar implantação do modelo.

Os modelos são parametrizados, com os parâmetros contidos em arquivos JSON separados. Você pode modificar os parâmetros nesses arquivos para configurar a implantação para atender às suas próprias necessidades. Você não precisa modificar os modelos em si. Observe que você não deve alterar os esquemas dos objetos em arquivos de parâmetro.

Quando você edita os modelos, criar objetos que seguem as convenções de nomenclatura descritas em [Recomendados convenções de nomenclatura para recursos do Azure][naming-conventions].

A solução de exemplo cria e configura o ambiente na nuvem que compõem o DMZ de sub-rede e servidores, a sub-rede do ADFS e servidores, sub-rede de proxy do ADFS e, ADICIONA, camada da web, camada de negócios e componentes de nível de acesso de dados, gateway VPN e nível de gerenciamento. A solução de exemplo também inclui uma configuração opcional para a criação de um ambiente local simulada.

As seções a seguir descrevem os elementos as locais e na nuvem configurações.

### <a name="on-premises-components"></a>Componentes de local

>[AZURE.NOTE] Esses componentes não são o foco principal da arquitetura descrito neste documento e são fornecidos simplesmente para dar a você uma oportunidade para testar o ambiente de nuvem com segurança, em vez de usar um ambiente de produção real. Por esse motivo, esta seção resume apenas os arquivos de parâmetro chave. Você pode modificar configurações como os endereços IP ou os tamanhos das VMs, mas é aconselhável deixar muitos dos outros parâmetros inalterados.

Esse ambiente consiste em uma floresta do AD para um domínio denominado contoso.com. O domínio contém dois servidores ADICIONA com endereços IP 192.168.0.4 e 192.168.0.5. Esses dois servidores também executam o serviço DNS. A conta de administrador local em ambas as VMs é chamada `testuser` com senha `AweS0me@PW`. Além disso, a configuração configura um gateway VPN para conexão com a VNet na nuvem. Você pode modificar a configuração editando os seguintes arquivos JSON localizados nos [**parâmetros/onpremise**] [ on-premises-folder] pasta:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Esse arquivo define o espaço de endereço de rede para o ambiente local.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Este arquivo contém a configuração para VMs local ADICIONA serviços de hospedagem. Por padrão, duas VMs *padrão-DS3-v2* são criadas.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** e ** [connection.parameters.json][on-premises-connection-parameters]**. Esses arquivos mantenha as configurações para a conexão VPN ao gateway VPN Azure na nuvem, incluindo a chave compartilhada a ser usado para proteger o tráfego percorrer o gateway.

Os arquivos restantes na pasta contêm as informações de configuração usadas para criar o domínio local usando este infraestrutura. Você pode usá-los para instalar ADICIONA, configuração de DNS, crie uma floresta e configurar os sites de replicação da floresta.

### <a name="cloud-components"></a>Componentes de nuvem

Esses componentes formam o núcleo dessa arquitetura. Os [**parâmetros/azure**] [ azure-folder] pasta contém os seguintes arquivos de parâmetro para configurar esses componentes:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Esse arquivo define a estrutura do VNet para VMs e outros componentes na nuvem. Ele inclui configurações, como o nome, espaço de endereço, sub-redes e os endereços de todos os servidores DNS necessários. Observe que os endereços DNS mostrados neste exemplo referência os endereços IP dos servidores DNS locais e também o servidor DNS Azure padrão. Modificar esses endereços para fazer referência a sua própria configuração DNS se você não estiver usando o ambiente do local de exemplo:

    ```json
    {
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "ra-adfs-network-rg",
                "addressPrefixes": [
                    "10.0.0.0/16"
                ],
                "subnets": [
                    {
                        "name": "dmz-private-in",
                        "addressPrefix": "10.0.0.0/27"
                    },
                    {
                        "name": "dmz-private-out",
                        "addressPrefix": "10.0.0.32/27"
                    },
                    {
                        "name": "dmz-public-in",
                        "addressPrefix": "10.0.0.64/27"
                    },
                    {
                        "name": "dmz-public-out",
                        "addressPrefix": "10.0.0.96/27"
                    },
                    {
                        "name": "mgmt",
                        "addressPrefix": "10.0.0.128/25"
                    },
                    {
                        "name": "GatewaySubnet",
                        "addressPrefix": "10.0.255.224/27"
                    },
                    {
                        "name": "web",
                        "addressPrefix": "10.0.1.0/24"
                    },
                    {
                        "name": "biz",
                        "addressPrefix": "10.0.2.0/24"
                    },
                    {
                        "name": "data",
                        "addressPrefix": "10.0.3.0/24"
                    },
                    {
                        "name": "adds",
                        "addressPrefix": "10.0.4.0/27"
                    },
                    {
                        "name": "adfs",
                        "addressPrefix": "10.0.5.0/27"
                    },
                    {
                        "name": "proxy",
                        "addressPrefix": "10.0.6.0/27"
                    }
                ],
                "dnsServers": [
                    "192.168.0.4",
                    "192.168.0.5",
                    "168.63.129.16"
                ]
            }
        }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. Este arquivo configura as VMs executadas ADICIONA na nuvem. A configuração consiste em duas VMs. Você deve alterar o nome de usuário de administrador e a senha no `virtualMachineSettings` seção e, opcionalmente, pode modificar o tamanho de máquina virtual para corresponder aos requisitos do domínio:

    Para obter mais informações, consulte [Estendendo Active Directory do Azure][extending-ad-to-azure].

    ```json
            "virtualMachinesSettings": {
                "value": {
                    "namePrefix": "ra-adfs-ad",
                    "computerNamePrefix": "aad",
                    "size": "Standard_DS3_v2",
                    "osType": "Windows",
                    "adminUsername": "testuser",
                    "adminPassword": "AweS0me@PW",
                    "osAuthenticationType": "password",
                    "nics": [
                        {
                            "isPublic": "false",
                            "subnetName": "adds",
                            "privateIPAllocationMethod": "Static",
                            "startingIPAddress": "10.0.4.4",
                            "enableIPForwarding": false,
                            "dnsServers": [
                            ],
                            "isPrimary": "true"
                        }
                    ],
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2012-R2-Datacenter",
                        "version": "latest"
                    },
                    "dataDisks": {
                        "count": 1,
                        "properties": {
                            "diskSizeGB": 127,
                            "caching": "None",
                            "createOption": "Empty"
                        }
                    },
                    "osDisk": {
                        "caching": "ReadWrite"
                    },
                    "extensions": [
                    ],
                    "availabilitySet": {
                        "useExistingAvailabilitySet": "No",
                        "name": "ra-adfs-as"
                    }
                }
            },
            "virtualNetworkSettings": {
                "value": {
                    "name": "ra-adfs-vnet",
                    "resourceGroup": "ra-adfs-network-rg"
                }
            },
            "buildingBlockSettings": {
                "value": {
                    "storageAccountsCount": 2,
                    "vmCount": 2,
                    "vmStartIndex": 1
                }
            }
        }
    ```

- ** [Adicionar-adiciona-domínio-controller.parameters.json][add-adds-domain-controller-parameters]**. Este arquivo contém as configurações para criar o domínio CONTOSO abrangendo os servidores ADICIONA. Ele usa extensões personalizadas que estabelecer o domínio e adicionar os servidores de ADICIONA a ele. A menos que você crie servidores ADICIONA adicionais (caso em que você deve adicioná-los para o `vms` matriz), alterar seus nomes padrão ou deseja criar um domínio com um nome diferente, você não precisa modificar esse arquivo.

- ** [loadBalancer-adfs.parameters.json] [loadbalancer-adfs-parameters] ** O arquivo contém dois conjuntos de parâmetros de configuração. O `virtualMachineSettings` seção define as VMs que hospedam o serviço ADFS na nuvem. Por padrão, o script cria dois desses VMs no mesmo conjunto de disponibilidade:

    ```json
        "virtualMachinesSettings": {
            "value": {
                "namePrefix": "ra-adfs-adfs",
                "computerNamePrefix": "adfs",
                "size": "Standard_DS1_v2",
                "osType": "windows",
                "adminUsername": "testuser",
                "adminPassword": "AweS0me@PW",
                "osAuthenticationType": "password",
                "nics": [
                    {
                        "isPublic": "false",
                        "subnetName": "adfs",
                        "privateIPAllocationMethod": "Static",
                        "startingIPAddress": "10.0.5.4",
                        "isPrimary": "true",
                        "enableIPForwarding": false,
                        "dnsServers": [ ]
                    }
                ],
                "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version": "latest"
                },
                "dataDisks": {
                    "count": 1,
                    "properties": {
                        "diskSizeGB": 128,
                        "caching": "None",
                        "createOption": "Empty"
                    }
                },
                "osDisk": {
                    "caching": "ReadWrite"
                },
                "extensions": [ ],
                "availabilitySet": {
                    "useExistingAvailabilitySet": "No",
                    "name": "ra-adfs-adfs-vm-as"
                }
            }
        }
        ...
        "buildingBlockSettings": {
            "value": {
                "storageAccountsCount": 2,
                "vmCount": 2,
                "vmStartIndex": 1
            }
        }
    ```

    O `loadBalancerSettings` seção fornece a descrição de Balanceador de carga para esses VMs. O balanceador de carga passa tráfego que aparece na porta 443 (HTTPS) para um ou outros das VMs:

    ```json
        "loadBalancerSettings": {
            "value": {
                "name": "ra-adfs-adfs-lb",
                "frontendIPConfigurations": [
                    {
                        "name": "ra-adfs-adfs-lb-fe",
                        "loadBalancerType": "internal",
                        "internalLoadBalancerSettings": {
                            "privateIPAddress": "10.0.5.30",
                            "subnetName": "adfs"
                        }
                    }
                ],
                "backendPools": [
                    {
                        "name": "ra-adfs-adfs-lb-bep",
                        "nicIndex": 0
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "https-rule",
                        "frontendPort": 443,
                        "backendPort": 443,
                        "protocol": "Tcp",
                        "backendPoolName": "ra-adfs-adfs-lb-bep",
                        "frontendIPConfigurationName": "ra-adfs-adfs-lb-fe",
                        "probeName": "https-probe",
                        "enableFloatingIP": false
                    }
                ],
                "probes": [
                    {
                        "name": "https-probe",
                        "port": 443,
                        "protocol": "Tcp",
                        "requestPath": null
                    }
                ],
                "inboundNatRules": [ ]
            }
        },
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "johns-adfs-network-rg"
            }
        }
    ```

- ** [adfs-farm-domínio-join.parameters.json ] [ adfs-farm-domain-join-parameters] **. Este arquivo contém as configurações usadas para adicionar os servidores ADFS no domínio CONTOSO. Você só precisa modificar esse arquivo, se você tiver criado servidores ADFS adicionais (atualizar o `vms` matriz nesse caso), ou você alterou o nome de domínio.

- ** [gmsa.parameters.json][gmsa-parameters]**, ** [adfs de farm de first.parameters.json][adfs-farm-first-parameters]**, e ** [adfs de farm de rest.parameters.json][adfs-farm-rest-parameters]**. O script usa as configurações nesses arquivos para criar o farm de servidores do ADFS. 

    O arquivo *gmsa.parameters.json* contém as configurações para a conta de serviço de grupo gerenciado usado pelo serviço ADFS. Você pode modificar esse arquivo se desejar alterar o nome da conta ou o domínio.

    O arquivo de *adfs de farm de first.parameters.json* contém as informações necessárias para criar o farm de servidores do ADFS e adicionar o primeiro servidor. Se você tiver alterado o domínio ou o nome da conta de serviço de grupo gerenciado, você deve atualizar este arquivo.

    O arquivo de *adfs de farm de rest.parameters.json* é usado para adicionar os servidores ADFS restantes ao farm. Novamente, se você tiver alterado o domínio ou o nome da conta de serviço de grupo gerenciado, você deve atualizar este arquivo. Atualizar o `vms` matriz se você tiver criado servidores ADFS adicionais.

- ** [loadBalancer-adfsproxy.parameters.json][loadBalancer-adfsproxy-parameters]**. Este arquivo é semelhante em estrutura e o conteúdo para o arquivo de *adfs.parameters.json loadBalancer* . Ele contém os dados para criar os servidores de proxy do ADFS e balanceador de carga.

- ** [adfsproxy-farm-first.parameters.json][adfsproxy-farm-first-parameters]**, e ** [adfsproxy de farm de rest.parameters.json][adfsproxy-farm-rest-parameters]**. O script usa as configurações nesses arquivos para criar o farm de servidor de proxy do ADFS. 

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Este arquivo contém as configurações usadas para criar o gateway VPN Azure na nuvem usada para se conectar à rede local. Você deve modificar o `sharedKey` valor o `connectionsSettings` seção que coincida com o dispositivo VPN local. Para obter mais informações, consulte [Implementando uma arquitetura de rede híbrida com o Azure e local VPN][hybrid-azure-on-prem-vpn].

- ** [dmz-private.parameters.json] [ dmz-private-parameters] ** e ** [dmz-public.parameters.json ] [ dmz-public-parameters] **. Esses arquivos configuram (público) entrado e saídos (privados) lados das VMs que compõem DMZ, protegendo os servidores na nuvem. Para obter mais informações sobre esses elementos e sua configuração, consulte [Implementando DMZ entre Azure e a Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer-web.parameters-json][loadBalancer-web-parameters]**, ** [loadBalancer-biz.parameters-json][loadBalancer-biz-parameters]**, e ** [loadBalancer-data.parameters-json][loadBalancer-data-parameters]**. Esses arquivos de parâmetros contêm as especificações de máquina virtual para os níveis de acesso da web, negócios e dados e configurar balanceadores de carga para cada nível. Estas são as VMs que hospedam a web apps e bancos de dados e executam as cargas de trabalho de negócios para a organização. Você pode modificar os tamanhos e o número de VMs em cada camada de acordo com suas necessidades.

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Este arquivo contém a configuração para o salto caixa/gerenciamento VMs. Só é possível obter logon e acesso administrativo às VMs na web, business e camadas de dados da caixa de salto. Por padrão, o script cria uma única *Standard_DS1_v2* máquina virtual, mas você pode modificar esse arquivo para criar VMs aumentar ou adicionais se o gerenciamento de carga de trabalho é provável que seja significativo.

## <a name="solution-deployment"></a>Implantação da solução

A solução pressupõe os seguintes pré-requisitos:

- Você tem uma assinatura existente do Azure na qual você pode criar grupos de recursos.

- Baixar e instalar a versão mais recente do Azure Powershell. Veja [aqui] [ azure-powershell-download] para obter instruções.

Para executar o script que implanta a solução:

1. Mover para uma pasta conveniente em seu computador local e crie as seguintes subpastas:

    - Scripts

    - Scripts/parâmetros

    - Parâmetros/scripts/Onpremise

    - Parâmetros/scripts/Azure

2. Baixar o [Implantar-ReferenceArchitecture.ps1] [ solution-script] arquivo para a pasta de Scripts

3. Baixar o conteúdo dos [parâmetros/onpremise] [ on-premises-folder] para a pasta de parâmetros/Scripts/Onpremise:

4. Baixar o conteúdo dos [parâmetros/azure] [ azure-folder] para a pasta de parâmetros/Scripts/Azure.

5. Editar o arquivo ReferenceArchitecture.ps1 implantar na pasta Scripts e altere as seguintes linhas para especificar os grupos de recursos que devem ser criados ou usados para manter os recursos criados pelo script:

    ```powershell
    # Azure Onpremise Deployments
        $onpremiseNetworkResourceGroupName = "ra-adfs-onpremise-rg"
        
        # Azure ADDS Deployments
        $azureNetworkResourceGroupName = "ra-adfs-network-rg"
        $workloadResourceGroupName = "ra-adfs-workload-rg"
        $securityResourceGroupName = "ra-adfs-security-rg"
        $addsResourceGroupName = "ra-adfs-adds-rg"
        $adfsResourceGroupName = "ra-adfs-adfs-rg"
        $adfsproxyResourceGroupName = "ra-adfs-proxy-rg"
    ```

6. Edite os arquivos de parâmetro nas pastas de parâmetros/Scripts/Azure e parâmetros/Scripts/Onpremise. Atualize as referências de grupo de recursos nesses arquivos para corresponder os nomes dos grupos de recursos atribuídos às variáveis no arquivo ReferenceArchitecture.ps1 implantar. A tabela a seguir mostra quais arquivos de parâmetro referência qual grupo de recursos. Observe que os grupos de *ar-adfs-carga-rg*, *ar-adfs-segurança-rg*, *ar-adfs-adiciona-rg*, *ar-adfs adfs rg*e *ar-adfs-proxy-rg* são usados somente no script PowerShell e não ocorrem em arquivos de parâmetro.

  	|Grupo de recursos|Arquivos de parâmetro|
  	|--------------|--------------|
  	|ar-adfs-onpremise-rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|ar-adfs-rede-rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-Public.Parameters.JSON<br />parameters\azure\loadBalancer-ADFS.Parameters.JSON<br />parameters\azure\loadBalancer-adfsproxy.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-with-OnPremise-and-Azure-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*duas ocorrências*)

    Além disso, defina a configuração para os locais e na nuvem componentes, conforme descrito na seção [solução Components] [componentes da solução].

7. Abra uma janela do PowerShell do Azure, mover para a pasta de Scripts e execute o seguinte comando:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Substituir `<subscription id>` com sua ID do Azure assinatura.

    Para `<location>`, especifique uma região Azure, tais como `eastus` ou `westus`.

    O `<mode>` parâmetro pode ter um dos seguintes valores:

    - `Onpremise`, para criar o ambiente local simulada.

    - `Infrastructure`, para criar a infraestrutura de VNet e saltar caixa na nuvem.

    - `CreateVpn`, para criar o gateway de rede virtual Azure e conecte-o à rede local.

    - `AzureADDS`, para construir VMs atuando como ADICIONA servidores, implantar o Active Directory para essas VMs e criar o domínio na nuvem.

    - `AdfsVm`para criar as VMs do ADFS e associá-los ao domínio na nuvem.

    - `ProxyVm`para criar o proxy do AD FS VMs e associá-los ao domínio na nuvem.

    - `Prepare`, que executa todas as tarefas acima. **Esta é a opção recomendada se você estiver criando uma implantação inteiramente nova e você não tiver uma infraestrutura local existente.**

    >[AZURE.NOTE] Você também pode executar o script com uma `<mode>` parâmetro da `Workload` para criar a web, a negócios e a camada de dados VMs e a rede. Essa configuração não está incluída como parte do `Prepare` modo.

    Se você usar o `Prepare` opção, o script leva várias horas para ser concluída e terminar com a mensagem *preparação seja concluída. Instale o certificado todos os AD FS e proxy VMs.*

8.  Reinicie a caixa de salto (*ar-adfs-gerenciamento-vm1* no grupo *ar-adfs-segurança-rg* ) para permitir que suas configurações de DNS seja efetivada.

9.  [Obter um certificado SSL para ADFS] [ adfs_certificates] e instalar este certificado nas VMs ADFS. Observe que você pode conectar a VMs ADFS por meio da caixa de salto. Os endereços IP são *10.0.5.4* e *10.0.5.5*. O nome de usuário padrão é *contoso\testuser* com senha *AweSome@PW*.

    >[AZURE.NOTE] Os comentários no script implantar ReferenceArchitecture.ps1 neste ponto fornecem instruções detalhadas para criar um certificado autoassinado teste e autoridade usando o `makecert` comando. No entanto, não use os certificados gerados pelo makecert em um ambiente de produção.

10. Execute o seguinte comando do Powershell para configurar o farm de servidor do AD FS:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

11. Na caixa de salto, navegue até *https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm* para testar a instalação do ADFS (você pode receber um aviso de certificado, você pode ignorar para este teste). Verifique se a página de entrada Contoso Corporation aparece. Entrar como *contoso\testuser* com senha *AweS0me@PW*.

12. Instale o certificado SSL no proxy do AD FS VMs. Os endereços IP são *10.0.6.4* e *10.0.6.5*.

13. Execute o seguinte comando do Powershell para configurar o primeiro servidor de proxy do AD FS:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

14. Siga as instruções exibidas pelo script para testar a instalação do primeiro servidor de proxy do AD FS.

15. Execute o seguinte comando do Powershell para configurar o segundo servidor de proxy ADFS primeiro:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

16. Siga as instruções exibidas pelo script para testar a configuração de proxy do AD FS completa.

## <a name="next-steps"></a>Próximas etapas

- [Active Directory do Azure][aad].

- [Active Directory do Azure B2C][aadb2c].

<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-identity-adds-extend-domain.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: ../active-directory/active-directory-aadconnect-azure-adfs.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[loadbalancer-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfs.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/add-adds-domain-controller.parameters.json
[gmsa-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/gmsa.parameters.json
[adfs-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-first.parameters.json
[adfs-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-rest.parameters.json
[adfs-farm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-domain-join.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-mgmt.parameters.json
[loadBalancer-adfsproxy-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfsproxy.parameters.json
[adfsproxy-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-first.parameters.json
[adfsproxy-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-rest.parameters.json
[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "Arquitetura de rede híbrida com o Active Directory protegida"
