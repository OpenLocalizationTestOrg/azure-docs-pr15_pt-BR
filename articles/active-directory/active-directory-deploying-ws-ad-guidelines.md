<properties
   pageTitle="Diretrizes para implantar o servidor Active Directory do Windows Azure máquinas virtuais | Microsoft Azure"
   description="Se você souber como implantar os serviços de domínio do AD e os serviços de Federação do AD no local, saber como elas funcionam em Azure máquinas virtuais."
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/27/2016"
   ms.author="femila"/>

# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Diretrizes para implantar o Active Directory do Windows Server em Azure máquinas virtuais

Este artigo explica as diferenças importantes entre Implantando Windows Server Active Directory Domain Services (AD DS) e os serviços de Federação do Active Directory (AD FS) local versus implantá-los em máquinas virtuais do Microsoft Azure.

## <a name="scope-and-audience"></a>Escopo e público

O artigo destina-se para aqueles que já experiente Implantando o Active Directory local. Ela aborda as diferenças entre implantar o Active Directory em redes virtuais do Microsoft Azure máquinas virtuais/Azure e implantações do Active Directory local tradicional. Azure máquinas virtuais e redes virtuais Azure fazem parte de um infraestrutura-como um serviço (IaaS) oferecendo para as organizações aproveitar os recursos de computação na nuvem.

