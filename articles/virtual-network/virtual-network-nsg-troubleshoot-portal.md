<properties 
   pageTitle="Solucionar problemas de grupos de segurança de rede - Portal | Microsoft Azure"
   description="Saiba como solucionar problemas de grupos de segurança de rede no modelo de implantação do Gerenciador de recursos do Azure usando o Portal do Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Solucionar problemas de grupos de segurança de rede usando o Portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Se você configurou grupos de segurança de rede (NSGs) em sua máquina virtual (VM) e está enfrentando problemas de conectividade de máquina virtual, este artigo fornece uma visão geral dos recursos de diagnóstico para NSGs ajudar a solucionar ainda mais.

NSGs permitem que você controle os tipos de tráfego fluxo e sair de suas máquinas virtuais (VMs). NSGs podem ser aplicadas a sub-redes em uma rede Virtual do Azure (VNet), interfaces de rede (NIC) ou ambos. As regras eficazes aplicadas a uma NIC são uma agregação das regras que consta os NSGs aplicados a uma NIC e a sub-rede que ele está conectado ao. Regras entre esses NSGs às vezes podem entrar em conflito com os outros e afetar a conectividade de rede de uma máquina virtual.  

Você pode exibir todas as regras de segurança eficaz de sua NSGs, conforme aplicado em NICs do sua máquina virtual. Este artigo mostra como solucionar problemas de conectividade de máquina virtual usando essas regras no modelo de implantação do Gerenciador de recursos do Azure. Se você não estiver familiarizado com os conceitos de VNet e NSG, leia os artigos de visão geral de [rede Virtual](virtual-networks-overview.md) e [grupos de segurança de rede](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Usando regras de segurança eficaz para solucionar problemas de fluxo de tráfego de máquina virtual

O cenário que segue é um exemplo de um problema de conexão comuns:

Uma máquina virtual chamada *VM1* faz parte de uma sub-rede denominada *Subnet1* dentro de um VNet chamado *WestUS-VNet1*. A tentativa de conexão para a máquina virtual usando o RDP pela porta TCP 3389 falhará. NSGs são aplicadas a NIC *VM1 NIC1* e a sub-rede *Subnet1*. O tráfego para a porta TCP 3389 é permitido no NSG associado com a interface de rede *VM1 NIC1*, porém ping TCP para falha de porta 3389 do VM1.

Embora este exemplo usa a porta TCP 3389, as etapas a seguir podem ser usadas para determinar falhas de conexão de entrada e saída por qualquer porta.

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>Exibir regras de segurança eficaz para uma máquina virtual

Conclua estas etapas para solucionar problemas de NSGs para uma máquina virtual:

Você pode exibir a lista completa das regras de segurança eficaz em uma NIC, da máquina virtual em si. Você também pode adicionar, modificar e excluir regras NSG NIC e sub-rede da lâmina regras eficaz, se você tem permissões para executar essas operações.

1. Fazer login no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, clique em **máquinas virtuais** na lista que aparece.
3. Selecione uma máquina virtual para solucionar problemas na lista exibida e uma lâmina de máquina virtual com opções aparece.
4. Clique em **diagnosticar & resolver problemas** e selecione um problema comum. Neste exemplo, **não é possível conectar-se para minha máquina virtual Windows** está selecionada. 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)

5. Etapas aparecem sob o problema, conforme mostrado na figura a seguir: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)

    Clique em *regras de segurança eficaz de grupo* na lista de etapas recomendadas.

