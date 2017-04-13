<properties
   pageTitle="Redefinir uma senha de Windows local ao agente de convidado Azure não estiver instalado | Microsoft Azure"
   description="Como redefinir a senha de uma conta de usuário do Windows local quando o agente de convidado Azure não está instalado ou funcionando em uma máquina virtual"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="iainfou"/>

# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Como redefinir a senha do Windows local para máquina virtual do Azure
Você pode redefinir a senha do Windows local de uma máquina virtual Azure usando que o [portal Azure ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) fornecido que o agente do Azure convidado estiver instalado. Esse método é a principal maneira para redefinir uma senha para uma máquina virtual do Azure. Se você encontrar problemas com o agente de convidado Azure não responde, ou falhando instalar após o upload de uma imagem personalizada, você pode redefinir manualmente uma senha do Windows. Este artigo detalha como redefinir uma senha de conta local anexando disco virtual de origem SO para máquina virtual outra. 

> [AZURE.WARNING] Use esse processo apenas como último recurso. Sempre tente redefinir uma senha usando o [portal do Azure ou PowerShell do Azure](virtual-machines-windows-reset-rdp.md) primeiro.


## <a name="overview-of-the-process"></a>Visão geral do processo
As principais etapas para realizar uma senha local Redefinir para uma máquina de virtual do Windows no Azure quando há sem acesso ao agente do Azure convidado é da seguinte maneira:

- Exclua a fonte de máquina virtual. Os discos virtuais são mantidos.
- Anexe o disco de sistema operacional da VM fonte para outra máquina virtual dentro de sua assinatura do Azure. Esta máquina virtual é conhecido como a máquina virtual a solução de problemas.
- Usando a máquina virtual a solução de problemas, crie alguns arquivos config sistema operacional disco a origem máquina virtual.
- Desanexe disco de sistema operacional da VM da máquina virtual a solução de problemas.
- Use um modelo do Gerenciador de recursos para criar uma máquina virtual, usando o disco virtual original.
- Quando a máquina virtual novo inicializado, os arquivos de configuração criados atualizam a senha do usuário necessário.


## <a name="detailed-steps"></a>Etapas detalhadas
Sempre tente redefinir uma senha usando o [portal Azure ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) antes de tentar as etapas a seguir. Certifique-se de que fazer um backup da sua máquina virtual antes de começar. 

1. Exclua a máquina virtual afetada no portal do Azure. Excluindo a máquina virtual exclui apenas os metadados, a referência da máquina virtual dentro do Azure. Os discos virtuais são mantidos quando a máquina virtual é excluída:

    - Selecione a máquina virtual no portal do Azure, clique em *Excluir*:

    ![Excluir máquina virtual existente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)

2. Anexe o disco de sistema operacional da VM fonte para a máquina virtual a solução de problemas. A máquina virtual a solução de problemas deve estar na mesma região como disco do sistema operacional da VM fonte (como `West US`):

    - Selecione a máquina virtual a solução de problemas no portal do Azure. Clique em *discos* | *Anexar existente*:

    ![Anexar disco existente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)

    Selecione o *Arquivo VHD* e selecione a conta de armazenamento que contém sua fonte de máquina virtual:

    ![Selecione a conta de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)

    Selecione o recipiente de origem. O contêiner de origem é geralmente *vhds*:

    ![Selecione o contêiner de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)

    Selecione o vhd do sistema operacional para anexar. Clique em *Selecionar* para concluir o processo:

    ![Selecione o disco virtual de origem](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)

3. Conectar-se para a solução de problemas máquina virtual usando a área de trabalho remota e verifique o que disco de sistema operacional da VM fonte esteja visível:

    - Selecione a máquina virtual a solução de problemas no portal do Azure e clique em *Conectar*.
    - Abra o arquivo RDP que downloads. Insira o nome de usuário e a senha da máquina virtual solução de problemas.
    - No Explorador de arquivos, procure o disco de dados anexado. Se a fonte VHD da máquina virtual é o disco de apenas os dados anexado para a máquina virtual a solução de problemas, ele deverá ser a unidade f::

    ![Visualizar dados anexados disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)

