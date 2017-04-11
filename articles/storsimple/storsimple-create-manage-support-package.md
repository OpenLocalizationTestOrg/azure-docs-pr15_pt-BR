<properties
   pageTitle="Criar um pacote de suporte StorSimple | Microsoft Azure"
   description="Aprenda a criar, descriptografar e editar um pacote de suporte para o seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />


# <a name="create-and-manage-a-storsimple-support-package"></a>Criar e gerenciar um pacote de suporte StorSimple

## <a name="overview"></a>Visão geral

Um pacote de suporte StorSimple é um mecanismo de fácil de usar que reúne todos os logs relevantes para ajudar o Microsoft Support para solucionar qualquer problema de dispositivo StorSimple. Os logs coletados são criptografados e compactados.

Este tutorial inclui instruções passo a passo para criar e gerenciar o pacote de suporte.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Criar e carregar um pacote de suporte no portal de clássico do Azure

Você pode criar e carregar um pacote de suporte para o site Microsoft Support através da página de **manutenção** do serviço no portal de clássico do Azure.

> [AZURE.NOTE] O carregamento requer uma chave de acesso de suporte. Sua engenharia de suporte deve fornecer isso para você em um email.

Um pacote de suporte criptografados e compactados (arquivo. cab) é criado e carregado no site de suporte. A suporte de engenharia pode recuperar esse pacote do site do suporte para solucionar o problema.

Execute as seguintes etapas no portal do clássico para criar um pacote de suporte.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Para criar um pacote de suporte no portal de clássico do Azure

1. Selecione **dispositivos** > **manutenção**.

2. Na seção de **suporte a pacote** , selecione **criar e carregar pacote de suporte**.

3. Na caixa de diálogo **criar e carregar pacote de suporte** , faça o seguinte:

    ![Criar pacote de suporte](./media/storsimple-create-manage-support-package/IC740923.png)

    - Na caixa de texto de **Chave de acesso de suporte** , insira a chave de acesso. Sua engenharia de suporte da Microsoft deve enviar essa chave de acesso para você por email.

    - Marque a caixa de seleção para fornecer consentimento para carregar automaticamente o pacote de suporte para o site Microsoft Support.

    - Clique no ícone de seleção ![Ícone de seleção](./media/storsimple-create-manage-support-package/IC740895.png).


## <a name="manually-create-a-support-package"></a>Criar manualmente um pacote de suporte

Em alguns casos, você precisará criar manualmente o pacote de suporte através do Windows PowerShell para StorSimple. Por exemplo:

- Se você precisar remover informações confidenciais de seus arquivos de log antes de compartilhar com a Microsoft Support.

- Se você tiver dificuldades para carregar o pacote devido a problemas de conectividade.

Você pode compartilhar seu pacote de suporte gerada manualmente com o Microsoft Support através de e-mail. Execute as seguintes etapas para criar um pacote de suporte no Windows PowerShell para StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Criar um pacote de suporte no Windows PowerShell para StorSimple

1. Para iniciar uma sessão do Windows PowerShell como administrador no computador remoto que é usado para conectar ao seu dispositivo StorSimple, digite o seguinte comando:

    `Start PowerShell`

2. Na sessão do Windows PowerShell, conectar-se ao SSAdmin Console do seu dispositivo:

    - No prompt de comando, digite:

        `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`

    1. Na caixa de diálogo que é aberta, digite sua senha de administrador do dispositivo. A senha padrão é:

        `Password1`

        ![Caixa de diálogo de credencial do PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)

    2. Selecione **Okey**.
    1. No prompt de comando, digite:

        `Enter-PSSession $MS`

3. Na sessão que é aberta, digite o comando apropriado.

    - Para compartilhamentos de rede que são protegidas por senha, digite:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

        Você será solicitado para uma senha, e um caminho para a pasta de rede compartilhada e uma senha de criptografia (porque o pacote de suporte é criptografado). Um pacote de suporte será criado na pasta especificada.

    - Para compartilhamentos que não são protegidas por senha, não é necessário o `-Credential` parâmetro. Insira o seguinte:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

        O pacote de suporte é criado para ambos os controladores na pasta compartilhada de rede especificado. É um arquivo compactado criptografado que pode ser enviado à Microsoft Support para solução de problemas. Para obter mais informações, consulte [Suporte da Microsoft de contato](storsimple-contact-microsoft-support.md).


### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Os parâmetros de cmdlet HcsSupportPackage de exportação
Você pode usar os seguintes parâmetros com o cmdlet HcsSupportPackage de exportação.

| Parâmetro            | Obrigatório/opcional | Descrição                                                                                                                                                             |
|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-Path`                 | Necessário          | Use para fornecer o local da pasta de rede compartilhada em que o pacote de suporte é colocado.                                                                 |
| `-EncryptionPassphrase` | Necessário          | Use para fornecer uma senha para ajudar a criptografar o pacote de suporte.                                                                                                        |
| `-Credential`           | Opcional          | Use para fornecer credenciais de acesso para a pasta de rede compartilhada.                                                                                        |
| `-Force`                | Opcional          | Use esta opção para ignorar a etapa de confirmação de senha de criptografia.                                                                                                                |
| `-PackageTag`           | Opcional          | Use para especificar um diretório em *caminho* no qual o pacote de suporte é colocado. O padrão é [nome do dispositivo]-[data atual e time:yyyy-MM-dd-HH-mm-ss].       |
| `-Scope`                | Opcional          | Especifique como **Cluster** (padrão) para criar um pacote de suporte para ambos os controladores. Se você quiser criar um pacote somente para o controlador atual, especifique **controlador**. |


## <a name="edit-a-support-package"></a>Editar um pacote de suporte

Após ter gerado um pacote de suporte, você precisará editar o pacote para remover informações confidenciais. Isso pode incluir volume nomes, endereços IP do dispositivo e nomes de backup dos arquivos de log.

> [AZURE.IMPORTANT] Você só pode editar um pacote de suporte que foi gerado através do Windows PowerShell para StorSimple. Você não pode editar um pacote criado no portal do clássico Azure com o serviço do Gerenciador de StorSimple.

Para editar um pacote de suporte antes de carregar no site do Microsoft Support, primeiro descriptografar o pacote de suporte, edite os arquivos e, em seguida, criptografá-lo novamente. Execute as etapas a seguir.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar um pacote de suporte no Windows PowerShell para StorSimple

1. Gere um pacote de suporte, conforme descrito anteriormente, [para criar um pacote de suporte no Windows PowerShell para StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).

2. [Baixe o script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente em seu cliente.

3. Importe o módulo do Windows PowerShell. Especifique o caminho para a pasta local em que você baixou o script. Para importar o módulo, digite:

    `Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Todos os arquivos são arquivos de *.aes* que são compactados e criptografados. Para descompactar e descriptografar arquivos, digite:

    `Open-HcsSupportPackage <Path to the folder that contains support package files>`

    Observe que as extensões de arquivo real agora são exibidas para todos os arquivos.

    ![Editar pacote de suporte](./media/storsimple-create-manage-support-package/IC750706.png)

5. Quando solicitado para a senha de criptografia, digite a senha que você usou quando o pacote de suporte foi criado.

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:EncryptionPassphrase: ****

6. Navegue até a pasta que contém os arquivos de log. Como os arquivos de log são agora descompactados e descriptografar, esses terão extensões de arquivo original. Modificar esses arquivos para remover todas as informações específicas do cliente, como nomes de volume e endereços IP do dispositivo e salve os arquivos.

7. Feche os arquivos para compactá-las com gzip e criptografá-los com AES-256. Isso é para a velocidade e a segurança em transferir o pacote de suporte em uma rede. Para compactar e criptografar arquivos, insira o seguinte:

    `Close-HcsSupportPackage <Path to the folder that contains support package files>`

    ![Editar pacote de suporte](./media/storsimple-create-manage-support-package/IC750707.png)

8. Quando solicitado, forneça uma senha de criptografia para o pacote de suporte modificada.

        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****

9. Anote a nova senha, para que você pode compartilhá-lo com o Microsoft Support quando solicitado.


### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemplo: Editar arquivos em um pacote de suporte em um compartilhamento protegido por senha

O exemplo a seguir mostra como descriptografar, editar e criptografar novamente um pacote de suporte.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar logs de dispositivo para solucionar sua implantação do dispositivo e pacotes de suporte](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Saiba como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
