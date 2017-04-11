<properties 
   pageTitle="Configurar MPIO em seu host de array virtual StorSimple | Microsoft Azure"
   description="Descreve como configurar e/s de vários caminhos (MPIO) para sua StorSimple array virtual conectado a um host executando o Windows Server 2012 R2."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurar vários caminhos i/o host do Windows Server para a matriz Virtual StorSimple

## <a name="overview"></a>Visão geral

Este artigo descreve como instalar o recurso de vários caminhos i/o (MPIO) em seu host do Windows Server, aplicar configurações específicas para volumes somente StorSimple e, em seguida, verifique se MPIO para volumes de StorSimple. O procedimento pressupõe que a matriz de Virtual StorSimple 1200 com duas interfaces de rede está conectada a um host do Windows Server com duas interfaces de rede. As informações contidas neste artigo se aplica somente a matriz virtual. Para obter informações sobre os dispositivos de série 8000 StorSimple, vá para [Configurar MPIO do host de StorSimple](storsimple-configure-mpio-windows-server.md). 

O recurso MPIO em configurações de armazenamento altamente disponível e tolerância de compilação de Ajuda do Windows Server. MPIO usa componentes de caminho físico redundantes — adaptadores, cabos e opções — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se houver uma falha de componente, causando um caminho lógico falha, lógica de vários caminhos usa um caminho alternativo para i/o para que os aplicativos ainda possam acessar seus dados. Além disso dependendo da configuração, MPIO pode também melhorar o desempenho por novamente balanceamento de carga por todos esses caminhos. Para obter mais informações, consulte [Visão geral do MPIO](https://technet.microsoft.com/library/cc725907.aspx "recursos e visão geral do MPIO").  

Para a alta disponibilidade de sua solução de StorSimple, configure MPIO nos hosts Windows Server conectados à sua matriz de Virtual StorSimple 1200 (também conhecido como o local virtual dispositivo). Os servidores de host, em seguida, podem suportar um link, rede ou falha de interface. 

Você precisará seguir estas etapas para configurar MPIO: 

- Pré-requisitos de configuração

- Etapa 1: Instalar MPIO do host do Windows Server

- Etapa 2: Configurar MPIO para volumes de StorSimple

- Etapa 3: Volumes de montagem StorSimple do host

Cada uma das etapas acima é discutida nas seções a seguir.


## <a name="prerequisites"></a>Pré-requisitos

Esta seção detalha os pré-requisitos de configuração para o host do Windows Server e seu array virtual.

### <a name="on-windows-server-host"></a>Host do Windows Server

-  Certifique-se de que seu host do Windows Server tem 2 interfaces de rede habilitadas.


### <a name="on-storsimple-virtual-array"></a>Em matriz virtual StorSimple

- A matriz virtual deve ser configurada como um servidor iSCSI. Para saber mais, consulte [Configurar array virtual como um servidor de iSCSI](storsimple-ova-deploy3-iscsi-setup.md). Uma ou mais interfaces de rede devem ser habilitadas no array.   

- Interfaces de rede em seu array virtual devem ser acessadas a partir do host do Windows Server.

- Um ou mais volumes devem ser criados no Array Virtual StorSimple. Para saber mais, consulte [Adicionar um volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) no array virtual StorSimple 1200. Neste procedimento, criamos 3 volumes (2 localmente fixados e 1 hierárquico volume conforme mostrado abaixo) no array virtual.
    
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuração de hardware para array virtual StorSimple

A figura a seguir mostra a configuração de hardware para alta disponibilidade e vários caminhos de balanceamento de carga de seu host do Windows Server e StorSimple array virtual usados neste procedimento.  

![configuração de hardware do MPIO](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Como mostrado na figura anterior:

- Sua matriz virtual StorSimple provisionado no Hyper-V é um dispositivo de ativos de nó único configurado como um servidor de iSCSI.

- Duas interfaces de rede virtual estão ativadas em sua matriz. No local da interface do usuário da sua matriz virtual 1200 web, verifique se que duas interfaces de rede estão habilitadas, navegando até **Configurações de rede** , conforme mostrado abaixo:

    ![Interfaces de rede ativadas 1200](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
    
    Observação os endereços IPv4 enabled interfaces de rede (Ethernet, 2 Ethernet por padrão) e salva para uso posterior do host.

- Duas interfaces de rede estão ativadas em seu host do Windows Server. Se as interfaces conectadas para o host e array estiverem na mesma sub-rede, em seguida, haverá 4 caminhos disponíveis. Isso era o caso neste procedimento. No entanto, se cada interface de rede na interface do array e host em uma sub-rede IP diferente (e não roteáveis), somente 2 caminhos estará disponíveis.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Etapa 1: Instalar MPIO do host do Windows Server

MPIO é um recurso opcional no Windows Server e não é instalado por padrão. Ele deve ser instalado como um recurso através do Gerenciador de servidor. Para instalar este recurso em seu host do Windows Server, conclua o procedimento a seguir.

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Etapa 2: Configurar MPIO para volumes de StorSimple

MPIO precisa estar configurado para identificar os volumes de StorSimple. Para configurar MPIO para reconhecer volumes de StorSimple, execute as etapas a seguir.

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Etapa 3: Volumes de montagem StorSimple do host

Depois de MPIO estiver configurado no Windows Server, volumes criados na matriz StorSimple podem ser montado e, em seguida, poderá tirar proveito de MPIO para redundância. Para montar um volume, execute as seguintes etapas.

#### <a name="to-mount-volumes-on-the-host"></a>Para montar volumes do host

1. Abra a janela de **Propriedades do iniciador iSCSI** do host do Windows Server. Clique em **Server Manager > Painel de controle > Ferramentas > iniciador iSCSI**.
2. Na caixa de diálogo **Propriedades do iniciador iSCSI** , clique na guia Discovery e, em seguida, clique em **Descubra o Portal de destino**.
3. Na caixa de diálogo **Descubra o Portal de destino** , faça o seguinte:
    
    - Insira o endereço IP da interface de rede habilitado primeiro no array virtual StorSimple. Por padrão, isso seria **Ethernet**. 
    - Clique em **Okey** para retornar à caixa de diálogo **Propriedades do iniciador iSCSI** .

    >[AZURE.IMPORTANT] **Se você estiver usando uma rede privada para conexões iSCSI, insira o endereço IP da porta de dados que está conectado à rede privada.**

4. Repita as etapas 2-3 para uma segunda interface de rede (por exemplo, Ethernet 2) em sua matriz. 

5. Selecione a guia **destinos** na caixa de diálogo **Propriedades do iniciador iSCSI** . Para sua matriz virtual, você deve ver cada superfície de volume como um destino em **Destinos detectados**. Nesse caso, três destinos (correspondente a três volumes) deve ser descobertos.

    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. Clique em **Conectar** para estabelecer uma sessão iSCSI com sua matriz de StorSimple. Uma caixa de diálogo **conectar ao destino** aparecerá. Marque a caixa de seleção **Habilitar múltiplos caminhos** . Clique em **Avançado**.

    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. Na caixa de diálogo **Configurações avançadas** , faça o seguinte:                                       
    -    Na lista suspensa **Adaptador Local** , selecione **Microsoft iSCSI iniciador**.
    -    Na lista suspensa **IP do iniciador** , selecione o endereço IP do host.
    -    Na lista suspensa do IP de **Portal de destino** , selecione o IP da interface de matriz.
    -    Clique em **Okey** para retornar à caixa de diálogo **Propriedades do iniciador iSCSI** .

    ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. Clique em **Propriedades**. 

    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. Na caixa de diálogo **Propriedades** , clique em **Adicionar sessão**.

    ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. Na caixa de diálogo **conectar ao destino** , marque a caixa de seleção **Habilitar múltiplos caminhos** . Clique em **Avançado**.
11. Na caixa de diálogo **Configurações avançadas** :                                        
    -  Na lista suspensa **adaptador Local** , selecione Microsoft iSCSI iniciador.
    -  Na lista suspensa **IP do iniciador** , selecione o endereço IP correspondente para o host. Nesse caso, você está se conectando duas interfaces de rede na matriz uma interface de rede do host. Portanto, esta interface é a mesma que fornecido para a primeira sessão.
    -  Na lista suspensa **IP do Portal de destino** , selecione o endereço IP para a segunda interface de dados habilitado na matriz.
    -  Clique em **Okey** para retornar à caixa de diálogo de propriedades do iniciador iSCSI. Você adicionou uma segunda sessão ao destino.

        ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

    - Depois de adicionar as sessões desejadas (caminhos), na caixa de diálogo **Propriedades do iniciador iSCSI** , selecione o destino e clique em **Propriedades**. Na guia sessões da caixa de diálogo **Propriedades** , observe a sessão quatro identificadores que correspondem aos permutações caminho possível. Para cancelar uma sessão, marque a caixa de seleção ao lado de um identificador de sessão e, em seguida, clique em **Desconectar**.
 
    - Para exibir os dispositivos apresentados em sessões, selecione a guia **dispositivos** . Para configurar a política MPIO para um dispositivo selecionado, clique em **MPIO**. O * *
    -  Detalhes** caixa de diálogo será exibida. Sobre o **MPIO** guia, você pode selecionar o apropriado **diretiva de balanceamento de carga** configurações. Você também pode visualizar o **ativo** ou **tipo de caminho espera * *.

10. Repita as etapas 8-11 para adicionar sessões adicionais (caminhos) para o destino. Com duas interfaces do host e dois no array virtual, você pode adicionar um total de quatro sessões para cada destino. 

    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. Você precisará repetir essas etapas para cada volume (emerge as como um destino).

    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. Abra **Gerenciamento do computador** , navegando até **Server Manager > Painel de controle > Gerenciamento do computador**. No painel esquerdo, clique em **armazenamento > Gerenciamento de disco**. Os volumes criados na matriz virtual StorSimple que estão visíveis para este host aparecerá em **Gerenciamento de disco** como novo s.

13. Inicializar o disco e crie um novo volume. Durante o processo de formato, selecione um tamanho de unidade de alocação (Austrália) de 64 KB. Repita o processo para todos os volumes disponíveis.

    ![Gerenciamento de disco](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. Em **Gerenciamento de disco**, clique com botão direito no **disco** e selecione **Propriedades**.

15. Na caixa de diálogo **Propriedades do dispositivo de disco de múltiplos caminhos** , clique na guia **MPIO** .

    ![Propriedades do disco](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. Na seção **Nome DSM** , clique em **detalhes** e verificar se os parâmetros estão definidos para os parâmetros padrão. Os parâmetros padrão são:

    - Caminho verificar período = 30
    - Repetir contagem = 3
    - Período de remoção de PDO = 20
    - Intervalo de repetição = 1
    - Verificar o caminho habilitado = desmarcada.

    >[AZURE.NOTE] **Não modifique os parâmetros padrão.**


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar sua matriz Virtual StorSimple](storsimple-ova-manager-service-administration.md).
 
