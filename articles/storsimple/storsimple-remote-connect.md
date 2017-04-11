<properties 
   pageTitle="Conectar-se remotamente ao seu dispositivo de StorSimple | Microsoft Azure"
   description="Explica como configurar seu dispositivo para gerenciamento remoto e como se conectar ao Windows PowerShell para StorSimple via HTTP ou HTTPS."
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
   ms.date="06/21/2016"
   ms.author="alkohli" />

# <a name="connect-remotely-to-your-storsimple-device"></a>Conectar-se remotamente ao seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Você pode usar o Windows PowerShell remoto para se conectar ao seu dispositivo StorSimple. Quando você conecta dessa maneira, você não verá um menu. (Você verá um menu apenas se você usar o console serial no dispositivo para conectar-se.) Comunicação remota do Windows PowerShell, você se conectar a um espaço de execução específico. Você também pode especificar o idioma de exibição. 

Para obter mais informações sobre como usar o Windows PowerShell remoto para gerenciar seu dispositivo, vá para [Usar o Windows PowerShell para StorSimple administrar seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).

Este tutorial explica como configurar seu dispositivo para gerenciamento remoto e para se conectar ao Windows PowerShell para StorSimple. Você pode usar HTTP ou HTTPS para conectar-se através do Windows PowerShell remoto. No entanto, ao decidir como se conectar ao Windows PowerShell para StorSimple, considere o seguinte: 

- Conectar-se diretamente para o console serial do dispositivo é seguro, mas se conectando ao console serial sobre opções de rede não está. Tenha cuidado do risco de segurança ao conectar-se ao console serial dispositivo sobre opções de rede. 

- Conectando através de uma sessão HTTP pode oferecer mais segurança do que se conectando através do console serial através da rede. Embora não seja o método mais seguro, é aceitável em redes confiáveis. 

- Conectando através de uma sessão HTTPS com um certificado auto-assinado é o mais seguro e a opção recomendada.

Você pode se conectar remotamente para a interface do Windows PowerShell. No entanto, o acesso remoto para o seu dispositivo de StorSimple por meio da interface do Windows PowerShell não está habilitado por padrão. Você precisa habilitar o gerenciamento remoto do dispositivo primeiro e, em seguida, no cliente que é usado para acessar o seu dispositivo.

As etapas descritas neste artigo foram realizadas em um sistema de host executando o Windows Server 2012 R2.

## <a name="connect-through-http"></a>Conectar-se através de HTTP

Conectando ao Windows PowerShell para StorSimple por meio de uma sessão HTTP oferece mais segurança que conectando através do console serial do seu dispositivo de StorSimple. Embora não seja o método mais seguro, é aceitável em redes confiáveis.

Você pode usar o Azure portal clássico ou o console serial para configurar o gerenciamento remoto. Selecione os procedimentos a seguir:

