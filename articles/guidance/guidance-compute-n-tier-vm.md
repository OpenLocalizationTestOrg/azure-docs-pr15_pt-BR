<properties
   pageTitle="Executando o Windows VMs para uma arquitetura de N camadas | Arquitetura de referência | Microsoft Azure"
   description="Como implementar uma arquitetura de vários nível no Azure, pagamento atenção especial para disponibilidade, segurança, escalabilidade e segurança de capacidade de gerenciamento."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-for-an-n-tier-architecture-on-azure"></a>Executando o Windows VMs para uma arquitetura de N camadas no Azure

> [AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Executando Linux VMs para uma arquitetura de N camadas no Azure](guidance-compute-n-tier-vm-linux.md)
- [Executando o Windows VMs para uma arquitetura de N camadas no Azure](guidance-compute-n-tier-vm.md)

Este artigo descreve um conjunto de práticas comprovadas para execução de máquinas virtuais do Windows (VMs) para um aplicativo com uma arquitetura de N camadas. Ele se baseia no artigo [executando várias VMs no Azure][multi-vm].

> [AZURE.NOTE] Azure tem dois diferentes modelos de implantação: [Gerenciador de recursos] [ resource-manager-overview] e clássico. Este artigo usa o Gerenciador de recursos, a Microsoft recomenda para novas implantações.

## <a name="architecture-diagram"></a>Diagrama de arquitetura

Há variações de arquiteturas de N camadas. Para maior parte, as diferenças não importam para fins dessas recomendações. Este artigo pressupõe um aplicativo web típico de nível 3:

- **Nível da Web.** Trata solicitações HTTP de entrada. As respostas são retornadas por essa camada.

- **Nível de negócios.** Processos de negócios implementa e outra lógica funcional para o sistema.

- **Camada de banco de dados.** Fornece armazenamento de dados persistentes, usando o [SQL Server sempre em grupos de disponibilidade] [ sql-alwayson] para alta disponibilidade.

> Um documento do Visio que inclui este diagrama de arquitetura está disponível para download no [Centro de download da Microsoft][visio-download]. Este diagrama é em "computação - página de camada (Windows) de vários.

![[0]][0]

- **Conjuntos de disponibilidade.** Criar um [Conjunto de disponibilidade] [ azure-availability-sets] para cada nível e provisionar pelo menos duas VMs em cada camada. Essa abordagem é necessária para alcançar a disponibilidade [SLA] [ vm-sla] para VMs.

- **Sub-redes.** Crie uma sub-rede separada para cada nível. Especifique a endereço intervalo e máscara de sub-rede usando a notação [CIDR] . 

- **Balanceadores de carga.** Use um [balanceador de carga de voltado para a Internet] [ load-balancer-external] para distribuir o tráfego da Internet para a camada da web e um [balanceador de carga interno] [ load-balancer-internal] para distribuir tráfego de rede da camada de web para o nível de negócios.

- **Jumpbox**. Um _jumpbox_, também chamado de um [host bastião], é uma máquina virtual na rede que os administradores usam para se conectar a outras VMs. O jumpbox tem um NSG que permite que o tráfego remoto somente a partir de endereços IP públicos de lista branca. O NSG deve permitir o tráfego de área de trabalho remota (RDP).

- **Monitoramento**. Software como [Nagios], [Zabbix]ou [Icinga] de monitoramento pode dar percepção tempo de resposta, tempo de atividade de máquina virtual e a integridade geral do seu sistema. Instale o software de monitoramento em uma máquina virtual que é colocada em uma sub-rede de gerenciamento separada.

- **NSGs**. Usar [grupos de segurança de rede] [ nsg] (NSGs) para restringir o tráfego de rede dentro do VNet. Por exemplo, a arquitetura de nível 3 mostrado aqui, a camada de banco de dados não aceitem tráfego de web front-end, apenas na camada de negócios e a sub-rede de gerenciamento.

- **SQL Server sempre no grupo de disponibilidade.** Fornece alta disponibilidade a camada de dados, permitindo replicação e failover.

