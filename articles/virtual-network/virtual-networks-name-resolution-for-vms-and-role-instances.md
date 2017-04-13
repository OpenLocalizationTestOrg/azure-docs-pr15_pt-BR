<properties 
   pageTitle="Resolução para VMs e instâncias de função"
   description="Cenários de resolução de nomes para Azure IaaS, soluções híbridas, entre os serviços de nuvem diferentes, do Active Directory e usar seu próprio servidor DNS "
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="telmos" />

# <a name="name-resolution-for-vms-and-role-instances"></a>Resolução de nomes para VMs e instâncias de função

Dependendo de como você usar Azure para hospedar IaaS, PaaS e soluções híbridas, você talvez precise permitir que as VMs e as instâncias de função que você cria para se comunicar com os outros. Embora essa comunicação pode ser feita usando endereços IP, é muito mais simples de usar nomes que podem ser facilmente lembrados e não forem alterados. 

Quando instâncias de função e VMs hospedadas no Azure precisarem resolver nomes de domínio para endereços IP internos, eles podem usar um dos dois métodos:

- [Resolução de nome fornecido do Azure](#azure-provided-name-resolution)

- [Resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) (isso poderá encaminhar consultas para os servidores DNS fornecido Azure) 

O tipo de resolução de nome que você usa depende de como seu VMs e instâncias de função precisam se comunicar com os outros.

**A tabela a seguir ilustra cenários e soluções de resolução de nome correspondentes:**

| **Cenário** | **Solução** | **Sufixo** |
|--------------|--------------|----------|
| Resolução de nomes entre instâncias de função ou VMs localizadas na rede virtual ou mesmo serviço de nuvem | [Resolução de nome fornecido do Azure](#azure-provided-name-resolution)| nome do host ou FQDN |
| Resolução de nomes entre instâncias de função ou VMs localizadas em diferentes redes virtuais | Gerenciado pelo cliente servidores DNS encaminhar consultas entre vnets para resolução por Azure (proxy DNS).  consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)| FQDN somente |
| Resolução de nomes de computador e serviço de locais de instâncias de função ou VMs no Azure | Gerenciado pelo cliente servidores DNS (controlador de domínio local por exemplo, controlador de domínio local de somente leitura ou um DNS secundário sincronizado usando transferências de zona).  Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)|FQDN somente |
| Resolução de nomes de host Azure computadores no local | Encaminhar consultas para um servidor de proxy DNS gerenciado pelo cliente na vnet correspondente, o servidor proxy encaminha consultas ao Azure para resolução. Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)| FQDN somente |
| DNS reverso para IPs interno | [Resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) | n/d |
| Resolução de nomes entre VMs ou instâncias de função localizadas em serviços de nuvem diferente, não em uma rede virtual| Não aplicável. Não há suporte para a conectividade entre VMs e instâncias de função nos serviços de nuvem diferentes fora de uma rede virtual.| n/d |



## <a name="azure-provided-name-resolution"></a>Resolução de nome fornecido do Azure

Juntamente com resolução de nomes DNS públicos, o Azure fornece resolução de nome interno para VMs e instâncias de função que residem na mesma rede virtual ou serviço de nuvem.  VMs/instâncias em um serviço de nuvem compartilham o mesmo sufixo DNS (para que o nome de host sozinho é suficiente), mas na nuvem diferentes clássico redes virtuais serviços têm diferentes sufixos DNS para que o FQDN é necessária para resolver os nomes entre os serviços de nuvem diferentes.  Redes virtuais no modelo de implantação do Gerenciador de recursos, o sufixo DNS é consistente em toda a rede virtual (para que o FQDN não é necessária) e nomes DNS podem ser atribuídos a placas de rede e VMs. Embora a resolução de nome fornecido Azure não exige qualquer configuração, não é a opção adequada para todos os cenários de implantação, conforme visto na tabela acima.