6. A lâmina **obter regras de segurança efetiva** é exibida, conforme mostrado na figura a seguir:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)

    Observe as seguintes seções da imagem:

    - **Escopo:** Definir como *VM1*, a máquina virtual selecionado na etapa 3.
    - **Interface de rede:** *VM1 NIC1* está selecionada. Uma máquina virtual pode ter várias interfaces de rede (NIC). Cada NIC pode ter regras de segurança eficaz exclusivo. Quando a solução de problemas, talvez seja necessário exibir as regras de segurança eficaz para cada NIC.
    - **Associado NSGs:** NSGs podem ser aplicadas a NIC e a sub-rede que a NIC é conectada ao. Na imagem, um NSG foi aplicada a NIC e a sub-rede que ele está conectado ao. Você pode clicar nos nomes de NSG para modificar diretamente as regras nas NSGs.
    - **Guia VM1 nsg:** Na lista de regras exibidas na imagem destina-se a NSG aplicado à NIC. Várias regras padrão são criadas por Azure sempre que um NSG é criado. Não é possível remover as regras padrão, mas você pode substituí-los com regras de prioridade mais alta. Para saber mais sobre regras padrão, leia o artigo de [Visão geral NSG](virtual-networks-nsg.md#default-rules) .
    - **Coluna de destino:** Algumas das regras têm texto na coluna, enquanto outras pessoas têm prefixos de endereço. O texto é o nome de marcas de formatação padrão aplicada a regra de segurança quando ele foi criado. As marcas são fornecidas pelo sistema identificadores que representam vários prefixos. Selecionando uma regra com uma marca, como *AllowInternetOutBound*, lista os prefixos na lâmina **prefixos de endereço** .
    - **Download:** A lista de regras pode ser longa. Você pode baixar um arquivo. csv das regras para análise offline clicando em **Baixar** e salvar o arquivo.
    - **AllowRDP** Regra de entrada: esta regra permite conexões RDP para a máquina virtual.
7. Clique na guia **Subnet1 NSG** para exibir as regras eficazes do NSG aplicados à sub-rede, conforme mostrado na figura a seguir: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)

    Observe que a regra de **entrada** do *denyRDP* . As regras de entrada aplicadas no sub-rede são avaliadas antes regras aplicadas na interface de rede. Desde que a regra de negar é aplicada em sub-rede, a solicitação para se conectar ao TCP 3389 falhar, porque a regra permitir a NIC nunca é avaliada. 

    A regra de *denyRDP* é o motivo por que a conexão RDP está falhando. Remoção deve resolver o problema.

    >[AZURE.NOTE]Se a máquina virtual associada a NIC não está em um estado de execução ou NSGs ainda não foram aplicados ao sub-rede ou NIC, nenhuma regra é mostrada.

8. Para editar as regras NSG, clique em *Subnet1 NSG* na seção **NSGs associado** .
   Isso abre a lâmina **Subnet1-NSG** . Você pode editar diretamente as regras clicando em **regras de segurança de entrada**.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)

9. Após removendo a regra de entrada do *denyRDP* a **Subnet1-NSG** e adicionar uma regra de *allowRDP* , a lista de regras eficaz é semelhante a imagem a seguir:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)

    Confirme que a porta TCP 3389 esteja aberta abrindo uma conexão de RDP para a máquina virtual ou usando a ferramenta de PsPing. Você pode saber mais sobre PsPing lendo a [página de download de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="view-effective-security-rules-for-a-network-interface"></a>Exibir regras de segurança eficaz para uma interface de rede

Se seu fluxo de tráfego de máquina virtual é afetado para uma NIC específica, você pode exibir uma lista completa das regras eficazes para a NIC do contexto de interfaces de rede realizando as seguintes etapas:

1. Fazer login no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, clique em **interfaces de rede** na lista que aparece.
3. Selecione uma interface de rede. Na figura a seguir, uma NIC denominada *VM1 NIC1* está selecionada.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)

    Observe que o **escopo** está definido para a interface de rede selecionada. Para saber mais sobre as informações adicionais mostradas, leia a etapa 6 da seção **Solucionar NSGs para uma máquina virtual** deste artigo.

    >[AZURE.NOTE] Se um NSG é removido de uma interface de rede, a sub-rede NSG ainda seja efetivo no NIC determinado. Nesse caso, o resultado seria Mostrar apenas regras da sub-rede NSG. Regras aparecem somente se a NIC estiver conectada a uma máquina virtual.

4. Você pode editar diretamente regras para NSGs associados a uma NIC e uma sub-rede. Para saber como fazer isso, leia a etapa 8 da seção **regras de segurança eficaz de modo de exibição para uma máquina virtual** deste artigo.

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>Exibir regras de segurança eficaz para um grupo de segurança de rede (NSG)

