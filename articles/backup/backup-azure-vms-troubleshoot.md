<properties
    pageTitle="Solucionar problemas de backup do Azure máquina virtual | Microsoft Azure"
    description="Solucionar problemas de backup e restauração do Azure máquinas virtuais"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="trinadhk;jimpark;"/>


# <a name="troubleshoot-azure-virtual-machine-backup"></a>Solucionar problemas de backup do Azure máquina virtual

> [AZURE.SELECTOR]
- [Compartimento de serviços de recuperação](backup-azure-vms-troubleshoot.md)
- [Compartimento de backup](backup-azure-vms-troubleshoot-classic.md)

Você pode solucionar problemas encontrados ao usar o Backup do Azure com informações listadas na tabela abaixo.

## <a name="backup"></a>Backup

| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
|Backup|    Não foi possível executar a operação, como máquina virtual não existe mais. -Pare proteção de máquina virtual sem excluir os dados de backup. Obter mais detalhes em http://go.microsoft.com/fwlink/?LinkId=808124   |Isso acontece quando a máquina virtual primária é excluída, mas a política de backup continua procurar uma máquina virtual executar o backup. Para corrigir esse erro: <ol><li> Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recurso [nome do serviço de nuvem],<br>(OU)</li><li> Pare a proteção de máquina virtual com ou sem excluir os dados de backup. [Mais detalhes](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Backup|Não pôde se comunicar com o agente de máquina virtual para status de instantâneo. -Certifique-se de que a máquina virtual tem acesso à internet. Além disso, atualizar o agente de máquina virtual conforme mencionado no guia solução de problemas em http://go.microsoft.com/fwlink/?LinkId=800034 |Este erro é lançado, se houver um problema com o agente de máquina virtual ou acesso de rede a infraestrutura do Azure está bloqueado de alguma maneira. Saiba mais sobre a depuração de máquina virtual instantâneo problemas.<br> Se o agente de máquina virtual não está causando problemas, reinicie a máquina virtual. Às vezes o estado de máquina virtual incorreto pode causar problemas e reiniciar a máquina virtual redefine este "inválida"|
|Backup|    Falha na operação de extensão de serviços de recuperação. -Verifique se que agent mais recente de máquina virtual está presente na máquina virtual e o serviço do agente está em execução. Verifique repetir a operação de backup e se ele falhar, entre em contato com o suporte da Microsoft.|   Este erro é lançado quando o agente de máquina virtual está desatualizada. Consulte a seção "Atualizando o agente de máquina virtual" abaixo para atualizar o agente de máquina virtual.|
|Backup |Máquina virtual não existe. -Por favor, verifique se que existe de máquina virtual ou selecione uma máquina virtual diferente. | Isso acontece quando a máquina virtual primária é excluída, mas a política de backup continua procurar uma máquina virtual executar o backup. Para corrigir esse erro: <ol><li> Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recurso [nome do serviço de nuvem],<br>(OU)<br></li><li>Pare a proteção de máquina virtual sem excluir os dados de backup. [Mais detalhes](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Backup |Falha na execução do comando. -Outra operação está em andamento neste item. Aguarde até que a operação anterior seja concluída e tente novamente |Um backup existente ou um trabalho de restauração para a máquina virtual está executando, e um novo trabalho não pode ser iniciado enquanto o trabalho existente está em execução.|
| Backup | Copiando VHDs do cofre backup expirou - Repita a operação em poucos minutos. Se o problema persistir, contate o Microsoft Support. | Isso acontece quando há muitos dados a ser copiada. Verifique se você tem menos de 16 discos de dados. |
| Backup | Backup falhou com um erro interno - Repita a operação em poucos minutos. Se o problema persistir, contate o Microsoft Support | Você pode receber esse erro por 2 motivos: <ol><li> Há um problema temporário em acessando o armazenamento de máquina virtual. Verifique o [Status do Azure](https://azure.microsoft.com/en-us/status/) para ver se há qualquer problema contínuo relacionado ao armazenamento/computação/rede na região. Por favor repetir o problema de backup post é minimizada. <li>A máquina virtual original foi excluída e, portanto, backup não pode ser feito. Para manter os dados de backup para uma máquina virtual excluída mas parar os erros de backup, desproteger a máquina virtual e escolha a opção para manter os dados. Isso interromperá o agendamento de backup e também as mensagens de erro recorrente. |
| Backup | Falha ao instalar os serviços de recuperação do Azure extensão no item selecionado - agente de máquina virtual é um pré-requisito para extensão de serviços de recuperação do Azure. Instale o agente de máquina virtual do Azure e reinicie a operação de registro | <ol> <li>Verifique se o agente de máquina virtual tiver sido instalado corretamente. <li>Certifique-se de que o sinalizador na máquina virtual config está definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre a instalação do agente de máquina virtual e como validar a instalação do agente de máquina virtual. |
| Backup | Extensão instalação falhou com o erro "COM + não conseguiu falar com o Microsoft Distributed Transaction Coordinator | Isso geralmente significa que o serviço COM + não está executando. Contate o suporte da Microsoft para obter ajuda sobre como corrigir esse problema. |
| Backup | Operação de instantâneo falhou com o erro de operação VSS "Esta unidade está bloqueada pela criptografia de unidade BitLocker. Você deve desbloquear esta unidade no painel de controle. | Desativar o BitLocker para todas as unidades a máquina virtual e observe se o problema VSS é resolvido |
| Backup | Não há suporte para máquinas virtuais tendo discos virtuais no armazenamento Premium para backup | Nenhum |
| Backup | Azure Máquina Virtual não foi encontrado. | Isso acontece quando a máquina virtual primária é excluída, mas a política de backup continua procurar uma máquina virtual executar o backup. Para corrigir esse erro: <ol><li>Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recurso [nome do serviço de nuvem], <br>(OU) <li> Desativar a proteção para essa máquina virtual para que os trabalhos de backup não serão criados </ol> |
| Backup | Agente de máquina virtual não estiver presente na máquina virtual - instale o pré-requisito necessário, agente de máquina virtual e reinicie a operação. | [Leia mais](#vm-agent) sobre a instalação do agente de máquina virtual e como validar a instalação do agente de máquina virtual. |

## <a name="jobs"></a>Trabalhos

| Operação | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Cancelar trabalho | Não há suporte para cancelamento para este tipo de trabalho - Aguarde até que o trabalho for concluído. | Nenhum |
| Cancelar trabalho | O trabalho não está em um estado pode ser cancelado - Aguarde até que o trabalho for concluído. <br>OU<br> Trabalho selecionado não está em um estado pode ser cancelado, aguarde o conclusão do trabalho.| Provavelmente o trabalho quase é preenchido; Aguarde até que o trabalho for concluído |
| Cancelar trabalho | Não é possível cancelar o trabalho porque ele não está em andamento - cancelamento tem suporte apenas para trabalhos que estão em andamento. Cancele tentativa em uma em andamento trabalho. | Isso acontece devido a um estado transitório. Aguarde um minuto e repita a operação de cancelar |
| Cancelar trabalho | Falha ao cancelar o trabalho - Aguarde até termina de trabalho. | Nenhum |


## <a name="restore"></a>Restaurar
| Operação | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Restaurar | Restauração falhou com erro interno de nuvem | <ol><li>Serviço de nuvem à qual você está tentando restaurar está configurado com configurações de DNS. Você pode verificar <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-Slot "Produção" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Se houver endereço configurado, isso significa que as configurações de DNS estão definidas.<br> <li>Serviço de nuvem para o qual você está tentando restaurar está configurado com IP reservado e VMs existentes no serviço de nuvem estão no estado parado.<br>Você pode verificar a que um serviço de nuvem reservou IP usando os seguintes cmdlets do powershell:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-DEP Slot "Produção" $. ReservedIPName <br><li>Você está tentando restaurar uma máquina virtual com as seguintes configurações de rede especial no mesmo serviço de nuvem. <br>-Máquinas virtuais em configuração de Balanceador de carga (interno e externo)<br>-Máquinas virtuais com vários IPs reservadas<br>-Máquinas virtuais com várias placas de rede<br>Selecione um novo serviço de nuvem na interface de usuário ou consulte [restaurar as considerações](./backup-azure-arm-restore-vms.md/#restoring-vms-with-special-network-configurations) para VMs com configurações de rede especial</ol> |
| Restaurar | O nome DNS selecionado já está sendo usado - especifique um nome DNS diferente e tente novamente. | O nome DNS aqui se refere ao nome de serviço de nuvem (geralmente que terminam com. cloudapp.net). Isso deve ser exclusivo. Se você encontrar esse erro, é necessário escolher um nome diferente de máquina virtual durante a restauração. <br><br> Observe que esse erro é mostrado somente para usuários do portal do Azure. A operação de restauração através do PowerShell terá êxito porque ele só restaura os discos e não cria a máquina virtual. O erro, terá de encarar quando a máquina virtual explicitamente é criada por você após o disco restaurar a operação. |
| Restaurar | A configuração de rede virtual especificada não está correta - especifique uma configuração de rede virtual diferente e tente novamente. | Nenhum |
| Restaurar | O serviço de nuvem especificada está usando um IP reservado, que não correspondam com a configuração da máquina virtual está sendo restaurada - especifique um serviço de nuvem diferente que não está usando o IP reservado ou escolher outro ponto de recuperação para restaurar a partir de. | Nenhum |
| Restaurar | Serviço de nuvem atingiu o limite no número de pontos de extremidade de entrada - repetir a operação, especificando um serviço de nuvem diferente ou usando um ponto de extremidade existente. | Nenhum |
| Restaurar | Conta de armazenamento de compartimento e destino backup são em duas regiões diferentes, verifique se a conta de armazenamento especificada na operação de restauração está na mesma região Azure como backup cofre. | Nenhum |
| Restaurar | Conta de armazenamento especificada para a operação de restauração não é suportado - contas de armazenamento apenas Basic/padrão com localmente redundantes ou configurações de replicação redundante geográfica têm suporte. Selecione uma conta de armazenamento com suporte | Nenhum |
| Restaurar | Tipo de conta de armazenamento especificadas para a operação de restauração não está online - Certifique-se de que a conta de armazenamento especificada na operação de restauração está online | Isso pode acontecer por causa de um erro temporário no armazenamento do Azure ou devido a uma interrupção. Escolha outra conta de armazenamento. |
| Restaurar | Cota de grupo de recursos foi atingida - exclua alguns grupos de recursos do portal Azure ou contate o suporte Azure para aumentar os limites. | Nenhum |
| Restaurar | Sub-rede selecionada não existir, selecione uma sub-rede que existe | Nenhum |


## <a name="policy"></a>Política
| Operação | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Criar uma política | Falha ao criar a política - Reduza as escolhas de retenção para continuar com a configuração de política. | Nenhum |


## <a name="vm-agent"></a>Agente de máquina virtual

### <a name="setting-up-the-vm-agent"></a>Configurando o agente de máquina virtual
Geralmente, o agente de máquina virtual já está presente em VMs que são criadas da Galeria Azure. No entanto, máquinas virtuais que são migradas do local dos data centers não teria o agente de máquina virtual instalado. Para tais VMs, o agente de máquina virtual precisa estar instalado explicitamente. Leia mais sobre como [instalar o agente de máquina virtual em uma máquina virtual existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para VMs do Windows:

- Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará privilégios de administrador para concluir a instalação.
- [Atualizar a propriedade de máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

Para VMs Linux:

- Instale o mais recente [Linux agente](https://github.com/Azure/WALinuxAgent) do github.
- [Atualizar a propriedade de máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.


### <a name="updating-the-vm-agent"></a>Atualizando o agente de máquina virtual
Para VMs do Windows:

- Atualizar o agente de máquina virtual é tão simple quanto reinstalar os [binários do agente de máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). No entanto, você precisa garantir que nenhuma operação de backup está em execução enquanto o agente de máquina virtual está sendo atualizado.

Para VMs Linux:

- Siga as instruções em [Atualizar agente de máquina virtual Linux](../virtual-machines/virtual-machines-linux-update-agent.md).


### <a name="validating-vm-agent-installation"></a>Validando a instalação do agente de máquina virtual
Como verificar a versão do agente de máquina virtual em VMs do Windows:

1. Faça logon na máquina virtual Azure e navegue até a pasta *C:\WindowsAzure\Packages*. Você deve encontrar o arquivo de WaAppAgent.exe presente.
2. Clique com botão direito no arquivo, vá para **Propriedades**e selecione a guia **detalhes** . O campo de versão do produto deve ser 2.6.1198.718 ou superior

## <a name="troubleshoot-vm-snapshot-issues"></a>Solucionar problemas de instantâneo de máquina virtual
O backup de máquina virtual depende emitir comando instantâneo para armazenamento subjacente. Não tem acesso ao armazenamento ou atraso na execução de tarefa de instantâneo pode falhar o backup. O seguinte pode causar a falha de tarefa instantâneo.

1. Acesso de rede ao armazenamento está bloqueado usando NSG<br>
   Saiba mais sobre como [Habilitar o acesso de rede](backup-azure-vms-prepare.md#2-network-connectivity) ao armazenamento usando qualquer lista de exceções de IPs ou por meio do servidor proxy.
2.  VMs com backup do Sql Server configurado podem causar atraso de tarefa de instantâneo <br>
    Por padrão máquina virtual problemas de backup VSS backup total em VMs do Windows. Em VMs que estiver executando o Sql Server e Sql Server backup está configurado, isso pode causar atraso na execução de instantâneo. Defina o seguinte chave do registro se você estiver enfrentando falhas de backup devido a problemas de instantâneo.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
    "USEVSSCOPYBACKUP"="TRUE"
    ```
3.  Status de máquina virtual informado incorretamente como máquina virtual é desligamento no RDP.  <br>
    Se você tiver desligar a máquina virtual no RDP, verifique novamente no portal do que o status de máquina virtual é refletido corretamente. Caso contrário, desligue a máquina virtual no portal usando 'Desligamento' opção no painel de máquina virtual.
4.  Se mais de compartilhamento da máquina quatro virtual a mesma nuvem serviço, configure várias políticas de backup para testar os tempos de backup portanto, não há que mais de quatro backups de máquina virtual são iniciados ao mesmo tempo. Tente distribuídas backup iniciar uma hora separadamente entre diretivas de horários. 
5.  Máquina virtual está em execução no alto da CPU/memória.<br>
    Se a máquina virtual está sendo executado em alta CPU usage(>90%) ou memória, tarefa de instantâneo é enfileirados, atrasada e serão eventualmente obtém excedido. Tente sob demanda backup em tais situações.

<br>

## <a name="networking"></a>Rede
Como todas as extensões, extensão de Backup precisam ter acesso à internet pública para trabalhar. Não tem acesso à internet pública pode manifesto em si de várias maneiras:

- A instalação de extensão pode falhar
- As operações de backup (como instantâneo de disco) podem falhar
- Exibir o status da operação de backup pode falhar

A necessidade de resolução de endereços de internet pública foram organizada [aqui](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Será necessário verificar as configurações de DNS para o VNET e certifique-se de que os URIs do Azure possam ser resolvidos.

Depois que a resolução de nome é feita corretamente, acesso ao IPs Azure também precisa ser fornecido. Para desbloquear o acesso a infraestrutura do Azure, siga uma destas etapas:

1. Intervalos de IP do data center Azure branca.
    - Obter a lista de [análise Azure IPs](https://www.microsoft.com/download/details.aspx?id=41653) sejam lista branca.
    - Desbloquear o IPs usando o cmdlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) . Execute este cmdlet dentro a máquina virtual do Azure, em uma janela do PowerShell elevada (Executar como administrador).
    - Adicione regras para a NSG (se você tiver um no local) para permitir acesso ao IPs.
2. Criar um caminho para o fluxo de tráfego HTTP
    - Se você tiver alguma restrição de rede no lugar (uma rede do grupo de segurança, por exemplo) implantar um servidor de proxy HTTP para rotear o tráfego. Etapas para implantar um servidor de HTTP Proxy podem ser encontradas [aqui](backup-azure-vms-prepare.md#2-network-connectivity).
    - Adicione regras para a NSG (se você tiver um no local) para permitir o acesso à INTERNET do HTTP Proxy.

>[AZURE.NOTE] DHCP deve ser habilitado dentro do convidado para Backup de máquina virtual IaaS trabalhar.  Se você precisar IP estático particular, você deve configurá-lo na plataforma. A opção de DHCP dentro a máquina virtual deve ser habilitada para a esquerda.
Exiba mais informações sobre a [configuração de IP particular interno estático](../virtual-network/virtual-networks-reserved-private-ip.md).
