
<properties
   pageTitle="Começar a criar um Internet opostas balanceador de carga no modelo de implantação clássico usando o portal de clássico Azure | Microsoft Azure"
   description="Aprenda a criar uma Internet opostas balanceador de carga no modelo de implantação clássico no portal Azure clássico"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Começar a criar um Internet opostas balanceador de carga (clássico) no portal de clássico do Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo discute o modelo de implantação clássico. Você também pode [aprender a criar uma Internet opostas balanceador de carga usando o Gerenciador de recursos do Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurar um balanceador de carga voltado para a Internet para máquinas virtuais

Para carregar o tráfego de rede de saldo da Internet máquinas virtuais de um serviço de nuvem, você deve criar um conjunto de balanceamento de carga. Este procedimento supõe que você já tiver criado as máquinas virtuais e que eles estão tudo dentro do mesmo serviço de nuvem.

**Para configurar um conjunto de balanceamento de carga para máquinas virtuais**

1. No portal do Azure clássico, clique em **máquinas virtuais**e clique no nome de uma máquina virtual do conjunto de balanceamento de carga.

2. Clique em **pontos de extremidade**e clique em **Adicionar**.

3. Na página **Adicionar um ponto de extremidade para uma máquina virtual** , clique na seta à direita.

4. Na página **especificar os detalhes do ponto de extremidade** :

    * Em **nome**, digite um nome para o ponto de extremidade ou selecione o nome da lista de pontos de extremidade predefinidos para protocolos comuns.
    * Em **protocolo**, selecione o protocolo necessário para o tipo de ponto de extremidade, TCP ou UDP, conforme necessário.
    * Em **porta pública e privada**, digite os números de porta que você deseja a máquina virtual para usar, conforme necessário. Você pode usar as regras de firewall e porta privada na máquina virtual para redirecionar o tráfego de uma maneira que seja apropriado para seu aplicativo. A porta particular pode ser a mesma que a porta pública. Por exemplo, para um ponto de extremidade para tráfego da web (HTTP), você poderia atribuir porta 80 para a porta pública e privada.

5. Selecione **criar um conjunto de balanceamento de carga**e, em seguida, clique na seta à direita.

6. Na página **Configurar o conjunto de balanceamento de carga** , digite um nome para o conjunto de balanceamento de carga e atribuir os valores de comportamento de teste de Balanceador de carga de Azure. O balanceador de carga usa testes para determinar se as máquinas virtuais do conjunto de balanceamento de carga estão disponíveis para receber tráfego de entrada.

7. Clique na marca de seleção para criar o ponto de extremidade de balanceamento de carga. Você verá **Sim** na coluna **nome do conjunto de balanceamento de carga** da página **pontos de extremidade** da máquina virtual.

8. No portal do, clique em **máquinas virtuais**, clique no nome de uma máquina virtual adicional no conjunto de balanceamento de carga, clique em **pontos de extremidade**e clique em **Adicionar**.

9. Na página **Adicionar um ponto de extremidade para uma máquina virtual** , clique em **ponto de extremidade de adicionar a um conjunto de balanceamento de carga existente**, selecione o nome do conjunto de balanceamento de carga e em seguida, clique na seta à direita.

10. Na página **especificar os detalhes do ponto de extremidade** , digite um nome para o ponto de extremidade e, em seguida, clique na marca de seleção.

Para as máquinas virtuais adicionais no conjunto de balanceamento de carga, repita as etapas 8-10.



## <a name="next-steps"></a>Próximas etapas

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)