- [Usar o portal de clássico Azure para habilitar o gerenciamento remoto sobre HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Usar o console serial para habilitar o gerenciamento remoto sobre HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Depois de habilitar gerenciamento remoto, use o procedimento a seguir para preparar o cliente para uma conexão remota.

- [Preparar o cliente para conexão remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Usar o portal de clássico Azure para habilitar o gerenciamento remoto sobre HTTP 

Execute as seguintes etapas no portal de clássico do Azure para habilitar o gerenciamento remoto sobre HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Para habilitar o gerenciamento remoto por meio do portal de clássico Azure

1. Acessar **dispositivos** > **Configurar** para o seu dispositivo.

2. Role até a seção de **Gerenciamento remoto** .

3. Definir a **Habilitar o gerenciamento remoto** para **Sim**.

4. Agora, você pode optar por conectar usando HTTP. (O padrão é conectar-se por HTTPS.) Certifique-se de que o HTTP está selecionada.

    >[AZURE.NOTE] Conectando através de HTTP é aceitável somente em redes confiáveis.

6. Clique em **Salvar** na parte inferior da página.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Usar o console serial para habilitar o gerenciamento remoto sobre HTTP

Execute as seguintes etapas no console serial do dispositivo para habilitar o gerenciamento remoto.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar o gerenciamento remoto através do console do dispositivo serial

1. No menu console serial, selecione a opção 1. Para obter mais informações sobre como usar o console serial no dispositivo, vá para [conectar ao Windows PowerShell para StorSimple via console serial do dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. No prompt, digite:`Enable-HcsRemoteManagement –AllowHttp`

3. Você será notificado sobre as vulnerabilidades de segurança do uso de HTTP para se conectar ao dispositivo. Quando solicitado, confirme digitando **Y**.

4. Verificar se HTTP está habilitado, digitando:`Get-HcsSystem`

5. Verifique se o campo **RemoteManagementMode** mostra **HttpsAndHttpEnabled**. A ilustração a seguir mostra essas configurações no acabamento.

     ![Serial HTTPS e HTTP habilitado](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar o cliente para conexão remota

Execute as seguintes etapas no cliente para habilitar o gerenciamento remoto.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar o cliente para conexão remota

1. Inicie uma sessão do Windows PowerShell como administrador.

2. Digite o seguinte comando para adicionar o endereço IP do dispositivo StorSimple à lista de hosts confiáveis do cliente: 

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Substitua <*device_ip*> com o endereço IP do seu dispositivo; Por exemplo: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Digite o seguinte comando para salvar as credenciais de dispositivo em uma variável: 

     *$cred = get-Credential*

4. Na caixa de diálogo que é exibida:

    1. Digite o nome de usuário neste formato: *device_ip\SSAdmin*.
    2. Digite a senha de administrador do dispositivo foi definida quando o dispositivo foi configurado com o Assistente de configuração. A senha padrão é *Senha1*.

7. Inicie uma sessão do Windows PowerShell no dispositivo digitando o seguinte comando:

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Para criar uma sessão do Windows PowerShell para uso com o dispositivo virtual StorSimple, acrescentar a `–Port` parâmetro e especifique a porta pública que você configurou no remota para dispositivo Virtual StorSimple.

     Neste ponto, você deve ter uma sessão do Windows PowerShell remota ativa no dispositivo.

    ![Conexão remota do PowerShell usando HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Conectar-se através de HTTPS

Conectando ao Windows PowerShell para StorSimple por meio de uma sessão HTTPS é o método mais seguro e recomendado de se conectar remotamente ao seu dispositivo de StorSimple do Microsoft Azure. Os procedimentos a seguir explicam como configurar os computadores cliente e console seriais para que você possa usar HTTPS para se conectar ao Windows PowerShell para StorSimple.

Você pode usar o Azure portal clássico ou o console serial para configurar o gerenciamento remoto. Selecione os procedimentos a seguir:

- [Usar o portal de clássico Azure para habilitar o gerenciamento remoto em HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Usar o console serial para habilitar o gerenciamento remoto em HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Depois de habilitar gerenciamento remoto, use os procedimentos a seguir para preparar o host para um gerenciamento remoto e conectar ao dispositivo do host remoto.

- [Preparar o host para gerenciamento remoto](#prepare-the-host-for-remote-management)

- [Conectar ao dispositivo do host remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Usar o portal de clássico Azure para habilitar o gerenciamento remoto em HTTPS

Execute as seguintes etapas no portal de clássico do Azure para habilitar o gerenciamento remoto em HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Para habilitar o gerenciamento remoto em HTTPS do portal clássico do Azure

1. Acessar **dispositivos** > **Configurar** para o seu dispositivo.

2. Role até a seção de **Gerenciamento remoto** .

3. Definir a **Habilitar o gerenciamento remoto** para **Sim**.

4. Agora, você pode optar por se conectar usando HTTPS. (O padrão é conectar-se por HTTPS.) Certifique-se de que o HTTPS estiver selecionado. 

5. Clique em **baixar o certificado de gerenciamento remoto**. Especifique um local para salvar o arquivo. Você precisará instalar este certificado no computador cliente ou host que você usará para se conectar ao dispositivo.

6. Clique em **Salvar** na parte inferior da página.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Usar o console serial para habilitar o gerenciamento remoto em HTTPS

Execute as seguintes etapas no console serial do dispositivo para habilitar o gerenciamento remoto.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar o gerenciamento remoto através do console do dispositivo serial

1. No menu console serial, selecione a opção 1. Para obter mais informações sobre como usar o console serial no dispositivo, vá para [conectar ao Windows PowerShell para StorSimple via console serial do dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. No prompt, digite: 

     `Enable-HcsRemoteManagement`

    Isso deve ativar HTTPS em seu dispositivo.

3. Verifique se que HTTPS foi ativado digitando: 

     `Get-HcsSystem`

    Certifique-se de que o campo **RemoteManagementMode** mostra **HttpsEnabled**. A ilustração a seguir mostra essas configurações no acabamento.

     ![HTTPS serial habilitado](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. Da saída de `Get-HcsSystem`, copie o número de série do dispositivo e salvá-lo para uso posterior.

    >[AZURE.NOTE] O número de série de mapas para o nome CN no certificado.

5. Obter um certificado de gerenciamento remoto digitando: 
 
     `Get-HcsRemoteManagementCert`

    Um certificado semelhante à seguinte será exibida.

    ![Obter certificado de gerenciamento remoto](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Copie as informações no certificado de **---comece certificado---** **---** finalizar certificado---um editor de texto como bloco de notas e salvá-lo como um arquivo. cer. (Você copiará esse arquivo em seu host remoto ao preparar o host.)

    >[AZURE.NOTE] Para gerar um novo certificado, use o `Set-HcsRemoteManagementCert` cmdlet.

### <a name="prepare-the-host-for-remote-management"></a>Preparar o host para gerenciamento remoto

Para preparar o computador host para uma conexão remoto que usa uma sessão HTTPS, execute os procedimentos a seguir:

- [Importar o arquivo. cer na loja de raiz do cliente ou do host remoto](#to-import-the-certificate-on-the-remote-host).

- [Adicione os números de série do dispositivo para o arquivo de hosts em seu host remoto](#to-add-device-serial-numbers-to-the-remote-host).

Cada um desses procedimentos é descrita abaixo.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar o certificado no host remoto

1. Clique com botão direito no arquivo. cer e selecione **Instalar certificado**. Isso iniciará o Assistente de importação de certificado.

    ![Assistente de importação de certificado 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. Para o **local de armazenamento**, selecione **Computador Local**e clique em **Avançar**.

3. Selecione **colocar todos os certificados no repositório a seguir**e clique em **Procurar**. Navegue para o armazenamento de raiz do seu host remoto e clique em **Avançar**.

    ![Assistente de importação de certificado 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Clique em **Concluir**. Aparece uma mensagem que informa que a importação foi bem-sucedido.

    ![Assistente de importação de certificado 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para adicionar números de série do dispositivo para o host remoto

1. Iniciar o bloco de notas como administrador e abra o arquivo de hosts localizado em \Windows\System32\Drivers\etc.

2. Adicione as seguintes entradas de três para o seu arquivo de hosts: **endereço IP de dados 0**, o **endereço IP fixo controlador 0**e o **endereço IP fixo do controlador de 1**.

3. Insira o número de série do dispositivo que você salvou anteriormente. Mapear para o endereço IP conforme mostrado na imagem a seguir. Para controlador 0 e 1 de controlador, acrescente **Controller0** e **controlador1** no final do número de série (nome CN).

    ![Adicionando nome CN ao arquivo hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Salve o arquivo de hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Conectar ao dispositivo do host remoto

Use o Windows PowerShell e SSL para inserir uma sessão de SSAdmin em seu dispositivo um cliente ou host remoto. A sessão SSAdmin mapeia a opção 1 no menu [console serial](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) do seu dispositivo.

Execute o procedimento a seguir no computador do qual você deseja fazer a conexão do Windows PowerShell remoto.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para inserir uma sessão de SSAdmin no dispositivo usando o Windows PowerShell e SSL

1. Inicie uma sessão do Windows PowerShell como administrador.

2. Adicione o endereço IP do dispositivo para hosts confiáveis do cliente digitando:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Onde <*device_ip*> é o endereço IP do seu dispositivo; Por exemplo: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Crie uma nova credencial digitando: 

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    Onde <*IP do dispositivo de destino*> é o endereço IP de dados de 0 para seu dispositivo; Por exemplo, **10.126.173.90** conforme mostrado na imagem anterior do arquivo hosts. Além disso, fornece a senha de administrador para o seu dispositivo.

4. Crie uma sessão digitando:

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    Para o parâmetro - ComputerName o cmdlet, fornece o <*número de série do dispositivo de destino*>. Este número de série foi mapeado para o endereço IP do dados 0 no arquivo hosts em seu host remoto; Por exemplo, **SHX0991003G44MT** conforme mostrado na imagem a seguir.

5. Tipo: 

     `Enter-PSSession $session`

6. Você precisará Aguarde alguns minutos e, em seguida, você será conectado ao seu dispositivo via HTTPS sobre SSL. Você verá uma mensagem que indica que você está conectado ao seu dispositivo.

    ![Conexão remota do PowerShell usando HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como [usar o Windows PowerShell para administrar seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).

- Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