> [AZURE.NOTE] No caso de funções de trabalho e da web, você também pode acessar os endereços IP internos de instâncias de função com base no número de nome e a instância de função usando a API REST de gerenciamento de serviço de Azure. Para obter mais informações, consulte [Referência de API do serviço de gerenciamento restante](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### <a name="features-and-considerations"></a>Recursos e considerações

**Recursos:**

- Facilidade de uso: nenhuma configuração é necessária para poder usar a resolução de nome fornecido Azure.

- O serviço de resolução de nome fornecido Azure é altamente disponível, economizando a necessidade de criar e gerenciar clusters de seus próprios servidores DNS.

- Pode ser usado em conjunto com seus próprios servidores DNS para resolver nomes de host Azure tanto no local.

- Resolução do nome é fornecida entre instâncias de função/VMs dentro do mesmo serviço de nuvem sem necessidade de um FQDN.

- Resolução do nome é fornecida entre VMs em redes virtuais que usam o modelo de implantação do Gerenciador de recursos, sem necessidade para o FQDN. Redes virtuais no modelo clássico de implantação exigem o FQDN durante a resolução de nomes em serviços de nuvem diferentes. 

- Você pode usar nomes de host que melhor descrevem suas implantações, em vez de trabalhar com nomes de gerado automaticamente.

**Considerações sobre:**

- O sufixo DNS criados Azure não pode ser modificado.

- Você não pode registrar seus próprios registros manualmente.

- WINS e NetBIOS não são suportados. (Você não consegue ver suas VMs no Windows Explorer.)

- Nomes de host devem ser compatível com o DNS (eles devem usar somente 0-9, a-z e '-' e não é possível iniciar ou terminar com um '-'. Consulte RFC 3696 seção 2).

- Tráfego de consulta DNS é acelerado para cada máquina virtual. Isso não deve afetar a maioria dos aplicativos.  Se a otimização de solicitação for observada, certifique-se de que o cache do cliente está habilitado.  Para obter mais detalhes, consulte [obter o máximo de resolução de nome fornecido Azure](#Getting-the-most-from-Azure-provided-name-resolution).

- Somente VMs em serviços de 180 nuvem primeiro são registradas para cada rede virtual em um modelo de implantação clássico. Isso não se aplica ao redes virtuais nos modelos de implantação do Gerenciador de recursos.


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtendo o máximo de resolução de nome fornecido do Azure
**Cache do cliente:**

Nem todo consulta DNS precisa ser enviados pela rede.  Cache do lado do cliente ajuda a reduzir a latência e melhorar resistência a pontos de luz de rede Resolvendo recorrentes consultas DNS de um cache local.  Os registros DNS contêm um Time To Live (TTL) que permite que o cache armazenar o registro do contanto que possível sem afetar a atualização do registro, para que o cache do cliente é adequado para a maioria das situações.

O cliente de DNS do Windows padrão tem um cache DNS interno.  Alguns Linux distros não inclua em cache por padrão, é recomendável que um ser adicionado a cada VM Linux (após a verificação de que um cache local já existe).

Há um número de diferentes DNS cache pacotes disponíveis, por exemplo, dnsmasq, aqui estão as etapas para instalar o dnsmasq na distros mais comuns:

- **Ubuntu (usa resolvconf)**:
    - apenas instale o pacote de dnsmasq ("sudo chance-get instalar dnsmasq").
- **SUSE (usa netconf)**:
    - Instale o pacote de dnsmasq ("sudo zypper instalar dnsmasq") 
    - Ativar o serviço de dnsmasq ("systemctl habilitar dnsmasq.service") 
    - Iniciar o serviço de dnsmasq ("systemctl iniciar dnsmasq.service") 
    - Editar "/ etc/sysconfig/rede/config" e alterar NETCONFIG_DNS_FORWARDER = "" para "dnsmasq"
    - Atualizar resolv. conf ("netconfig atualização") para definir o cache como a resolução DNS local
- **OpenLogic (usa NetworkManager)**:
    - Instale o pacote de dnsmasq ("sudo yum instalar dnsmasq")
    - Ativar o serviço de dnsmasq ("systemctl habilitar dnsmasq.service")
    - Iniciar o serviço de dnsmasq ("systemctl iniciar dnsmasq.service")
    - Adicionar "preceder-servidores de nomes domínio 127.0.0.1;" para "/etc/dhclient-eth0.conf"
    - Reinicie o serviço de rede ("rede reiniciar o serviço") para definir o cache como a resolução DNS local

> [AZURE.NOTE] O pacote de 'dnsmasq' é apenas um dos vários caches DNS disponíveis para Linux.  Antes de utilizá-lo, verifique sua adequado de ou para suas necessidades específicas e que nenhum outro cache está instalado.

**Tentativas do lado do cliente:**

O DNS é principalmente um protocolo UDP.  Como o protocolo UDP não garante a entrega de mensagens, lógica de repetição é tratada no próprio protocolo DNS.  Cada cliente DNS (sistema operacional) pode apresentar lógica de repetição diferentes dependendo a preferência de criadores:

 - Sistemas operacionais Windows novamente após 1 segundo e novamente após outro 2, 4 e outra 4 segundos. 
 - As tentativas de configuração de Linux padrão depois de 5 segundos.  É recomendável alterar isso para repetir 5 vezes intervalos 1 segundo.  

Para verificar as configurações atuais em uma VM Linux, 'gato /etc/resolv.conf' e examine a linha 'Opções', por exemplo:

    options timeout:1 attempts:5

O arquivo resolv. conf é geralmente geradas automaticamente e não deve ser editado.  As etapas específicas para adicionar a linha 'Opções' variam de distribuição:

- **Ubuntu** (usa resolvconf):
    - Adicione a linha de opções ao ' / etc/resolveconf/resolv.conf.d/head' 
    - Executar 'resolvconf -u' Atualizar
- **SUSE** (usa netconf):
    - Adicionar 'timeout:1 tentativas: 5' para as NETCONFIG_DNS_RESOLVER_OPTIONS = "" parâmetro em '/ etc/sysconfig/rede/config' 
    - Executar 'netconfig update' para atualizar
- **OpenLogic** (usa NetworkManager):
    - Adicionar 'eco "opções timeout:1 tentativas: 5" ' para ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
    - Executar 'serviço rede reiniciar' Atualizar

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nomes usando seu próprio servidor DNS
Há várias situações onde suas necessidades de resolução de nome podem ir além dos recursos oferecidos pelo Azure, por exemplo quando usando domínios do Active Directory ou quando precisar de resolução DNS entre redes virtuais (vnets).  Para cobrir esses cenários, o Azure fornece a capacidade de usar seus próprios servidores DNS.  

Servidores DNS dentro de uma rede virtual podem encaminhar consultas DNS para resolvedores de recursiva do Azure para resolver nomes de host dentro dessa rede virtual.  Por exemplo, um controlador de domínio (DC) em execução no Azure pode responder a consultas DNS para seus domínios e encaminhar todas as outras consultas no Azure.  Isso permite VMs ver seus recursos no local (via o DC) e nomes de host fornecido Azure (via os encaminhadores).  Acesso para resolvedores de recursiva do Azure é fornecido via o IP virtual 168.63.129.16.

Encaminhamento de DNS também permite a resolução DNS vnet inter e permite suas máquinas no local resolver nomes de host fornecido Azure.  Para resolver o nome do host de uma máquina virtual, o servidor DNS máquina virtual deve residem na mesma rede virtual e ser configurado para encaminhar hostname consultas no Azure.  Como o sufixo DNS é diferente em cada vnet, você pode usar regras de encaminhamento condicional para enviar consultas DNS para o vnet correto para resolução.  A imagem a seguir mostra dois vnets e uma rede local fazendo vnet inter resolução DNS usando esse método.  Um encaminhadores DNS de exemplo estão disponível na [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Vnet inter DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Ao usar a resolução de nome fornecido Azure, um sufixo DNS interno (*. internal.cloudapp.net) é fornecido para cada máquina virtual usando DHCP.  Isso permite a resolução de nome do host como os registros de nome de host estão na zona internal.cloudapp.net.  Ao usar sua própria solução de resolução de nome, o sufixo IDNS não é fornecido em VMs porque ele interfira com outras arquiteturas DNS (como cenários de domínio).  Em vez disso, fornecemos um espaço de reservado não estão funcionando (reddog.microsoft.com).  

Se necessário, o sufixo DNS interno pode ser determinado usando o PowerShell ou API:

-  Para redes virtuais nos modelos de implantação do Gerenciador de recursos, o sufixo está disponível por meio do recurso de [placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou o cmdlet [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) .    
-  Em modelos de implantação clássico, o sufixo está disponível por meio da chamada da [API de implantação de obter](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou o [Get-AzureVM-depurar](https://msdn.microsoft.com/library/azure/dn495236.aspx) cmdlet.


Se o encaminhamento de consultas no Azure não atender às suas necessidades, você precisará fornecer sua própria solução DNS.  A solução DNS será necessário:

-  Fornece resolução de nome do host apropriado, por exemplo, via [DDNS](virtual-networks-name-resolution-ddns.md).  Observação: se usar DDNS você talvez precise desativar eliminação de registro DNS conforme concessões DHCP do Azure são muito longos e eliminação removerá os registros DNS prematuramente. 
-  Fornece a resolução recursiva apropriadas para permitir a resolução de nomes de domínio externo.
-  Ser acessível (TCP e UDP na porta 53) dos clientes do qual que ele serve e ser capaz de acesso à internet.
-  Ser protegido contra acesso da internet, a reduzir ameaças representadas por agentes externos.

> [AZURE.NOTE] Para obter melhor desempenho, ao usar o Azure VMs como servidores DNS, o IPv6 deve ser desativado e um [Nível de instância pública IP](virtual-networks-instance-level-public-ip.md) devem ser atribuídos a cada máquina virtual do servidor DNS.  Se você optar por usar o Windows Server como seu servidor DNS, [Este artigo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fornece otimizações e análise de desempenho adicional.


### <a name="specifying-dns-servers"></a>Especificando servidores DNS

Ao usar seus próprios servidores DNS, o Azure fornece a capacidade de especificar vários servidores DNS por rede virtual ou por interface de rede (Resource Manager) / (clássico) do serviço em nuvem.  Servidores DNS especificados para uma interface de rede do serviços de nuvem obtém precedência sobre aquelas especificadas para a rede virtual.

> [AZURE.NOTE] Propriedades de conexão de rede, como servidor DNS IPs, não deve ser editada diretamente no Windows VMs como eles podem obter apagados durante o serviço reparar quando o adaptador de rede virtual é substituído. 


Ao usar o modelo de implantação do Gerenciador de recursos, servidores DNS podem ser especificados no Portal, API/modelos ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) ou PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Ao usar o modelo clássico de implantação, servidores DNS para a rede virtual podem ser especificados no Portal ou [o arquivo de *Configuração de rede* ](https://msdn.microsoft.com/library/azure/jj157100).  Para serviços de nuvem, os servidores DNS são especificados por meio do [arquivo de *Configuração do serviço* ](https://msdn.microsoft.com/library/azure/ee758710) ou no PowerShell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [AZURE.NOTE] Se você alterar as configurações de DNS para um computador de rede/virtual que já está implantado, você precisa reiniciar cada VM afetado para que as alterações tenham efeito.


## <a name="next-steps"></a>Próximas etapas

Modelo de implantação do Gerenciador de recursos:

- [Criar ou atualizar uma rede virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx)
- [Criar ou atualizar uma placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx)
- [Novo AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
- [Novo AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

 
Modelo de implantação clássico:

- [Esquema de configuração do serviço Azure](https://msdn.microsoft.com/library/azure/ee758710)
- [Esquema de configuração de rede virtual](https://msdn.microsoft.com/library/azure/jj157100)
- [Configurar uma rede Virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md) 