- **Servidores do active Directory Domain Services (AD DS)**. Serviços de domínio Active Directory (AD DS) armazena dados de diretório e gerencia a comunicação entre usuários e domínios, inclusive processos de logon do usuário, autenticação e pesquisas de diretório. Um controlador de domínio do Active Directory é um servidor que está executando o AD DS. Antes de Windows Server 2016, sempre em grupos de disponibilidade deve ser associados a um domínio. Isso ocorre porque os grupos de disponibilidade dependem da tecnologia do Windows Server Failover Cluster (WSFC). Windows Server 2016 introduz a capacidade de criar um Cluster de Failover sem o Active Directory. Para obter mais informações, consulte [o que há de novo no cluster de Failover no Windows Server 2016][wsfc-whats-new]

## <a name="recommendations"></a>Recomendações

Azure oferece muitos recursos diferentes e tipos de recurso, portanto, essa arquitetura de referência pode ser provisionado muitas maneiras diferentes. Fornecemos um modelo do Gerenciador de recursos do Azure para instalar a arquitetura de referência que segue essas recomendações. Se você optar por criar sua própria arquitetura de referência seguir essas recomendações, a menos que tenha um requisito específico que não correspondam a uma recomendação.

### <a name="vnet--subnets"></a>VNet / sub-redes

Quando você cria o VNet, alocar bastante espaço de endereço para as sub-redes que você precisará. Especifica a máscara VNet de intervalo e sub-rede endereço usando a notação [CIDR] . Usar um espaço de endereço que esteja dentro de padrão [blocos de endereço IP particulares][private-ip-space], que são 10.0.0.0/8, 172.16.0.0/12/12 e 192.168.0.0/16.

Escolha um intervalo de endereços que não sobreponha a sua rede local, caso você precise configurar um gateway entre o VNet e sua rede local mais tarde. Depois de criar a VNet, você não pode alterar o intervalo de endereços.

Design sub-redes com os requisitos de segurança e funcionalidade em mente. Todas as VMs dentro do mesmo nível ou função devem ir para a mesma sub-rede, que pode ser um limite de segurança. Para obter mais informações sobre como criar VNets e sub-redes, consulte [plano e design redes virtuais Azure][plan-network].

Para cada sub-rede, especifique o espaço de endereço para a sub-rede na notação CIDR. Por exemplo, '10.0.0.0/24' cria um intervalo de 256 endereços IP. (VMs podem usar 251 desses; cinco são reservados. Consulte as [perguntas Frequentes de rede Virtual][vnet faq].) Certificar-se de que os intervalos de endereços não se sobreponham em sub-redes.

### <a name="network-security-groups"></a>Grupos de segurança de rede

Use regras NSG para restringir o tráfego entre os níveis. Por exemplo, a arquitetura de nível 3 mostrado acima, a camada da web não se comunicar diretamente com a camada de banco de dados. Para impor isso, a camada de banco de dados deve bloquear tráfego de entrada da sub-rede camada da web.  

  1. Criar um NSG e associá-lo à sub-rede de nível de banco de dados.

  2. Adicione uma regra que nega todo o tráfego da VNet. (Use a `VIRTUAL_NETWORK` marca na regra.) 

  3. Adicione uma regra com uma prioridade mais alta que permite o tráfego de entrada da sub-rede nível comercial. Essa regra substitui a regra anterior e permite a camada de negócios conversar com a camada de banco de dados.

  4. Adicione uma regra que permita o tráfego de entrada de dentro da sub-rede de nível de banco de dados em si. Essa regra permite a comunicação entre VMs na camada de banco de dados, o que é necessário para replicação de banco de dados e failover.

  5. Adicione uma regra que permita o tráfego RDP da sub-rede jumpbox. Essa regra permite que administradores de se conectar à camada de banco de dados a jumpbox.

  > [AZURE.NOTE] Um NSG tem [regras padrão] [ nsg-rules] que permitir que qualquer tráfego de entrada de dentro do VNet. Essas regras não podem ser excluídas, mas você pode substituí-los criando regras de prioridade mais alta.

