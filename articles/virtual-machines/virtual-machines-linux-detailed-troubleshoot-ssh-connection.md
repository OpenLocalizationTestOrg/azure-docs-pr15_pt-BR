<properties
    pageTitle="Detalhadas SSH solução de problemas para uma máquina virtual do Azure | Microsoft Azure"
    description="Mais detalhadas SSH etapas para problemas de conexão com uma máquina virtual Azure de solução de problemas"
    keywords="SSH conexão recusou, ssh erro, azure ssh conexão SSH falhou"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="detailed-ssh-troubleshooting-steps"></a>Etapas detalhadas de solução de problemas de SSH

Há vários motivos possíveis que o cliente SSH pode não conseguir acessar o serviço SSH na máquina virtual. Se você seguiu as [etapas de solução de problemas gerais SSH](virtual-machines-linux-troubleshoot-ssh-connection.md)de mais, você precisa solucionar o problema de conexão. Este artigo orienta você pelas etapas de solução de problemas detalhadas para determinar onde a conexão SSH está falhando e como resolvê-lo.

## <a name="take-preliminary-steps"></a>Siga os passos preliminares

O diagrama a seguir mostra os componentes que estão envolvidos.

![Diagrama que mostra os componentes de serviço SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

As etapas a seguir o ajudarão a isolar a origem da falha e descobrir soluções ou soluções alternativas.

Primeiro, verifique o status da máquina virtual no portal.

No [portal do Azure](https://portal.azure.com):

1. Para VMs criadas usando o modelo de implantação clássico, selecione **Procurar** > **máquinas virtuais (clássico)** > *nome de máquina virtual*.

    -OU-

    Para VMs criadas usando o modelo do Gerenciador de recurso, selecione **Procurar** > **máquinas virtuais** > *nome de máquina virtual*.

    O painel de status para a máquina virtual deve mostrar **em execução**. Role para baixo até Mostrar atividade recente para computação, armazenamento e recursos de rede.

2. Selecione **configurações** para examinar pontos de extremidade, endereços IP e outras configurações.

    Para identificar pontos extremos VMs que foram criados usando o Gerenciador de recursos, verifique se que um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) definida. Verifique também que as regras foram aplicadas ao grupo de segurança de rede e que eles sejam referenciados na sub-rede.

No [portal de clássico Azure](https://manage.windowsazure.com), para VMs que foram criados usando o modelo de implantação clássico:

1. Selecione **máquinas virtuais** > *nome de máquina virtual*.
2. Selecione a máquina virtual **Dashboard** para verificar seu status.
3. Selecione o **Monitor** para ver as atividades recentes para computação, armazenamento e recursos de rede.
4. Selecione **pontos de extremidade** para garantir que não há um ponto de extremidade para o tráfego SSH.

Para verificar a conectividade de rede, verifique os pontos de extremidade configurados e veja se você pode acessar a máquina virtual por meio de outro protocolo, como HTTP ou outro serviço.

Após essas etapas, tente novamente a conexão SSH.


## <a name="find-the-source-of-the-issue"></a>Localizar a origem do problema

O cliente SSH em seu computador pode falhar ao alcançar o serviço SSH na VM Azure devido a problemas ou incorretas no seguinte:

- [Computador de cliente SSH](#source-1-ssh-client-computer)
- [Dispositivo de borda de organização](#source-2-organization-edge-device)
- [Ponto de extremidade do serviço em nuvem e controle lista de acesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Grupos de segurança de rede](#source-4-network-security-groups)
- [Baseado em Linux Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Fonte 1: SSH computador cliente

Para eliminar o seu computador como a origem da falha, verifique se que ele pode fazer conexões SSH para outro local, computador baseado em Linux.

![Diagrama que destaca os componentes do computador cliente SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se a conexão falhar, verifique o seguinte no seu computador:

- Uma configuração de firewall local está bloqueando o tráfego SSH entrado ou saído (TCP 22)
- Localmente instalado software de proxy de cliente que está impedindo conexões SSH
- Localmente instalado software que está impedindo conexões SSH de monitoramento de rede
- Outros tipos de software de segurança que monitoram o tráfego ou permitem/proíbem tipos específicos de tráfego

Se uma das seguintes condições se aplicarem, temporariamente desative o software e tente uma conexão SSH em um computador local para descobrir o motivo pelo qual que a conexão está sendo bloqueado em seu computador. Trabalhe com seu administrador de rede para corrigir as configurações de software para permitir conexões de SSH.

Se você estiver usando autenticação de certificado, verifique se você tem estas permissões para a pasta .ssh no seu diretório inicial:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/\*. pub
- Chmod 600 ~/.ssh/id_rsa (ou outros arquivos que têm suas chaves particulares armazenados nelas)
- ~/.Ssh/known_hosts chmod 644 (contém hosts que você conectou à via SSH)

## <a name="source-2-organization-edge-device"></a>Origem 2: Dispositivo de borda de organização

Para eliminar o seu dispositivo de borda de organização como a origem da falha, verifique se que um computador que esteja conectado diretamente à Internet pode fazer conexões SSH em sua máquina virtual do Azure. Se você estiver acessando a máquina virtual por meio de uma VPN to-site ou uma conexão de rota expressa do Azure, vá para [4 de origem: grupos de segurança de rede](#nsg).

![Diagrama que destaca o dispositivo de borda de organização](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se você não tiver um computador que esteja diretamente conectado à Internet, crie uma nova VM Azure em seu próprio grupo de recursos ou serviço em nuvem e usá-lo. Para obter mais informações, consulte [criar uma máquina virtual executando Linux no Azure](virtual-machines-linux-quick-create-cli.md). Exclua o grupo de recursos ou serviço de nuvem e máquina virtual quando terminar o teste.

Se você pode criar uma conexão SSH com um computador que esteja conectado diretamente à Internet, verifique seu dispositivo de borda da organização para:

- Um firewall interno que está bloqueando o tráfego SSH com a Internet
- Um servidor proxy que está impedindo conexões SSH
- Detecção de intrusos ou software executados em dispositivos em sua rede de borda que está impedindo conexões SSH de monitoramento de rede

Trabalhar com o administrador de rede para corrigir as configurações de seus dispositivos de borda da organização para permitir o tráfego SSH com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem 3: Ponto de extremidade de serviço de nuvem e ACL

> [AZURE.NOTE] Esta fonte se aplica somente a VMs que foram criadas usando o modelo de implantação clássico. Para VMs que foram criadas usando o Gerenciador de recursos, vá para [4 de origem: grupos de segurança de rede](#nsg).

Para eliminar o ponto de extremidade de serviço de nuvem e ACL como a origem da falha, verifique se que outra VM Azure na mesma rede virtual pode fazer conexões SSH em sua máquina virtual.

![Diagrama que destaca o ponto de extremidade de serviço de nuvem e ACL](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se você não tiver máquina virtual outro na mesma rede virtual, você poderá criar facilmente um novo. Para obter mais informações, consulte [criar uma máquina virtual de Linux no Azure usando o CLI](virtual-machines-linux-quick-create-cli.md). Exclua a máquina virtual extra quando terminar com o teste.

Se você pode criar uma conexão SSH com uma máquina virtual na mesma rede virtual, verifique o seguinte:

- **A configuração de ponto de extremidade para o tráfego SSH no destino máquina virtual.** A porta TCP particular do ponto de extremidade deve corresponder a porta TCP na qual o serviço SSH na máquina virtual está atendendo. (A porta padrão é 22). Para VMs criadas usando o modelo de implantação do Gerenciador de recursos, verifique se o número da porta TCP SSH no portal do Azure selecionando **Procurar** > **máquinas virtuais (v2)** > *nome de máquina virtual* > **configurações** > **pontos de extremidade**.

- **A ACL para o ponto de extremidade de tráfego SSH na máquina virtual de destino.** Uma ACL permite especificar permitido ou negado tráfego da Internet, com base em seu endereço de IP de origem. ACLs estão configurados incorretamente podem impedir que o tráfego de entrada SSH para o ponto de extremidade. Verificar seu ACLs para garantir que tráfego de entrada dos endereços IP públicos do seu proxy ou outro servidor de borda é permitido. Para obter mais informações, consulte [sobre o acesso de rede listas de controle (ACLs)](../virtual-network/virtual-networks-acl.md).

Para eliminar o ponto de extremidade como uma fonte do problema, remova o ponto de extremidade atual, criar uma nova empresa e especifique o nome SSH (porta TCP 22 para o número da porta pública e privada). Para obter mais informações, consulte [Configurar pontos de extremidade em uma máquina virtual no Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
## <a name="source-4-network-security-groups"></a>Origem 4: Grupos de segurança de rede

Grupos de segurança de rede permitem que você tenha um controle mais granular de tráfego de entrada e saído permitidos. Você pode criar regras que abrangem sub-redes e serviços em uma rede virtual Azure em nuvem. Verifique suas regras de grupo de segurança de rede para garantir que o tráfego SSH para e da Internet é permitido.
Para obter mais informações, consulte [sobre os grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

## <a name="source-5-linux-based-azure-virtual-machine"></a>: 5 baseados em Linux Azure máquina virtual de origem

Última fonte de possíveis problemas é a própria máquina virtual Azure.

![Diagrama que destaca baseados em Linux Azure máquina virtual](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se você ainda não fez isso, siga as instruções [para redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-classic-reset-access.md).

Tente conectar do seu computador novamente. Se ele ainda falhar, estes são alguns dos problemas possíveis:

- O serviço SSH não está executando na máquina virtual de destino.
- O serviço SSH não é escutar na porta TCP 22. Para testar isso, instale um cliente telnet em seu computador local e execute "telnet *cloudServiceName*. cloudapp.net 22". Isso determina se a máquina virtual permite que a comunicação de entrada e saída para o ponto de extremidade SSH.
- O firewall local na máquina virtual de destino tem regras que estão impedindo o tráfego SSH de entrada ou de saída.
- Detecção de intrusos ou software que está em execução na máquina virtual Azure de monitoramento de rede está impedindo conexões SSH.


## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre como solucionar problemas de acesso do aplicativo, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)