Ao modificar regras NSG, talvez você queira examinar o impacto das regras a ser adicionado em uma determinada VM. Você pode exibir uma lista completa das regras de segurança efetivas para todas as NICs que um determinado NSG é aplicado, sem precisar alternar o contexto da lâmina NSG determinado. Para solucionar problemas de regras eficazes dentro de um NSG, conclua as seguintes etapas:

1. Fazer login no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**, clique em **grupos de segurança de rede** na lista que aparece.
3. Selecione um NSG. Na figura a seguir, um NSG chamado VM1-nsg foi selecionada.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)

    Observe as seguintes seções da imagem anterior:

    - **Escopo:** Defina como o NSG selecionado.
    - **Máquina Virtual:** Quando um NSG é aplicado a uma sub-rede, ela é aplicada a todas as interfaces de rede anexadas a todas as VMs conectadas à sub-rede. Esta lista mostra todas as VMs este NSG é aplicado a. Você pode selecionar qualquer máquina virtual na lista.

    >[AZURE.NOTE] Se um NSG é aplicado a apenas uma sub-rede vazia, VMs não serão listadas. Se um NSG é aplicado a uma NIC que não está associada uma máquina virtual, esses NICs também não serão listadas. 
    - **Interface de rede:** Uma máquina virtual pode ter várias interfaces de rede. Você pode selecionar uma interface de rede anexada para a máquina virtual selecionada.
    - **AssociatedNSGs:** A qualquer momento, uma NIC pode ter até dois NSGs eficazes, aplicado a NIC e o outro à sub-rede. Embora o escopo for selecionado como VM1-nsg, se a NIC tem uma sub-rede eficaz NSG, a saída mostrará os dois NSGs.
4. Você pode editar diretamente regras para NSGs associados a uma NIC ou sub-rede. Para saber como fazer isso, leia a etapa 8 da seção **regras de segurança eficaz de modo de exibição para uma máquina virtual** deste artigo.

Para saber mais sobre as informações adicionais mostradas, leia a etapa 6 da seção **regras de segurança eficaz de modo de exibição para uma máquina virtual** deste artigo.

>[AZURE.NOTE] Embora uma sub-rede e NIC cada só podem ter um que NSG aplicada a eles, um NSG pode ser associada a várias placas de rede e várias sub-redes.

## <a name="considerations"></a>Considerações

Considere os seguintes pontos ao solucionar problemas de conectividade:

- Regras NSG padrão bloqueará o acesso de entrada da internet e permitir apenas VNet o tráfego de entrada. Regras devem ser explicitamente adicionadas para permitir o acesso de entrada da Internet, conforme necessário.
- Se não houver nenhuma regra de segurança NSG causando conectividade de rede de uma máquina virtual falha, o problema pode ser devido a:
    - Software de firewall executado no sistema operacional da VM
    - Rotas configuradas para dispositivos virtuais ou tráfego de local. Tráfego de Internet pode ser redirecionado para o local por meio de túnel forçada. Uma conexão de RDP/SSH da Internet para sua máquina virtual pode não funcionar com essa configuração, dependendo de como o hardware de rede local lida com esse tráfego. Leia o artigo de [Solução de problemas de rotas](virtual-network-routes-troubleshoot-powershell.md) para aprender a diagnosticar problemas de rota que podem ser impedir o fluxo de tráfego e sair a máquina virtual. 
- Se você tiver peered VNets, por padrão, a marca VIRTUAL_NETWORK se expandirá automaticamente para incluir prefixos para VNets peered. Você pode exibir esses prefixos na lista **ExpandedAddressPrefix** , para solucionar qualquer problema relacionado à conectividade aos VNet. 
- Regras de segurança efetivas são mostradas somente se houver um NSG associado a máquina virtual NIC e ou sub-rede. 
- Não se houver nenhum NSGs associados a NIC ou sub-rede e você tem um endereço IP público atribuído a sua máquina virtual, todas as portas serão abertas para acesso de entrada e saído. Se a máquina virtual tem um endereço IP público, aplicar NSGs ao sub-rede ou NIC é recomendável.