Para aqueles que não estão familiarizados com implantação do AD, consulte o [Guia de implantação do AD DS](https://technet.microsoft.com/library/cc753963) ou [planejar sua implantação do AD FS](https://technet.microsoft.com/library/dn151324.aspx) conforme apropriado.

Este artigo pressupõe que o leitor esteja familiarizado com os seguintes conceitos:

- Gerenciamento e implantação do Windows Server AD DS
- Implantação e configuração do DNS para dar suporte a uma infraestrutura do Windows Server AD DS
- Gerenciamento e implantação do Windows Server AD FS
- Implantar, configurar e gerenciar aplicativos de terceiros terceira (sites e serviços da web) que podem consumir tokens do Windows Server AD FS
- Conceitos gerais máquina virtual, como como configurar uma máquina virtual, discos virtuais e redes virtuais

Este artigo destaca os requisitos para um cenário de implantação híbrida em que Windows Server AD DS ou o AD FS são parcialmente implantado local e parcialmente implantados em Azure máquinas virtuais. O documento primeiro aborda as diferenças importantes entre executando o Windows Server AD DS e do AD FS em máquinas virtuais Azure versus local e decisões importantes que afetam o design e a implantação. O restante do papel explica as diretrizes para cada um dos pontos de decisão mais detalhadamente e como aplicar as diretrizes para vários cenários de implantação.

Este artigo não aborda a configuração do [Active Directory do Azure](http://azure.microsoft.com/services/active-directory/), que é um serviço baseado em REST que oferece gerenciamento de identidade e recursos de controle de acesso para aplicativos em nuvem. Azure Active Directory (AD Azure) e Windows Server AD DS, no entanto, projetados para trabalhar em conjunto para fornecer uma solução de gerenciamento de identidades e acesso de TI híbridos de hoje ambientes e aplicativos modernos. Para ajudar a compreender as diferenças e as relações entre Windows Server AD DS e Azure AD, considere o seguinte:

1. Você pode executar o Windows Server AD DS na nuvem em Azure máquinas virtuais quando você estiver usando o Azure para estender seu centro de dados local para a nuvem.
2. Você pode usar o Azure AD para dar a seu usuários logon único para aplicativos de Software-como um serviço (SaaS). Por exemplo, o Microsoft Office 365 utiliza essa tecnologia, e aplicativos em execução no Azure ou outras plataformas na nuvem também podem usá-lo.
3. Você pode usar o Azure AD (seu serviço de controle de acesso) para permitir que usuários entrar usando identidades do Facebook, Google, Microsoft e de outros provedores de identidade para aplicativos que são hospedados na nuvem ou local.

Para obter mais informações sobre essas diferenças, consulte [Identidade do Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Recursos relacionados

Você pode baixar e executar a [Avaliação de prontidão de máquina Virtual do Azure](https://www.microsoft.com/download/details.aspx?id=40898). A avaliação será automaticamente inspecionar seu ambiente local e gerar um relatório personalizado com base na orientação encontrada neste tópico para ajudá-lo a migrar o ambiente para o Azure.

Recomendamos que você também primeiro examinar os tutoriais, guias e vídeos que abrangem os seguintes tópicos:

- [Configurar uma rede Virtual somente na nuvem no Portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [Configurar uma VPN to-Site no Portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Instalar uma nova floresta do Active Directory em uma rede virtual Azure](active-directory-new-forest-virtual-machine.md)
- [Instalar um controlador de domínio do Active Directory de réplica no Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IT Pro IaaS: conceitos básicos (01) máquina Virtual](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e conectividade entre locais](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introdução

Requisitos fundamentais para implantar o Active Directory do Windows Server em Azure máquinas virtuais diferem muito pouco de implantá-lo em máquinas virtuais de local (e, até certo ponto, máquinas físicas). Por exemplo, no caso do Windows Server AD DS, se o domínio (controladores) que você implantar em Azure máquinas virtuais são réplicas em existente locais floresta/domínio corporativa, e em seguida, a implantação do Azure amplamente pode ser tratada da mesma forma como você pode tratar qualquer outro site do Active Directory do Windows Server adicional. Isto é, sub-redes devem ser definidos no Windows Server AD DS, um site criado, as sub-redes vinculado ao site e conectado a outros sites usando links de sites apropriado. No entanto, há algumas diferenças que são comuns a todas as implantações do Azure e alguns que variar de acordo com o cenário de implantação específicas. Duas diferenças fundamentais são descritas abaixo:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Azure máquinas virtuais pode ser necessário conectividade à rede corporativa no local.

A conexão do Azure máquinas virtuais para uma rede corporativa local requer Azure rede virtual, que inclui um-to-site ou site-a-ponto virtual privada (VPN) componente de rede capaz de se conectar perfeitamente Azure máquinas virtuais e máquinas locais. Este componente VPN também pode habilitar computadores de membros do domínio local acessar um domínio do Active Directory do Windows Server cujos controladores de domínio são hospedados exclusivamente no Azure máquinas virtuais. É importante observar, porém, que se a VPN falhar, autenticação e outras operações que dependem do Active Directory do Windows Server também falhará. Enquanto os usuários podem ser capazes de entrar utilizando existentes cache credenciais, tudo-a-ponto ou tentativas de autenticação de cliente e servidor para o qual tíquetes ainda precisam ser emitido ou tornaram obsoletos falhará.

Consulte [Rede Virtual](http://azure.microsoft.com/documentation/services/virtual-network/) para uma demonstração em vídeo e uma lista de tutoriais passo a passo, incluindo [Configurar uma VPN to-Site no portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Você também pode implantar o Active Directory do Windows Server em uma rede virtual Azure que não tiver conectividade com uma rede local. As diretrizes neste tópico, no entanto, supõem que uma rede virtual Azure é usada porque ele fornece recursos que são essenciais para o Windows Server de endereçamento IP.

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Endereços IP estáticos deverá ser configurados com o PowerShell do Azure.

Endereços dinâmicos estão alocados por padrão, mas usam o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático, em vez disso. Que define um endereço IP estático que serão mantidas por meio de reparo de serviço e máquina virtual desligamento/reiniciar. Para obter mais informações, consulte [endereço IP estático interno para máquinas virtuais](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termos e definições

A seguir está uma lista parcial de termos para várias tecnologias Azure que serão referenciados neste artigo.

- **Azure máquinas virtuais**: IaaS a oferta no Azure que permite aos clientes implantar VMs executando praticamente qualquer normalmente carga de trabalho de servidor local.

- **Rede virtual Azure**: O serviço de rede no Azure que permite aos clientes criar e gerenciar redes virtuais no Azure e vinculá-las com segurança em suas próprias local infraestrutura de rede usando uma rede virtual privada (VPN).

- **Endereço IP virtual**: um endereço IP voltado para a internet que não está associado a uma placa de interface de rede ou computador específico. Serviços de nuvem são atribuídos a um endereço IP virtual para o recebimento de tráfego de rede que será redirecionado para uma máquina virtual do Azure. Um endereço IP virtual é uma propriedade de um serviço de nuvem que pode conter um ou mais máquinas virtuais Azure. Observe também que uma rede virtual Azure pode conter um ou mais serviços de nuvem. Endereços IP virtuais fornecem recursos de balanceamento de carga nativos.

- **Endereço IP dinâmico**: Este é o endereço IP que só é interno. Ele deve ser configurado como um endereço IP estático (usando o cmdlet Set-AzureStaticVNetIP) para VMs que hospedam as funções de servidor DNS/DC.

- **Serviço de reparo**: O processo no qual Azure retorna automaticamente um serviço para um estado de execução novamente após detecta que o serviço falhou. Serviço de reparo é um dos aspectos do Azure que ofereça suporte a disponibilidade e resiliência. Enquanto provável, o resultado seguindo um serviço de reparo incidente para um DC executando em uma máquina virtual é semelhante a uma reinicialização planejada, mas tem algumas lado-efeitos:

 - O adaptador de rede virtual na máquina virtual será alterado
 - O endereço de MAC do adaptador de rede virtual será alterado
 - A ID de processador/CPU da máquina virtual será alterado
 - A configuração de IP do adaptador de rede virtual não serão alteradas desde a máquina virtual está conectada a uma rede virtual e endereço IP da VM é estático.

 Nenhum desses comportamentos afetam o Active Directory do Windows Server porque ela não tem dependência no endereço MAC ou processador/CPU ID, e todas as implantações do Active Directory do Windows Server no Azure são recomendadas para ser executada em uma rede virtual Azure conforme descrito acima.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>É seguro a virtualização controladores de domínio do Active Directory do Windows Server?

Implantar o Windows Server Active Directory controladores de domínio em Azure máquinas virtuais está sujeitas as mesmas diretrizes como executando controladores de domínio local em uma máquina virtual. A execução de controladores de domínio virtualizados é uma prática segura desde que as diretrizes de backup e restauração controladores de domínio sejam seguidas. Para obter mais informações sobre restrições e diretrizes para execução virtualizados controladores de domínio, consulte [Controladores de domínio em execução no Hyper-V](https://technet.microsoft.com/library/dd363553).

Hipervisores fornecem ou trivialize tecnologias que podem causar problemas para vários sistemas distribuídos, inclusive do Active Directory do Windows Server. Por exemplo, em um servidor físico, você pode clonar um disco ou usar métodos sem suporte para reverter o estado de um servidor, incluindo usando SANs e assim por diante, mas fazer isso em um servidor físico é muito mais difícil do que a restauração de um instantâneo de máquina virtual em um hipervisor. Azure oferece funcionalidade que pode resultar na mesma condição indesejável. Por exemplo, você não deve copiar arquivos VHD de controladores de domínio em vez de realização de backups regulares como restaurá-los pode resultar em uma situação semelhante ao uso de recursos de restauração de instantâneo.

Tais reversões apresentam bolhas USN que podem levar a estados permanentemente divergentes entre controladores de domínio. Que podem causar problemas, como:

- Objetos remanescentes
- Senhas inconsistentes
- Valores de atributo inconsistente
- Diferenças de esquema se o mestre de esquema é revertido

Para obter mais informações sobre como controladores de domínio são afetados, consulte [USN e reversão do USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Começando com o Windows Server 2012, [garantias adicionais são criadas AD DS](https://technet.microsoft.com/library/hh831734.aspx). Essas garantias ajudam a proteger controladores de domínio virtualizado contra os problemas mencionados anteriormente, desde que a plataforma hipervisor subjacente suporta GenerationID de máquina virtual. Azure suporta máquina virtual-GenerationID, o que significa que controladores de domínio que executam o Windows Server 2012 ou posterior no Azure máquinas virtuais tem as medidas de segurança adicionais.

> [AZURE.NOTE] Você deve desligar e reiniciar uma máquina virtual que executa a função de controlador de domínio no Azure no sistema operacional convidado em vez de usar a opção **Desligar** no portal de clássico do Azure. Hoje, usando o portal de clássico para desligar uma máquina virtual faz com que a máquina virtual desalocamento. Uma máquina virtual liberada tem a vantagem de não cobrado, mas ele também redefine a máquina virtual-GenerationID, que é indesejável para um DC. Quando a máquina virtual-GenerationID é redefinida, a invocationID do banco de dados AD DS também é redefinida, o pool RID é descartado e SYSVOL está marcado como não autoritativas. Para obter mais informações, consulte [Introdução aos serviços de domínio Active Directory (AD DS) virtualização](https://technet.microsoft.com/library/hh831734.aspx) e [Virtualização DFSR com segurança](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Por que implantar o Windows Server AD DS em máquinas virtuais do Azure?

Muitos cenários de implantação do Windows Server AD DS são adequados para implantação como VMs no Azure. Por exemplo, suponha que você tenha uma empresa na Europa que precisa autenticar usuários em um local remoto na Ásia. A empresa não anteriormente implantou o Active Directory DCs do Windows Server na Ásia devido a do custo para implantar experiência-los e limitada para gerenciar a implantação de POST de servidores. Como resultado, solicitações de autenticação de Ásia forem atendidas pelo controladores de domínio na Europa com resultados com qualidade inferior. Nesse caso, você pode implantar um DC em uma máquina virtual que você especificou deve ser executado dentro do data center Azure na Ásia. Anexar DC a uma rede virtual Azure que esteja conectada diretamente para o local remoto melhora o desempenho de autenticação.

Azure também é adequado como substituto caso contrário dispendioso desastres (DR) nos locais de recuperação. O custo relativamente baixo de hospedar um pequeno número de controladores de domínio e uma única rede virtual no Azure representa uma alternativa atraente.

Por fim, talvez você queira implantar um aplicativo de rede no Azure, como o SharePoint, que requer o Windows Server Active Directory, mas não tem dependência na rede local ou o Windows Server Active Directory corporativa. Nesse caso, os requisitos do servidor Implantando uma floresta isolada no Azure para cumprir do SharePoint é ideal. Novamente, também há suporte para implantação de aplicativos de rede que requerem conectividade com a rede local e o Active Directory corporativa.

> [AZURE.NOTE] Como ele fornece uma conexão de camada 3, o componente VPN que fornece conectividade entre uma rede virtual Azure e uma rede local também pode habilitar servidores membro que executam o local para aproveitar controladores de domínio que são executadas como Azure máquinas virtuais em uma rede virtual Azure. Mas, se a VPN estiver disponível, a comunicação entre computadores locais e controladores de domínio com base no Azure não funcionará, resultando em autenticação e diversos outros erros.  

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Contrastes entre implantando controladores de domínio Active Directory do Windows Server em máquinas virtuais do Azure versus local

- Para qualquer cenário de implantação do Active Directory do Windows Server que inclui mais de uma única VM, é necessário usar uma rede virtual Azure consistência de endereço IP. Observe que este guia pressupõe que controladores de domínio estão executando em uma rede virtual Azure.

- Como com controladores de domínio local, os endereços IP estáticos são recomendados. Um endereço IP estático só pode ser configurado usando o PowerShell do Azure. Consulte [interno endereço IP estático VMs](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) para obter mais detalhes. Se você tiver o monitoramento sistemas ou outras soluções que verificar configuração de endereço IP estático no sistema operacional convidado, você pode atribuir o mesmo endereço IP estático para as propriedades do adaptador de rede da máquina virtual. Mas lembre-se de que o adaptador de rede será descartado se a máquina virtual passa por serviço reparo ou está desligada no portal do clássico e foram desalocados de seu endereço. Nesse caso, o endereço IP estático dentro do convidado precisará ser redefinidas.

- Implantar VMs em uma rede virtual não sugerem (ou exigir) conectividade para uma rede local; a rede virtual simplesmente permite que essa possibilidade. Você deve criar uma rede virtual privada comunicação entre Azure e sua rede local. Você precisa implantar um ponto de extremidade VPN na rede local. A VPN é aberta a partir do Azure à rede local. Para obter mais informações, consulte [Visão geral de rede Virtual](../virtual-network/virtual-networks-overview.md) e [Configurar uma VPN to-Site no Portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Uma opção para [criar uma VPN de ponto-a-site](../vpn-gateway/vpn-gateway-point-to-site-create.md) está disponível para se conectar a computadores individuais baseado no Windows diretamente a uma rede virtual Azure.

- Independentemente de fazê uma virtual rede ou não, encargos Azure para tráfego de saída, mas não o ingresso. Várias opções de design do Active Directory do Windows Server podem afetar quanto tráfego de saída é gerado por uma implantação. Por exemplo, implantando um controlador de domínio somente leitura (RODC) limita o tráfego de saída porque ela não replica saída. Mas a decisão de implantar um RODC precisa ser avaliada em relação a necessidade de executar operações de gravação contra o DC e a [compatibilidade](https://technet.microsoft.com/library/cc755190) com aplicativos e serviços no site com RODCs. Para obter mais informações sobre encargos de tráfego, consulte [Azure preços em geral](http://azure.microsoft.com/pricing/).

- Enquanto você tiver concluído controle sobre qual servidor recursos a serem usados para VMs no local, como RAM, tamanho do disco e assim por diante, no Azure que você deve selecionar de uma lista de tamanhos de servidor pré-configurado. Para um DC, um disco de dados é necessária além do disco do sistema operacional para armazenar o banco de dados do Active Directory do Windows Server.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Você pode implantar o Windows Server AD FS no Azure máquinas virtuais?

Sim, você pode implantar o Windows Server AD FS no Azure máquinas virtuais e o [práticas recomendadas para a implantação do AD FS](https://technet.microsoft.com/library/dn151324.aspx) local se aplicam igualmente a implantação do AD FS no Azure. Mas algumas das práticas recomendadas como balanceamento de carga e alta disponibilidade exigir tecnologias além do que o AD FS oferece em si. Eles devem ser fornecidos pela infraestrutura subjacente. Vamos analisar algumas dessas práticas recomendadas e ver como ele podem ser obtidos usando VMs do Azure e uma rede virtual Azure.

1. **Nunca expor servidores de serviço (STS) de token de segurança diretamente à Internet.**

    Isso é importante porque o STS emite tokens de segurança. Como resultado, servidores de STS como servidores do AD FS devem ser tratados com o mesmo nível de proteção como controlador de domínio. Se um STS for comprometida, usuários mal-intencionados têm a capacidade para emitir tokens de acesso potencialmente contendo declarações de sua escolha para aplicativos de terceiros confiante e outros servidores de STS no confiar em organizações.

2. **Implante controladores de domínio Active Directory para todos os domínios de usuário na mesma rede que os servidores do AD FS.**

    Servidores do AD FS usam serviços de domínio Active Directory para autenticar os usuários. É recomendável implantar controladores de domínio na mesma rede que os servidores do AD FS. Isso fornece continuidade de negócios, caso o vínculo entre a rede Azure e sua rede local está quebrado e habilita menor latência e desempenho aprimorado para logon.

3. **Implante vários nós do AD FS para alta disponibilidade e balanceamento de carga.**

    Na maioria dos casos, a falha de um aplicativo que permite do AD FS é aceitável porque os aplicativos que exigem tokens de segurança são geralmente essenciais. Como resultado, e como o AD FS agora reside no caminho crítico para acessar aplicativos essenciais, o serviço do AD FS deve ser altamente disponível por meio de vários proxies do AD FS e servidores do AD FS. Para obter a distribuição de solicitações, balanceadores de carga são normalmente implantados na frente do AD FS Proxies e servidores do AD FS.

4. **Implante um ou mais nós de Proxy de aplicativo Web para acesso à internet.**

    Quando os usuários precisam acessar aplicativos protegidos pelo serviço do AD FS, o serviço do AD FS precisa estar disponível da internet. Isso é feito Implantando o serviço de Proxy de aplicativo Web. É altamente recomendável implantar mais de um nó para fins de alta disponibilidade e balanceamento de carga.

5. **Restringir o acesso de nós de Proxy de aplicativo Web aos recursos de rede interna.**

    Para permitir que usuários externos acessem o AD FS da internet, você precisa implantar nós de Proxy de aplicativo Web (ou Proxy do AD FS em versões anteriores do Windows Server). Nós de proxy do aplicativo da Web estão expostos diretamente à Internet. Eles não são necessários para ser domínio e apenas precisam acesso aos servidores do AD FS em portas TCP 443 e 80. É altamente recomendável que a comunicação com todos os outros computadores (especialmente controladores de domínio) está bloqueada.

    Isso é geralmente obtido local por meio de um DMZ. Firewalls usam um modo de lista branca de operação para restringir o tráfego da DMZ à rede local (ou seja, somente o tráfego de endereços IP especificados e sobre portas especificadas é permitido e todos os outros tráfego é bloqueado).

O diagrama a seguir mostra um tradicional locais implantação do AD FS.

![Diagrama de uma implantação de serviços de Federação do Active Directory local tradicional](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

No entanto, como Azure não fornece nativo, capacidade de firewall completo, outras opções precisam ser usado para restringir o tráfego. A tabela a seguir mostra cada opção e suas vantagens e desvantagens.

| Opção | Vantagem | Desvantagem |
| ------ | --------- | ------------ |
| [ACLs da rede Azure](virtual-networks-acl.md) | Menos dispendiosa e mais simples de configuração inicial | Configuração de ACL de rede adicionais necessários se quaisquer novas VMs são adicionadas à implantação |
| [Firewall Barracuda NG](https://www.barracuda.com/products/ngfirewall) | Branca modo de operação e ele não exige nenhuma configuração de ACL de rede | Aumento de custos e complexidade para configuração inicial |

As etapas de alto nível para implantar o AD FS nesse caso são da seguinte maneira:

1. Crie uma rede virtual com conectividade entre locais, usando uma VPN ou [rota expressa](http://azure.microsoft.com/services/expressroute/).

2. Implante controladores de domínio em uma rede virtual. Esta etapa é opcional, mas recomendado.

3. Implante domínio AD FS servidores em uma rede virtual.

4. Crie um [conjunto de balanceamento de carga interna](http://azure.microsoft.com/blog/internal-load-balancing/) que inclui os servidores do AD FS e usa um novo endereço IP particular dentro da rede virtual (um endereço IP dinâmico).

  1. Atualize o DNS para criar o FQDN para apontar para o endereço IP (dinâmico) privado do conjunto de balanceamento de carga interno.

5. Crie um serviço de nuvem (ou uma rede virtual separada) para os nós de Proxy de aplicativo Web.

6. Implantar os nós de Proxy de aplicativo Web no serviço de nuvem ou rede virtual

  1. Crie um conjunto de balanceamento de carga externo que inclui os nós de Proxy de aplicativo Web.

  2. Atualize o nome DNS externo (FQDN) para apontar para o nuvem serviço endereço IP público (o endereço IP virtual).

  3. Configure proxies do AD FS para usar o FQDN que corresponde ao conjunto de balanceamento de carga interna para os servidores do AD FS.

  4. Atualize baseada em declarações de sites para usar o FQDN externo para seu provedor de declarações.

7. Restringir o acesso entre o Proxy de aplicativo da Web a qualquer máquina de rede virtual do AD FS.

Para restringir o tráfego, o conjunto de balanceamento de carga de Balanceador de carga interno Azure precisa ser configurada para somente o tráfego para as portas TCP 80 e 443 e todos os outro tráfego para o endereço IP dinâmico interno do conjunto de balanceamento de carga for cancelado.

![Diagrama de ADFS ACLs de rede com TCP 443 + 80 permitidos.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Tráfego para os servidores do AD FS seria permitido apenas por seguintes fontes:

- O balanceador de carga interno Azure.
- O endereço IP de um administrador da rede local.

> [AZURE.WARNING] O design deve impedir nós de Proxy de aplicativo Web contatem quaisquer outras VMs da rede virtual Azure ou qualquer locais na rede local. Isso pode ser feito configurando regras de firewall no aparelho de locais para conexões via expressa ou o dispositivo VPN para conexões VPN de site para site.

Uma desvantagem a essa opção é a necessidade de configurar a rede ACLs para vários dispositivos, incluindo balanceador de carga interno, os servidores do AD FS e quaisquer outros servidores que seja adicionados à rede virtual. Se qualquer dispositivo for adicionado à implantação sem configurar ACLs de rede para restringir o tráfego para ele, a implantação inteira pode estar em risco. Se mudar os endereços IP de nós do Proxy de aplicativo Web, a rede ACLs deve redefinir (que significa que os proxies devem ser configurados para usar [endereços IP dinâmicos estáticos](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![ADFS no Azure com rede ACLS.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Outra opção é usar o aparelho [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) para controlar o tráfego entre servidores de proxy do AD FS e servidores do AD FS. Esta opção esteja em conformidade com as práticas recomendadas para segurança e alta disponibilidade e requer menos administração após a configuração inicial porque o aparelho Barracuda NG Firewall fornece um modo de lista branca de administração de firewall e ele pode ser instalado diretamente em uma rede virtual Azure. Que elimina a necessidade de configurar ACLs de rede sempre que um novo servidor é adicionado à implantação. Mas essa opção adiciona custos e complexidade de implantação inicial.

Nesse caso, duas redes virtuais são implantadas em vez de um. Vai chamamos-las VNet1 e VNet2. VNet1 contém os proxies e VNet2 contém os STSs e a conexão de rede volta à rede corporativa. VNet1 é portanto física (embora praticamente) isolada de VNet2 e, por sua vez, da rede corporativa. VNet1 é então conectada a VNet2 usando uma tecnologia de túnel especial conhecida como transporte independentes rede arquitetura (TINA). Túnel TINA está conectado a cada uma das redes virtuais usando um firewall Barracuda NG — um Barracuda em cada uma das redes virtuais.  Para alta disponibilidade, é recomendável que você implantar dois Barracudas em cada rede virtual; um ativo, o outro passivo. Eles oferecem recursos de firewall bastante variados que nos permitem simular a operação de DMZ tradicionais no local no Azure.

![ADFS no Azure com firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Para obter mais informações, consulte [do AD FS: estender um aplicativo de front-end local reconhecimento de declarações à Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Uma alternativa para implantação do AD FS se o objetivo for SSO do Office 365 sozinho

Não há outra alternativa para a implantação do AD totalmente FS se seu objetivo é apenas habilitar entrar para o Office 365. Nesse caso, você pode simplesmente implantar DirSync com senha sincronização local e atingir o mesmo resultado final com complexidade de implantação mínimo, pois essa abordagem não exige o AD FS ou Azure.

A tabela a seguir compara como os processos de entrada funcionam com e sem implantar o AD FS.

| O Office 365 único logon usando AD FS e DirSync | Office 365 mesmo logon usando DirSync + sincronização de senha |
| ------------- | ------------- |
| 1. o usuário faz logon em uma rede corporativa e está autenticado do Active Directory do Windows Server. | 1. o usuário faz logon em uma rede corporativa e está autenticado do Active Directory do Windows Server. |
| 2. o usuário tentar acessar o Office 365 (eu sou @contoso.com). | 2. o usuário tentar acessar o Office 365 (eu sou @contoso.com). |
| 3. o office 365 redireciona o usuário para o Azure AD. | 3. o office 365 redireciona o usuário para o Azure AD. |
| 4. como Azure AD não pode autenticar o usuário e entende que não há uma relação de confiança com o AD FS locais, ele redireciona o usuário para o AD FS. | 4. azure AD não aceita tíquetes Kerberos diretamente e não existe nenhuma relação de confiança para que ele solicita que o usuário inserir credenciais. |
| 5. o usuário envia um tíquete Kerberos para o STS do AD FS. | 5. o usuário insere a mesma senha local, e as Azure AD valida contra o nome de usuário e senha que foi sincronizada pela DirSync. |
| 6. AD FS transforma a permissão de Kerberos para as formato/declarações em necessárias token e redireciona o usuário para Azure AD. | 6. azure AD redireciona o usuário para o Office 365. |
| 7. a autenticação do usuário para o Azure AD (outra transformação ocorre). |  7. o usuário pode entrar no Office 365 e OWA usando o token do Azure AD. |
| 8. azure AD redireciona o usuário para o Office 365. |  |
| 9. o usuário estiver conectado silenciosamente Office 365. |  |

No Office 365 com o DirSync com cenário de sincronização de senha (sem AD FS), é substituído logon único por "mesmo logon" onde "mesmo" simplesmente significa que os usuários devem inserir novamente suas credenciais locais mesmo ao acessar o Office 365. Observe que esses dados podem ser lembrados pelo navegador do usuário para ajudar a reduzir solicitações subsequentes.

### <a name="additional-food-for-thought"></a>Alimentos adicionais para se pensar

- Se você implantar um proxy do AD FS em uma máquina virtual Azure, é necessária a conectividade com os servidores do AD FS. Se eles estiverem no local, é recomendável que você aproveitar a conectividade VPN-to-site fornecida pela rede virtual para permitir que os nós de Proxy de aplicativo Web para se comunicar com seus servidores do AD FS.

- Se você implantar um servidor do AD FS em uma máquina virtual Azure, conectividade com controladores de domínio do Active Directory do Windows Server, armazenamentos de atributo e bancos de dados de configuração é necessária e também pode exigir uma rota expressa ou uma conexão de VPN-to-site entre a rede virtual Azure e da rede local.

- Encargos são aplicados a todo o tráfego do Azure máquinas virtuais (tráfego de saída). Se custo for o fator determinante, é aconselhável implantar os nós de Proxy de aplicativo Web no Azure, deixando o AD FS servidores local. Se os servidores do AD FS são implantados em máquinas virtuais Azure também, custos adicionais serão incorridos para autenticar usuários locais. O tráfego de saída gera um custo, independentemente de estarem ou não-cruza a rota expressa ou a conexão de-to-site VPN.

- Se você decidir usar recursos de alta disponibilidade dos servidores do AD FS de balanceamento de carga de servidor nativo do Azure, observe que o balanceamento de carga oferece testes que são usados para determinar a integridade das máquinas virtuais dentro do serviço de nuvem. No caso de máquinas virtuais Azure (em vez de funções da web ou de trabalho), um teste personalizado deve ser usado como o agente que responde aos testes padrão não estiver presente no Azure máquinas virtuais. Para simplificar, você pode usar um teste TCP personalizado — isso requer apenas que uma conexão de TCP (um segmento TCP SYN enviado e respondidas com um segmento TCP SYN ACK) ser estabelecida com êxito para determinar a integridade de máquina virtual. Você pode configurar o teste personalizado para usar qualquer porta TCP à qual suas máquinas virtuais são ativamente listening.

> [AZURE.NOTE] Máquinas que precisam expor o mesmo conjunto de portas diretamente à Internet (como portas 80 e 443) não podem compartilhar o mesmo serviço de nuvem. Portanto, recomenda-se que você crie uma nuvem dedicada serviços para seus servidores do Windows Server AD FS para evitar o potencial sobreposto entre os requisitos de porta para um aplicativo e Windows Server AD FS.

## <a name="deployment-scenarios"></a>Cenários de implantação

A seção a seguir descreve os cenários de implantação comuns para chamar a atenção para considerações importantes que devem ser levadas em consideração. Cada cenário tem links para mais detalhes sobre as decisões e fatores a considerar.

1. [AD DS: Implantar um aplicativo de reconhecimento de AD DS sem a necessidade de conectividade de rede corporativa](#BKMK_CloudOnly)

    Por exemplo, um serviço do SharePoint voltados para Internet é implantado em uma máquina virtual Azure. O aplicativo não tem dependências nos recursos de rede corporativa. O aplicativo requer o Windows Server AD DS, mas não exige o AD DS corporativo do Windows Server.

2. [O AD FS: Estender um aplicativo de front-end local reconhecimento de declarações à Internet](#BKMK_CloudOnlyFed)

    Por exemplo, um aplicativo de reconhecimento de declarações que foi implantado com êxito no local e usados por usuários corporativos precisa ficar acessíveis da Internet. O aplicativo precisa ser acessado diretamente pela Internet por parceiros de negócios usando suas próprias identidades corporativas e por usuários corporativos existentes.

3. [AD DS: Implantar um aplicativo Windows Server AD DS reconhecimento que requerem conectividade com a rede corporativa](#BKMK_HybridExt)

    Por exemplo, um aplicativo de reconhecimento de LDAP que oferece suporte à autenticação integrada do Windows e usa o Windows Server AD DS como um repositório para dados de configuração e perfil de usuário é implantado em uma máquina virtual Azure. É desejável para o aplicativo aproveitar existente Windows Server AD DS corporativo e fornecer logon único. O aplicativo não é compatível com declarações.

### <a name="BKMK_CloudOnly"></a>1. AD DS: Implantar um aplicativo de reconhecimento de AD DS sem a necessidade de conectividade de rede corporativa

![Implantação do AD DS somente na nuvem](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figura 1**

#### <a name="description"></a>Descrição

SharePoint é implantado em uma máquina virtual Azure e o aplicativo não tem dependências nos recursos de rede corporativa. O aplicativo requer o Windows Server AD DS mas não *não* exigem Windows Server AD DS corporativo. Nenhum Kerberos ou federadas relações de confiança são necessárias porque os usuários auto provisionados por meio do aplicativo para o domínio do Windows Server AD DS que também está hospedado na nuvem em Azure máquinas virtuais.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações de cenário e como áreas de tecnologia se aplicam ao cenário

- [Topologia de rede](#BKMK_NetworkTopology): criar uma rede virtual Azure sem conectividade entre locais (também conhecido como conectividade to-site).

- [Configuração de implantação de CC](#BKMK_DeploymentConfig): implantar um novo controlador de domínio em uma floresta nova, domínio único, do Active Directory do Windows Server. Isso deve ser implantado juntamente com o servidor DNS do Windows.

- [Topologia de site do Active Directory do Windows Server](#BKMK_ADSiteTopology): usar o site do Active Directory do Windows Server padrão (todos os computadores estarão no padrão-primeiro-Site-Name).

- [Endereçamento IP e DNS](#BKMK_IPAddressDNS):

 - Defina um endereço IP estático para o DC usando o cmdlet Set-AzureStaticVNetIP Azure PowerShell.
 - Instalar e configurar o DNS do Windows Server na controladores de domínio no Azure.
 - Configure as propriedades de uma rede virtual com o nome e o endereço IP da máquina virtual que hospeda as funções de servidor DNS e DC.

- [Catálogo global](#BKMK_GC): O DC primeiro na floresta deve ser um servidor de catálogo global. Controladores de domínio adicionais também devem ser configurados como GCs porque em uma floresta de domínio único, o catálogo global não exige qualquer trabalho adicional do DC.

- [Posicionamento do banco de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB): adicionar um disco de dados para executar como VMs do Azure para armazenar o banco de dados do Active Directory do Windows Server, logs e SYSVOL de controladores de domínio.

- [Backup e restauração](#BKMK_BUR): determinar onde você deseja armazenar backups de estado do sistema. Se necessário, adicione outro disco de dados para a máquina virtual de DC para armazenar backups.

### <a name="BKMK_CloudOnlyFed"></a>2 o AD FS: estender um aplicativo de front-end local reconhecimento de declarações à Internet

![A federação com conectividade entre locais](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figura 2**

#### <a name="description"></a>Descrição

Um aplicativo de reconhecimento de declarações que foi implantado com êxito no local e usados por usuários corporativos precisa ficar acessíveis diretamente da Internet. O aplicativo serve como um front-end da web para um banco de dados do SQL que armazena dados. Os servidores SQL usados pelo aplicativo também estão localizados na rede corporativa. Dois STSs de TI do Windows Server AD e um balanceador de carga foram implantado local para fornecer acesso aos usuários corporativos. O aplicativo agora precisa ser acessado Além disso diretamente pela Internet por parceiros de negócios usando suas próprias identidades corporativas e por usuários corporativos existentes.

Em um esforço para simplificar e atende às necessidades de implantação e configuração deste novo requisito, ele é decidido que dois adicionais da web frontends e dois servidores de proxy do Windows Server AD FS estar instalado no Azure máquinas virtuais. Todos os quatro VMs serão expostas diretamente à Internet e serão fornecidas conectividade com a rede local usando o recurso VPN-to-site da rede Virtual do Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações de cenário e como áreas de tecnologia se aplicam ao cenário

- [Topologia de rede](#BKMK_NetworkTopology): criar uma rede virtual Azure e [Configurar a conectividade entre locais](../vpn-gateway/vpn-gateway-site-to-site-create.md).

 > [AZURE.NOTE] Para cada um dos certificados Windows Server AD FS, certifique-se de que a URL definida dentro do modelo de certificado e os certificados resultantes pode ser acessada pelas instâncias de FS do AD do Windows Server em execução no Azure. Isso pode exigir conectividade entre locais a partes de sua infraestrutura PKI. Por exemplo se o ponto de extremidade da CRL é baseado em LDAP e hospedado exclusivamente no local, em seguida, entre locais conectividade será necessária. Se isso não for desejável, pode ser necessário usar certificados emitidos por uma autoridade de certificação cujo CRL está acessível pela Internet.

- [Configuração de serviços de nuvem](#BKMK_CloudSvcConfig): Certifique-se de que você tem dois serviços de nuvem em ordem fornecem dois balanceamento de carga endereços IP virtuais. Endereço IP virtual do serviço de nuvem primeiro será direcionado para as duas VMs de proxy do Windows Server AD FS nas portas 80 e 443. O proxy do Windows Server AD FS VMs será configurado para apontar para o endereço IP do balanceador de carga local que o Windows Server AD FS STSs aspectos. Endereço IP virtual do serviço de nuvem segunda será direcionado para as duas VMs executando o frontend web novamente nas portas 80 e 443. Configure um teste personalizado para garantir que o balanceador de carga somente direciona o tráfego para o funcionamento do Windows Server AD FS proxy e web frontend VMs.

- [Configuração do servidor de Federação](#BKMK_FedSrvConfig): configurar o Windows Server AD FS como um servidor de Federação (STS) para gerar tokens de segurança para a floresta do Active Directory do Windows Server criado na nuvem. Defina federação relações de confiança de provedor de declarações com os parceiros diferentes que você deseja aceitar identidades de e configurar relações de confiança de terceiros confiante com os diferentes aplicativos que você deseja gerar tokens para.

    Na maioria dos cenários, servidores de proxy do Windows Server AD FS são implantados em uma capacidade de voltado para a Internet para fins de segurança enquanto seus correspondentes de Federação do Windows Server AD FS permanecem isolados direta conectividade com a Internet. Independentemente de seu cenário de implantação, você deve configurar o seu serviço de nuvem com um endereço IP virtual que fornecerá um endereço IP e porta que é capaz de balanceamento de carga entre o duas instâncias do Windows Server AD FS STS ou instâncias de proxy expostos publicamente.

- [Configuração de alta disponibilidade do Windows Server AD FS](#BKMK_ADFSHighAvail): é aconselhável implantar um farm do Windows Server AD FS com pelo menos dois servidores para failover e balanceamento de carga. Você pode usar o banco de dados interno de Windows (trabalho) para dados de configuração do Windows Server AD FS e usar o recurso de balanceamento de carga interno do Azure para distribuir solicitações de entrada entre os servidores no farm.

Para obter mais informações, consulte o [Guia de implantação do AD DS](https://technet.microsoft.com/library/cc753963).


### <a name="BKMK_HybridExt"></a>3. AD DS: Implantar um aplicativo Windows Server AD DS reconhecimento que requerem conectividade com a rede corporativa

![Implantação do AD DS entre locais](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**Figura 3**

#### <a name="description"></a>Descrição

Um aplicativo de reconhecimento de LDAP é implantado em uma máquina virtual Azure. Ele oferece suporte à autenticação integrada do Windows e usa o Windows Server AD DS como um repositório para os dados de perfil de usuário e configuração. O objetivo é para o aplicativo aproveitar o AD DS existente do Windows Server corporativo e fornecer logon único. O aplicativo não é compatível com declarações. Os usuários também precisam acessar o aplicativo diretamente da Internet. Para otimizar para desempenho e custo, ele é decidido que dois controladores de domínio adicionais que fazem parte de domínio corporativo ser implantado junto com o aplicativo no Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações de cenário e como áreas de tecnologia se aplicam ao cenário

- [Topologia de rede](#BKMK_NetworkTopology): criar uma rede virtual Azure com [conectividade entre locais](../vpn-gateway/vpn-gateway-site-to-site-create.md).

- [Método de instalação](#BKMK_InstallMethod): implantar controladores de domínio de réplica do domínio do Active Directory do Windows Server corporativo. Para uma réplica DC, você pode instalar o Windows Server AD DS na máquina virtual e, opcionalmente, use o recurso de instalação de mídia (IFM) para reduzir a quantidade de dados que precisam ser replicados para o novo DC durante a instalação. Para um tutorial, consulte [instalar um controlador de domínio do Active Directory de réplica no Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Mesmo se você usar IFM, talvez seja mais eficiente para criar o virtual DC local e mover o todo disco rígido Virtual (VHD) para a nuvem em vez de replicar Windows Server AD DS durante a instalação. Para segurança, é recomendável que você exclua o VHD da rede local depois que ele foi copiado para o Azure.

- [Topologia de site do Active Directory do Windows Server](#BKMK_ADSiteTopology): criar um novo site Azure em serviços e Sites do Active Directory. Crie um objeto de sub-rede do Active Directory do Windows Server para representar a rede virtual Azure e adicionar sub-rede ao site. Crie um novo link de site que inclui o novo site Azure e o site no qual a rede virtual Azure ponto de extremidade VPN está localizada para controlar e otimizar o tráfego do Active Directory do Windows Server para e do Azure.

- [Endereçamento IP e DNS](#BKMK_IPAddressDNS):

 - Defina um endereço IP estático para o DC usando o cmdlet Set-AzureStaticVNetIP Azure PowerShell.
 - Instalar e configurar o DNS do Windows Server na controladores de domínio no Azure.
 - Configure as propriedades de uma rede virtual com o nome e o endereço IP da máquina virtual que hospeda as funções de servidor DNS e DC.

- [Localização geográfica distribuídos controladores de domínio](#BKMK_DistributedDCs): configurar redes virtuais adicionais, conforme necessário. Se a topologia de sites do Active Directory requer controladores de domínio em regiões que correspondem a diferentes regiões Azure, que você deseja criar sites do Active Directory adequadamente.

- [Controladores de domínio somente leitura](#BKMK_RODC): você pode implantar um RODC no site do Azure, dependendo de seus requisitos para realizar escrever operações contra o DC e a compatibilidade de aplicativos e serviços no site com RODCs. Para obter mais informações sobre compatibilidade de aplicativos, consulte o [guia de compatibilidade de aplicativo de controladores de domínio somente leitura](https://technet.microsoft.com/library/cc755190).

- [Catálogo global](#BKMK_GC): GCs são necessários para solicitações de logon de serviço em florestas vários domínios. Se você não implantar um GC no site do Azure, provocará custos de tráfego de saída, como solicitações de autenticação causam consultas GCs em outros sites. Para minimizar o tráfego, você pode habilitar o cache de membros de grupo universal para o site Azure em serviços e Sites do Active Directory.

    Se você implantar um GC, configure links de sites e custos de links de site para que o GC no site do Azure não é preferencial como uma fonte de DC por outros GCs que precisam replicar as mesmas partições de domínio parcial.

- [Posicionamento do banco de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB): adicionar um disco de dados para controladores de domínio em execução no Azure VMs para armazenar o banco de dados do Active Directory do Windows Server, logs e SYSVOL.

- [Backup e restauração](#BKMK_BUR): determinar onde você deseja armazenar backups de estado do sistema. Se necessário, adicione outro disco de dados para a máquina virtual de DC para armazenar backups.

## <a name="deployment-decisions-and-factors"></a>Fatores e decisões de implantação

Esta tabela resume as áreas de tecnologia do Active Directory do Windows Server que são afetadas em cenários anteriores e as decisões correspondentes a considerar, com links para mais detalhes abaixo. Algumas áreas de tecnologia talvez não seja aplicáveis a cada cenário de implantação e algumas áreas de tecnologia podem ser mais importantes para um cenário de implantação outras áreas de tecnologia.

Por exemplo, se você implantar uma réplica DC em uma rede virtual e floresta tem apenas um único domínio, escolhendo implantar um servidor de catálogo global nesse caso não será essencial para o cenário de implantação porque ela não criará quaisquer requisitos adicionais de replicação. Por outro lado, se a floresta tiver vários domínios, a decisão de implantar um catálogo global em uma rede virtual pode afetar largura de banda disponível, desempenho, autenticação, pesquisas de diretório e assim por diante.

| Área de tecnologia do Active Directory do Windows Server | Decisões | Fatores |
| ---- | ---- | ---- |
| [Topologia de rede](#BKMK_NetworkTopology) | Você cria uma rede virtual? | <li>Requisitos para acessar recursos Corp</li> <li>Autenticação</li> <li>Gerenciamento de conta</li> |
| [Configuração de implantação de DC](#BKMK_DeploymentConfig) | <li>Implantar uma floresta separada sem qualquer relações de confiança?</li> <li>Implantar uma nova floresta com a federação?</li> <li>Implantar uma nova floresta com confiança de floresta do Active Directory do Windows Server ou Kerberos?</li> <li>Estender floresta Corp Implantando uma réplica DC?</li> <li>Estender floresta Corp Implantando um novo domínio filho ou uma árvore de domínio?</li> | <li>Segurança</li> <li>Conformidade</li> <li>Custo</li> <li>Resiliência e tolerância</li> <li>Compatibilidade de aplicativos</li> |
| [Topologia de site do Active Directory do Windows Server](#BKMK_ADSiteTopology) | Como você configurar sub-redes, sites e links de sites com o Azure rede Virtual para otimizar o tráfego e minimizar o custo? | <li>Definições de site e de sub-rede</li> <li>Propriedades do link de site e notificação de alteração</li> <li>Compactação de replicação</li> |
| [Endereçamento IP e DNS](#BKMK_IPAddressDNS) | Como configurar endereços IP e resolução de nomes? | <li>Use o cmdlet Set-AzureStaticVNetIP de uso para atribuir um endereço IP estático</li> <li>Instalar o Windows Server DNS server e configurar as propriedades de uma rede virtual com o nome e o endereço IP da máquina virtual que hospeda as funções de servidor DNS e DC</li> |
| [Localização geográfica distribuídos controladores de domínio](#BKMK_DistributedDCs) | Como duplicar para controladores de domínio em redes virtuais separadas? | Se a topologia de sites do Active Directory requer controladores de domínio em regiões que corresponde ao diferentes regiões Azure, que você deseja criar sites do Active Directory adequadamente. [Configurar uma rede virtual a conectividade de rede virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para replicar entre controladores de domínio em redes virtuais separadas. |
| [Controladores de domínio somente leitura](#BKMK_RODC) | Usar controladores de domínio somente leitura ou graváveis? | <li>AIN/IIP atributos de filtro</li> <li>Segredos de filtro</li> <li>Limite o tráfego de saída</li> |
| [Catálogo global](#BKMK_GC) | Instalar o catálogo global? | <li>Para floresta de domínio único, verifique todos os GCs controladores de domínio</li> <li>Para floresta com vários domínios, GCs são necessários para autenticação</li> |
| [Método de instalação](#BKMK_InstallMethod) | Como instalar o DC no Azure? | Ambos: <li>Instalar o AD DS usando o Windows PowerShell ou Dcpromo</li> <li>Mover VHD de um DC virtual local</li> |
| [Posicionamento do banco de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB) | Onde armazenar o Windows Server AD DS banco de dados, logs e SYSVOL? | Altere os valores padrão Dcpromo.exe. Estas críticas Active Directory arquivos *devem* ser colocados em discos de dados do Azure em vez de discos de sistema operacional que implementam o cache de gravação. |
| [Backup e restauração](#BKMK_BUR) | Como proteger e recuperar dados? | Criar sistema backups de estado |
| [Configuração do servidor de Federação](#BKMK_FedSrvConfig) | <li>Implantar uma nova floresta com a federação na nuvem?</li> <li>Implantar o AD FS local e expor um proxy na nuvem?</li> | <li>Segurança</li> <li>Conformidade</li> <li>Custo</li> <li>Acesso a aplicativos por parceiros de negócios</li> |
| [Configuração de serviços de nuvem](#BKMK_CloudSvcConfig) | Um serviço de nuvem é implicitamente implantado na primeira vez que você cria uma máquina virtual. Você precisa implantar os serviços de nuvem adicional? | <li>Uma máquina virtual ou VMs exigir exposição direta à Internet?</li> <li> O serviço requer balanceamento de carga?</li> |
| [Requisitos de servidor de federação para endereçamento (IP dinâmico versus IP virtual) de IP pública e privada](#BKMK_FedReqVIPDIP) | <li>A instância do Windows Server AD FS precisa ser acessada diretamente da Internet?</li> <li>O aplicativo está sendo implantado na nuvem requer seu próprio endereço IP voltado para a Internet e porta?</li> | Criar um serviço de nuvem para cada endereço IP virtual que é requerido pelo sua implantação |
| [Configuração de alta disponibilidade do Windows Server AD FS](#BKMK_ADFSHighAvail) | <li>Quantos nós em meu farm de servidores do Windows Server AD FS?</li> <li>Quantos nós para implantar em meu farm de proxy do Windows Server AD FS?</li> | Resiliência e tolerância a falhas |

### <a name="BKMK_NetworkTopology"></a>Topologia de rede

Para atender a requisitos de DNS do Windows Server AD DS e consistência de endereço IP, é necessário primeiro criar uma [rede virtual Azure](../virtual-network/virtual-networks-overview.md) e anexar a suas máquinas virtuais a ele. Durante a criação, você deve decidir se deseja estender opcionalmente conectividade à sua rede corporativa no local, que conecta transparente Azure máquinas virtuais máquinas local — isso é alcançado usando tecnologias VPN tradicionais e requer que um ponto de extremidade VPN ser expostos na borda da rede corporativa. Isto é, a VPN é iniciada a partir do Azure à rede corporativa, contrário não ocorre.

Observe que cobranças adicionais se aplicam ao estender uma rede virtual à sua rede local além os encargos padrão que se aplicam a cada máquina virtual. Especificamente, há encargos para o tempo de CPU do gateway rede Virtual do Azure e para o tráfego de saída gerado por cada máquina virtual que se comunica com máquinas local pela VPN. Para obter mais informações sobre encargos de tráfego de rede, consulte [Azure preços em geral](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuração de implantação de DC

A forma como você configura o DC depende os requisitos para o serviço que você deseja executar no Azure. Por exemplo, você pode implantar uma nova floresta, isolada de sua própria floresta corporativa, para testar um prova de conceito, um novo aplicativo ou algum outro projeto de curto prazo que requer os serviços de diretório, mas não específico acesso aos recursos corporativos internos.

Como um benefício, uma floresta isolada com que DC não replica locais controladores de domínio, resultando em menos saído tráfego de rede gerado pelo sistema em si, reduzindo custos diretamente. Para obter mais informações sobre encargos de tráfego de rede, consulte [Azure preços em geral](http://azure.microsoft.com/pricing/).

Como outro exemplo, suponha que você tem requisitos de privacidade para um serviço, mas o serviço depende de acesso ao seu Windows Server Active Directory interno. Se você tem permissão para dados de host para o serviço na nuvem, você pode implantar um novo domínio filho para sua floresta interna no Azure. Nesse caso, você pode implantar um DC para o novo domínio filho (sem o catálogo global para ajudar a resolver problemas de privacidade). Neste cenário, juntamente com uma réplica implantação DC, requer uma rede virtual para conectividade com seus controladores de domínio local.

Se você criar uma nova floresta, opta por usar [relações de confiança do Active Directory](https://technet.microsoft.com/library/cc771397) ou [relações de confiança de Federação](https://technet.microsoft.com/library/dd807036). Saldo os requisitos ditados pela compatibilidade, segurança, conformidade, custo e resiliência. Por exemplo, para tirar proveito de [autenticação seletiva](https://technet.microsoft.com/library/cc755844) você pode optar por implantar uma nova floresta no Azure e criar uma relação de confiança do Active Directory do Windows Server entre a floresta local e a floresta de nuvem. Se o aplicativo for reconhecimento de declarações, no entanto, você pode implantar relações de confiança de Federação em vez de relações de confiança do Active Directory. Outro fator será o custo para replicar mais dados estendendo seu local Active Directory do Windows Server para a nuvem ou gerar mais tráfego de saída como resultado de autenticação e a carga de consulta.

Requisitos para disponibilidade e tolerância a falhas também afetam sua escolha. Por exemplo, se o link for interrompido, a aplicativos aproveitando uma relação de confiança de Kerberos ou uma federação confiam são todos provavelmente inteiramente divididos, a menos que você implantou infraestrutura suficiente no Azure. Configurações de implantação alternativa como controladores de domínio de réplica (gravável ou RODCs) aumentar a probabilidade de ser capaz de suportar interrupções de link.

### <a name="BKMK_ADSiteTopology"></a>Topologia de site do Active Directory do Windows Server

Você precisa definir corretamente e links de sites para otimizar o tráfego e minimizar o custo. Sites, links de sites e sub-redes afetam a topologia de replicação entre controladores de domínio e o fluxo de tráfego de autenticação. Considere as seguintes cobranças de tráfego e implantar e configurar controladores de domínio acordo com os requisitos do seu cenário de implantação:

- Há uma taxa nominal por hora para o gateway em si:

 - Ele pode ser iniciado e interrompido ajustá-la

 - Se interrompido, Azure VMs são isoladas da rede corporativa

- Tráfego de entrada é gratuito

- Tráfego de saída é cobrado, de acordo com o [Azure preços em geral](http://azure.microsoft.com/pricing/). Você pode otimizar propriedades de link de site entre sites locais e os sites de nuvem da seguinte maneira:

 - Se você estiver usando várias redes virtuais, configure os links de site e seus custos adequadamente para impedir que o Windows Server AD DS priorizar o site Azure sobre uma que pode fornecer os mesmos níveis de serviço sem nenhum custo. Você também pode considerar desabilitar a ponte de todos os sites opção de link (BASL) (que é ativada por padrão). Isso garante que apenas diretamente conectada sites replicam uns com os outros. Controladores de domínio em sites temporariamente conectados já não sejam capazes de duplicar diretamente uns com os outros, mas devem ser replicados por meio de um site comum ou sites. Se os sites intermediários ficam indisponíveis por algum motivo, duplicação entre controladores de domínio em sites temporariamente conectados não ocorrerá mesmo se a conectividade entre os sites está disponível. Finalmente, onde as seções do comportamento de replicação transitivos permanecerão desejáveis, crie sites pontes de link que contêm os links de sites apropriado e sites, como local, sites de rede corporativa.

 - [Configurar os custos de link de site](https://technet.microsoft.com/library/cc794882) adequadamente para evitar o tráfego não intencional. Por exemplo, se **Tentar próximo Site** configuração é ativada, verifique se que os sites de rede virtual não são mais próxima do local a próxima, aumentando o custo associado do objeto de link de site que conecta o site Azure voltar à rede corporativa.

 - Configure sites link [intervalos](https://technet.microsoft.com/library/cc794878) e [cronogramas de](https://technet.microsoft.com/library/cc816906) acordo com os requisitos de consistência e taxa de alterações do objeto. Alinhe o agendamento de replicação com tolerância de latência. Controladores de domínio replicam somente o último estado de um valor, portanto diminuir o intervalo de replicação poderá economizar se houver um objeto suficiente alterar taxa.

- Se minimizar custos for uma prioridade, certifique-se de replicação está agendada e notificação de alteração não está habilitada. Esta é a configuração padrão na replicação entre sites. Isso não é importante se você estiver implantando um RODC em uma rede virtual porque o RODC não serão duplicar alterações de saída. Mas se você implantar um DC gravável, você deverá verificar se que o link de site não está configurado para replicar atualizações com frequência desnecessária. Se você implantar um servidor de catálogo global (GC), certifique-se de que todos os outros sites que contém um GC replica partições de domínio de uma fonte DC em um site que está conectado com um link de site ou links de site que têm um custo menor que o GC no site do Azure.


- É possível reduzir ainda mais o tráfego de rede gerado pela replicação entre sites, alterando o algoritmo de compactação de replicação. O algoritmo de compactação é controlado pelo algoritmo de compactação REG_DWORD registro entrada HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator. O valor padrão é 3, que corresponde ao algoritmo de Xpress compactar. Você pode alterar o valor 2, que altera o algoritmo para MSZip. Na maioria dos casos, isso aumentará a compactação, mas isso é feito à custa da CPU. Para obter mais informações, consulte a [topologia de replicação como Active Directory funciona](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Endereçamento IP e DNS

Azure máquinas virtuais estão alocadas "concedido pelo DHCP endereços" por padrão. Porque endereços dinâmicos do Azure rede virtual persistirem com uma máquina virtual para o ciclo de vida da máquina virtual, os requisitos do Windows Server AD DS são atendidos.

Como resultado, quando você usa um endereço dinâmico no Azure, na realidade você está usando um endereço IP estático, porque ele é roteável para o período da concessão e o período da concessão é igual a vida útil do serviço de nuvem.

No entanto, o endereço dinâmico desalocado se a máquina virtual está desligado. Para impedir que o endereço IP sendo desalocadas, você pode [usar Set-AzureStaticVNetIP para atribuir um endereço IP estático](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Resolução de nomes, implantar sua própria (ou aproveitar as existentes) infraestrutura de servidor DNS; DNS fornecido Azure não atende às necessidades de resolução de nome avançado do Windows Server AD DS. Por exemplo, ele não suportar dinâmicos registros SRV e assim por diante. Resolução de nomes é um item de configuração crítica para clientes de domínio e controladores de domínio. Controladores de domínio devem ser capazes de registrar registros de recursos e resolver registros de recursos do outro DC.
Por razões de desempenho e tolerância de falhas, é ideal para instalar o serviço de DNS do Windows Server em controladores de domínio em execução no Azure. Configure as propriedades de uma rede virtual Azure com o nome e o endereço IP do servidor DNS. Quando começa a outras VMs em uma rede virtual, suas configurações de resolução de cliente DNS serão configuradas com o servidor DNS como parte da alocação de endereço IP dinâmica.

> [AZURE.NOTE] Você não pode ingressar computadores locais em um domínio do Active Directory do Windows Server AD DS que está hospedado no Azure diretamente pela Internet. Requisitos de porta para o Active Directory e a operação de associação de domínio renderizar-prático diretamente expõem as portas necessárias e em vigor, um DC inteiro com a Internet.

VMs registrar seu nome DNS automaticamente na inicialização ou quando há uma alteração de nome.

Para obter mais informações sobre este exemplo e outro exemplo que mostra como provisionar a máquina virtual primeira e instalar o AD DS nele, consulte [instalar uma nova floresta do Active Directory no Microsoft Azure](active-directory-new-forest-virtual-machine.md). Para obter mais informações sobre como usar o Windows PowerShell, consulte [Instalar o Azure PowerShell](../powershell-install-configure.md) e [Cmdlets de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/jj152841).

### <a name="BKMK_DistributedDCs"></a>Localização geográfica distribuídos controladores de domínio

Azure oferece vantagens ao hospedar vários controladores de domínio em redes virtuais diferentes:

- Vários local tolerância

- Proximidade física para escritórios de ramificação (latência inferior)

Para obter informações sobre como configurar a comunicação direta entre redes virtuais, consulte [Configurar uma rede virtual à conectividade de rede virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Controladores de domínio somente leitura

Você precisa optar por implantar controladores de domínio somente leitura ou graváveis. Você pode ser inclinados a implantar RODCs porque você não terá controle físico sobre eles, mas RODCs foram projetados para ser implantado em locais onde seu segurança física está em risco, como escritórios de ramificação.

Azure não apresenta o risco de segurança físico de uma filial, mas RODCs podem ainda demorará mais econômico porque os recursos que eles fornecem são adequados para esses ambientes, embora por razões muito diferente. Por exemplo, RODCs não tem nenhuma replicação de saída e são capazes de preencher seletivamente segredos (senhas). A desvantagem, a falta desses segredos pode exigir o tráfego de saída sob demanda validá-los como um usuário ou computador autentica. Mas segredos podem ser seletivamente pré-populadas e armazenados em cache.

RODCs fornecem uma vantagem adicional em e em torno de preocupações AIN e IIP porque você pode adicionar atributos que contêm dados confidenciais no RODC filtrado conjunto de atributos (FAS). Os FAS é um conjunto personalizável de atributos que não são replicados para RODCs. Você pode usar os FAS como proteção caso você não é permitidas ou não deseja armazenar IIP ou AIN no Azure. Para obter mais informações, consulte [RODC filtrado atributo. definir [(https://technet.microsoft.com/library/cc753459)]

Certifique-se de que aplicativos será compatíveis com RODCs você planeja usar. Muitos aplicativos habilitados para Active Directory do Windows Server funcionam bem com RODCs, mas alguns aplicativos podem realizar forma ineficaz ou falhar se não tiverem acesso a um DC gravável. Para obter mais informações, consulte o [guia de compatibilidade de aplicativo de controladores de domínio somente leitura](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catálogo global

Você precisa opta por instalar um catálogo global (GC). Em uma floresta de domínio único, você deve configurar todos os controladores de domínio como servidores de catálogo global. Ele não aumentará custos porque não haverá nenhum tráfego de replicação adicionais.

Em uma floresta de vários domínios, GCs são necessários para expandir as associações de grupo Universal durante o processo de autenticação. Se você não implantar um GC, cargas de trabalho em uma rede virtual que autenticam um DC no Azure indiretamente gerará tráfego de autenticação de saída para consultar GCs local durante cada tentativa de autenticação.

Os custos associados GCs são menos previsíveis, pois eles hospedam cada domínio (na parte). Se a carga de trabalho hospeda um serviço voltado para a Internet e autentica usuários no Windows Server AD DS, os custos podem ser completamente imprevisíveis. Para ajudar a reduzir as consultas de GC fora do site de nuvem durante a autenticação, você pode [Habilitar o cache de membros de grupo Universal](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Método de instalação

Você precisa escolher como instalar controladores de domínio em uma rede virtual:

- Promova novos controladores de domínio. Para obter mais informações, consulte [instalar uma nova floresta do Active Directory em uma rede virtual Azure](active-directory-new-forest-virtual-machine.md).

- Mova o VHD de um DC virtual local para a nuvem. Nesse caso, você deve garantir que o DC virtual local for "movido," não "copiados" ou "clonados".

Use somente Azure máquinas virtuais para controladores de domínio (em vez de Azure "web" ou "colega" função VMs). Eles são duráveis e durabilidade do estado para um DC é necessária. Azure máquinas virtuais foram projetadas para cargas de trabalho como controladores de domínio.

Não use SYSPREP para implantar ou clonar controladores de domínio. A capacidade de clonar controladores de domínio é apenas disponível início no Windows Server 2012. O recurso de clonagem requer suporte para VMGenerationID em hipervisor subjacente. Hyper-V no Windows Server 2012 e Azure redes virtuais ambos oferecem suporte ao VMGenerationID, como fornecedores de software de virtualização de terceiros.

### <a name="BKMK_PlaceDB"></a>Posicionamento do banco de dados do Windows Server AD DS e SYSVOL

Selecione onde localizar o banco de dados do Windows Server AD DS, logs e SYSVOL. Eles devem ser implantados em discos de dados do Azure.

> [AZURE.NOTE] Azure discos de dados são restritas para 1 TB.

Unidades de disco de dados faça gravações de cache não por padrão. Unidades de disco de dados que são anexadas a uma máquina virtual usar gravação por cache. Gravação por cache torna-se de que a gravação está comprometida em armazenamento Azure durável antes que a transação seja concluída da perspectiva do sistema operacional da VM. Ele oferece durabilidade, ao custo gravações um pouco mais lentas.

Isso é importante para o Windows Server AD DS porque o cache de disco write-behind invalida suposições feitas pelo DC. Windows Server AD DS tenta desativar o cache de gravação, mas é para cima até o disco de sistema IO aceitá-lo. Falha ao desabilitar o cache de gravação pode, em determinadas circunstâncias, apresentar reversão do USN resulta em demorando objetos e outros problemas.

Como uma prática recomendada para controladores de domínio virtuais, faça o seguinte:

- Defina a configuração de preferência de Cache de Host no disco dados Azure para nenhum. Isso evita problemas com o cache de gravação para operações de AD DS.

- Armazene o banco de dados, logs e SYSVOL no mesmo disco de dados ou discos de dados separado. Normalmente, isso é um disco separado do disco usado para o próprio sistema operacional. O principal argumento é que o banco de dados do Windows Server AD DS e SYSVOL não devem ser armazenados em um tipo de disco do sistema operacional de Azure. Por padrão, o processo de instalação do AD DS instala esses componentes na pasta % systemroot %, que não é recomendada para o Azure.

### <a name="BKMK_BUR"></a>Backup e restauração

Lembre-se do que é e não há suporte para backup e restauração de um DC em geral e mais especificamente, aqueles executar uma máquina virtual. Consulte [Backup e restauração considerações para controladores de domínio virtualizados](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Crie backups de estado de sistema usando somente software de backup que está especificamente ciente de requisitos de backup para o Windows Server AD DS, como o Backup do Windows Server.

Não copiar ou clonar arquivos VHD de controladores de domínio em vez de realização de backups regulares. Uma restauração nunca será necessária, fazê-lo, usar VHDs clonados ou copiadas sem um hipervisor compatível e o Windows Server 2012 apresentará USN bolhas.

### <a name="BKMK_FedSrvConfig"></a>Configuração do servidor de Federação

A configuração de servidores de Federação do Windows Server AD FS (STSs) dependerá em parte se os aplicativos que você deseja implantar no Azure precisam acessar recursos em sua rede local.

Se os aplicativos atenderem aos seguintes critérios, você pode implantar os aplicativos em isolamento da sua rede local.

- Aceitarem tokens de segurança do SAML

- Eles são exposable com a Internet

- Eles não acessar os recursos de local

Nesse caso, configure o Windows Server AD FS STSs da seguinte maneira:

1. Configure uma floresta de domínio único isolada no Azure.

2. Fornece acesso federado à floresta Configurando um farm de servidores de Federação do AD FS do Windows Server.

3. Configure o Windows Server AD FS (farm de servidor de Federação e farm do proxy de servidor de federação) na floresta local.

4. Estabelece uma relação de confiança de federação entre o local e o Azure instâncias do Windows Server AD FS.

Por outro lado, se os aplicativos requerem acesso aos recursos de locais, você pode configurar o Windows Server AD FS com o aplicativo no Azure da seguinte maneira:

1. Configure a conectividade entre Azure e redes de local.

2. Configure um farm de servidores de Federação do Windows Server AD FS na floresta local.

3. Configure um farm de proxy do servidor de Federação do AD FS do Windows Server no Azure.

Essa configuração tem a vantagem de reduzir a exposição dos recursos locais, semelhantes ao configurar o Windows Server AD FS com aplicativos em uma rede de perímetro.

Observe que o cenário, você pode estabelecer relações de confiança com mais provedores de identidade, se for necessário colaboração para empresas.

### <a name="BKMK_CloudSvcConfig"></a>Configuração de serviços de nuvem

Serviços de nuvem são necessários se você quiser expor uma máquina virtual diretamente à Internet ou para expor um aplicativo de balanceamento de carga de voltado para a Internet. Isso é possível porque cada serviço de nuvem oferece um único endereço IP virtual configurável.

### <a name="BKMK_FedReqVIPDIP"></a>Requisitos de servidor de federação para endereçamento (IP dinâmico versus IP virtual) de IP pública e privada

Cada máquina virtual Azure recebe um endereço IP dinâmico. Um endereço IP dinâmico é um endereço privado acessível somente dentro do Azure. Na maioria dos casos, no entanto, será necessário configurar um endereço IP virtual para implantações do Windows Server AD FS. O IP virtual é necessário expor pontos de extremidade do Windows Server AD FS com a Internet e será usado por clientes e parceiros federados para autenticação e o gerenciamento contínuo. Um endereço IP virtual é uma propriedade de um serviço de nuvem que contém uma ou mais máquinas virtuais Azure. Se o aplicativo de reconhecimento de declarações implantado em Azure e Windows Server AD FS é voltado para a Internet e portas comuns de compartilhamento, cada exigem um endereço IP virtual própria e, portanto, será necessário criar um serviço de nuvem para o aplicativo e um segundo para o Windows Server AD FS.

Definições de termos para o endereço IP virtual e endereço IP dinâmico, consulte [termos e definições](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuração de alta disponibilidade do Windows Server AD FS

Embora seja possível implantar os serviços de Federação do AD FS do Windows Server autônomo, é recomendável para implantar um farm com pelo menos dois nós para o AD FS STS e proxies para ambientes de produção.

Consulte [Considerações de topologia do AD FS 2.0 implantação](https://technet.microsoft.com/library/gg982489) a [AD FS 2.0 Design Guide](https://technet.microsoft.com/library/dd807036) decida quais opções de configuração de implantação melhores atender às suas necessidades específicas.

> [AZURE.NOTE] Para obter balanceamento de carga para pontos de extremidade do Windows Server AD FS no Azure, configure todos os membros do farm do Windows Server AD FS no serviço de nuvem mesmo e usar o recurso de balanceamento de carga do Azure para HTTP (padrão 80) e portas HTTPS (padrão 443). Para obter mais informações, consulte [teste de Balanceador de carga Azure](https://msdn.microsoft.com/library/azure/jj151530).
Não há suporte para o Windows Server rede NLB Balanceamento de carga () no Azure.
