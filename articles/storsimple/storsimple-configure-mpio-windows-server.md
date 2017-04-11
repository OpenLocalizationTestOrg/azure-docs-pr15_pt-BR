<properties 
   pageTitle="Configurar MPIO para seu dispositivo de StorSimple | Microsoft Azure"
   description="Descreve como configurar e/s de vários caminhos (MPIO) para seu dispositivo de StorSimple conectado a um host executando o Windows Server 2012 R2."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurar vários caminhos i/o para o seu dispositivo de StorSimple

A Microsoft criou o suporte para o recurso de e/s de vários caminhos (MPIO) no Windows Server configurações de SAN ajuda compilação altamente disponível, tolerância. MPIO usa componentes de caminho físico redundantes — adaptadores, cabos e opções — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se houver uma falha de componente, causando um caminho lógico falha, lógica de vários caminhos usa um caminho alternativo para i/o para que os aplicativos ainda possam acessar seus dados. Além disso dependendo da configuração, MPIO pode também melhorar o desempenho por novamente balanceamento de carga por todos esses caminhos. Para obter mais informações, consulte [Visão geral do MPIO](https://technet.microsoft.com/library/cc725907.aspx "recursos e visão geral do MPIO").  

Para a alta disponibilidade de sua solução de StorSimple, MPIO deve ser configurado em seu dispositivo de StorSimple. Quando MPIO é instalado em servidores host executando o Windows Server 2012 R2, os servidores, em seguida, podem suportar um link, rede ou falha de interface. 

MPIO é um recurso opcional no Windows Server e não é instalado por padrão. Ele deve ser instalado como um recurso através do Gerenciador de servidor. Este tópico descreve as etapas que você deverá seguir para instalar e usar o recurso MPIO em um host executando o Windows Server 2012 R2 e conectado a um dispositivo físico StorSimple.

>[AZURE.NOTE] **Este procedimento é aplicável somente para a série 8000 StorSimple. MPIO não é suportado atualmente em um dispositivo virtual StorSimple.**

Você precisará seguir estas etapas para configurar MPIO em seu dispositivo de StorSimple:

- Etapa 1: Instalar MPIO do host do Windows Server

- Etapa 2: Configurar MPIO para volumes de StorSimple

- Etapa 3: Volumes de montagem StorSimple do host

- Etapa 4: Configurar MPIO para alta disponibilidade e balanceamento de carga

Cada uma das etapas acima é discutida nas seções a seguir.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Etapa 1: Instalar MPIO do host do Windows Server

Para instalar este recurso em seu host do Windows Server, conclua o procedimento a seguir.

#### <a name="to-install-mpio-on-the-host"></a>Para instalar o MPIO do host

1. Abra o Gerenciador de servidor em seu host do Windows Server. Por padrão, o Gerenciador de servidor inicia quando um membro do grupo Administradores faz em um computador que está executando o Windows Server 2012 R2 ou Windows Server 2012. Se o Gerenciador de servidores não ainda estiver aberto, clique em **Iniciar > Gerenciador de servidor**.
![Gerenciador de servidor](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Clique em **Server Manager > Painel de controle > Adicionar funções e recursos**. Isso inicia o assistente **para adicionar funções e recursos** .
![Adicionar funções e recursos assistente 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. No assistente **para adicionar funções e recursos** , faça o seguinte:

    - Na página **antes de começar** , clique em **Avançar**.
    - Na página **Selecionar o tipo de instalação** , aceite a configuração padrão da instalação **baseada em função ou baseada em recursos** . Clique em **Avançar**. ![Adicionar funções e recursos Assistente 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
    - Na página **Selecionar o servidor de destino** , escolha **Selecionar um servidor do pool de servidor**. Seu servidor host deve ser detectado automaticamente. Clique em **Avançar**.
    - Na página **Selecionar funções de servidor** , clique em **Avançar**.
    - Na página **Selecionar recursos** , selecione **I/o de vários caminhos**e clique em **Avançar**. ![Adicionar funções e 5 do Assistente de recursos](./media/storsimple-configure-mpio-windows-server/IC741000.png)
    - Na página **Confirmar seleções de instalação** , confirmar a seleção e selecione **reiniciar o servidor de destino automaticamente se necessário**, conforme mostrado abaixo. Clique em **instalar**. ![Adicionar funções e recursos Assistente 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
    - Você será notificado quando a instalação estiver concluída. Clique em **Fechar** para fechar o assistente. ![Adicionar funções e recursos Assistente 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Etapa 2: Configurar MPIO para volumes de StorSimple

MPIO precisa estar configurado para identificar os volumes de StorSimple. Para configurar MPIO para reconhecer volumes de StorSimple, execute as etapas a seguir.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Configurar MPIO para volumes de StorSimple

1. Abra a **configuração MPIO**. Clique em **Gerenciador do servidor > Painel de controle > Ferramentas > MPIO**.

2. Na caixa de diálogo **Propriedades do MPIO** , selecione a guia **Descobrir vários caminhos** .

3. Selecione **Adicionar suporte para dispositivos iSCSI**e clique em **Adicionar**.  
![Propriedades do MPIO descobrir vários caminhos](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Reinicie o servidor quando solicitado.
5. Na caixa de diálogo **Propriedades do MPIO** , clique na guia **Dispositivos de MPIO** . Clique em **Adicionar**.
    </br>![MPIO propriedades MPIO dispositivos](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Na caixa de diálogo **Adicionar suporte a MPIO** em **ID de Hardware do dispositivo**, insira seu número de série do dispositivo. Você pode obter o número de série do dispositivo acessando seu serviço de Gerenciador de StorSimple e navegando até **dispositivos > Dashboard**. O número de série do dispositivo é exibido no canto direito painel **Resumo de geral rápida** do painel de dispositivo.
    </br>![Adicionar suporte a MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicie o servidor quando solicitado.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Etapa 3: Volumes de montagem StorSimple do host

Depois de MPIO estiver configurado no Windows Server, volumes criados no dispositivo StorSimple podem ser montado e, em seguida, poderá tirar proveito de MPIO para redundância. Para montar um volume, execute as seguintes etapas.

#### <a name="to-mount-volumes-on-the-host"></a>Para montar volumes do host

1. Abra a janela de **Propriedades do iniciador iSCSI** do host do Windows Server. Clique em **Server Manager > Painel de controle > Ferramentas > iniciador iSCSI**.
2. Na caixa de diálogo **Propriedades do iniciador iSCSI** , clique na guia Discovery e, em seguida, clique em **Descubra o Portal de destino**.
3. Na caixa de diálogo **Descubra o Portal de destino** , faça o seguinte:
    
    - Insira o endereço IP da porta de dados do seu dispositivo de StorSimple (por exemplo, insira dados 0).
    - Clique em **Okey** para retornar à caixa de diálogo **Propriedades do iniciador iSCSI** .

    >[AZURE.IMPORTANT] **Se você estiver usando uma rede privada para conexões iSCSI, insira o endereço IP da porta de dados que está conectado à rede privada.**

4. Repita as etapas 2-3 para uma segunda interface de rede (por exemplo, 1 de dados) em seu dispositivo. Tenha em mente que essas interfaces devem ser habilitadas para iSCSI. Para saber mais sobre isso, consulte [interfaces de rede de modificar](storsimple-modify-device-config.md#modify-network-interfaces).
5. Selecione a guia **destinos** na caixa de diálogo **Propriedades do iniciador iSCSI** . Você deve ver o destino de dispositivo de StorSimple IQN em **Destinos detectados**.
 ![Guia de destinos de propriedades do iniciador iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Clique em **Conectar** para estabelecer uma sessão iSCSI com o dispositivo StorSimple. Uma caixa de diálogo **conectar ao destino** aparecerá.

7. Na caixa de diálogo **conectar ao destino** , marque a caixa de seleção **Habilitar múltiplos caminhos** . Clique em **Avançado**.

8. Na caixa de diálogo **Configurações avançadas** , faça o seguinte:                                       
    -    Na lista suspensa **Adaptador Local** , selecione **Microsoft iSCSI iniciador**.
    -    Na lista suspensa **IP do iniciador** , selecione o endereço IP do host.
    -    Na lista suspensa do IP de **Portal de destino** , selecione o IP da interface de dispositivo.
    -    Clique em **Okey** para retornar à caixa de diálogo **Propriedades do iniciador iSCSI** .

9. Clique em **Propriedades**. Na caixa de diálogo **Propriedades** , clique em **Adicionar sessão**.
10. Na caixa de diálogo **conectar ao destino** , marque a caixa de seleção **Habilitar múltiplos caminhos** . Clique em **Avançado**.
11. Na caixa de diálogo **Configurações avançadas** :                                        
    -  Na lista suspensa **adaptador Local** , selecione Microsoft iSCSI iniciador.
    -  Na lista suspensa **IP do iniciador** , selecione o endereço IP correspondente para o host. Nesse caso, você está se conectando duas interfaces de rede do dispositivo uma interface de rede do host. Portanto, esta interface é a mesma que fornecido para a primeira sessão.
    -  Na lista suspensa **IP do Portal de destino** , selecione o endereço IP para a segunda interface de dados habilitado no dispositivo.
    -  Clique em **Okey** para retornar à caixa de diálogo de propriedades do iniciador iSCSI. Você adicionou uma segunda sessão ao destino.

12. Abra **Gerenciamento do computador** , navegando até **Server Manager > Painel de controle > Gerenciamento do computador**. No painel esquerdo, clique em **armazenamento > Gerenciamento de disco**. Os volumes criados no dispositivo StorSimple que estão visíveis para este host aparecerá em **Gerenciamento de disco** como novo s.

13. Inicializar o disco e crie um novo volume. Durante o processo de formato, selecione um tamanho de bloco de 64 KB.
![Gerenciamento de disco](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Em **Gerenciamento de disco**, clique com botão direito no **disco** e selecione **Propriedades**.
15. No modelo de StorSimple # caixa de diálogo de **Propriedades do dispositivo de disco de múltiplos caminhos** , clique na guia **MPIO** .
![StorSimple 8100 disco de vários caminhos DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. Na seção **Nome DSM** , clique em **detalhes** e verificar se os parâmetros estão definidos para os parâmetros padrão. Os parâmetros padrão são:

    - Caminho verificar período = 30
    - Repetir contagem = 3
    - Período de remoção de PDO = 20
    - Intervalo de repetição = 1
    - Verificar o caminho habilitado = desmarcada.


>[AZURE.NOTE] **Não modifique os parâmetros padrão.**

## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Etapa 4: Configurar MPIO para alta disponibilidade e balanceamento de carga

Para vários caminhos baseados em alta disponibilidade e balanceamento de carga, várias sessões devem ser adicionadas manualmente a declarar os diversos caminhos disponíveis. Por exemplo, se o host tem duas interfaces conectado a SAN e o dispositivo tem duas interfaces conectadas a SAN, necessárias quatro sessões configuradas com permutações de caminho apropriado (somente duas sessões será necessário se cada interface de dados e a interface de host estiverem em uma sub-rede IP diferente e não roteáveis).

>[AZURE.IMPORTANT] **Recomendamos que você não misture 1 GbE interfaces e 10 GbE rede. Se você usa duas interfaces de rede, ambas as interfaces devem ser do tipo idêntico.**

O procedimento a seguir descreve como adicionar sessões quando um dispositivo StorSimple com duas interfaces de rede está conectado a um host com duas interfaces de rede.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Para configurar MPIO para alta disponibilidade e balanceamento de carga

1. Executar uma descoberta do destino: na caixa de diálogo **iSCSI iniciador propriedades** , na guia **Descoberta** , clique em **Descubra o Portal**.
2. Na caixa de diálogo **conectar ao destino** , insira o endereço IP de um dispositivo interfaces de rede.
3. Clique em **Okey** para retornar à caixa de diálogo **Propriedades do iniciador iSCSI** .

4. Na caixa de diálogo **Propriedades do iniciador iSCSI** , selecione a guia **destinos** , realce o destino descoberto e clique em **Conectar**. Caixa de diálogo **conectar ao destino** é exibida.

5. Na caixa de diálogo **conectar ao destino** :
    
    - Deixe o destino selecionado padrão configuração para **Adicionar esta conexão** à lista de destinos favoritos. Isso fará com que o dispositivo automaticamente tente reiniciar a conexão sempre reiniciar este computador.
    - Marque a caixa de seleção **Habilitar múltiplos caminhos** .
    - Clique em **Avançado**.

6. Na caixa de diálogo **Configurações avançadas** :
    - Na lista suspensa **Adaptador Local** , selecione **Microsoft iSCSI iniciador**.
    - Na lista suspensa **IP do iniciador** , selecione o endereço IP do host.
    - Na lista suspensa **IP do Portal de destino** , selecione o endereço IP da interface de dados habilitado no dispositivo.
    - Clique em **Okey** para retornar à caixa de diálogo de propriedades do iniciador iSCSI.

7. Clique em **Propriedades**e, na caixa de diálogo **Propriedades** , clique em **Adicionar sessão**.

8. Na caixa de diálogo **conectar ao destino** , marque a caixa de seleção **Habilitar múltiplos caminhos** e, em seguida, clique em **Avançado**.

9. Na caixa de diálogo **Configurações avançadas** :
    1. Na lista suspensa **adaptador Local** , selecione **Microsoft iSCSI iniciador**.
    2. Na lista suspensa **IP do iniciador** , selecione o endereço IP correspondente a segunda interface do host.
    3. Na lista suspensa **IP do Portal de destino** , selecione o endereço IP para a segunda interface de dados habilitado no dispositivo.
    4. Clique em **Okey** para retornar à caixa de diálogo **Propriedades do iniciador iSCSI** . Agora que você adicionou uma segunda sessão ao destino.

10. Repita as etapas 8 a 10 para adicionar sessões adicionais (caminhos) para o destino. Com duas interfaces do host e dois no dispositivo, você pode adicionar um total de quatro sessões.

11. Depois de adicionar as sessões desejadas (caminhos), na caixa de diálogo **Propriedades do iniciador iSCSI** , selecione o destino e clique em **Propriedades**. Na guia sessões da caixa de diálogo **Propriedades** , observe a sessão quatro identificadores que correspondem aos permutações caminho possível. Para cancelar uma sessão, marque a caixa de seleção ao lado de um identificador de sessão e, em seguida, clique em **Desconectar**.

12. Para exibir os dispositivos apresentados em sessões, selecione a guia **dispositivos** . Para configurar a política MPIO para um dispositivo selecionado, clique em **MPIO**. Caixa de diálogo **Detalhes do dispositivo** será exibida. Na guia **MPIO** , você pode selecionar as configurações de **Diretiva de balanceamento de carga** apropriadas. Você também pode exibir o tipo de caminho **ativo** ou **em espera** .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para modificar a configuração de dispositivo StorSimple](storsimple-modify-device-config.md).
 
