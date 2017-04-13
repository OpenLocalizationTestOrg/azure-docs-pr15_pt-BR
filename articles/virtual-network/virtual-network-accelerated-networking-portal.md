<properties 
   pageTitle="Acelerada, de rede para uma máquina virtual - Portal | Microsoft Azure"
   description="Saiba como configurar a rede acelerada para uma máquina virtual Azure usando o Portal do Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Rede acelerada para uma máquina virtual

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Rede acelerada permite a única raiz e/s virtualização (SR-IOV) a uma máquina virtual (VM), bastante melhorar seu desempenho de rede. Este caminho de alto desempenho ignora o host do datapath reduzindo latência, variação e utilização de CPU para uso com as cargas de trabalho de rede maiores em tipos de máquina virtual com suporte. Este artigo explica como usar o Portal do Azure para configurar a rede acelerada no modelo de implantação do Gerenciador de recursos do Azure. Você também pode criar uma máquina virtual com acelerada rede usando o PowerShell do Azure. Para saber como fazer isso, clique na caixa do PowerShell na parte superior deste artigo.

A figura a seguir mostra a comunicação entre duas máquinas virtuais (VM) com e sem acelerada de rede:

![Comparação](./media/virtual-network-accelerated-networking-portal/image1.png)

Sem acelerada, de rede, todo o tráfego de rede e sair a máquina virtual deve percorrer o host e switch virtual. Switch virtual fornece todas as imposição de política, como grupos de segurança de rede, listas de controle de acesso, isolamento e outros serviços de rede virtualizado tráfego de rede. Para saber mais, leia o artigo [virtualização de rede do Hyper-V e Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) .

Com acelerada, de rede, o tráfego de rede chega o cartão de rede (NIC) e é então encaminhado para a máquina virtual. Todas as diretivas de rede que switch virtual aplica sem acelerada rede são descarregadas e aplicadas em hardware. Aplicação da política em hardware habilita a NIC para encaminhar o tráfego de rede diretamente para a máquina virtual, ignorando o host e a opção virtual, mantendo todo a política que ela aplicada no host.

Os benefícios da rede acelerada aplicam-se apenas para a máquina virtual que ele está habilitado. Para obter melhores resultados, ele é ideal para ativar esse recurso em pelo menos duas VMs conectada para o mesmo VNet. Ao se comunicar em VNets ou conexão local, este recurso tem um impacto mínimo latência geral.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Benefícios

- **Menor latência / superiores pacotes por segundo (pps):** Removendo switch virtual do datapath remove o tempo gastam pacotes do host para processamento de política e aumenta o número de pacotes que pode ser processado dentro a máquina virtual.
- **Reduzida variação:** Processamento de switch virtual depende da quantidade de política que precisa ser aplicada e a carga de trabalho da CPU que está fazendo o processamento. Transferindo a imposição de política para o hardware remove essa variabilidade oferecendo pacotes diretamente para a máquina virtual, removendo o host para comunicação de máquina virtual e todas as interrupções de software e opções de contexto.
- **Reduzida da CPU:** Ignorar switch virtual no host leva a menor utilização da CPU para o processamento de tráfego de rede.

## <a name="limitations"></a>Limitações

As seguintes limitações existem ao usar esse recurso:
 
- **Criação da interface de rede:** Rede acelerada só pode ser habilitada para uma nova interface de rede.  Ele não pode ser habilitado em uma interface de rede existente.
- **Criação de máquina virtual:** Uma interface de rede com a rede acelerada habilitada somente pode ser anexada a uma máquina virtual quando a máquina virtual é criada. A interface de rede não pode ser anexada a uma máquina virtual existente.
- **Regiões:** Oferecida em apenas as regiões Central Oeste dos EUA e Oeste Europa Azure. O conjunto de regiões expandirá no futuro.
- **Sistema operacional de suporte:** Microsoft Windows Server 2012 R2 e Windows Server 2016 Technical Preview 5. Suporte Linux e Windows Server 2012 será adicionado em breve.
- **Tamanho de máquina virtual:** Standard_D15_v2 e Standard_DS15_v2 são que os únicos tamanhos suportados de máquina virtual instância. Para obter mais informações, consulte o artigo de [tamanhos de máquina virtual do Windows](../virtual-machines/virtual-machines-windows-sizes.md) . O conjunto de tamanhos de instância de máquina virtual suportados expandirá no futuro.

Alterações para essas limitações serão anunciadas através da página de [atualizações de rede Virtual do Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Criar uma máquina virtual do Windows com a rede acelerada

1. Registre-se para a visualização enviando um email para [Acelerada assinaturas de rede](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido. Não conclua as etapas restantes até após receber um email notificando que você já foi aceita em visualização.
2. Fazer login no Portal do Azure em http://portal.azure.com.
3. Crie uma máquina virtual realizando as etapas no artigo [criar uma máquina virtual Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) selecionando as opções a seguir:
    - Selecione um sistema operacional listado na seção limitações deste artigo.
    - Selecione um local (região) listado na seção limitações deste artigo.
    - Selecione um tamanho de máquina virtual listado na seção limitações deste artigo. Se um dos tamanhos suportados não estiver listado, clique em **Exibir tudo** na lâmina **Escolher um tamanho** para selecionar um tamanho de uma lista expandida.
    - Na lâmina **configurações** , clique em *ativado* para **uma rede acelerada**, conforme mostrado na figura a seguir:

        ![Configurações](./media/virtual-network-accelerated-networking-portal/image3.png)

    >[AZURE.NOTE] A opção acelerada rede só ficará visível se você:
    >
    >- Foi aceita em visualização
    >- Selecionado o sistema operacional compatível, local e tamanhos de máquina virtual mencionados na seção limitações deste artigo.

5. Assim que a máquina virtual for criada, baixe o [driver de rede acelerada](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), conectar-se e faça logon na máquina virtual e executar o instalador do driver dentro a máquina virtual.
6. Clique no botão do Windows e clique em **Gerenciador de dispositivos**. Verifique se o **Adaptador de Ethernet de função Virtual Mellanox ConnectX-3** aparece sob a opção de **rede** quando expandida, conforme mostrado na figura a seguir:

    ![Gerenciador de dispositivos](./media/virtual-network-accelerated-networking-portal/image2.png)