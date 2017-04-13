<properties
    pageTitle="Solucionar problemas de conexão SSH para uma máquina virtual | Microsoft Azure"
    description="Como solucionar problemas de como 'Conexão SSH falhou' ou 'Conexão SSH recusada' para uma máquina virtual de Azure executando Linux."
    keywords="SSH conexão recusou, ssh erro, azure ssh conexão SSH falhou"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Solucionar problemas de conexões SSH para uma máquina de Linux Azure virtual que falhar, erros, ou é recusado
Há vários motivos que você encontrar erros de Secure Shell (SSH), falhas de conexão SSH, ou SSH é recusado quando você tenta se conectar a uma máquina virtual do Linux (máquina virtual). Este artigo ajuda você a localizar e corrigir os problemas. Você pode usar o portal do Azure, Azure CLI ou extensão de acesso de máquina virtual para Linux para solucionar e resolver problemas de conexão.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se precisar de mais ajuda a qualquer momento neste artigo, você pode contatar os especialistas Azure no [MSDN Azure e fóruns de estouro de pilha](http://azure.microsoft.com/support/forums/). Como alternativa, você pode enviar um incidente de suporte Azure. Vá para o [site de suporte do Azure](http://azure.microsoft.com/support/options/) e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas Frequentes de suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).


## <a name="quick-troubleshooting-steps"></a>Etapas de solução de problemas rápidas
Após cada etapa de solução de problemas, tente reconectar para a máquina virtual.

1. Redefina a configuração de SSH.
2. Redefina as credenciais do usuário.
3. Verifique se que as regras de [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) permitem o tráfego SSH.
    - Certifique-se de que existe uma regra de grupo de segurança de rede para permitir o tráfego SSH (por padrão, porta TCP 22).
    - Você não pode usar o redirecionamento de porta / mapeamento sem usar um balanceador de carga Azure.
4. Verificar a [integridade do recurso de máquina virtual](../resource-health/resource-health-overview.md). 
    - Certifique-se de que a máquina virtual relata como sendo íntegra.
    - Se você tiver o diagnóstico de inicialização ativado, verifique se que a máquina virtual não é o relatório de erros de inicialização nos logs.
5. Reinicie a máquina virtual.
6. Reimplante a máquina virtual.

Continue lendo para etapas de solução de problemas mais detalhadas e explicações.


## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponíveis para solucionar problemas de conexão SSH

Você pode redefinir suas credenciais ou configuração SSH usando um dos seguintes métodos:

- [Portal do azure](#using-the-azure-portal) - excelente se você precisar redefinir rapidamente a configuração do SSH ou chave SSH e você não tem as ferramentas Azure instaladas.
- [Comandos do azure](#using-the-azure-cli) - se você já estiver na linha de comando, redefinir rapidamente as credenciais ou configuração de SSH.
- [Extensão do azure VMAccessForLinux](#using-the-vmaccess-extension) - crie e reutilizar arquivos de definição de json para redefinir as credenciais de usuário ou configurações de SSH.

Após cada etapa de solução de problemas, tente conectar à sua máquina virtual novamente. Se você ainda não consegue se conectar, tente a próxima etapa.


## <a name="using-the-azure-portal"></a>Usando o portal do Azure
O portal Azure fornece uma maneira rápida de redefinir as credenciais de usuário ou configurações de SSH sem instalar qualquer ferramentas no computador local.

Selecione sua máquina virtual no portal do Azure. Role até a seção **suporte + solução de problemas** e selecione **Redefinir a senha** como no exemplo seguinte:

![Redefinir a configuração de SSH ou credenciais no portal do Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Redefinir a configuração de SSH
A primeira etapa, selecione `Reset SSH configuration only` no menu suspenso **modo** como a captura de tela anterior, clique no botão **Redefinir** . Após concluir esta ação, tente acessar sua máquina virtual novamente.

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir SSH credenciais de um usuário
Para redefinir as credenciais de um usuário existente, selecione `Reset SSH public key` ou `Reset password` no menu suspenso de **modo** como a captura de tela anterior. Especificar o nome de usuário e uma chave SSH ou nova senha e, em seguida, clique no botão **Redefinir** .

Você também pode criar um usuário com privilégios de sudo na máquina virtual neste menu. Insira um novo nome de usuário e senha associada ou chave SSH e, em seguida, clique no botão **Redefinir** .


## <a name="using-the-azure-cli"></a>Usando a CLI do Azure
Se você ainda não fez isso, [instale a CLI do Azure e conecte-se à sua assinatura do Azure](../xplat-cli-install.md). Verifique se você usar o modo de gerente de recursos da seguinte maneira:

```
azure config mode arm
```

Se você criou e carregar uma imagem de disco Linux personalizada, verifique se o [Agente do Microsoft Azure Linux](virtual-machines-linux-agent-user-guide.md) versão 2.0.5 ou posterior está instalado. Para VMs criadas usando imagens da galeria, é possível que essa extensão de acesso já é instalado e configurado para você.

### <a name="reset-ssh-configuration"></a>Redefinir a configuração de SSH
A configuração de SSHD em si pode ser configurado incorretamente ou o serviço encontrou um erro. Você pode redefinir SSHD para certificar-se de que a configuração de SSH em si é válida. A redefinição de SSHD deve ser a primeira etapa de solução de problemas que você realizar.

O exemplo a seguir redefine SSHD em uma máquina virtual chamada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios nomes de grupo de recursos e de máquina virtual da seguinte maneira:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir SSH credenciais de um usuário
Se aparecer SSHD funcione corretamente, você pode redefinir a senha de um usuário doador. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`, sobre a máquina virtual chamada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se usando autenticação de chave SSH, você pode redefinir a chave SSH para um determinado usuário. O exemplo a seguir atualiza a chave SSH armazenada em `~/.ssh/azure_id_rsa.pub` para o usuário chamado `myUsername`, sobre a máquina virtual chamada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-file ~/.ssh/azure_id_rsa.pub
```


## <a name="using-the-vmaccess-extension"></a>Usando a extensão de VMAccess
Extensão de acesso da máquina virtual para Linux lê um arquivo de json que define ações para executar. Essas ações incluem redefinição SSHD, a redefinição de uma chave SSH ou adicionando um usuário. Você ainda usar a CLI do Azure para chamar a extensão de VMAccess, mas você pode reutilizar os arquivos json entre várias VMs se desejado. Essa abordagem permite que você crie um repositório de arquivos de json que pode ser chamado para determinado cenários.

### <a name="reset-sshd"></a>Redefinir SSHD
Crie um arquivo denominado `PrivateConf.json` com o seguinte conteúdo:

```bash
{  
    "reset_ssh":"True"
}
```

Usando a CLI do Azure, você chama o `VMAccessForLinux` extensão para redefinir sua conexão SSHD especificando seu arquivo de json. O exemplo a seguir redefine SSHD na VM denominada `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```bash
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Redefinir SSH credenciais de um usuário
Se aparecer SSHD funcione corretamente, você pode redefinir as credenciais de um usuário doador. Para redefinir a senha de um usuário, crie um arquivo denominado `PrivateConf.json`. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`. Insira as seguintes linhas em seu `PrivateConf.json` arquivo, usando seus próprios valores:

```bash
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou para redefinir a chave SSH para um usuário, primeiro crie um arquivo denominado `PrivateConf.json`. O exemplo a seguir redefine as credenciais para `myUsername` para o valor especificado em `myPassword`, sobre a máquina virtual chamada `myVM` em `myResourceGroup`. Insira as seguintes linhas em seu `PrivateConf.json` arquivo, usando seus próprios valores:

```bash
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Depois de criar seu arquivo de json, use a CLI do Azure para chamar o `VMAccessForLinux` extensão redefinir suas credenciais de usuário SSH, especificando o arquivo de json. O exemplo a seguir redefine credenciais na máquina virtual chamado `myVM` em `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```


## <a name="restart-a-vm"></a>Reiniciar uma máquina virtual
Se você tiver redefinir as credenciais de usuário e configuração do SSH ou encontrou um erro ao fazer isso, você pode tentar reiniciar a máquina virtual para o endereço base problemas de computação.

### <a name="azure-portal"></a>Portal do Azure
Para reiniciar uma máquina virtual usando o portal de Azure, selecione sua máquina virtual e clique no * botão**Reiniciar** como no exemplo seguinte:

![Reiniciar uma máquina virtual no portal do Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>CLI Azure
O exemplo a seguir reinicia a máquina virtual chamada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```bash
azure vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Reimplantar uma máquina virtual
Você pode reimplantar uma máquina virtual para outro nó dentro Azure, que pode corrigir qualquer problema de rede subjacente. Para obter informações sobre reimplantar uma máquina virtual, consulte [reimplantar máquina virtual para o novo nó Azure](virtual-machines-windows-redeploy-to-new-node.md).

> [AZURE.NOTE] Após concluir esta operação, dados de disco efêmeras serão perdidos e endereços IP dinâmicos que estão associados a máquina virtual serão atualizados.

### <a name="azure-portal"></a>Portal do Azure
Para reimplantar uma máquina virtual usando o portal de Azure, selecione sua máquina virtual e role até a seção **suporte + solução de problemas** . Clique no botão **reimplantar** como no exemplo seguinte:

![Reimplantar uma máquina virtual no portal do Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>CLI Azure
O exemplo a seguir reimplanta a máquina virtual chamada `myVM` no grupo de recursos denominado `myResourceGroup`. Use seus próprios valores da seguinte maneira:

```bash
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VMs criadas usando o modelo de implantação do clássico

Tente estas etapas para resolver falhas de conexão SSH mais comuns para VMs que foram criadas usando o modelo de implantação clássico. Após cada etapa, tente reconectar para a máquina virtual.

- Redefina o acesso remoto a partir do [portal do Azure](https://portal.azure.com). No portal do Azure, selecione sua máquina virtual e clique no botão **Redefinir remoto …** .

- Reinicie a máquina virtual. No [portal do Azure](https://portal.azure.com), selecione sua máquina virtual e clique no botão **Reiniciar** .

    -OU-

    No [portal de clássico Azure](https://manage.windowsazure.com), selecione **máquinas virtuais** > **instâncias** > **Reiniciar**.

- Reimplante a máquina virtual para um novo nó Azure. Para obter informações sobre como reimplantar uma máquina virtual, consulte [reimplantar máquina virtual para o novo nó Azure](virtual-machines-windows-redeploy-to-new-node.md).

    Após concluir esta operação, dados de disco efêmeras serão perdidos e endereços IP dinâmicos que estão associados a máquina virtual serão atualizados.

- Siga as instruções em [como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-classic-reset-access.md) :
    - Redefina a senha ou chave SSH.
    - Crie uma conta de usuário _sudo_ .
    - Redefina a configuração de SSH.

- Verificar a integridade do recurso da VM por problemas de plataforma.<br>
  Selecione sua máquina virtual e role para baixo **configurações** > **Verificar integridade**.


## <a name="additional-resources"></a>Recursos adicionais

- Se você ainda não puder SSH para sua máquina virtual após seguir as etapas após, consulte [mais detalhadas etapas de solução de problemas](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) para revisar etapas adicionais para resolver o problema.

- Para obter mais informações sobre como solucionar problemas de acesso do aplicativo, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)

- Para obter mais informações sobre máquinas virtuais que foram criadas usando o modelo clássico de implantação de solução de problemas, consulte [como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-classic-reset-access.md).