### <a name="load-balancers"></a>Balanceadores de carga

O balanceador de carga externos distribui o tráfego de Internet para a camada da web. Crie um endereço IP público para essa balanceador de carga. Consulte [Criando um balanceador de carga voltado para a Internet][lb-external-create].

O balanceador de carga interno distribui o tráfego de rede da camada de web para o nível de negócios. Para dar a esse balanceador de carga de um endereço IP privado, crie uma configuração de IP frontend e associá-lo com a sub-rede para a camada de negócios. Consulte [começar a criar um balanceador de carga interno][lb-internal-create].

### <a name="sql-server-always-on-availability-groups"></a>SQL Server sempre em grupos de disponibilidade

Recomendamos [Sempre em grupos de disponibilidade] [ sql-alwayson] para alta disponibilidade do SQL Server. Sempre em grupos de disponibilidade requerem um controlador de domínio. Todos os nós no grupo de disponibilidade devem estar no mesmo domínio AD.

Outros níveis se conectar ao banco de dados por meio de um [ouvinte de grupo de disponibilidade][sql-alwayson-listeners]. O ouvinte permite que um cliente SQL para se conectar sem saber o nome da instância física do SQL Server. VMs que acessar o banco de dados deve ser associado ao domínio. O cliente (neste caso, a outra camada) usa o DNS para resolver o nome de rede virtual do ouvinte em endereços IP.

Configure SQL Server sempre em da seguinte maneira:

- Crie um cluster de agrupamento de Failover do Windows Server (WSFC) e um grupo de disponibilidade do SQL Server sempre ligado. Para obter mais informações, consulte [Introdução ao sempre em grupos de disponibilidade][sql-alwayson-getting-started].

- Crie um balanceador de carga interna com um endereço IP privado estático.

- Crie um ouvinte de grupo de disponibilidade e mapear nome DNS do ouvinte para o endereço IP de um balanceador de carga interno. 

- Crie uma regra de Balanceador de carga para a porta ouvinte do SQL Server (porta TCP 1433 por padrão). A regra de Balanceador de carga deve habilitar _IP flutuante_, também chamado servidor direto retornar. Isso faz com que a máquina virtual responder diretamente para o cliente, que permite uma conexão direta para a réplica principal.

    > [AZURE.NOTE] Quando flutuante IP está habilitado, o número da porta front-end deve ser a mesma que o número da porta de back-end na regra de Balanceador de carga.

Quando um cliente SQL tenta se conectar, o balanceador de carga roteia a solicitação de conexão para a réplica que é o principal atual. Se houver um failover para outra réplica, balanceador de carga circula automaticamente as solicitações subsequentes para a nova réplica primária. Para obter mais informações, consulte [Configurar balanceador de carga para SQL sempre em][sql-alwayson-ilb].

Durante um failover, conexões de cliente existentes são fechadas. Após o failover, novas conexões serão roteados para a nova réplica primária.

Se seu aplicativo faz leituras significativamente mais que as gravações, você pode descarregar alguma das consultas somente leitura em uma réplica secundária. Consulte [usando um ouvinte para se conectar a um secundário somente leitura réplica (somente leitura roteamento)][sql-alwayson-read-only-routing].

Testar sua implantação forçando [um failover manual][sql-alwayson-force-failover].

### <a name="jumpbox"></a>Jumpbox

Não permitir o acesso RDP da Internet pública às VMs que executam a carga de trabalho do aplicativo. Em vez disso, todo o acesso RDP/SSH a essas VMs deve ocorrer através do jumpbox. Um administrador efetua login a jumpbox e registra nas outras máquinas virtuais da jumpbox. O jumpbox permite tráfego RDP da Internet, mas somente a partir de endereços IP conhecidos, na lista branca.

Coloque o jumpbox na mesma VNet como as outras VMs, mas em uma sub-rede de gerenciamento separada.

Crie um [endereço IP público] para o jumpbox.

Use um tamanho pequeno de máquina virtual para o jumpbox, como A1 padrão.

