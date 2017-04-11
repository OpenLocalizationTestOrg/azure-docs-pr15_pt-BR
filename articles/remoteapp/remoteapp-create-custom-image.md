<properties
    pageTitle="Como criar uma imagem de modelo personalizado para Azure RemoteApp | Microsoft Azure"
    description="Saiba como criar uma imagem de modelo personalizado para RemoteApp do Azure. Você pode usar este modelo com conjunto um híbrido ou nuvem."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>Como criar uma imagem de modelo personalizado para RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

RemoteApp Azure usa uma imagem de modelo do Windows Server 2012 R2 para hospedar todos os programas que você deseja compartilhar com seus usuários. Para criar uma imagem de modelo RemoteApp personalizada, você pode começar com uma imagem existente ou crie um novo. 


> [AZURE.TIP] Você sabia que você pode criar uma imagem de uma máquina virtual do Azure? História True e ele reduz a quantidade de tempo ele leva para importar a imagem. Confira as etapas [aqui](remoteapp-image-on-azurevm.md).

Os requisitos para a imagem que podem ser carregados para uso com o Azure RemoteApp são:


- O tamanho da imagem deve ser um múltiplo de MB. Se você tentar carregar uma imagem que não é um múltiplo exato, o carregamento falhará.
- O tamanho da imagem deve ser 127 GB ou menor.
- Ele deve estar em um arquivo VHD (VHDX arquivos [Hyper-V virtuais unidades de disco rígido] não são atualmente suportados).
- O VHD não deve ser uma máquina virtual de geração 2.
- O VHD pode ser tamanho fixo ou expansível dinamicamente. Um VHD expansível dinamicamente é recomendado porque ele leva menos tempo para carregar no Azure que um arquivo VHD de tamanho fixo.
- O disco deve ser inicializado usando o registro de inicialização de mestre (MBR) estilo de partição. Não há suporte para o estilo de partição GUID partição GPT (tabela).
- O VHD deve conter uma única instalação do Windows Server 2012 R2. Ele pode conter vários volumes, mas somente um que contém uma instalação do Windows.
- A função de Host de sessão de área de trabalho remota (RDSH) e o recurso Experiência Desktop devem ser instalados.
- A função do agente de Conexão de área de trabalho remota deve *não* ser instalado.
- O arquivo EFS (sistema) deve ser desabilitado.
- A imagem deve ser submetida a Sysprep usando os parâmetros **/oobe /generalize /shutdown** (não use o parâmetro **/mode:vm** ).
- Carregar seu VHD de uma cadeia de instantâneo não é suportado.


**Antes de começar**

Você precisa fazer o seguinte antes de criar o serviço:

