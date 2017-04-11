<properties
   pageTitle="Começar a criar um balanceador de carga interno no Gerenciador de recursos usando o portal de Azure | Microsoft Azure"
   description="Aprenda a criar um balanceador de carga interno no Gerenciador de recursos usando o portal do Azure"
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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Criar um balanceador de carga interno no portal do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implantação clássico](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Começar a criar um balanceador de carga interno usando o portal do Azure

Use as seguintes etapas para criar um balanceador de carga interno do Portal do Azure.

1. Abra um navegador, navegue até o [portal do Azure](http://portal.azure.com)e entre com sua conta do Azure.
2. No lado superior esquerdo da tela, clique em **novo** > **rede** > **balanceador de carga**.
3. Na lâmina **balanceador de carga de criar** , insira um **nome** para seu balanceador de carga.
4. Em **esquema**, clique em **interno**.
5. Clique em **rede Virtual**e, em seguida, selecione a rede virtual onde você deseja criar o balanceador de carga.

    >[AZURE.NOTE] Se você não vir a rede virtual que você deseja usar, verifique o **local** que você está usando para o balanceador de carga e alterá-lo de acordo.

6. Clique em **sub-rede**e selecione a sub-rede onde você deseja criar o balanceador de carga.
7. Em **atribuição de endereço IP**, clique em **dinâmico** ou **estático**, dependendo se você deseja que o endereço IP para o balanceador de carga ser corrigido (estática) ou não.

    >[AZURE.NOTE] Se você selecionar usar um endereço IP estático, você precisará fornecer um endereço de Balanceador de carga.

8. Em **grupo de recursos** especifique o nome de um novo grupo de recursos para o balanceador de carga, ou clique em **Selecionar existente** e selecione um grupo de recursos existente.
9. Clique em **criar**.

## <a name="configure-load-balancing-rules"></a>Configurar regras de balanceamento de carga

Após a criação de Balanceador de carga, navegue para o recurso de Balanceador de carga configurá-lo.
Você precisa configurar primeiro um pool de endereços de back-end e um teste antes de configurar uma regra de balanceamento de carga.

### <a name="step-1-configure-a-back-end-pool"></a>Etapa 1: Configurar um pool de back-end

1. No portal do Azure, clique em **Procurar** > **balanceadores de carga**e clique em balanceador de carga que você criou acima.
2. Na lâmina **configurações** , clique **em pools back-end**.
3. Na lâmina **pools de endereços de back-end** , clique em **Adicionar**.
4. Na lâmina **Adicionar pool de back-end** , insira um **nome** para o pool de back-end e, em seguida, clique em **Okey**.

### <a name="step-2-configure-a-probe"></a>Etapa 2: Configurar um teste

1. No portal do Azure, clique em **Procurar** > **balanceadores de carga**e clique em balanceador de carga que você criou acima.
2. Na lâmina **configurações** , clique em **testes**.
3. Na lâmina **testes** , clique em **Adicionar**.
4. Na lâmina **Adicionar teste** , insira um **nome** para o teste.
5. Em **protocolo**, selecione **HTTP** (para sites da web) ou **TCP** (para outros aplicativos de TCP com base).
6. Em **porta**, especifique a porta para usar quando acessar o teste.
7. Em **caminho** (para somente testes HTTP), especifique o caminho para usar como um teste.
8. Em **intervalo** , especifique a frequência de teste do aplicativo.
9. Em **limite não íntegra**, especifique quantas tentativas devem falhar antes que a máquina virtual de back-end está marcada como não íntegra.
10. Clique em **Okey** para criar teste.

### <a name="step-3-configure-load-balancing-rules"></a>Etapa 3: Configurar regras de balanceamento de carga

1. No portal do Azure, clique em **Procurar** > **balanceadores de carga**e clique em balanceador de carga que você criou acima.
2. Na lâmina **configurações** , clique em **regras de balanceamento de carga**.
3. Na lâmina **balanceamento de carga de regras** , clique em **Adicionar**.
4. Na lâmina **Adicionar regra de balanceamento de carga** , insira um **nome** para a regra.
5. Em **protocolo**, selecione **HTTP** (para sites da web) ou **TCP** (para outros aplicativos de TCP com base).
6. Em **porta**, especifique os porta os clientes se conectam no balanceador de carga.
7. Em **porta de back-end**, especifique a porta a ser usada no pool de back-end (geralmente, a porta de Balanceador de carga e a porta de back-end são as mesmas).
8. Em **pool de back-end**, selecione o pool de back-end criado acima.
9. Em **persistência sessão**, selecione como você deseja que as sessões persistir.
10. Em **tempo limite ocioso (minutos)**, especifique o tempo limite ocioso.
11. Em **IP flutuante (servidor direta retorno)**, clique em **habilitada**ou **desabilitada** .
12. Clique em **Okey**.

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição de Balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)