Configure os NSGs para a camada da web, camada de negócios e sub-redes de camada de banco de dados para permitir o tráfego (RDP) administrativo passar através da sub-rede gerenciamento.

Para proteger o jumpbox, crie um NSG e aplicá-lo à sub-rede jumpbox. Adicione uma regra NSG que permite conexões RDP somente a partir de um conjunto de lista branca de endereços IP públicos.

O NSG pode ser anexado a sub-rede ou a jumpbox NIC. Nesse caso, é recomendável anexá-lo para a NIC, para que o tráfego RDP é permitido somente para o jumpbox, mesmo se você adicionar outras VMs à mesma sub-rede.

## <a name="availability-considerations"></a>Considerações de disponibilidade

Coloca cada camada ou a função de máquina virtual em um conjunto de disponibilidade separada. Não coloque VMs de diferentes camadas o mesmo conjunto de disponibilidade. 

Na camada de banco de dados, ter várias VMs não automaticamente traduzir em um banco de dados altamente disponível. Para um banco de dados relacional, normalmente você precisará usar replicação e failover para obter alta disponibilidade. Para SQL Server, é recomendável usar [Sempre em grupos de disponibilidade][sql-alwayson]. 

Se você precisa de disponibilidade maior do que o [Azure SLA para VMs] [ vm-sla] fornece, replicar o aplicativo em duas regiões e usar o Gerenciador de tráfego do Azure para failover. Para obter mais informações, consulte [Executando o Windows VMs em vários regiões para alta disponibilidade][multi-dc].   

## <a name="security-considerations"></a>Considerações de segurança

Criptografe dados inativos. Usar o [Azure chave cofre] [ azure-key-vault] para gerenciar as chaves de criptografia do banco de dados. Chave cofre pode armazenar chaves de criptografia em módulos de segurança de hardware (HSMs). Para obter mais informações, consulte [Configurar integração de cofre do Azure chave para o SQL Server em VMs Azure] [ sql-keyvault] também recomenda-se para armazenar segredos do aplicativo, como cadeias de caracteres de conexão de banco de dados, no cofre de chave.

Considere adicionar um dispositivo de rede virtual (NVA) para criar um DMZ entre a Internet pública e a rede virtual Azure. NVA é um termo genérico para um dispositivo virtual que pode realizar tarefas relacionadas à rede como firewall, inspeção de pacote, auditoria, roteamento personalizado ou uma variedade de outras operações. Para obter mais informações, consulte [Implementando DMZ entre Azure e a Internet][dmz].

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Os balanceadores de carga distribuir o tráfego de rede para os níveis de negócios e da web. Dimensionar horizontalmente, adicionando novas instâncias de máquina virtual. Observe que você pode dimensionar os níveis da web e business independentemente, com base na carga. Para reduzir possíveis complicações causadas pela necessidade de manter afinidade de cliente, VMs na camada da web devem estar sem estado. As VMs que hospeda a lógica comercial também devem estar sem estado.

## <a name="manageability-considerations"></a>Considerações de capacidade de gerenciamento

Simplificar o gerenciamento de todo o sistema usando ferramentas de administração centralizada como [Automação Azure][azure-administration], [Pacote de gerenciamento de operações do Microsoft][operations-management-suite], [chefe][chef], ou [Puppet][puppet]. Essas ferramentas podem consolidar informações de diagnóstico e integridade capturadas de várias VMs para fornecer uma visão geral do sistema.

## <a name="solution-deployment"></a>Implantação da solução

Uma implantação para uma arquitetura de referência que implementa essas recomendações está disponível no [Github][github-folder]. Essa arquitetura de referência inclui uma camada de web, camada de negócios, uma camada de dados, bem como um jumpbox controladores de domínio Active Directory e de máquina virtual.

A arquitetura de referência pode ser implantada seguindo as instruções abaixo: 

1. Clique no botão abaixo.  
[! ["Implantar para Azure"] [1]][2]

