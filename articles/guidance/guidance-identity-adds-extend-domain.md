<properties
   pageTitle="Referência de arquitetura do Windows Azure - IaaS: Estendendo o Active Directory no Azure | Microsoft Azure"
   description="Como implementar uma arquitetura de rede seguro híbrida com autorização do Active Directory no Azure."
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
   ms.date="07/19/2016"
   ms.author="telmos"/>

# <a name="extending-active-directory-directory-services-adds-to-azure"></a>Estendendo serviços de diretório do Active Directory (ADICIONA) para o Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as práticas recomendadas para estender seu ambiente do Active Directory (AD) no Azure, para fornecer os serviços de autenticação distribuída usando [Os serviços de domínio Active Directory (AD DS)][active-directory-domain-services]. Essa arquitetura estende que descrito nos artigos [implementar uma arquitetura de rede de seguro híbrido no Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementar uma arquitetura de rede de seguro híbrida com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

Usar o AD DS para autenticar identidades. Essas identidades podem pertencer a usuários, computadores, aplicativos ou outros recursos que fazem parte de um domínio de segurança. Você pode hospedar AD DS local, mas em um cenário de híbrido onde os elementos de um aplicativo estão localizados no Azure pode ser mais eficiente para replicar essa funcionalidade e o repositório do AD para a nuvem. Essa abordagem pode ajudar a reduzir a latência causada enviando autenticação e solicitações de autorização local da nuvem de volta para o AD DS em execução no local. 

Há duas maneiras para hospedar seus serviços de diretório no Azure:

1. Você pode usar o [Azure Active Directory (AAD)] [ azure-active-directory] para criar um novo domínio AD na nuvem e vinculá-lo a um local domínio do AD. Configurar o [Azure AD Connect] [ azure-ad-connect] promessas replicar identidades contidas no repositório local para a nuvem. Observe que o diretório na nuvem é **não** uma extensão do sistema local, em vez disso, é uma cópia que contém as mesmas identidades. Alterações feitas essas identidades local será copiado para a nuvem, mas as alterações feitas na nuvem, **não poderá** ser replicados para o domínio local. Por exemplo, redefinições de senha devem ser realizado no local e usar Azure AD Connect para copiar a alteração na nuvem. Além disso, observe que a mesma instância do AAD pode ser vinculada a mais de uma instância do AD DS; AAD conterá as identidades de cada repositório do AD ao qual ele está vinculado.

    AAD é útil para situações em que a rede local e o Azure rede virtual que hospedam os recursos de nuvem não são diretamente vinculados usando um túnel VPN ou rota expressa circuito. Embora esta solução seja simple, talvez não seja adequado para sistemas onde componentes podem migrar através da fronteira no local/nuvem como isso poderia exigir reconfiguração de AAD. Além disso, AAD apenas lida com a autenticação de usuário, em vez da autenticação do computador. Alguns aplicativos e serviços, como o SQL Server, podem exigir autenticação do computador caso em que esta solução não for apropriada.

2. Você pode implantar uma máquina virtual com o AD DS como controlador de domínio no Azure, ampliando sua infraestrutura existente do AD do seu centro de dados local. Essa abordagem é mais comum para cenários onde a rede local e uma rede virtual Azure estão conectados por uma conexão VPN e/ou rota expressa. Essa solução também suporta bidirecional replicação permitindo que você faça alterações na nuvem e no local, onde ele é mais apropriado. Dependendo de seus requisitos de segurança, a instalação do AD na nuvem pode ser:
    - parte do mesmo domínio que mantido local
    - um novo domínio em uma floresta compartilhada
    - uma floresta separada

<!-- we might want to add info on how to choose from the three options above -->

Essa arquitetura se concentra na solução 2, usando o mesmo domínio AD DS do Azure e locais.

Casos de uso típico para essa arquitetura incluem:

- Aplicativos híbridos onde executar cargas de trabalho parcialmente local e parcialmente no Azure.

- Aplicativos e serviços que executam autenticação usando o Active Directory.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

