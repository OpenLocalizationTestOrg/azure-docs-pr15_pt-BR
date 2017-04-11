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

## <a name="discovery"></a>Descoberta

| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Descoberta | Falha ao descobrir novos itens - Microsoft Azure Backup encontrou e erro interno. Aguarde alguns minutos e tente novamente a operação. | Repita o processo de detecção após 15 minutos.
| Descoberta | Falha ao descobrir novos itens – descoberta outra operação já está em andamento. Aguarde até que a operação de descoberta atual for concluída. | Nenhum |

## <a name="register"></a>Registrar
| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Registrar | Número de discos de dados conectados à máquina virtual excedido o limite suportado - Verifique desanexar alguns discos dados nessa máquina e repita a operação. Backup do Azure suporta até 16 discos de dados conectados a uma máquina virtual Azure para backup | Nenhum |
| Registrar | Microsoft Azure Backup encontrou um erro interno - Aguarde alguns minutos e tente novamente a operação. Se o problema persistir, contate o Microsoft Support. | Você pode receber esse erro devido a um da seguinte configuração sem suporte da máquina virtual em LRS Premium. <br> Armazenamento de Premium VMs pode ser feito usando Cofre de serviços de recuperação. [Saiba Mais](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| Registrar | Falha no registro com o tempo limite da operação instalar agente | Verifique se a versão do sistema operacional da máquina virtual é suportada. |
| Registrar | Comando execução falhou - outra operação está em andamento neste item. Aguarde a conclusão da operação anterior | Nenhum |
| Registrar | Não há suporte para máquinas virtuais tendo discos virtuais no armazenamento Premium para backup | Nenhum |
| Registrar | Agente de máquina virtual não estiver presente na máquina virtual - instale o pré-requisito necessário, agente de máquina virtual e reinicie a operação. | [Leia mais](#vm-agent) sobre a instalação do agente de máquina virtual e como validar a instalação do agente de máquina virtual. |

## <a name="backup"></a>Backup

| Operação de backup | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Backup | Não pôde se comunicar com o agente de máquina virtual para status de instantâneo. Tarefa de sub de máquina virtual do instantâneo expirou. -Por favor, consulte o guia de solução de problemas sobre como resolver esse problema. | Este erro é lançado, se houver um problema com o agente de máquina virtual ou acesso de rede a infraestrutura do Azure está bloqueado de alguma maneira. Saiba mais sobre [problemas de depuração de instantâneo de máquina virtual](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Se o agente de máquina virtual não está causando problemas, reinicie a máquina virtual. Às vezes o estado de máquina virtual incorreto pode causar problemas e reiniciar a máquina virtual redefine este "inválida" |
| Backup | Backup falhou com um erro interno - Repita a operação em poucos minutos. Se o problema persistir, contate o Microsoft Support | Verifique se há um problema temporário em acessando o armazenamento de máquina virtual. Verifique o [Status do Azure](https://azure.microsoft.com/en-us/status/) para ver se há qualquer problema contínuo relacionado ao armazenamento/computação/rede na região. Por favor repetir o problema de backup post é minimizada. |
| Backup | Não foi possível executar a operação, como máquina virtual não existe mais. | Backup não pode ser executado como a máquina virtual configurada para backup foi excluída. Parar backups posterior indo ao modo de exibição protegido itens, selecione o item protegido e clique em Parar proteção. Você pode manter dados selecionando a opção de manter o Backup de dados. Você pode continuar a proteção posteriormente para esta máquina virtual clicando em configurar a proteção do modo de exibição de itens registrados|
| Backup | Falha ao instalar os serviços de recuperação do Azure extensão no item selecionado - agente de máquina virtual é um pré-requisito para extensão de serviços de recuperação do Azure. Instale o agente de máquina virtual do Azure e reinicie a operação de registro | <ol> <li>Verifique se o agente de máquina virtual tiver sido instalado corretamente. <li>Certifique-se de que o sinalizador na máquina virtual config está definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre a instalação do agente de máquina virtual e como validar a instalação do agente de máquina virtual. |
| Backup | Comando execução falhou - outra operação está em andamento neste item. Aguarde até que a operação anterior seja concluída e tente novamente | Um backup existente ou um trabalho de restauração para a máquina virtual está executando, e um novo trabalho não pode ser iniciado enquanto o trabalho existente está em execução. |
| Backup | Extensão instalação falhou com o erro "COM + não conseguiu falar com o Microsoft Distributed Transaction Coordinator | Isso geralmente significa que o serviço COM + não está executando. Contate o suporte da Microsoft para obter ajuda sobre como corrigir esse problema. |
| Backup | Operação de instantâneo falhou com o erro de operação VSS "Esta unidade está bloqueada pela criptografia de unidade BitLocker. Você deve desbloquear esta unidade no painel de controle. | Desativar o BitLocker para todas as unidades a máquina virtual e observe se o problema VSS é resolvido |
| Backup | Não há suporte para máquinas virtuais tendo discos virtuais no armazenamento Premium para backup | Nenhum |
| Backup | Azure Máquina Virtual não foi encontrado. | Isso acontece quando a máquina virtual primária é excluída, mas a política de backup continua procurar uma máquina virtual executar o backup. Para corrigir esse erro: <ol><li>Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recurso [nome do serviço de nuvem], <br>(OU) <li> Desative a proteção para essa máquina virtual para que não vai ser disparados backups subsequentes. </ol> |
| Backup | Agente de máquina virtual não estiver presente na máquina virtual - instale o pré-requisito necessário, agente de máquina virtual e reinicie a operação. | [Leia mais](#vm-agent) sobre a instalação do agente de máquina virtual e como validar a instalação do agente de máquina virtual. |

## <a name="jobs"></a>Trabalhos
| Operação | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Cancelar trabalho | Não há suporte para cancelamento para este tipo de trabalho - Aguarde até que o trabalho for concluído. | Nenhum |
| Cancelar trabalho | O trabalho não está em um estado pode ser cancelado - Aguarde até que o trabalho for concluído. <br>OU<br> Trabalho selecionado não está em um estado pode ser cancelado, aguarde o conclusão do trabalho.| Provavelmente, o trabalho está quase concluído; Aguarde até que o trabalho for concluído |
| Cancelar trabalho | Não é possível cancelar o trabalho porque ele não está em andamento - cancelamento tem suporte apenas para trabalhos que estão em andamento. Cancele tentativa em uma em andamento trabalho. | Isso acontece devido a um estado transitório. Aguarde um minuto e repita a operação de cancelar |
| Cancelar trabalho | Falha ao cancelar o trabalho - Aguarde até que o trabalho termina. | Nenhum |


## <a name="restore"></a>Restaurar
| Operação | Detalhes do erro | Solução alternativa |
| -------- | -------- | -------|
| Restaurar | Restauração falhou com erro interno de nuvem | <ol><li>Serviço de nuvem à qual você está tentando restaurar está configurado com configurações de DNS. Você pode verificar <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-Slot "Produção" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Se houver endereço configurado, isso significa que as configurações de DNS estão definidas.<br> <li>Serviço de nuvem para o qual você está tentando restaurar está configurado com IP reservado e VMs existentes no serviço de nuvem estão no estado parado.<br>Você pode verificar a que um serviço de nuvem reservou IP usando os seguintes cmdlets do powershell:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-DEP Slot "Produção" $. ReservedIPName <br><li>Você está tentando restaurar uma máquina virtual com as seguintes configurações de rede especial no mesmo serviço de nuvem. <br>-Máquinas virtuais em configuração de Balanceador de carga (interno e externo)<br>-Máquinas virtuais com vários IPs reservadas<br>-Máquinas virtuais com várias placas de rede<br>Selecione um novo serviço de nuvem na interface de usuário ou consulte [restaurar as considerações](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) para VMs com configurações de rede especial</ol> |
| Restaurar | O nome DNS selecionado já está sendo usado - especifique um nome DNS diferente e tente novamente. | O nome DNS aqui se refere ao nome de serviço de nuvem (geralmente que terminam com. cloudapp.net). Isso deve ser exclusivo. Se você encontrar esse erro, é necessário escolher um nome diferente de máquina virtual durante a restauração. <br><br> Esse erro é mostrado somente para usuários do portal do Azure. A operação de restauração através do PowerShell sucesso porque ele só restaura os discos e não cria a máquina virtual. O erro, terá de encarar quando a máquina virtual explicitamente é criada por você após o disco restaurar a operação. |
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





