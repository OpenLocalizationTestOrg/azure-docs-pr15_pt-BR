<properties
   pageTitle="Falha de Backup de máquina virtual Azure: não pôde se comunicar com o agente de máquina virtual para status instantâneo - subtarefa de máquina virtual instantâneo expirou | Microsoft Azure"
   description="Sintomas causas e resoluções para falhas de backup de máquina virtual do Azure relacionadas a não podem se comunicar com o agente de máquina virtual para status de instantâneo. Instantâneo máquina virtual subtarefa expirou erro"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="cfreeman"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jimpark; markgal;genli"/>

# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Falha de Backup de máquina virtual Azure: não pôde se comunicar com o agente de máquina virtual para status instantâneo - subtarefa de máquina virtual instantâneo expirou

## <a name="summary"></a>Resumo

Depois de registrar e agendando uma máquina Virtual (máquina virtual) para Backup do Azure, o serviço de Backup do Azure inicia o trabalho de backup no horário agendado comunicando-se com a extensão de backup na VM para fazer um instantâneo no momento. Existem em condições que podem impedir que o instantâneo sendo disparadas que leva a uma falha de backup. Este artigo fornece etapas de solução de problemas para problemas relacionados a falhas de backup de máquina virtual do Azure relacionadas a erro de tempo limite de instantâneo.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma

Microsoft Azure Backup para infraestrutura como um serviço (IaaS) máquina virtual falha e retorna a seguinte mensagem de erro nos detalhes do erro de trabalho no [portal do Azure](https://portal.azure.com/):

**Não pôde se comunicar com o agente de máquina virtual para status instantâneo - subtarefa de máquina virtual instantâneo expirou.**

## <a name="cause"></a>Causa
Há quatro causas comuns para este erro:

- A máquina virtual não tem acesso à Internet.
- O agente de máquina virtual do Microsoft Azure instalado na VM está desatualizado (para Linux VMs).
- A extensão de backup falha ao atualizar ou carregar.
- O status de instantâneos não podem ser recuperado ou os instantâneos não podem ser criados.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Causa 1: A máquina virtual não tem acesso à Internet
Pelo requisito de implantação, a máquina virtual tem sem acesso à Internet, ou restrições no local que impedem o acesso a infraestrutura do Azure.

A extensão de backup requerem conectividade com os endereços IP públicos do Azure para funcionar corretamente. Isso ocorre porque ele envia comandos para um ponto de extremidade do armazenamento do Azure (HTTP URL) para gerenciar os instantâneos da máquina virtual. Se a extensão não tem acesso à Internet pública, o backup eventualmente falhará.

### <a name="solution"></a>Solução
Neste cenário, use um dos seguintes métodos para resolver o problema:

- Intervalos de IP do data center Azure branca
- Criar um caminho para o fluxo de tráfego HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>À branca o Azure intervalos IP do data center

1. Obter a [lista de IPs de análise Azure](https://www.microsoft.com/download/details.aspx?id=41653) para ser lista branca.
2. Desbloquear o IPs usando o cmdlet New-NetRoute. Execute este cmdlet na VM Azure em uma janela do PowerShell elevada (Executar como administrador).
3. Adicione regras para o grupo de segurança de rede (NSG) se você tiver um para permitir acesso ao IPs.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Para criar um caminho para o fluxo de tráfego HTTP

1. Se você tem restrições de rede (por exemplo, um NSG), implante um servidor de proxy HTTP para rotear o tráfego.
2. Se você tiver um grupo de segurança de rede (NSG), adicione regras para permitir o acesso à Internet do proxy HTTP.

Saiba como [Configurar um proxy HTTP para backups de máquina virtual](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: O agente de máquina virtual do Microsoft Azure instalado na VM está desatualizado (para Linux VMs)

### <a name="solution"></a>Solução
Falhas mais relacionados ao agente ou extensão para Linux VMs são causadas por problemas que afetam um agente de máquina virtual antigo. Como orientação geral, as primeiras etapas para solucionar esse problema são as seguintes:

1. [Instalar o agente de máquina virtual do Azure mais recente](https://github.com/Azure/WALinuxAgent).
2. Certifique-se de que o Azure agent está em execução na máquina virtual. Para fazer isso, execute o seguinte comando:```ps -e```

    Se esse processo não está sendo executado, use os seguintes comandos reiniciá-lo.

    Para Ubuntu:```service walinuxagent start```

    Para outras distribuições: ' ' Iniciar waagent de serviço
```

3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.

    We require the following logs from the customer’s VM:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:

    Enable verbose logging (y|n)

2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension

1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| ----- | ----- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. | Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. | If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. | It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. | If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
|The VM cannot get host/fabric address from DHCP.|DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md).|