- [Inscrever-se](https://azure.microsoft.com/services/remoteapp/) para RemoteApp.
- Crie uma conta de usuário no Active Directory para usar como a conta de serviço de RemoteApp. Restrinja as permissões para essa conta para que ele só pode ingressar máquinas ao domínio. Consulte [Configurar o Active Directory do Azure para RemoteApp](remoteapp-ad.md) para obter mais informações.
- Coletar informações sobre sua rede local: endereço IP informações e detalhes de dispositivo VPN.
- Instale o módulo do [PowerShell do Azure](../powershell-install-configure.md) .
- Colete informações sobre os usuários que você deseja conceder acesso a. Isso pode ser informações de conta da Microsoft ou informações de conta de trabalho do Active Directory para os usuários.



## <a name="create-a-template-image"></a>Criar uma imagem de modelo ##

Estas são as etapas de alto níveis para criar uma nova imagem de modelo do zero:

1.  Localize uma imagem de DVD de atualização do Windows Server 2012 R2 ou ISO.
2.  Crie um arquivo VHD.
4.  Instale o Windows Server 2012 R2.
5.  Instale a função de Host de sessão de área de trabalho remota (RDSH) e o recurso Experiência Desktop.
6.  Instale recursos adicionais exigidos por seus aplicativos.
7.  Instalar e configurar seus aplicativos. Para facilitar o compartilhamento de aplicativos, adicione quaisquer aplicativos ou programas que você deseja compartilhar no menu **Iniciar** da imagem, especificamente em **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs.
8.  Realize configurações adicionais Windows exigidas pelos aplicativos.
9.  Desabilite o sistema de arquivos criptografados (EFS).
10. **Obrigatória:** Vá para o Windows Update e instale todas as atualizações importantes.
9.  SYSPREP a imagem.

As etapas detalhadas para a criação de uma nova imagem são:

1.  Localize uma imagem de DVD de atualização do Windows Server 2012 R2 ou ISO.
2.  Crie um arquivo VHD usando o gerenciamento de disco.
    1.  Inicie o gerenciamento de disco (Diskmgmt. msc).
    2.  Crie um VHD expansível dinamicamente de 40 GB ou mais em tamanho. (Estimar a quantidade de espaço necessário para Windows, seus aplicativos e personalizações. Windows Server com a função RDSH e o recurso Experiência Desktop instalado exigem cerca de 10 GB de espaço).
        1.  Clique em **ação > Criar VHD**.
        2.  Especifique o local, tamanho e formato VHD. Selecione **expansível dinamicamente**e, em seguida, clique em **Okey**.

            Isso será executado por vários segundos. Quando a criação de VHD for concluída, você verá um novo disco sem qualquer letra de unidade e em estado "Não inicializado" no console de gerenciamento de disco.

        - Clique no disco (não o espaço não alocado) e, em seguida, clique em **Inicializar disco**. Selecione **MBR** (registro mestre de inicialização) como o estilo de partição e clique em **Okey**.
        - Criar um novo volume: clique com botão direito no espaço não alocado e clique em **Novo Volume simples**. Você pode aceitar os padrões do assistente, mas certifique-se de que você atribuir uma letra de unidade para evitar possíveis problemas ao carregar a imagem de modelo.
        - Clique com botão direito no disco e clique em **Desanexar VHD**.





1. Instale o Windows Server 2012 R2:
    1. Crie uma nova máquina virtual. Use o novo Assistente de máquina Virtual do Hyper-V Gerenciador ou cliente Hyper-V.
        1. Na página especificar geração, escolha **geração 1**.
        2. Na página conectar do disco rígido Virtual, selecione **usar um disco rígido virtual existente**e navegue até o VHD que você criou na etapa anterior.
        2. Na página Opções de instalação, selecione **instalar um sistema operacional de um CD/DVD_ROM de inicialização**e, em seguida, selecione o local da mídia de instalação do Windows Server 2012 R2.
        3. Escolha outras opções no Assistente de necessários para instalar o Windows e seus aplicativos. Conclua o assistente.
    2.  Após concluir o assistente, edite as configurações da máquina virtual e faça as alterações necessárias para instalar o Windows e seus programas, como o número de processadores virtuais e clique em **Okey**.
    4.  Conectar-se à máquina virtual e instale o Windows Server 2012 R2.
1. Instale a função de Host de sessão de área de trabalho remota (RDSH) e o recurso Experiência Desktop:
    1. Inicie o Gerenciador de servidor.
    2. Clique em **Adicionar funções e recursos** na tela de boas-vindas ou no menu **Gerenciar** .
    3. Clique em **Avançar** na página antes de começar.
    4. Selecione **instalação baseada em função ou baseada em recursos**e clique em **Avançar**.
    5. Selecione máquina local na lista e clique em **Avançar**.
    6. Selecione **Os serviços de área de trabalho remota**e clique em **Avançar**.
    7. Expanda **Interfaces do usuário e infraestrutura** e selecione **Experiência Desktop**.
    8. Clique em **Adicionar recursos**e clique em **Avançar**.
    9. Na página serviços de área de trabalho remota, clique em **Avançar**.
    10. Clique em **Host da sessão da área de trabalho remota**.
    11. Clique em **Adicionar recursos**e clique em **Avançar**.
    12. Na página Confirmar instalação seleções, selecione **reiniciar o servidor de destino automaticamente se necessário**e, em seguida, clique em **Sim** no aviso de reiniciar.
    13. Clique em **instalar**. O computador irá reiniciar.
1.  Instale recursos adicionais exigidos pelo seus aplicativos, como o .NET Framework 3.5. Para instalar os recursos, execute o Assistente de recursos e adicionar funções.
7.  Instalar e configurar os programas e aplicativos que você deseja publicar através de RemoteApp.

>[AZURE.IMPORTANT]
>
>Instale a função RDSH antes de instalar os aplicativos para garantir que os problemas de compatibilidade de aplicativo com forem descobertos antes que a imagem é carregada RemoteApp.
>
>Certificar-se de que um atalho para seu aplicativo**(arquivo)** aparece no menu **Iniciar** para todos os usuários (%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs). Também, certifique-se de que o ícone que você vê no menu **Iniciar** é o que você deseja que os usuários vejam. Caso contrário, alterá-lo. (Você não *tiver* adicionar o aplicativo para o início menu, mas ele torna mais fácil publicar o aplicativo no RemoteApp. Caso contrário, é necessário fornecer o caminho de instalação para o aplicativo quando você publica o aplicativo).


8.  Realize configurações adicionais Windows exigidas pelos aplicativos.
9.  Desabilite o sistema de arquivos criptografados (EFS). Execute o seguinte comando em uma janela de comando elevado:

        Fsutil behavior set disableencryption 1

    Como alternativa, você pode definir ou adicionar o seguinte valor DWORD no registro:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  Se você estiver criando sua imagem dentro de uma máquina virtual Azure, renomeie o ** \%windir%\Panther\Unattend.xml** de arquivos, como isso bloqueará o script de carregamento usado mais tarde de funcionar. Altere o nome do arquivo para Unattend.old para que você ainda terá o arquivo caso você precise reverter sua implantação.
10. Vá para o Windows Update e instale todas as atualizações importantes. Talvez seja necessário executar várias vezes para obter todas as atualizações do Windows Update. (Às vezes, instalar uma atualização e atualização em si requer uma atualização.)
10. SYSPREP a imagem. Em um prompt de comando elevado, execute o seguinte comando:

    **C:\Windows\System32\sysprep\sysprep.exe /generalize /oobe /shutdown**

    **Observação:** Não use a opção de **/mode:vm** do comando SYSPREP apesar de isso é uma máquina virtual.


## <a name="next-steps"></a>Próximas etapas ##
Agora que você tem a imagem de modelo personalizado, você precisa carregar imagem à sua coleção de RemoteApp. Use as informações nos seguintes artigos para criar sua coleção:


- [Como criar uma coleção de híbrido de RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Como criar uma coleção de nuvem de RemoteApp](remoteapp-create-cloud-deployment.md)
 