2. Depois que o link for aberto no portal do Azure, insira os valores de acompanhamento: 
  - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-ntier-sql-network-rg` na caixa de texto.
  - Selecione a região na caixa suspensa **local** .
  - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
  - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
  - Clique no botão de **compra** .

3. Verificar a notificação de portal Azure para uma mensagem a implantação for concluída.

4. Clique no botão abaixo.  
[! ["Implantar para Azure"] [1]][3]

5. Depois que o link for aberto no portal do Azure, insira os valores de acompanhamento: 
  - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Usar existente** e insira `ra-ntier-sql-workload-rg` na caixa de texto.
  - Selecione a região na caixa suspensa **local** .
  - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
  - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
  - Clique no botão de **compra** .

6. Verificar a notificação de portal Azure para uma mensagem a implantação for concluída.

7. Clique no botão abaixo.  
[! ["Implantar para Azure"] [1]][4]

8. Depois que o link for aberto no portal do Azure, insira os valores de acompanhamento: 
  - O nome do **grupo de recursos** já está definido no arquivo de parâmetros, portanto selecione **Criar novo** e digite `ra-ntier-sql-network-rg` na caixa de texto.
  - Selecione a região na caixa suspensa **local** .
  - Não edite o **Modelo raiz Uri** ou as caixas de texto de **Uri de raiz de parâmetro** .
  - Examine os termos e condições, clique na caixa de seleção **para concordar com os termos e condições mencionadas acima** .
  - Clique no botão de **compra** .

9. Verificar a notificação de portal Azure para uma mensagem a implantação for concluída.

10. Os arquivos de parâmetro incluem um embutida nomes de usuário de administrador e senhas e é altamente recomendável que você altere imediatamente ambos em todas as VMs. Clique em cada máquina virtual no Portal do Azure e clique em **Redefinir senha** na lâmina **suporte + solução de problemas** . Selecione **Redefinir a senha** na caixa de lista suspensa **modo** e, em seguida, selecione um novo **nome de usuário** e **senha**. Clique no botão de **atualização** para manter o novo nome de usuário e senha. 

Para obter informações sobre outras maneiras para implantar essa arquitetura de referência, consulte o arquivo Leiame na [orientação-único-máquina virtual] [ github-folder] Github pasta. 

## <a name="next-steps"></a>Próximas etapas

Para obter alta disponibilidade para essa arquitetura de referência, recomendamos [Implantando para várias regiões][multi-dc].

<!-- links -->

[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[azure-administration]: ../automation/automation-intro.md
[azure-audit-logs]: ../resource-group-audit.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-key-vault]: https://azure.microsoft.com/services/key-vault.md
[azure-load-balancer]: ../load-balancer/load-balancer-overview.md
[host bastião]: https://en.wikipedia.org/wiki/Bastion_host
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier-sql
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters.md
[multi-vm]: guidance-compute-multi-vm.md
[n-tier]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[endereço IP público]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[sql-alwayson]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/en-us/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/en-us/library/gg509118.aspx
[sql-alwayson-ilb]: https://blogs.msdn.microsoft.com/igorpag/2016/01/25/configure-an-ilb-listener-for-sql-server-alwayson-availability-groups-in-azure-arm/
[sql-alwayson-listeners]: https://msdn.microsoft.com/en-us/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/en-us/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: ../virtual-machines/virtual-machines-windows-ps-sql-keyvault.md
[vm-planned-maintenance]: ../virtual-machines/virtual-machines-windows-planned-maintenance.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/deploy-reference-architecture.sh
[vnet-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/virtualNetwork.parameters.json
[vnet-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/networkSecurityGroups.parameters.json
[nsg-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/webTier.parameters.json
[webtier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/webTier.parameters.json
[businesstier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/businessTier.parameters.json
[businesstier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/businessTier.parameters.json
[datatier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/dataTier.parameters.json
[datatier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/dataTier.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/compute-n-tier.png "Arquitetura de N camadas usando o Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2FvirtualNetwork.azuredeploy.json
[3]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fworkload.azuredeploy.json
[4]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fsecurity.azuredeploy.json