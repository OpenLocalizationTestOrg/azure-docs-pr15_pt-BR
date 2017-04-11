<properties
    pageTitle="Configurando DHCPv6 para Linux VMs | Microsoft Azure"
    description="Como configurar DHCPv6 para VMs Linux."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6, balanceador de carga azure, pilha dupla, ip público, ipv6 nativo, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="configuring-dhcpv6-for-linux-vms"></a>Configurando DHCPv6 para Linux VMs

Algumas imagens máquina virtual Linux do Azure Marketplace não têm DHCPv6 configurado por padrão. Para oferecer suporte a IPv6, DHCPv6 devem ser configurados no dentro da distribuição de SO Linux que você está usando. Distribuições de Linux diferentes têm diferentes maneiras de configurar DHCPv6 porque eles usam diferentes pacotes.

>[AZURE.NOTE] Recentes SUSE Linux e CoreOS imagens em do Azure Marketplace foram pré-configurado com DHCPv6. Nenhuma alteração adicionais é necessárias ao usar essas imagens.

Este documento descreve como habilitar DHCPv6 para que o computador virtual Linux obtém um endereço IPv6.

>[AZURE.WARNING] Incorretamente editando arquivos de configuração de rede pode causar a perder o acesso de rede à sua máquina virtual. É recomendável que você teste as alterações de configuração em sistemas de não produção. As instruções neste artigo foram testadas nas versões mais recentes do imagens Linux do Azure Marketplace. Consulte a documentação para a versão específica do Linux para obter instruções mais detalhadas.

## <a name="ubuntu"></a>Ubuntu

1. Editar o arquivo `/etc/dhcp/dhclient6.conf` e adicione a seguinte linha:

        timeout 10;

2. Edite a configuração de rede para a interface eth0 com a seguinte configuração:

    * No **Ubuntu 12.04 e 14.04**, edite o arquivo`/etc/network/interfaces.d/eth0.cfg`
    * No **Ubuntu 16.04**, edite o arquivo`/etc/network/interfaces.d/50-cloud-init.cfg`

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Renove endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Editar o arquivo `/etc/dhcp/dhclient6.conf` e adicione a seguinte linha:

        timeout 10;

2. Editar o arquivo `/etc/network/interfaces` e adicione a seguinte configuração:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Linux Oracle

1. Editar o arquivo `/etc/sysconfig/network` e adicione o seguinte parâmetro:

        NETWORKING_IPV6=yes

2. Editar o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicionar dois parâmetros a seguir:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renove endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Imagens SLES e openSUSE recentes no Azure foram pré-configurado com DHCPv6. Nenhuma alteração adicionais é necessárias ao usar essas imagens. Se você tiver uma máquina virtual com base em uma imagem SUSE antiga ou personalizada, use as seguintes etapas:

1. Instalar o `dhcp-client` pacote, se necessário:

    ```bash
    # sudo zypper install dhcp-client
    ```

2. Editar o arquivo `/etc/sysconfig/network/ifcfg-eth0` e adicione o seguinte parâmetro:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>12 de SLES e openSUSE salto

Imagens SLES e openSUSE recentes no Azure foram pré-configurado com DHCPv6. Nenhuma alteração adicionais é necessárias ao usar essas imagens. Se você tiver uma máquina virtual com base em uma imagem SUSE antiga ou personalizada, use as seguintes etapas:

1. Editar o arquivo `/etc/sysconfig/network/ifcfg-eth0` e substitua esse parâmetro

        #BOOTPROTO='dhcp4'

    com o seguinte valor:

        BOOTPROTO='dhcp'

2. Adicionar o seguinte parâmetro para `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Imagens de CoreOS recentes no Azure foram pré-configurado com DHCPv6. Nenhuma alteração adicionais é necessárias ao usar essas imagens. Se você tiver uma máquina virtual com base em uma imagem de CoreOS antiga ou personalizada, use as seguintes etapas:

1. Editar o arquivo`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renove o endereço IPv6:

    ```bash
    # sudo systemctl restart systemd-networkd
    ```
