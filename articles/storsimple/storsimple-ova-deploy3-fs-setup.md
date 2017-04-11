<properties
   pageTitle="Implantar StorSimple Virtual Array 3: configurar o dispositivo virtual como servidor de arquivos"
   description="Este tutorial terceiro na implantação de Array Virtual StorSimple instrui você a configurar um dispositivo virtual como servidor de arquivos."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Implantar StorSimple Virtual matriz - conjunto para cima como servidor de arquivos

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introdução 

Este artigo se aplica à versão do Microsoft Azure StorSimple Array Virtual (também conhecido como o dispositivo virtual do StorSimple local ou um dispositivo virtual StorSimple) em execução março de 2016 disponibilidade geral (GA). Este artigo descreve como executar a configuração inicial, registrar seu servidor de arquivos StorSimple, concluir a configuração de dispositivo e criar e se conectar a compartilhamentos SMB. Este é o último artigo da série de tutoriais de implantação necessárias para implantar completamente sua matriz virtual como um servidor de arquivos ou um servidor de iSCSI.

O processo de instalação e configuração pode levar cerca de 10 minutos para ser concluída.


## <a name="setup-prerequisites"></a>Pré-requisitos de instalação

Antes de configurar e configurar o dispositivo virtual StorSimple, verifique se:

-   Você tiver configurado um dispositivo virtual e conectado a ela conforme detalhado na [provisionar uma matriz Virtual StorSimple no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [provisionar uma matriz de Virtual StorSimple em VMware](storsimple-ova-deploy2-provision-vmware.md).

-   Você tem a chave do registro de serviço do serviço do Gerenciador de StorSimple que você criou para gerenciar dispositivos virtuais StorSimple. Para obter mais informações, consulte [etapa 2: obter a chave do registro de serviço](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para StorSimple Virtual matriz.

-   Se esta for o segundo ou subsequente virtual dispositivo que você está registrando com um serviço de Gerenciador de StorSimple existente, você deve ter a chave de criptografia de dados de serviço. Esta chave gerada quando o primeiro dispositivo foi registrado com êxito com esse serviço. Se você tiver perdido essa chave, consulte [obter a chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) para seu Array Virtual StorSimple.

## <a name="step-by-step-setup"></a>Instalação passo a passo

Use as seguintes instruções passo a passo para configurar seu dispositivo virtual StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Etapa 1: Concluir a configuração de interface do usuário do local da web e registrar seu dispositivo 


#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registrar o dispositivo

1.  Abra uma janela do navegador e conecte-se para o local da interface do usuário da web. Tipo: 

    `https://<ip-address of network interface>`

    Use a URL de conexão indicada na etapa anterior. Você verá um erro indicando que há um problema com o certificado de segurança do site. Clique em **Continuar para esta página da Web**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Entrar na interface do usuário do seu dispositivo virtual Web como **StorSimpleAdmin**. Digite a senha de administrador do dispositivo que você alterou na etapa 3: Inicie o dispositivo virtual em [provisionar uma matriz Virtual StorSimple no Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou em [uma matriz de Virtual StorSimple em VMware provisionar](storsimple-ova-deploy2-provision-vmware.md).

    ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Você será levado para a página **Home** . Esta página descreve as diversas configurações necessárias para configurar e registrar o dispositivo virtual com o serviço do Gerenciador de StorSimple. Observe que as **configurações de rede**, **configurações de proxy da Web**e **configurações de tempo** são opcionais. As configurações necessárias apenas são **as configurações de dispositivo** e de **nuvem**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  Na página **configurações de rede** em **interfaces de rede**, dados 0 serão automaticamente configurados para você. Por padrão, cada interface de rede está definido para obter o endereço IP automaticamente (DHCP). Portanto, um endereço IP, sub-rede e gateway serão automaticamente atribuídas (para IPv4 e IPv6).

    ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

    Se você tiver adicionado mais de uma interface de rede durante o provisionamento do dispositivo, você pode configurá-los aqui. Observe que você pode configurar a interface de rede como IPv4 somente ou como IPv4 e IPv6. IPv6 somente configurações não são suportadas.

1.  Servidores DNS são necessários porque eles são usados quando seu dispositivo tenta se comunicar com seu provedores de serviço de armazenamento de nuvem ou para resolver o seu dispositivo por nome quando configurado como um servidor de arquivos. Na página de **configurações de rede** em **servidores DNS**:

    1.  Um servidor DNS primário e secundário será configurado automaticamente. Se você optar por configurar endereços IP estáticos, você pode especificar os servidores DNS. Para alta disponibilidade, recomendamos que você configure um primário e um servidor DNS secundário.

    2.  Clique em **Aplicar**. Isso aplicará e valide as configurações de rede.

2.  Na página de **configurações do dispositivo** :

    1.  Atribua um **nome** de exclusivo ao seu dispositivo. Esse nome pode ser 1-15 caracteres e pode conter letra, números e hifens.

    2.  Clique no ícone de **servidor de arquivos** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) para o **tipo** de dispositivo que você está criando. Um servidor de arquivos permitirá que você criar pastas compartilhadas.

    3.  Como o seu dispositivo é um servidor de arquivos, você precisará ingressar no dispositivo em um domínio. Insira um **nome de domínio**.

    1.  Clique em **Aplicar**.

2.  Uma caixa de diálogo aparecerá. Insira suas credenciais de domínio no formato especificado. Clique no ícone de seleção. As credenciais de domínio serão verificadas. Você verá uma mensagem de erro se as credenciais estão incorretas.

    ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Clique em **Aplicar**. Isso aplicará e valide as configurações do dispositivo.

    ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

    > [AZURE.NOTE]
    > 
    > Certifique-se de que seu array virtual está em sua própria unidade organizacional (OU) para o Active Directory e sem objetos de política de grupo (GPO) são aplicados a ele ou herdados. Política de grupo pode instalar aplicativos como o software antivírus no Array Virtual StorSimple. Instalar software adicional não é suportado e pode levar à corrupção de dados. 

1.  (Opcionalmente) configure seu servidor de proxy da web. Embora a configuração de proxy web seja opcional, lembre-se de que se você usar um proxy da web, você só pode configurá-lo aqui.

    ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

    Na página de **proxy da Web** :

    1.  Fornecer a **URL de proxy Web** neste formato: *http://&lt;host endereço IP ou FDQN&gt;: número da porta*. Observe que as URLs HTTPS não são suportados.

    2.  Especifica a **autenticação** como **básico** ou **Nenhum**.

    3.  Se usando autenticação, também precisará fornecer um **nome de usuário** e **senha**.

    4.  Clique em **Aplicar**. Isso validar e aplicar as configurações de proxy web configurado.

1.  (Opcionalmente) defina as configurações de tempo para o seu dispositivo, como o fuso horário e os servidores NTP primário e secundários. Servidores NTP são necessários porque seu dispositivo deve sincronizar tempo para que ele possa autenticar com seus provedores de serviço de nuvem.

    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

    Na página de **configurações de tempo** :

    1.  Na lista suspensa, selecione o **fuso horário** com base na localização geográfica na qual o dispositivo está sendo implantado. O fuso horário padrão para o seu dispositivo está PST. Seu dispositivo usará este fuso horário para todas as operações agendadas.

    2.  Especificar um **servidor NTP principal** para o seu dispositivo ou aceite o valor padrão de time.windows.com. Certifique-se de que sua rede permite o tráfego NTP passar do data center à Internet.

    3.  Opcionalmente, especifique um **servidor NTP secundário** para seu dispositivo.

    4.  Clique em **Aplicar**. Isso validar e aplicar as configurações de tempo configurado.

1.  Defina as configurações de nuvem para o seu dispositivo. Nesta etapa, você irá concluir a configuração de dispositivo local e, em seguida, registrar o dispositivo com o seu serviço de Gerenciador de StorSimple.

    1.  Insira a **chave do registro de serviço** obtido em [etapa 2: obter a chave do registro de serviço](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para StorSimple Virtual matriz.

    2.  Ignorar esta etapa se esta for sua primeiro dispositivo Registrando com este serviço e vá para a próxima etapa. Se esse não for o primeiro dispositivo que você está registrando com esse serviço, você precisará fornecer a **chave de criptografia de dados de serviço**. Esta chave é necessária para a chave do registro de serviço para registrar dispositivos adicionais com o serviço Gerenciador de StorSimple. Para obter mais informações, consulte para obter a [chave de criptografia de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) em sua web local da interface do usuário.

    3.  Clique em **registrar**. Isto irá reiniciar o dispositivo. Talvez você precise esperar por 2 a 3 minutos antes do dispositivo é registrado com êxito. Depois de reiniciar o dispositivo, você será levado para a página de entrada.

        ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
    

1.  Volte para o portal de clássico Azure. Na página **dispositivos** , verifique se o dispositivo com êxito tem conectado ao serviço pesquisando o status. O status do dispositivo deve estar **ativo**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Etapa 2: Concluir a configuração de dispositivo necessário

Para concluir a configuração de dispositivo do seu dispositivo de StorSimple, você precisa:

-   Selecione uma conta de armazenamento para associar seu dispositivo.

-   Escolha configurações de criptografia para os dados que são enviadas para a nuvem.

Execute as seguintes etapas no [Azure portal clássico](https://manage.windowsazure.com/) para concluir a configuração de dispositivo necessário.

#### <a name="to-complete-the-minimum-device-setup"></a>Para concluir a configuração de dispositivo mínimos

1.  Na página **dispositivos** , selecione o dispositivo que você acabou de criar. Este dispositivo aparecerão como **ativo**. Clique na seta contra o nome do dispositivo e clique em **Início rápido**.

2.  Clique em **configuração do dispositivo concluída** para iniciar o Assistente de dispositivo de configurar.

3.  No Assistente de dispositivo de configurar, na página **Configurações básicas** , faça o seguinte:

    1.  Especifique uma conta de armazenamento a ser usado com seu dispositivo. Você pode selecionar uma conta existente do armazenamento nesta assinatura na lista suspensa ou especificar **Adicionar mais** para escolher uma conta de uma assinatura diferente.

    2.  Defina as configurações de criptografia para todos os dados inativos (criptografia AES) que serão enviadas para a nuvem. Para criptografar seus dados, marque a caixa de combinação para **Habilitar a chave de criptografia de armazenamento de nuvem**. Insira uma criptografia de armazenamento de nuvem que contenha 32 caracteres. Redigite a chave para confirmá-la. Uma chave AES de 256 bits será usada com a chave definidas pelo usuário para criptografia.

    3.  Clique no ícone de seleção ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

        ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

As configurações serão atualizadas agora. Depois que as configurações forem atualizadas com êxito, o botão de configuração de dispositivo concluída estará esmaecido. Você voltará para a página de **Início rápido** do dispositivo.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> Você pode modificar todas as outras configurações de dispositivo a qualquer momento acessando a página de **Configurar** .

## <a name="step-3-add-a-share"></a>Etapa 3: Adicionar um compartilhamento

Execute as seguintes etapas do [portal clássico Azure](https://manage.windowsazure.com/) para criar um compartilhamento.

#### <a name="to-create-a-share"></a>Para criar um compartilhamento

1.  Na página de **Início rápido** do dispositivo, clique em **Adicionar um compartilhamento**. Isso inicia o Assistente adicionar um compartilhamento.

    ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  Na página **Configurações básicas** , faça o seguinte:

    1.  Especifique um nome exclusivo para o compartilhamento. O nome deve ser uma cadeia de caracteres que contém caracteres de 3 a 127.

    2.  (Opcional) Forneça uma descrição para o compartilhamento. A descrição ajudará a identificar os proprietários de compartilhamento.

    3.  Selecione um tipo de uso para o compartilhamento. O tipo de uso pode ser **hierárquico** ou **localmente fixos**, com hierárquico sendo o padrão. Para cargas de trabalho que exigem garantias locais, menos latências e melhor desempenho, selecione um compartilhamento **fixos localmente** . Para todos os outros dados, selecione um compartilhamento **hierárquico** .

    Um compartilhamento localmente fixado thickly está provisionado e garante que os dados primários no compartilhamento permanece no mesmo locais no dispositivo e não passando para a nuvem. Por outro lado um compartilhamento hierárquico thin está provisionado. Quando você cria um compartilhamento hierárquico, 10% do espaço está provisionado no nível local e 90% do espaço está provisionado na nuvem. Por exemplo, se você tiver configurado um volume de 1 TB, 100 GB reside no espaço local e 900 GB seria usado na nuvem quando os níveis de dados. Por sua vez, isso significa que, se você executar fora todo o espaço local no dispositivo, você não pode provisionar um compartilhamento hierárquico.

1.  Especifica a capacidade provisionada para o compartilhamento. Observe que a capacidade especificada deve ser menor do que a capacidade disponível. Se usando um compartilhamento hierárquico, o tamanho de compartilhamento deve estar entre 500 GB e 20 TB. Para um compartilhamento localmente fixado, especifique um tamanho de compartilhamento entre 50 GB e 2 TB. Use a capacidade disponível como um guia para provisionar um compartilhamento. Se a capacidade de local disponível for 0 GB, então você não poderá provisionar compartilhamentos locais ou hierárquicos.

    ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Clique no ícone de seta ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) para ir para a próxima página.

1.  Na página **Configurações adicionais** , atribua as permissões para o usuário ou grupo que acessarão este compartilhamento. Especifique o nome do usuário ou grupo de usuários no *<john@contoso.com>* formato. Recomendamos que você use um grupo de usuários (em vez de um único usuário) para permitir que os privilégios de administrador acessar esses compartilhamentos. Após ter atribuído as permissões aqui, você pode usar, em seguida, o Windows Explorer para modificar essas permissões.

    ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Clique no ícone de seleção ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Um compartilhamento será criado com as configurações especificadas. Por padrão, monitoramento e backup serão habilitados para o compartilhamento.

## <a name="step-4-connect-to-the-share"></a>Etapa 4: Conectar ao compartilhamento

Agora, você precisará se conectar ao compartilhamento (s) que você criou na etapa anterior. Execute estas etapas em seu host do Windows Server.

#### <a name="to-connect-to-the-share"></a>Para se conectar ao compartilhamento

1.  Pressione ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + r. Na janela Executar, especifique o * \\ * como o caminho, substituindo o *nome do servidor de arquivo* com o nome do dispositivo que você atribuiu ao seu servidor de arquivos. Clique em **Okey**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  Isso abrirá o Explorer. Você agora deve ser capaz de ver as ações que você criou como pastas. Selecione e clique duas vezes em um compartilhamento (pasta) para exibir o conteúdo.

    ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  Agora você pode adicionar arquivos a esses compartilhamentos e fazer um backup.

![ícone de vídeo](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **vídeo disponível**

Assista ao vídeo para ver como você pode configurar e registrar uma matriz Virtual StorSimple como um servidor de arquivos.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## <a name="next-steps"></a>Próximas etapas

Saiba como usar o local da web da interface do usuário para [administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).