O diagrama a seguir destaca os componentes importantes nesta arquitetura (*clique para ampliar*). Para obter mais informações sobre os elementos de acinzentada, leia [implementar uma arquitetura de rede de seguro híbrido no Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementar uma arquitetura de rede de seguro híbrida com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Rede local.** A rede local inclui servidores locais do AD que podem realizar autenticação e a autorização para componentes localizados no local.

- **Servidores de anúncio.** Estas são controladores de domínio a implementação de serviços de diretório (AD DS) executando como VMs na nuvem. Esses servidores podem fornecer autenticação de componentes executando em sua rede virtual Azure.

- **Sub-rede do Active Directory.** Os servidores de AD DS são hospedados em uma sub-rede separada. Regras NSG proteger os servidores de AD DS e podem fornecer um firewall contra tráfego de fontes inesperadas.

- **Sincronização Gateway do azure e AD.**. O gateway Azure oferece uma conexão entre a rede local e o VNet do Azure. Isso pode ser uma [conexão VPN] [ azure-vpn-gateway] ou [Rota expressa do Azure][azure-expressroute]. Todas as solicitações de sincronização entre os servidores de AD na nuvem e local passam pelo gateway. Rotas definidas pelo usuário (UDRs) manipulam roteamento de tráfego de sincronização que passa diretamente para o servidor do AD na nuvem e não passar os existente rede virtual eletrodomésticos (NVAs) usados neste cenário.

    Para obter mais informações sobre como configurar UDRs e os NVAs, consulte [Implementando uma arquitetura de rede de seguro híbrido no Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recomendações

Esta seção resume as recomendações para implementar o AD DS no Azure, abrangendo:

- Recomendações de máquina virtual.

- Recomendações de rede.

- Recomendações de segurança. 

- Recomendações de sites do diretório ativas.

- Recomendações de posicionamento de FSMO de diretório ativas.

>[AZURE.NOTE] O documento [diretrizes para implantação do Active Directory do Windows Server em máquinas virtuais do Azure] [ ad-azure-guidelines] contém informações mais detalhadas sobre muitos desses pontos.

### <a name="vm-recommendations"></a>Recomendações de máquina virtual

Crie VMs com recursos suficientes para lidar com o volume esperado de tráfego. Use o tamanho das máquinas AD DS local como ponto de partida de hospedagem. Monitorar a utilização de recursos; Você pode redimensionar as VMs e dimensionar para baixo se forem muito grandes. Para obter mais informações sobre o dimensionamento de controladores de domínio do AD DS, consulte [Planejamento da capacidade para serviços de domínio Active Directory][capacity-planning-for-adds].

Crie um disco de dados virtual separado para armazenar o banco de dados, logs e SYSVOL para AD. Não armazene esses itens no mesmo disco como o sistema operacional. Observe que por padrão, discos de dados que são anexados a uma máquina virtual usam gravação por cache. No entanto, essa forma de cache pode entrar em conflito com os requisitos do AD DS. Por esse motivo, defina a configuração de *Preferência de Cache de Host* no disco dados *Nenhum*. Para obter mais informações, consulte [posicionamento do banco de dados do Windows Server AD DS e SYSVOL][adds-data-disks].

Implante pelo menos duas VMs executando AD DS como controladores de domínio à sua rede virtual Azure por razões de [disponibilidade](#Availability-considerations) .

### <a name="networking-recommendations"></a>Recomendações de rede

Configure a interface de rede para cada uma das VMs AD DS com endereços IP particulares estáticos de hospedagem. Esta configuração melhor oferece suporte a DNS em cada uma das VMs AD. Para obter mais informações, consulte [como configurar um endereço IP privado estático no portal do Azure][set-a-static-ip-address].

> [AZURE.NOTE] Não forneça os endereços IP públicos do AD DS VMs. Consulte [Considerações sobre segurança] [ security-considerations] para obter mais detalhes.

Você deve garantir que o tráfego pode fluir livremente entre os servidores de AD na nuvem e local:

- Adicione regras NSG à sub-rede AD que permitem o tráfego de entrada do local. Para obter informações detalhadas sobre as portas que utilizam AD DS, consulte o [Active Directory e requisitos de porta de serviços do Active Directory Domain][ad-ds-ports].

- Certifique-se de tabelas UDR não rotear o tráfego de AD DS por meio do NVAs usada neste cenário. Para seus próprio implantações, dependendo de quais NVAs usar, talvez você queira redirecionar o tráfego.

### <a name="security-recommendations"></a>Recomendações de segurança

Servidores do AD DS lidar com a autenticação e, portanto, itens muito confidenciais. Evitar exposição direta dos servidores AD DS à Internet. Coloque servidores AD DS em uma sub-rede separada, com seu próprio firewall. Certifique-se de que as portas necessárias para usar o AD DS para autenticação e autorização e os servidores de synchronzing permaneçam abertas, mas Feche todas as outras portas. Para obter mais informações, consulte o [Active Directory e requisitos de porta de serviços do Active Directory Domain][ad-ds-ports]. Os [componentes de solução] [ solution-components] seção mais adiante neste documento mostra o NSG regras que a solução de amostra usa para abrir portas.

Você pode usar regras NSG para criar um firewall simples. Se você precisar de proteção mais abrangente que você pode implementar um perímetro de segurança adicional em torno de servidores usando um par de sub-redes e NVAs, conforme descrito pelo documento [implementar uma arquitetura de rede de seguro híbrida com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

Use a criptografia de disco BitLocker ou Azure para criptografar o disco que hospeda o banco de dados do AD DS.

### <a name="active-directory-site-recommendations"></a>Recomendações de sites do diretório ativas

Você pode usar os sites no AD DS para controladores de domínio juntos de grupo que estão conectadas por um link de forma rápido. Controladores de domínio no mesmo site AD DS replicam suas alterações de diretório automaticamente e pouca configuração é necessária para lidar com a replicação.

PARA controlar o tráfego de replicação entre Azure e data center local, é recomendável para adicionar um site do AD DS separado para representar o espaço de endereço usado pelo Azure. Você pode configurar um link de site entre seu local AD DS sites e controlar a replicação de intersite com mais eficiência.

Você também pode usar separação de site para aplicar diferentes política objetos de grupo (GPOs) associado computadores no Azure e tire proveito dos aplicativos que são "site ciente", como o System Center Configuration Manager.

### <a name="active-directory-fsmo-placement-recommendations"></a>Recomendações de posicionamento do Active Directory FSMO

Os servidores de operação de mestre único (FSMO) flexíveis são controladores de domínio especializado, reposnsible para consistência de dados em diferentes configurações no AD DS, listados abaixo.

- **Mestre de esquema**. Esta é uma função de floresta que mantém a estrutura do esquema usado pelo AD DS.

- **Mestre de nomeação de domínio**. Esta é uma função de floresta que gerencia informações sobre nomes de domínio dentro da floresta.

- **Controlador de domínio primário (PDC)**. Esta é uma função de todo o domínio. O PDC lida com as atualizações de senha e bloqueios de conta. Ele é consultado por outros controladores de domínio quando as solicitações de serviço de autenticação têm senhas não coincidentes. O PDC também trata das atualizações de política de grupo e é o DC de destino para aplicativos herdados e algumas ferramentas de administração que executam algumas operações graváveis.

- **Mestre de RID (identificação relativa)**. RIDs são usadas para ajudar a identificar exclusivamente objetos dentro de um diretório. Este servidor é responsável por gerar um pool de todo o domínio de RIDs para serem usados por todos os servidores de AD dentro do domínio.

- **Função de infraestrutura**. Um objeto em um domínio pode fazer referência a um objeto em outro domínio. Esta função de todo o domínio é responsável por atualizar o SID de um objeto e o nome diferenciado em uma referência de objeto no domínio cruzado. Observe que um servidor implementar esta função também não pode atuar como um servidor de Catálogo Global (GC).

Para obter mais informações, consulte [funções de FSMO do Active Directory do Windows][AD-FSMO-roles-in-windows].

Para esse cenário, recomendamos que você evite implantar funções FSMO para os controladores de domínio no Azure. 

## <a name="availability-considerations"></a>Considerações de disponibilidade

Crie uma disponibilidade definida para os servidores de AD. Certifique-se de que há pelo menos dois servidores do conjunto. Servidores de anúncio na nuvem devem ser controladores de domínio dentro do mesmo domínio. Isso permitirá replicação automática entre servidores.

Além disso, considere designando servidores como [mestres de operações em espera] [ standby-operations-masters] em caso de falha de conectividade com um servidor atuando como uma função FSMO.

## <a name="security-considerations"></a>Considerações de segurança

Se todas as tarefas de administração de domínio são probabilidade de ser executadas usando controladores de domínio local, considere controladores de domínio na nuvem somente leitura. Um DC somente leitura somente mantém um subconjunto de credenciais dos usuários (suficiente para executar a autenticação localmente) e pode ser configurado para informações de cache somente para usuários específicos. Portanto, se um DC somente leitura for comprometido, apenas as informações para usuários em cache serão afetadas, em vez dos detalhes de cada conta no domínio. Para obter mais informações, consulte [Controladores de domínio somente leitura][read-only-dc].

Para ajudar a minimizar a vulnerabilidade das contas de usuário individual e para impedir tentativas de invasão, siga as práticas recomendadas para configuração e manter senhas dos usuários no AD. Para obter mais informações, consulte [Práticas recomendadas para aplicar políticas de senha][best_practices_ad_password_policy]. Além disso, tenha cuidado para quais grupos você atribuir aos usuários. Por exemplo, não faça membros de usuários comuns do grupo Administradores de empresa, grupo de administradores de esquema e grupo Domain Admins.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

AD é automaticamente scalable controladores de domínio que fazem parte do mesmo domínio. Solicitações são distribuídas entre todos os controladores em um domínio. Você pode adicionar outro controlador de domínio e ele será sincronizado automaticamente com o domínio. Não configure um balanceador de carga separada para direcionar o tráfego para controladores dentro do domínio. Garantir que todos os controladores de domínio tenham memória suficiente e recursos de armazenamento para lidar com o banco de dados do domínio; ideal seria torne todo controlador de domínio VMs do mesmo tamanho.

## <a name="management-considerations"></a>Considerações de gerenciamento

Não copie os arquivos VHD controladores de domínio em vez de realização de backups regulares porque restaurá-los pode resultar em inconsistências no estado entre controladores de domínio.

Desligar e reiniciar uma máquina virtual que executa a função de controlador de domínio no Azure no sistema operacional convidado em vez de usar a opção Desligar no Portal do Azure. Usando o Portal do Azure para desligar uma máquina virtual faz com que a máquina virtual desalocamento. Essa ação redefine a máquina virtual-GenerationID, que é indesejável para um controlador de domínio. Quando a máquina virtual-GenerationID é redefinida, a invocationID do repositório do AD também é redefinida, o pool RID é descartado e SYSVOL está marcado como não autoritativas.

## <a name="monitoring-considerations"></a>Considerações de monitoramento

Falha ao monitorar e manter uma rede de servidores do AD pode resultar em problemas, como:

- **Falha de logon**. Falha de logon pode ocorrer em todo o domínio ou floresta se uma resolução de nome ou relação de confiança falhar, ou se um servidor de catálogo global não pode determinar a associação de grupo universal.

- **Bloqueio de conta**. Contas de usuário e serviço podem ficar bloqueadas se o controlador de domínio primário não estiver disponível, ou replicação falha entre vários controladores de domínio.

- **Falha do controlador de domínio**. Se a unidade que contém o arquivo dit é executado sem espaço de disco, o controlador de domínio pode parar de funcionar.

- **Falha no aplicativo**. Aplicativos que são fundamentais para seus negócios, como o Microsoft Exchange ou outro aplicativo de email, podem falhar se consultas de catálogo de endereços no diretório falharem.

- **Dados de diretório inconsistente**. Se houver falha replicação por um longo período de tempo, objetos duplicados podem ser criados no diretório e podem exigir diagnóstico abrangente e a hora para eliminar.

- **Falha de criação de conta**. Um controlador de domínio é possível criar contas de usuário ou computador se ele esgotar sua fonte de identificações relativas e o mestre de RID não está disponível.

- **Falha de política de segurança**. Se a pasta compartilhada SYSVOL não replicar corretamente, objetos de política de grupo e políticas de segurança não são corretamente aplicadas aos clientes.

Monitorar servidores AD cuidadosamente e esteja preparado para tomar medidas corretivas rapidamente. Crie uma lista de verificação de tarefas para ser executado em um intervalo apropriado de monitoramento. Por exemplo você pode agendar as seguintes tarefas críticas diariamente:

- Examinar e resolver alertas relatadas por controladores de domínio, 

- Verifique se todos os controladores de domínio possam se comunicar e replicação está funcionando.

- Certifique-se de que SYSVOL permanecerá compartilhada.

- Corrigir problemas de sincronização de tempo.

- Verificar se há espaço em disco nas unidades físicas usado pelo AD.

Você pode executar outras tarefas de rotina menos frequência. Por exemplo, você pode revisar relações de confiança verificar relações de confiança obsoletas ou quebradas semanalmente e confirme se todos os controladores de domínio estão executando usando os mesmos service packs e patches de correção quente mensalmente.

Para obter mais informações, consulte [Monitoring Active Directory][monitoring_ad]. Você pode instalar ferramentas como o [Microsoft Systems Center] [ microsoft_systems_center] no servidor de monitoramento (consulte o [diagrama de arquitetura][architecture]) para ajudar a realizar essas tarefas. 

## <a name="solution-components"></a>Componentes de solução

A solução fornecida para essa arquitetura cria uma rede híbrido seguro conforme descrito pelos documentos [implementar uma arquitetura de rede de seguro híbrido no Azure] [ implementing-a-secure-hybrid-network-architecture] e [implementar uma arquitetura de rede de seguro híbrida com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], mas com a adição dos seguintes itens:

- Um grupo de recursos Azure denominada *basename*dns-rg, onde *basename* é um prefixo especificado quando a implantação da solução.

- Duas VMs Azure chamado *basename*-IA1-máquina virtual e *basename*-ad2-máquina virtual, criado no grupo de recursos *basename*rg - dns. Essas VMs são configurados como servidores de anúncio com serviços de diretório e DNS instalado e configurado.

- Um NSG chamado *basename*-ad-nsg no grupo de recursos Azure *basename*rg - ntwk. Este grupo de recursos é parte da infraestrutura que constituem a rede híbrido seguro, mas o novo NSG é uma adição que define as regras de segurança de entrada para os servidores de AD conforme mostrado na tabela a seguir:


    Prioridade|Nome|Fonte|Destino|Serviço|Ação|
    --------|----|------|-----------|-------|------|
    170|vnet para port53|10.0.0.0/16|Qualquer|Custom(Any/53)|Permitir|
    180|vnet para port88|10.0.0.0/16|Qualquer|Custom(Any/88)|Permitir|
    190|vnet para port135|10.0.0.0/16|Qualquer|Custom(Any/135)|Permitir|
    200|vnet-para-port137-9|10.0.0.0/16|Qualquer|Custom(Any/137-139)|Permitir|
    210|vnet para port389|10.0.0.0/16|Qualquer|Custom(Any/389)|Permitir|
    220|vnet para port464|10.0.0.0/16|Qualquer|Custom(Any/464)|Permitir|
    230|vnet para rpc dinâmico|10.0.0.0/16|Qualquer|Custom(Any/49152-65535)|Permitir|
    240|ad onprem de port53|192.168.0.0/24|Qualquer|Custom(Any/53)|Permitir|
    250|ad onprem de port88|192.168.0.0/24|Qualquer|Custom(Any/88)|Permitir|
    260|ad onprem de port135|192.168.0.0/24|Qualquer|Custom(Any/135)|Permitir|
    270|ad onprem de port389|192.168.0.0/24|Qualquer|Custom(Any/389)|Permitir|
    280|ad onprem de port464|192.168.0.0/24|Qualquer|Custom(Any/464)|Permitir|
    290|gerenciamento-rdp-permitir|10.0.0.128/25|Qualquer|Custom(Any/3389)|Permitir|
    300|permitir que o gateway|10.0.255.224/27|Qualquer|Custom(any/any)|Permitir|
    310|Permitir automática|10.0.255.192/27|Qualquer|Custom(any/any)|Permitir|
    320|vnet-negar|Qualquer|Qualquer|Custom(any/any)|Permitir|

    AD DS usa portas 53, 89, 135, 389 e 464 aceitar replicação de entrada e o tráfego de autenticação. Nesta tabela, o controlador de domínio local está o endereço espaço 192.168.0.0/24 (seu espaço de endereço pode variar - especificar essas informações como um parâmetro para os modelos implantados pela solução.

    O NSG também define as seguintes regras de segurança de saída que permitem o tráfego de sincronização e a autorização para fluxo de volta para a rede local:

    Prioridade|Nome|Fonte|Destino|Serviço|Ação|
    --------|----|------|-----------|-------|------|
    100|out-port53|Qualquer|192.168.0.0/24|Custom(Any/53)|Permitir|
    110|out-port88|Qualquer|192.168.0.0/24|Custom(Any/88)|Permitir|
    120|out-port135|Qualquer|192.168.0.0/24|Custom(Any/135)|Permitir|
    130|out-port389|Qualquer|192.168.0.0/24|Custom(Any/389)|Permitir|
    140|out-port445|Qualquer|192.168.0.0/24|Custom(Any/445)|Permitir|
    150|out-port464|Qualquer|192.168.0.0/24|Custom(Any/464)|Permitir|
    160|out-rpc-dinâmico|Qualquer|192.168.0.0/24|Custom(Any/49152-65535)|Permitir|

O script fornecido com a solução também executar as seguintes tarefas:

- Adiciona o *basename*-IA1-máquina virtual e *basename*-ad2-servidores de máquina virtual como controladores de domínio para o domínio. Você pode exibir esses servidores no console de *computadores e usuários do Active Directory* no controlador de domínio local:

![[1]][1]

- Ele cria uma nova sub-rede (10.0.0.0/16) para um site do AD chamado Azure-VNet-Ad-Site para o domínio. Este site contém o *basename*-IA1-máquina virtual e *basename*-ad2-servidores de máquina virtual. 

- Ele adiciona configurações de transporte entre sites IP que define o intervalo de replicação entre o site local e controladores de domínio na nuvem. Você pode ver as configurações para a sub-rede, sites e configurações de transporte no console de *servidores e Sites do Active Directory* no controlador de domínio local:

![[2]][2]

## <a name="deployment"></a>Implantação

A solução de exemplo tem os seguintes prerequsites:

- Você já configurou seu domínio local, e que você tenha configurado DNS e serviços de roteamento e acesso remoto para oferecer suporte a VPN instalados se conectar ao gateway VPN do Azure.


- Você instalou a versão mais recente do Azure CLI. [Siga estas instruções para obter detalhes][cli-install].

- Se você estiver implantando a solução do Windows, você deve instalar uma ferramenta que fornece um shell bash, como [Área de trabalho do GitHub][github-desktop].

>[AZURE.NOTE] Se você não tiver acesso a um domínio local existente, você pode criar um ambiente de teste usando o [onpremdeploy.sh] [ onpremdeploy] bash script. Este script cria uma rede e máquina virtual na nuvem que simula uma instalação local muito básico. Editar esse script antes de executar e definir as seguintes variáveis definidas no início do arquivo:
>
> - **BASE_NAME**. Um prefixo definidas pelo usuário para o grupo de recursos e máquina virtual criado pelo script. Este item deve ter **não mais de 5 caracteres** caso contrário, que o script tentará gerar uma máquina virtual com um nome inválido e falhar.
> 
> - **Assinatura**. Sua ID do Azure assinatura. O grupo de recursos será criado neste suscription.
> 
> - **Local**. O Azure local no qual deseja criar o grupo de recursos, como *eastus* ou *westus*.
> 
> - **ADMIN_USER_NAME**. O nome a ser usado para a conta de administrador na máquina virtual.
> 
> - **ADMIN_PASSWORD**. A senha da conta de administrador.

Execute as seguintes etapas para criar a solução de exemplo:

1. baixar e editar o [azuredeploy.sh] [ azuredeploy] script e definir os seguintes parâmetros no início do arquivo:

    - **BASE_NAME**. Um prefixo definidas pelo usuário para os grupos de recursos e VMs criadas pelo script. Como antes, este item deve ser **não mais de 5 caracteres**.

    - **Assinatura**. Sua ID do Azure assinatura.

    - **Tipo_so**. O sistema operacional (*Windows* ou *Linux*) para usar para a camada de acesso da web, negócios e dados VMs. Observe que todos os servidores de AD criados pelo script executam o Windows Server 2012, independentemente dessa configuração.

    - **Nome_de_domínio**. O nome do domínio local. Observe que, se você usa o ambiente criado pelo script onpremdeploy.sh, isso deve ser *contoso.com*.

    - **Local**. O Azure local no qual deseja criar os grupos de recursos.

    - **ADMIN_USER_NAME**. O nome a ser usado para as contas de administrador em várias VMs.

    - **ADMIN_PASSWORD**. A senha da conta de administrador.

    - **ON_PREMISES_PUBLIC_IP**. O endereço IP público da máquina VPN local.

    - **ON_PREMISES_ADDRESS_SPACE**. O espaço de endereço interno da rede local. Se você estiver usando o ambiente criado pelo script onpremdeploy.sh, deve ser 192.168.0.0/16.

    - **VPN_IPSEC_SHARED_KEY**. A chave compartilhada do IPSec usada para estabelecer a conexão VPN entre a rede local e o gateway de VPN do Azure.

    - **ON_PREMISES_DNS_SERVER_ADDRESS**. O endereço IP do servidor DNS local. Se você estiver usando o ambiente criado pelo script onpremdeploy.sh, deve ser 192.168.0.4

    - **ON_PREMISES_DNS_SUBNET_PREFIX** O prefixo de endereço da sub-rede local. Se você estiver usando o ambiente criado pelo script onpremdeploy.sh, deve ser 192.168.0.0/24.

    >[AZURE.NOTE] PARA economizar tempo e recursos, o script não criar os níveis de acesso de dados ou comerciais. Se você solicitar esses itens, você pode não comentar a seção a seguir no script azuredeploy.sh:
    >
    >
    > ```
    > #### # create biz tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_BIZ_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${BIZ_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${BIZ_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_BIZ_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > #### 
    > #### # create db tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_DB_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${DB_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${DB_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_DB_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ```

2. Abra um prompt de shell bash e mover para a pasta que contém o script azuredeploy.sh.

3. Faça logon em sua conta do Azure. No shell bash, digite o seguinte comando:

    ```
    azure login
    ```

    Siga as instruções para se conectar ao Azure.

4. Execute o comando `./azuredeploy.sh`e aguarde enquanto o script cria a infraestrutura de rede.

5. No prompt de *Verifique se que a VNet foi criado*, use o portal do Azure para verificar que um grupo de recursos denominado *basename*- ntwk-rg foi criado e que contém itens semelhantes aos mostrados na imagem a seguir:

    ![[3]][3]

    >[AZURE.NOTE] Os exemplos mostrados, *basename* foi definida na *nuvem* quando o script foi executado.

    Clique na *basename*- vnet VNet *sub-redes*e em verificar que foram criadas as sub-redes mostradas abaixo:

    ![[4]][4]

6. No prompt na janela de shell bash, pressione uma tecla e aguarde a camada da web e balanceador de carga são criadas.

7. No prompt de *Verifique se que a camada da Web foi criada corretamente*, use o portal do Azure para verificar que um grupo de recursos chamado *basename*web camada rg foi criado e que contém itens semelhantes aos mostrado abaixo:

    ![[5]][5]

8. No prompt na janela de shell bash, pressione uma tecla e aguarde enquanto os NVAs são criados.

9. No prompt de *Verifique se que a NVA foi criado corretamente*, use o portal do Azure para verificar que um grupo de recursos chamado *basename*- gerenciamento-rg foi criado com o seguinte conteúdo:

    ![[6]][6]

10. No prompt na janela de shell bash, pressione uma tecla e aguarde enquanto o jumpbox é criado.

11. No prompt de *Verifique se que a jumpbox foi criado corretamente*, use o portal do Azure para verificar que os itens a seguir foram adicionados ao grupo de recursos de gerenciamento de - rg *basename*:

    - Um conjunto de disponibilidade chamado *basename*- jb-como.

    - Uma máquina virtual denominado *basename*- jb-máquina virtual.

    - Uma interface de rede chamada *basename*- jb-NIC.

12. No prompt na janela de shell bash, pressione uma tecla e aguarde enquanto o gateway VPN do Azure e conexão são criados. Observe que esta etapa pode levar até 30 minutos para ser concluída.

13. No prompt de *Verifique se que o gateway VPN foi criado corretamente*, use o portal do Azure para verificar que os itens a seguir foram adicionados ao grupo de recursos *basename*rg - ntwk:

    - Um gateway de rede local chamado lgw no local.
    
    - Um gateway de rede virtual chamado *basename*- vpngw.

    - Uma conexão de gateway nomeado *basename*- vnet-vpnconn. Observe que o status dessa conexão pode ser *conectada não* se você ainda não configurou o final do local da conexão; você abordará isso mais tarde.

14. No prompt na janela de shell bash, pressione uma tecla e aguarde as VMs e outros recursos para DMZ são criados.

15. No prompt de *Verifique se que DMZ foi criado corretamente*, use o portal do Azure para verificar que um grupo de recursos chamado *basename*-dmz-rg foi criado com o seguinte conteúdo:

    ![[7]][7]

16. No prompt na janela de shell bash, pressione uma tecla. Os seguintes prompts devem aparecer:

    ```text
    Manual Step...

    Please configure your on-premises network to connect to the Azure VNet

    Make sure that you can connect to the on-premises AD server from the Azure VMs
    ```

    Faça logon computador local que se conecta ao gateway do Azure e configurar a conexão adequadamente. Adicione rotas estáticas para o dispositivo de gateway local que direciona requestsfor o intervalo de endereços 10.0.0.0/16 através do gateway para a VNet. As etapas para isso irá variar de acordo com como você está se conectando. Consulte [Implementando uma arquitetura de rede híbrida com o Azure e local VPN] [ implementing-a-hybrid-network-architecture-with-vpn] para obter mais informações.

    Observe que você pode encontrar o endereço IP público do gateway VPN do Azure usando o portal do Azure para examinar *basename*- vpngw gateway no grupo de recursos *basename*rg - ntwk:

    ![[8]][8]

    Você pode determinar se a conexão foi estabelecida corretamente examinando o status da conexão de - vnet-vpnconn *basename*. Ele deve ser definido para *conectado*.

    Para testar a conexão, abra uma conexão de área de trabalho remota para o jumpbox (10.0.0.254) de um computador localizado em sua rede local.

17. No prompt na janela de shell bash, pressione uma tecla. No próximo prompt, *Pressione qualquer tecla para atualizar a configuração de VNet para o VNet apontar para o local DNS*, pressione uma tecla e aguarde enquanto as configurações de DNS para o VNet são atualizadas para o valor especificado como o parâmetro **ON_PREMISES_DNS_SERVER_ADDRESS** no script azuredeploy.sh.

18. No prompt, *Verifique se que a configuração de servidor DNS na VNet foi atualizada*, use o portal do Azure para examinar a configuração de *servidores DNS* da *basename*- vnet VNet no grupo de recursos *basename*rg - ntwk. Ele deve ser definido como *Custom DNS*e o *servidor DNS primário* deve ser o endereço do servidor DNS local:

    ![[9]][9]

19. No prompt *Pressione qualquer tecla para criar o grupo de recursos para os servidores de AD* na janela de shell bash, pressione uma tecla e aguarde enquanto o grupo de recursos de isenção de servidores de anúncio na nuvem é criado.

20. No prompt *Pressione qualquer tecla para criar VMs para os servidores de AD* na janela de shell bash, pressione uma tecla e aguarde VMs a ser criado e adicionado ao grupo de recursos.

21. Quando o *Pressione qualquer tecla para ingressar VMs ao domínio local* aparecer, acesse o portal do Azure e verifique se um grupo chamado *basename*dns-rg foi criado e que ele contém duas VMS (*basename*-IA1-máquina virtual e *basename*-ad2-máquina virtual):

    ![[10]][10]

22. No grupo de recursos *basename*rg - ntwk, seleção que foi criado um NSG chamado *basename*-ad-nsg. Examine as regras de segurança de entrada e saída para este NSG. Eles devem coincidir os listados nas tabelas nos [componentes da solução] [ solution-components] seção.

23. No prompt na janela de shell bash, pressione uma tecla e aguarde enquanto as VMs são adicionadas ao domínio local.

24. No *, vá para o local servidor do AD para verificar que os computadores foram adicionados aos domínios* solicitar, conectar ao seu computador local e usar o console *Active Directory Users e computadores* para verificar que ambos os VMs foram adicionadas ao domínio:

    ![[11]][11]

25. No prompt na janela de shell bash, pressione uma tecla e aguarde enquanto o site de replicação do AD é criado no domínio.

26. No *, vá para o local servidor do AD para confirmar que o site de replicação foi criado* solicitar, use o console *serviços e Sites do Active Directory* para verificar se um site de replicação chamado *Azure-Vnet-Ad-Site* foi criado com êxito, juntamente com um link de transporte entre sites IP chamado *AzureToOnpremLink*e uma sub-rede que faz referência a VNet:

    ![[12]][12]

27. No prompt na janela de shell bash, pressione uma tecla e aguarde enquanto o script instala os serviços de diretório e DNS em cada uma das VMs AD.

28. Quando o prompt *entre em sua conta para cada servidor do Azure AD para verificar se os serviços de diretório foi configurado com êxito* for exibida, abra uma conexão de área de trabalho remota de um computador local para o jumpbox (*basename*- jb-máquina virtual) e abra outra conexão de desktop remoto do jumpbox para o primeiro servidor do AD (*basename*-IA1-máquina virtual). Faça logon usando o **nome_de_domínio**, **ADMIN_USER_NAME**e **ADMIN_PASSWORD** que você especificou no script azuredeploy.sh. Usando o Gerenciador de servidor, verifique se que as funções do AD DS e DNS ambos foram adicionadas. Repita esse processo para o segundo servidor do AD (*basename*-ad2-máquina virtual).

29. No prompt na janela de shell bash, pressione uma tecla. Quando o prompt *Pressione qualquer tecla para definir as configurações de DNS de VNet do Azure para apontar para o DNS no Azure* for exibida, pressione uma tecla e permitir o script atualizar as configurações de DNS para o VNet.

30. Quando o prompt *Verifique se que o DNS VNet configuração foi atualizado referência o DNS de máquina virtual do Azure servidores* é exibida, usando a verificação de portal Azure a configuração *DNS Servers* a *basename*- vnet VNet no grupo de recursos *basename*rg - ntwk. Os servidores DNS primárias e secundários agora devem fazer referência as duas VMs AD:

    ![[13]][13]

31. Reinicie cada uma das VMs AD antes de continuar. Essa etapa é necessária para garantir que cada um deles pegar as configurações de DNS corretas do Azure. Aguarde até que as duas VMs estiver executando antes de continuar.

32. No prompt na janela de shell bash, pressione uma tecla. No próximo prompt, *Pressione qualquer tecla para aplicar o gateway UDR à sub-rede gateway (ele pode ter sido removido)*, pressione uma tecla e permitir o script atualizar o gateway UDR.

33. Verifique se o script for concluído com êxito.

## <a name="next-steps"></a>Próximas etapas

- Aprenda as práticas recomendadas para a [criação de uma floresta de recursos ADICIONA] [ adds-resource-forest] no Azure.

- Aprenda as práticas recomendadas para a [criação de uma infraestrutura de ADFS] [ adfs] no Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[architecture]: #architecture_diagram
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048(v=ws.11).aspx
[powershell-ad]: https://technet.microsoft.com/en-us/library/ee617195.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[cli-install]: https://azure.microsoft.com/documentation/articles/xplat-cli-install
[github-desktop]: https://desktop.github.com/
[sssd-and-active-directory]: https://help.ubuntu.com/lts/serverguide/sssd-ad.html
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[AD-FSMO-recommendations]: #active_directory_FSMO_placement_recommendations
[AD-FSMO-roles-in-windows]: https://support.microsoft.com/en-gb/kb/197132
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[transfer-FSMO-roles]: https://technet.microsoft.com/library/cc816946(v=ws.10).aspx
[view-fsmo-roles]: https://technet.microsoft.com/library/cc816893(v=ws.10).aspx
[read-only-dc]: https://technet.microsoft.com/library/cc732801(v=ws.10).aspx
[AD-sites-and-subnets]: https://blogs.technet.microsoft.com/canitpro/2015/03/03/step-by-step-setting-up-active-directory-sites-subnets-site-links/
[sites-overview]: https://technet.microsoft.com/library/cc782048(v=ws.10).aspx
[implementing-adfs]: ./guidance-iaas-ra-secure-vnet-adfs.md
[onpremdeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/onpremdeploy.sh
[azuredeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/azuredeploy.sh
[solution-components]: #solution_components

[0]: ./media/guidance-iaas-ra-secure-vnet-ad/figure1.png "Arquitetura de rede híbrida com o Active Directory protegida"
[1]: ./media/guidance-iaas-ra-secure-vnet-ad/figure2.png "Console de usuários e Active Directory computadores listando as duas VMs Azure como servidores"
[2]: ./media/guidance-iaas-ra-secure-vnet-ad/figure3.png "O console de serviços e Sites do Active Directory mostrando as configurações de replicação de site na nuvem"
[3]: ./media/guidance-iaas-ra-secure-vnet-ad/figure4.png "O conteúdo do grupo de recursos de basename-ntwk-rg"
[4]: ./media/guidance-iaas-ra-secure-vnet-ad/figure5.png "As sub-redes na VNet basename-vnet"
[5]: ./media/guidance-iaas-ra-secure-vnet-ad/figure6.png "Os itens na camada da web"
[6]: ./media/guidance-iaas-ra-secure-vnet-ad/figure7.png "Os NVAs no grupo de recursos de basename de gerenciamento de rg"
[7]: ./media/guidance-iaas-ra-secure-vnet-ad/figure8.png "Os recursos no grupo de recursos de basename-dmz-rg"
[8]: ./media/guidance-iaas-ra-secure-vnet-ad/figure9.png "Localizando o endereço IP público do gateway VPN do Azure"
[9]: ./media/guidance-iaas-ra-secure-vnet-ad/figure10.png "As configurações de servidor DNS para o * basename *-vnet VNet"
[10]: ./media/guidance-iaas-ra-secure-vnet-ad/figure11.png "O * basename *-dns-grupo de recursos de rg que contém o servidor do AD VMs"
[11]: ./media/guidance-iaas-ra-secure-vnet-ad/figure12.png "Console de usuários e Active Directory computadores listando o servidor do AD VMs como membros do domínio"
[12]: ./media/guidance-iaas-ra-secure-vnet-ad/figure13.png "O console de serviços e Sites do Active Directory mostrando o site de replicação para os servidores do Azure AD"
[13]: ./media/guidance-iaas-ra-secure-vnet-ad/figure14.png "As configurações de servidor DNS para o VNet vnet basename referenciando servidores de anúncio na nuvem"