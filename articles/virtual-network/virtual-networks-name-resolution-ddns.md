<properties
   pageTitle="Usando DNS dinâmico para registrar nomes de host"
   description="Esta página fornece detalhes sobre como configurar o DNS dinâmico para registrar nomes de host em seus próprios servidores DNS."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Usando DNS dinâmico para registrar nomes de host em seu próprio servidor DNS

[Azure fornece resolução de nomes](virtual-networks-name-resolution-for-vms-and-role-instances.md) para máquinas virtuais (VMs) e instâncias de função. No entanto, quando a resolução de nome precisa ir além daquelas fornecidas pelo Azure, você pode fornecer seus próprios servidores DNS. Isso lhe dá a capacidade de personalizar sua solução de DNS para atender às suas necessidades específicas. Por exemplo, você talvez precise acessar recursos no local por meio de seu controlador de domínio do Active Directory.

Quando seus servidores DNS personalizados são hospedados como VMs Azure você pode encaminhar consultas hostname para a mesma vnet ao Azure para resolver nomes de host. Se você não quiser usar esta rota, você pode registrar seus nomes de host de máquina virtual no seu servidor DNS usando DNS dinâmico.  Azure não têm a capacidade (por exemplo, credenciais) para criar diretamente registros em servidores DNS, portanto disposições alternativas geralmente são necessários. Aqui estão alguns cenários comuns com alternativas.

## <a name="windows-clients"></a>Clientes do Windows

Clientes do Windows não-domínio tentam desprotegida atualizações de DNS dinâmicas (DDNS) quando eles iniciam ou quando seus endereços IP forem alterados. O nome DNS é o nome de host mais o sufixo DNS primário. Azure deixa o sufixo DNS primário em branco, mas você pode definir isso na máquina virtual, por meio do [interface do usuário](https://technet.microsoft.com/library/cc794784.aspx) ou [usando automação](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Clientes de Windows domínio registram seus endereços IP com o controlador de domínio usando DNS dinâmico seguro. O processo de associação de domínio define o sufixo DNS primário no cliente e cria e mantém a relação de confiança.

## <a name="linux-clients"></a>Clientes Linux

Clientes Linux geralmente não registram-se com o servidor DNS na inicialização, eles pressupõem que o servidor DHCP significa. Servidores DHCP do Azure não tem as credenciais ou capacidade registre registros no seu servidor DNS.  Você pode usar uma ferramenta chamada *nsupdate*, que está incluído no pacote de vincular, para enviar atualizações de DNS dinâmico. Como o protocolo DNS dinâmico é padronizado, você pode usar *nsupdate* mesmo quando não estiver usando vincular no servidor DNS.

Você pode usar a fixação que é fornecidas pelo cliente DHCP para criar e manter a entrada de nome do host no servidor DNS. Durante o ciclo DHCP, o cliente executa os scripts em */etc/dhcp/dhclient-exit-hooks.d/*. Isso pode ser usado para registrar o novo endereço IP usando *nsupdate*. Por exemplo:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

Você também pode usar o comando *nsupdate* para executar atualizações de DNS dinâmico seguras. Por exemplo, quando você estiver usando um servidor DNS vincular, um par de chaves pública-particular é [gerado](http://linux.yyz.us/nsupdate/).  O servidor DNS está [configurado](http://linux.yyz.us/dns/ddns-server.html) com a parte da chave pública para que ele possa verificar a assinatura na solicitação. Você deve usar a opção *-k* para fornecer que o par de chaves para *nsupdate* para que o DNS dinâmico atualizar solicitação ser assinados.

Quando você estiver usando um servidor DNS Windows, você pode usar a autenticação Kerberos com o parâmetro *-g* em *nsupdate* (não disponível na versão Windows do *nsupdate*). Para fazer isso, use *kinit* para carregar as credenciais (por exemplo, a partir de um [arquivo keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Em seguida, *nsupdate -g* selecionará as credenciais do cache.

Se necessário, você pode adicionar um sufixo de pesquisa DNS em suas VMs. O sufixo DNS é especificado no arquivo */etc/resolv.conf* . A maioria dos distros Linux gerenciar automaticamente o conteúdo desse arquivo, então, normalmente você não pode editá-lo. No entanto, você pode substituir o sufixo por meio *substitui o* comando do cliente DHCP. Para fazer isso, em */etc/dhcp/dhclient.conf*, adicione:

        supersede domain-name <required-dns-suffix>;