4. Criar `gpt.ini` na `\Windows\System32\GroupPolicy` na unidade de origem da VM (se existir GPT. ini, renomeie gpt.ini.bak):

    > [AZURE.WARNING] Certifique-se de que você não acidentalmente crie os seguintes arquivos no C:\Windows, na unidade do sistema operacional para a máquina virtual a solução de problemas. Crie os seguintes arquivos na unidade de sistema operacional para sua fonte de máquina virtual que está anexado como um disco de dados.

    - Adicione as seguintes linhas para a `gpt.ini` arquivo criado:

    ```
    [General]
    gPCFunctionalityVersion=2
    gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
    Version=1
    ```

    ![Criar GPT. ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
 
5. Criar `scripts.ini` em `\Windows\System32\GroupPolicy\Machine\Scripts`. Verifique se as pastas ocultas são exibidas. Se necessário, crie a `Machine` ou `Scripts` pastas.

    - Adicione as seguintes linhas a `scripts.ini` arquivo criado:

    ```
    [Startup]
    0CmdLine=C:\Windows\System32\FixAzureVM.cmd
    0Parameters=
    ```

    ![Criar scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
 
6. Criar `FixAzureVM.cmd` no `\Windows\System32` com o seguinte conteúdo, substituindo `<username>` e `<newpassword>` com seus próprios valores:

    ```
    NET USER <username> <newpassword>
    ```

    ![Criar FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)

    Você deve atender aos requisitos de complexidade de senha configurada para sua máquina virtual ao definir a nova senha.

7. No portal do Azure, Desanexe o disco da máquina virtual a solução de problemas:

    - Selecione a máquina virtual a solução de problemas no portal do Azure, clique em *discos*.
    - Selecione o disco de dados anexado na etapa 2, clique em *Desanexar*:

    ![Desanexar disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)

8. Antes de criar uma máquina virtual, obtenha o URI ao seu disco de sistema operacional de origem:

    - Selecione a conta de armazenamento no portal do Azure, clique *Blobs*.
    - Selecione o contêiner. O contêiner de origem é geralmente *vhds*:

    ![Selecione blob de conta de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)

    Selecione sua fonte de máquina virtual SO VHD e clique no botão *Copiar* ao lado do nome de *URL* :

    ![Copiar disco URI](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)

9. Crie uma máquina virtual do disco do sistema operacional da VM fonte:

    - Use [Este modelo de Gerenciador de recursos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) para criar uma máquina virtual de um VHD especializado. Clique na `Deploy to Azure` botão para abrir o portal do Azure com os detalhes modelados preenchidos.
    - Se você quiser reter todas as configurações anteriores para a máquina virtual, selecione *Editar modelo* para fornecer o seu VNet existente, sub-rede, adaptador de rede ou IP público.
    - No `OSDISKVHDURI` caixa de texto de parâmetro, colar o URI de sua fonte de VHD obter na etapa anterior:

    ![Criar uma máquina virtual do modelo](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)

10. Após a máquina virtual novo estiver em execução, conectar-se para a máquina virtual usando a área de trabalho remota com a nova senha que você especificou no `FixAzureVM.cmd` script.

11. Na sua sessão remota para a máquina virtual novo, remova os arquivos a seguir para limpar o ambiente:

    - De %Windir%\System32.
        - Remover FixAzureVM.cmd
    - De %windir%\System32\GroupPolicy\Machine\
        - Remover scripts.ini
    - De %windir%\System32\GroupPolicy
        - Remover GPT. ini (se GPT. ini existentes antes e tiver sido renomeada para gpt.ini.bak, renomear o arquivo. bak de volta ao GPT. ini)

## <a name="next-steps"></a>Próximas etapas
Se você ainda não consegue se conectar usando a área de trabalho remota, consulte o [guia de solução de problemas de RDP](virtual-machines-windows-troubleshoot-rdp-connection.md). O [guia de solução de problemas de RDP detalhado](virtual-machines-windows-detailed-troubleshoot-rdp.md) examina métodos em vez de etapas específicas de solução de problemas. Você também pode [Abrir uma solicitação de suporte Azure](https://azure.microsoft.com/support/options/) para assistência prática.