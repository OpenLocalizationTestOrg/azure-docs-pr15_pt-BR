<properties 
   pageTitle="Opções de resolução de nome DNS para VMs Linux no Azure"
   description="Serviços DNS do nomes resolução cenários para VMs Linux no Azure IaaS, incluindo fornecidos, servidor externo de trazer seu próprio DNS e híbrida."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="rclaus" />

# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Opções de resolução de nome DNS para VMs Linux no Azure

Azure fornece resolução de nomes DNS por padrão para todas as VMs contidas em uma única rede Virtual. Você é capaz de implementar sua própria solução de resolução de nome DNS configurando seus próprios serviços DNS em suas VMs hospedados Azure. Cenários a seguir deverão ajudá-lo a escolher qual deles funciona melhor para sua situação específica.

- [Resolução de nome fornecido do Azure](#azure-provided-name-resolution)

- [Resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) 

O tipo de resolução de nome que você usa depende de como seu VMs e instâncias de função precisam se comunicar com os outros.

**A tabela a seguir ilustra cenários e soluções de resolução de nome correspondentes:**

| **Cenário** | **Solução** | **Sufixo** |
|--------------|--------------|----------|
| Resolução de nomes entre instâncias de função ou VMs localizadas na mesma rede virtual | [Resolução de nome fornecido do Azure](#azure-provided-name-resolution)| nome do host ou FQDN |
| Resolução de nomes entre instâncias de função ou VMs localizadas em diferentes redes virtuais | Gerenciado pelo cliente servidores DNS encaminhar consultas entre vnets para resolução por Azure (proxy DNS).  consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)| FQDN somente |
| Resolução de nomes de computador e serviço de locais de instâncias de função ou VMs no Azure | Gerenciado pelo cliente servidores DNS (por exemplo, controlador de domínio no local, controlador de domínio local de somente leitura ou um DNS secundário sincronizado usando transferências de zona).  Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)|FQDN somente |
| Resolução de nomes de host Azure computadores no local | Encaminhar consultas para um servidor de proxy DNS gerenciado pelo cliente na vnet correspondente, o servidor proxy encaminha consultas ao Azure para resolução. Consulte [resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server)| FQDN somente |
| DNS reverso para IPs interno | [Resolução de nomes usando seu próprio servidor DNS](#name-resolution-using-your-own-dns-server) | n/d |

## <a name="azure-provided-name-resolution"></a>Resolução de nome fornecido do Azure

Juntamente com resolução de nomes DNS públicos, o Azure fornece resolução de nome interno para VMs e instâncias de função que residem dentro da mesma rede virtual.  Redes virtuais baseado em ARM, o sufixo DNS é consistente em toda a rede virtual (para que o FQDN não é necessária) e nomes DNS podem ser atribuídos a placas de rede e VMs. Embora a resolução de nome fornecido Azure não exige qualquer configuração, não é a opção adequada para todos os cenários de implantação, conforme visto na tabela anterior.

### <a name="features-and-considerations"></a>Recursos e considerações

**Recursos:**

- Facilidade de uso: nenhuma configuração é necessária para usar a resolução de nome fornecido Azure.

- O serviço de resolução de nome fornecido Azure é altamente disponível, economizando a necessidade de criar e gerenciar clusters de seus próprios servidores DNS.

- Pode ser usado juntamente com seus próprios servidores DNS para resolver nomes de host Azure tanto no local.

- Resolução do nome é fornecida entre VMs em redes virtuais sem necessidade para o FQDN. 

- Você pode usar nomes de host que melhor descrevem suas implantações, em vez de trabalhar com nomes de gerado automaticamente.

**Considerações sobre:**

- O sufixo DNS criados Azure não pode ser modificado.

- Você não pode registrar seus próprios registros manualmente.

- WINS e NetBIOS não são suportados.

- Nomes de host devem ser compatível com o DNS (eles devem usar somente 0-9, a-z e '-' e não é possível iniciar ou terminar com um '-'. Consulte RFC 3696 seção 2).

- Tráfego de consulta DNS é acelerado para cada máquina virtual. Isso não deve afetar a maioria dos aplicativos.  Se a otimização de solicitação for observada, certifique-se de que o cache do cliente está habilitado.  Para obter mais informações, consulte [obter o máximo de resolução de nome fornecido Azure](#Getting-the-most-from-Azure-provided-name-resolution).


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtendo o máximo de resolução de nome fornecido do Azure
**Cache do cliente:**

Nem todo consulta DNS é enviada através da rede.  Cache do lado do cliente ajuda a reduzir a latência e melhorar resistência a pontos de luz de rede Resolvendo recorrentes consultas DNS de um cache local.  Os registros DNS contêm um Time To Live (TTL) que permite que o cache armazenar o registro do contanto que possível sem afetar a atualização do registro.  Por isso, o cache do cliente é adequado para a maioria das situações.

Alguns distros Linux não inclua em cache por padrão.  É recomendável que você adicione uma para cada VM Linux (após a verificação de que um cache local já existe).

Há vários diferentes DNS cache pacotes disponíveis, por exemplo dnsmasq, aqui estão as etapas para instalar o dnsmasq na distros mais comuns:

- **Ubuntu (usa resolvconf)**:
    - Instale o pacote de dnsmasq ("sudo chance-get instalar dnsmasq").
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

> [AZURE.NOTE]: O pacote 'dnsmasq' é apenas um dos vários caches DNS disponíveis para Linux.  Antes de utilizá-lo, verifique sua adequado de ou para suas necessidades específicas e que nenhum outro cache está instalado.

**Tentativas do lado do cliente:**

O DNS é principalmente um protocolo UDP.  Como o protocolo UDP não garante a entrega de mensagens, lógica de repetição é tratada no próprio protocolo DNS.  Cada cliente DNS (sistema operacional) pode apresentar lógica de repetição diferentes dependendo a preferência de criadores:

 - Sistemas operacionais Windows novamente após um segundo e novamente após outro dois, quatro e outra quatro segundos. 
 - As tentativas de configuração de Linux padrão após cinco segundos.  Você deve alterar isso para repetir cinco vezes em intervalos de um segundo.  

Para verificar as configurações atuais em uma VM Linux, 'gato /etc/resolv.conf' e examine a linha 'Opções', por exemplo:

    options timeout:1 attempts:5

O arquivo resolv. conf é gerado automaticamente e não deve ser editado.  As etapas específicas para adicionar a linha 'Opções' variam de distribuição:

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
Existem várias situações onde suas necessidades de resolução de nome podem ir além dos recursos fornecidos pelo Azure, por exemplo quando você precisa de resolução DNS entre redes virtuais (vnets).  Para cobrir esse cenário, o Azure fornece a capacidade de usar seus próprios servidores DNS.  

Servidores DNS dentro de uma rede virtual podem encaminhar consultas DNS para resolvedores de recursiva do Azure para resolver nomes de host dentro dessa rede virtual.  Por exemplo, um servidor DNS em execução no Azure pode responder a consultas DNS para seus próprio arquivos de zona DNS e encaminhar todas as outras consultas no Azure.  Isso permite VMs ver ambas as suas entradas em seus arquivos de zona e nomes de host fornecido Azure (via os encaminhadores).  Acesso para resolvedores de recursiva do Azure é fornecido via o IP virtual 168.63.129.16.

Encaminhamento de DNS também permite a resolução DNS vnet inter e permite suas máquinas no local resolver nomes de host fornecido Azure.  Para resolver hostname de uma máquina virtual, o servidor DNS máquina virtual deve residem na mesma rede virtual e ser configurado para encaminhar hostname consultas no Azure.  Como o sufixo DNS é diferente em cada vnet, você pode usar regras de encaminhamento condicional para enviar consultas DNS para o vnet correto para resolução.  A imagem a seguir mostra dois vnets e uma rede local fazendo vnet inter resolução DNS usando esse método:

![Vnet inter DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Ao usar a resolução de nome fornecido Azure, o sufixo DNS interno é fornecido para cada máquina virtual usando DHCP.  Ao usar sua própria solução de resolução de nome, esse sufixo não for fornecido para VMs porque ele interfira com outras arquiteturas DNS.  Para se referir a máquinas por FQDN ou configurar o sufixo em suas VMs, o sufixo pode ser determinado usando o PowerShell ou API:

-  Para gerenciamento de recursos do Azure gerenciado vnets, o sufixo está disponível por meio do recurso de [placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou você pode executar o comando `azure network public-ip show <resource group> <pip name>` para exibir os detalhes do seu IP público, incluindo o FQDN do NIC.    


Se o encaminhamento de consultas no Azure não atender às suas necessidades, você precisará fornecer sua própria solução DNS.  A solução DNS precisa:

-  Fornece resolução de nome do host apropriado, por exemplo via [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md).  Observação: se usar DDNS você talvez precise desativar eliminação de registro DNS conforme concessões DHCP do Azure são muito longos e eliminação removerá os registros DNS prematuramente. 
-  Fornece a resolução recursiva apropriadas para permitir a resolução de nomes de domínio externo.
-  Ser acessível (TCP e UDP na porta 53) dos clientes do qual que ele serve e ser capaz de acesso à internet.
-  Ser protegido contra acesso da internet, a reduzir ameaças representadas por agentes externos.

> [AZURE.NOTE] Para obter melhor desempenho, ao usar o Azure VMs como servidores DNS, o IPv6 deve ser desativado e um [Nível de instância pública IP](../virtual-network/virtual-networks-instance-level-public-ip.md) devem ser atribuídos a cada máquina virtual do servidor DNS.  

