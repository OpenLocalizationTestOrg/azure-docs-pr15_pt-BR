<properties 
   pageTitle="Configuração de servidor de iSCSI Array Virtual StorSimple | Microsoft Azure"
   description="Descreve como realizar a configuração inicial, registrar seu servidor de iSCSI StorSimple e concluir a configuração do dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/18/2016"
   ms.author="alkohli" />


# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Implantar StorSimple Virtual Array – configurar seu dispositivo virtual como um servidor de iSCSI

![fluxo de processo de configuração de iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Visão geral

Este tutorial de implantação aplica-se para a versão de disponibilidade geral (GA) de março de 2016 em execução do Microsoft Azure StorSimple Array Virtual (também conhecido como o dispositivo virtual do local de StorSimple ou o dispositivo virtual StorSimple). Este tutorial descreve como executar a configuração inicial, registrar seu servidor de iSCSI StorSimple, concluir a configuração de dispositivo e, em seguida, criar, montar, inicializar e formatar volumes no servidor StorSimple dispositivo virtual iSCSI. As informações de instalação StorSimple neste artigo se aplica apenas ao StorSimple Virtual matrizes. 

Os procedimentos descritos aqui levar aproximadamente 30 minutos para 1 hora para ser concluída. As informações publicadas neste artigo se aplica somente a StorSimple Virtual matrizes.

## <a name="setup-prerequisites"></a>Pré-requisitos de instalação

Antes de configurar e configurar o dispositivo virtual StorSimple, verifique se:

- Você tiver configurado um dispositivo virtual e conectados a ele, conforme descrito em [Implantar StorSimple Virtual matriz - provisionar uma matriz virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implantar StorSimple Array Virtual - provisionar uma matriz virtual em VMware](storsimple-ova-deploy2-provision-vmware.md).

- Você tem a chave do registro de serviço do serviço do Gerenciador de StorSimple que você criou para gerenciar dispositivos virtuais StorSimple. Para obter mais informações, consulte **etapa 2: obter a chave do registro de serviço** em [Implantar StorSimple Virtual matriz - preparar o portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Se esta for o segundo ou subsequente virtual dispositivo que você está registrando com um serviço de Gerenciador de StorSimple existente, você deve ter a chave de criptografia de dados de serviço. Esta chave gerada quando o primeiro dispositivo foi registrado com êxito com esse serviço. Se você tiver perdido essa chave, consulte **obter a chave de criptografia de dados de serviço** em [uso a IU da Web para administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Instalação passo a passo 

Use as seguintes instruções passo a passo para configurar seu dispositivo virtual StorSimple:

-  [Etapa 1: Concluir a configuração de interface do usuário do local da web e registrar seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Etapa 2: Concluir a configuração de dispositivo necessário](#step-2-complete-the-required-device-setup)
-  [Etapa 3: Adicionar um volume](#step-3-add-a-volume)
-  [Etapa 4: Montar, inicializar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Etapa 1: Concluir a configuração de interface do usuário do local da web e registrar seu dispositivo 

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registrar o dispositivo

1. Abra uma janela do navegador e se conectar à web UI, digitando:

    `https://<ip-address of network interface>`

    Use a URL de conexão indicada na etapa anterior. Você verá um erro informando que não há um problema com o certificado de segurança do site. Clique em **Continuar para esta página da web**.

    ![Erro de certificado de segurança](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Entrar na interface do usuário do seu dispositivo virtual Web como **StorSimpleAdmin**. Digite a senha de administrador do dispositivo que você alterou na etapa 3: Inicie o dispositivo virtual em [Implantar StorSimple Virtual matriz - provisionar um dispositivo virtual no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implantar StorSimple Array Virtual - provisionar um dispositivo virtual em VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Página de entrada](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Você será levado para a página **Home** . Esta página descreve as diversas configurações necessárias para configurar e registrar o dispositivo virtual com o serviço do Gerenciador de StorSimple. Observe que as **configurações de rede**, **configurações de proxy da Web**e **configurações de tempo** são opcionais. As configurações necessárias apenas são **as configurações de dispositivo** e de **nuvem**.

    ![Página inicial](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. Na página **configurações de rede** em **interfaces de rede**, dados 0 serão automaticamente configurados para você. Por padrão, a cada interface de rede está configurado para obter um endereço IP automaticamente (DHCP). Portanto, um endereço IP, sub-rede e gateway serão automaticamente atribuídas (para IPv4 e IPv6).

    Como você planeja implantar seu dispositivo como um servidor de iSCSI (para provisionar o armazenamento de bloco), recomendamos que você desative a opção de **endereço IP obter automaticamente** e configurar endereços IP estáticos.

    ![Página de configurações de rede](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Se você tiver adicionado mais de uma interface de rede durante o provisionamento do dispositivo, você pode configurá-los aqui. Observe que você pode configurar a interface de rede como IPv4 somente ou como IPv4 e IPv6. IPv6 somente configurações não são suportadas.

5. Servidores DNS são necessários, pois elas são usadas quando seu dispositivo tenta se comunicar com seu provedores de serviço de armazenamento de nuvem ou para resolver o seu dispositivo por nome se ele estiver configurado como um servidor de arquivos. Na página **configurações de rede** em **servidores DNS**:

    1. Um servidor DNS primário e secundário será configurado automaticamente. Se você optar por configurar endereços IP estáticos, você pode especificar os servidores DNS. Para alta disponibilidade, recomendamos que você configure um primário e um servidor DNS secundário.

    2. Clique em **Aplicar**. Isso aplicará e valide as configurações de rede.

6. Na página **configurações do dispositivo** :

    1. Atribua um **nome** de exclusivo ao seu dispositivo. Esse nome pode ser 1-15 caracteres e pode conter letra, números e hifens.

    2. Clique no ícone de **servidor iSCSI** ![ícone do servidor iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) para o **tipo** de dispositivo que você está criando. Um servidor iSCSI permitirá que você provisionamento de armazenamento de bloco.

    3. Especifique se deseja que esse dispositivo seja domínio. Se seu dispositivo não for um servidor iSCSI, em seguida, ingressar no domínio é opcional. Se você decidir não ingressar no seu servidor iSCSI a um domínio, clique em **Aplicar**, aguarde as configurações para ser aplicado e, em seguida, pule para a próxima etapa.

        Se você quiser participar do dispositivo em um domínio. Insira um **nome de domínio**e clique em **Aplicar**.

        > [AZURE.NOTE] Se ingressando em seu servidor iSCSI a um domínio, certifique-se de que seu array virtual está em sua própria unidade organizacional (OU) para Microsoft Azure Active Directory e sem objetos de política de grupo (GPO) são aplicados a ele.

    5. Uma caixa de diálogo aparecerá. Insira suas credenciais de domínio no formato especificado. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). As credenciais de domínio serão verificadas. Você verá uma mensagem de erro se as credenciais estão incorretas.

        ![credenciais](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Clique em **Aplicar**. Isso aplicará e valide as configurações do dispositivo.
 
7. (Opcionalmente) configure seu servidor de proxy da web. Embora a configuração de proxy web seja opcional, lembre-se de que se você usar um proxy da web, você só pode configurá-lo aqui.

    ![Configurar o proxy da web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    Na página **da Web proxy** :

    1. Fornecer a **URL de proxy Web** neste formato: *http://host-IP endereço* ou *número de FDQN:Port*. Observe que as URLs HTTPS não são suportados.

    2. Especifica a **autenticação** como **básico** ou **Nenhum**.

    3. Se você estiver usando autenticação, também precisará fornecer um **nome de usuário** e **senha**.

    4. Clique em **Aplicar**. Isso validar e aplicar as configurações de proxy web configurado.
 
8. (Opcionalmente) defina as configurações de tempo para o seu dispositivo, como o fuso horário e os servidores NTP primário e secundários. Servidores NTP são necessários porque seu dispositivo deve sincronizar tempo para que ele possa autenticar com seus provedores de serviço de nuvem.

    ![Configurações de tempo](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    Na página **configurações de tempo** :

    1. Na lista suspensa, selecione o **fuso horário** com base na localização geográfica na qual o dispositivo está sendo implantado. O fuso horário padrão para o seu dispositivo está PST. Seu dispositivo usará este fuso horário para todas as operações agendadas.

    2. Especificar um **servidor NTP principal** para o seu dispositivo ou aceite o valor padrão de time.windows.com. Certifique-se de que sua rede permite o tráfego NTP passar do data center à Internet.

    3. Opcionalmente, especifique um **servidor NTP secundário** para seu dispositivo.

    4. Clique em **Aplicar**. Isso validar e aplicar as configurações de tempo configurado.

9. Defina as configurações de nuvem para o seu dispositivo. Nesta etapa, você irá concluir a configuração de dispositivo local e, em seguida, registrar o dispositivo com o seu serviço de Gerenciador de StorSimple.

    1. Insira a **chave do registro de serviço** obtido em **etapa 2: obter a chave do registro de serviço** em [Implantar StorSimple Virtual matriz - preparar o Portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. Se esse não for o primeiro dispositivo que você está registrando com esse serviço, você precisará fornecer a **chave de criptografia de dados de serviço**. Esta chave é necessária para a chave do registro de serviço para registrar dispositivos adicionais com o serviço Gerenciador de StorSimple. Para obter mais informações, consulte [obter a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) em sua web local da interface do usuário.

    3. Clique em **registrar**. Isto irá reiniciar o dispositivo. Talvez você precise esperar por 2 a 3 minutos antes do dispositivo é registrado com êxito. Depois de reiniciar o dispositivo, você será levado para a página de entrada.

       ![Registrar o dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Volte para o portal de clássico Azure. Na página **dispositivos** , verifique se o dispositivo com êxito tem conectado ao serviço pesquisando o status. O status do dispositivo deve estar **ativo**.

    ![Página de dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Etapa 2: Concluir a configuração de dispositivo necessário

Para concluir a configuração de dispositivo do seu dispositivo de StorSimple, você precisa:

- Selecione uma conta de armazenamento para associar seu dispositivo.

- Escolha configurações de criptografia para os dados que são enviadas para a nuvem.

Execute as seguintes etapas no portal de clássico do Azure para concluir a configuração de dispositivo necessário.

#### <a name="to-complete-the-minimum-device-setup"></a>Para concluir a configuração de dispositivo mínimos

1. Na página **dispositivos** , selecione o dispositivo que você acabou de criar. Este dispositivo aparecerão como **ativo**. Clique na seta próxima o nome do dispositivo e clique em **Início rápido**.

    ![Página de dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Clique em **configuração do dispositivo concluída** para iniciar o Assistente de dispositivo de configurar.

    ![Configurar o Assistente de dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. No Assistente de dispositivo de configurar, na página **Configurações básicas** , faça o seguinte:

   1. Especifique uma conta de armazenamento a ser usado com seu dispositivo. Nesta inscrição, você pode selecionar uma conta de armazenamento existente na lista suspensa, ou você pode especificar **Adicionar mais** para escolher uma conta de uma assinatura diferente.

   2. Defina as configurações de criptografia para todos os dados no restante que será enviada para a nuvem. (StorSimple usa criptografia AES-256.) Para criptografar seus dados, marque a caixa de seleção **Ativar criptografia de armazenamento de nuvem** . Insira uma criptografia de armazenamento de nuvem que contenha 32 caracteres. Redigite a chave para confirmá-la.

   3. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Configurações básicas](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    As configurações serão atualizadas agora. Depois que as configurações forem atualizadas com êxito, o botão de configuração de dispositivo concluída ficarão indisponível. Você voltará para a página de **Início rápido** do dispositivo.                                                        

>[AZURE.NOTE]Você pode modificar todas as outras configurações de dispositivo a qualquer momento acessando a página de **Configurar** .

## <a name="step-3-add-a-volume"></a>Etapa 3: Adicionar um volume

Execute as seguintes etapas no portal de clássico do Azure para criar um volume.

#### <a name="to-create-a-volume"></a>Para criar um volume

1. Na página de **Início rápido** do dispositivo, clique em **Adicionar um volume**. Isso inicia o Assistente adicionar um volume.

2. No Assistente adicionar um volume, em **Configurações básicas**, faça o seguinte:

    1. Forneça um nome exclusivo para o volume. O nome deve ser uma cadeia de caracteres que contém caracteres de 3 a 127.

    2. Forneça uma descrição para o volume. A descrição ajudará a identificar os proprietários de volume.

    3. Selecione um tipo de uso para o volume. O tipo de uso pode ser **hierárquico volume** ou **localmente fixos volume.** (**Hierárquico volume** é o padrão). Para cargas de trabalho que exigem garantias locais, menos latências e desempenho superior, selecione **localmente fixos** **volume**. Para todos os outros dados, selecione **hierárquico** **volume**.

        Um volume localmente fixado thickly está provisionado e garante que os dados primários no volume permanecem no dispositivo e não passando para a nuvem. Se você criar um volume localmente fixado, o dispositivo verificará espaço disponível nos níveis locais para configurar um volume do tamanho solicitado. Criando um volume localmente fixado pode exigir derramada dados existentes do dispositivo para a nuvem e o tempo necessário para criar o volume pode ser longo. O tempo total depende do tamanho do volume provisionado, largura de banda de rede disponível e os dados em seu dispositivo.

        Por outro lado, um volume hierárquico thin está provisionado e pode ser criado rapidamente. Quando você cria um volume hierárquico, aproximadamente 10% do espaço está provisionado no nível local e 90% do espaço está provisionado na nuvem. Por exemplo, se você tiver configurado um volume de 1 TB, 100 GB reside no espaço local e 900 GB seria usado na nuvem quando os níveis de dados. Isso significa alternadamente é que se você ficar sem todo o espaço local no dispositivo, você não pode provisionar um compartilhamento hierárquico (porque os 10% não estarão disponíveis).

    4. Especifica a capacidade provisionada para seu volume. Observe que a capacidade especificada deve ser menor do que a capacidade disponível. Se você estiver criando um volume hierárquico, o tamanho deve estar entre 500 GB e 5 TB. Para um volume localmente fixado, especifique um tamanho de volume entre 50 GB e 500 GB. Use a capacidade disponível como um guia para um volume de provisionamento. Se a capacidade de local disponível for 0 GB, então você não poderá provisionar um localmente fixada ou um volume hierárquico.

        ![Configurações básicas](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Clique no ícone de seta ![ícone de seta](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) para ir para a próxima página.

3. Na página **Configurações adicionais** , adicione um novo registro de controle de acesso (ACR):

    1. Fornece um **nome** para seu ACR.

    2. Em **nome do iniciador iSCSI**, forneça o iSCSI qualificado nome (IQN) do seu host do Windows. Se você não tiver o IQN, acesse [Apêndice r: Get o IQN de um host do Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. Recomendamos que você habilite um backup padrão marcando a caixa de seleção **Habilitar um backup padrão para este volume** . O backup padrão irá criar uma política que executa às 22:30 cada dia (hora do dispositivo) e cria um instantâneo de nuvem do volume.

        ![configurações adicionais](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Clique no ícone de seleção ![Verifique o ícone](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Isso inicia o trabalho de criação de volume. Você verá uma mensagem de progresso semelhante à seguinte.

        ![mensagem de progresso](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Um volume será criado com as configurações especificadas. Por padrão, monitoramento e backup serão habilitados para o volume.

    5. Para confirmar que o volume foi criado com êxito, vá para a página de **Volumes** . Você deve ver o volume listado.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Etapa 4: Montar, inicializar e formatar um volume

Execute as seguintes etapas para montar, inicializar e formatar seus volumes de StorSimple em um host do Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Montar, inicializar e formatar um volume

1. Inicie o iniciador Microsoft iSCSI.

2. Na janela **Propriedades do iniciador iSCSI** , na guia **Descoberta** , clique em **Descubra o Portal**.

    ![Descubra o portal](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Na caixa de diálogo **Descubra o Portal de destino** , forneça o endereço IP da sua interface de rede habilitados para iSCSI e clique em **Okey**.

    ![Endereço IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Na janela **Propriedades do iniciador iSCSI** , na guia **destinos** , localize os **destinos de Discovered**. (Cada volume será um destino descoberto.) O status do dispositivo deve aparecer como **inativo**.

    ![destinos detectados](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Selecione um dispositivo de destino e clique em **Conectar**. Quando o dispositivo estiver conectado, o status deve mudar para **conectado**. (Para obter mais informações sobre como usar o iniciador Microsoft iSCSI, consulte [Instalando e Configurando Microsoft iniciador iSCSI] [1].

    ![Selecione o dispositivo de destino](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. Em seu host do Windows, pressione a tecla de logotipo do Windows + X e, em seguida, clique em **Executar**.

7. Na caixa de diálogo **Executar** , digite **diskmgmt. msc**. Clique em **Okey**, e a caixa de diálogo de **Gerenciamento de disco** aparecerá. O painel direito mostrará os volumes em seu host.

8. Na janela **Gerenciamento de disco** , os volumes montados aparecerá como mostrado na ilustração a seguir. O volume de descobertas de atalho (clique o nome do disco) e, em seguida, clique em **Online**.

    ![gerenciamento de disco](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Clique com botão direito e selecione **Inicializar disco**.

    ![inicializar disco 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Na caixa de diálogo, selecione o s inicializar e clique em **Okey**.

    ![inicializar disco 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. Inicia o Assistente de novo Volume simples. Selecione um tamanho de disco e clique em **Avançar**.

    ![Assistente de novo volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Atribuir uma letra de unidade ao volume e clique em **Avançar**.

    ![Assistente de novo volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Insira os parâmetros para formatar o volume. **No Windows Server, somente NTFS tem suporte.** Defina a Austrália como 64K. Fornece um rótulo para o volume. É uma prática recomendada para esse nome seja idêntico ao nome do volume fornecido em seu dispositivo virtual StorSimple. Clique em **Avançar**.

    ![Assistente de novo volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Verifique os valores para o volume e clique em **Concluir**.

    ![Assistente de novo volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Os volumes aparecerá como **Online** na página de **Gerenciamento de disco** .

    ![volumes on-line](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como usar o local da web da interface do usuário para [administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apêndice a: obtenha o IQN de um host do Windows Server

Execute as seguintes etapas para obter o iSCSI qualificado nome (IQN) de um host do Windows que está executando o Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obter o IQN de um host do Windows

1. Inicie o iniciador Microsoft iSCSI em seu host do Windows.

2. Na janela **Propriedades do iniciador iSCSI** , na guia **configuração** , selecione e copie a cadeia de caracteres do campo **Nome do iniciador** .

    ![Propriedades do iniciador iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Salve essa cadeia de caracteres.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



