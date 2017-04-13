<properties
    pageTitle="Preparar um VHD do Windows para carregar no Azure | Microsoft Azure"
    description="Práticas recomendadas para preparar um VHD do Windows antes de carregar no Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>

# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Preparar um VHD do Windows para carregar no Azure
Para carregar uma VM do Windows do local para o Azure, você deve preparar corretamente o disco rígido virtual (VHD). Há várias etapas recomendadas para que você seja concluída antes de você carregar um VHD no Azure. Este artigo mostra como preparar um VHD do Windows para carregar no Microsoft Azure e ela também explica [quando e como usar o Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Preparar o disco virtual

>[AZURE.NOTE] 
> Azure suporta apenas [máquinas virtuais de geração 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) que estão no formato de arquivo VHD. Não há suporte para o formato mais recente do VHDX no Azure. 
>
> O VHD deve ser de tamanho fixo, não dinâmico. Se necessário, as instruções abaixo detalham sobre conversão de VHDX ou dinâmico discos. O tamanho máximo permitido do VHD é 1.023 GB.


Certifique-se de que o VHD Windows está funcionando corretamente no servidor local. Resolva erros dentro a máquina virtual em si antes de tentar converter ou carregar no Azure.

Se você precisar converter seu disco virtual para o formato obrigatório para o Azure, use um dos métodos observados nas seções a seguir. Faça backup a máquina virtual antes de executar qualquer processo de conversão de disco virtual ou Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Converter usando o Gerenciador de Hyper-V
- Abrir o Gerenciador de Hyper-V e selecione computador local à esquerda. No menu acima dela, clique em **ação** > **Editar disco**.
    - Na tela **Localize Virtual disco rígido** , procure e selecione seu disco virtual.
    - Selecione **Converter** na próxima tela
        - Se você precisar converter de VHDX, selecione **VHD** e clique em **Avançar**
        - Se você precisar converter do disco dinâmico, selecione **tamanho fixo** e clique em **Avançar**

    - Procure e selecione o **caminho para o novo arquivo VHD**.
    - Clique em **Concluir** para fechar.

### <a name="convert-using-powershell"></a>Converter usando o PowerShell
Você pode converter um disco virtual usando o [cmdlet do PowerShell VHD Convert](http://technet.microsoft.com/library/hh848454.aspx). No exemplo a seguir, estamos conversão de uma VHDX VHD e conversão de um dinâmico para tipo fixo:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter do formato de disco VMware VMDK
Se você tiver uma imagem de máquina virtual do Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), convertê-lo um VHD usando o [Conversor de máquina Virtual do Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Leia o blog [como converter um VMDK VMware Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) para obter mais informações.

## <a name="prepare-windows-configuration-for-upload"></a>Preparar a configuração do Windows para carregamento

> [AZURE.NOTE] Execute os seguintes comandos com [privilégios administrativos](https://technet.microsoft.com/library/cc947813.aspx).

1. Remova qualquer rota persistente estática na tabela de roteamento:

    - Para exibir a tabela de rota, executar `route print`.
    - Verifique as seções de **Rotas de persistência** . Se houver uma rota persistente, use a [rota excluir](https://technet.microsoft.com/library/cc739598.apx) para removê-lo.

2. Remova o proxy de WinHTTP:

    ```
    netsh winhttp reset proxy
    ```

3. Configure o disco de política de SAN para [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):

    ```
    diskpart san policy=onlineall
    ```

4. Usar o tempo Universal Coordenado (UTC) para Windows e defina o tipo de inicialização do serviço tempo do Windows (w32time) para **automaticamente**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Configurar serviços do Windows
5. Certifique-se de que cada um dos seguintes serviços Windows está definida como os **valores de padrão do Windows**. Eles são configurados com as configurações de inicialização indicadas na lista a seguir. Você pode executar esses comandos para redefinir as configurações de inicialização:

    ```
    sc config bfe start= auto

    sc config dcomlaunch start= auto

    sc config dhcp start= auto

    sc config dnscache start= auto

    sc config IKEEXT start= auto

    sc config iphlpsvc start= auto

    sc config PolicyAgent start= demand

    sc config LSM start= auto

    sc config netlogon start= demand

    sc config netman start= demand

    sc config NcaSvc start= demand

    sc config netprofm start= demand

    sc config NlaSvc start= auto

    sc config nsi start= auto

    sc config RpcSs start= auto

    sc config RpcEptMapper start= auto

    sc config termService start= demand

    sc config MpsSvc start= auto

    sc config WinHttpAutoProxySvc start= demand

    sc config LanmanWorkstation start= auto

    sc config RemoteRegistry start= auto
    ```


## <a name="configure-remote-desktop-configuration"></a>Definir a configuração de área de trabalho remota
6. Se houver quaisquer certificados autoassinados vinculados para o ouvinte de protocolo RDP (Remote Desktop), removê-los:

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Para obter mais informações sobre como configurar certificados ouvinte RDP, consulte [Configurações de certificado de ouvinte no Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Configure os valores de [manutenção de atividade](https://technet.microsoft.com/library/cc957549.aspx) de serviço de RDP:

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Configure o modo de autenticação para o serviço RDP:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Ativar o serviço de RDP, adicionando as seguintes subchaves do registro:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Configurar regras de Firewall do Windows
10. Permitir WinRM por meio de perfis do firewall três (domínio, privado e público) e habilitar o serviço remota do PowerShell:

    ```
    Enable-PSRemoting -force
    ```

11. Verifique se as seguintes regras de firewall do sistema operacional de convidado no lugar:

    - Entrada

    ```
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    ```

    - Entrada e saída

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - Saída

    ```
    netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
    ```


## <a name="additional-windows-configuration-steps"></a>Outras etapas de configuração do Windows
12. Executar `winmgmt /verifyrepository` para confirmar que o repositório (Instrumentação de gerenciamento do Windows) seja consistente. Se o repositório estiver corrompido, consulte [esta postagem de blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Verifique se que as configurações de dados de configuração da inicialização (BCD) correspondem a seguir:

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Remova quaisquer filtros de Interface do Driver de transporte extras, como software que analisa pacotes TCP.
15. Para garantir que o disco está íntegra e consistente, execute o `CHKDSK /f` comando.
16. Desinstale qualquer outro software de terceiros e driver relacionados a componentes físicos ou qualquer outra tecnologia de virtualização.
17. Certifique-se de que um aplicativo de terceiros não está usando a porta 3389. Esta porta é usada para o serviço RDP no Azure. Você pode usar o `netstat -anob` comando para verificar as portas que estão usando pelos aplicativos.
18. Se o VHD do Windows que você deseja carregar um controlador de domínio, siga [estas etapas adicionais](https://support.microsoft.com/kb/2904015) para preparar o disco.
19. Reinicie a máquina virtual para certificar-se de que o Windows está ainda eficaz, pode ser contatado usando a conexão de RDP.
20. Redefinir a senha de administrador local atual e certifique-se de que você pode usar essa conta para entrar no Windows por meio da conexão RDP.  Essa permissão de acesso é controlada pelo objeto de diretiva "Permitir logon pelos serviços de área de trabalho remota". Esse objeto está localizado em "Computador configuração configurações Settings\Local diretivas atribuição de direitos."


## <a name="install-windows-updates"></a>Instalar atualizações do Windows
22. Instale as atualizações mais recentes para Windows. Se isso não for possível, certifique-se de que as seguintes atualizações são instaladas:

    - [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VMs não recuperar uma interrupção da rede e ocorrem problemas de corrupção de dados

    - [KB3115224](https://support.microsoft.com/kb/3115224) Melhorias de confiabilidade para VMs que estejam em execução em um host Windows Server 2012 R2 ou Windows Server 2012

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: A atualização de segurança do Microsoft Windows de elevação de endereço de privilégio: 8 de março de 2016

    - [KB3063075](https://support.microsoft.com/kb/3063075) Muitos eventos de ID 129 efetuou quando você executa uma máquina de virtual do Windows Server 2012 R2 no Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) Microsoft Azure VMs não recuperar uma interrupção da rede e ocorrem problemas de corrupção de dados

    - [KB3114025](https://support.microsoft.com/kb/3114025) Desempenho lento quando você acessa o armazenamento de arquivos Azure do Windows 8.1 ou Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) Hotfix aumenta o limite de 64K de buffers de RIO por processo para serviço Azure no Windows

    - [KB3004545](https://support.microsoft.com/kb/3004545) Você não consegue acessar máquinas virtuais hospedadas em serviços de hospedagem Azure por meio de uma conexão VPN no Windows

    - [KB3082343](https://support.microsoft.com/kb/3082343) Conectividade entre locais VPN é perdida quando Azure túneis VPN-to-site usam o Windows Server 2012 R2 RRAS

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: A atualização de segurança do Microsoft Windows de elevação de endereço de privilégio: 8 de março de 2016

    - [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Descrição da atualização de segurança para CSRSS: 12 de abril de 2016
    - [KB2904100](https://support.microsoft.com/kb/2904100) Sistema congela durante o disco e/s no Windows<a id="step23"></a>
23. Se você quiser criar uma imagem para implantar várias máquinas dela, você precisará generalizar a imagem executando `sysprep` antes de você carregar o VHD no Azure. Você não precisa executar `sysprep` usando um VHD especializado. Para obter mais informações sobre como criar uma imagem generalizada, consulte os seguintes artigos:

    - [Criar uma imagem de máquina virtual de uma VM Azure existente usando o modelo de implantação do Gerenciador de recursos](virtual-machines-windows-create-vm-generalized.md)
    - [Criar uma imagem de máquina virtual de uma VM Azure existente usando o modem de implantação clássico](virtual-machines-windows-classic-capture-image.md)
    - [Suporte a Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Configurações de extras sugeridas

As seguintes configurações não afetam VHD carregando. No entanto, é altamente recomendável que você os tiver configurado.

- Instale o [agente do Azure máquinas virtuais](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Depois de instalar o agente, você pode habilitar extensões de máquina virtual. As extensões de máquina virtual implementam a maioria da funcionalidade crítica que você deseja usar com suas VMs como a redefinição de senhas, configurando RDP e muitas outras pessoas.

- O log de despejo pode ser útil na solução de problemas de falha do Windows. Habilite a coleção de log de despejo:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Depois que a máquina virtual é criada no Azure, configurar o arquivo de paginação de tamanho de sistema definido na unidade de disco d:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Próximas etapas

- [Carregar uma imagem de máquina virtual do Windows Azure para implantações do Gerenciador de recursos](virtual-machines-windows-upload-image.md)
