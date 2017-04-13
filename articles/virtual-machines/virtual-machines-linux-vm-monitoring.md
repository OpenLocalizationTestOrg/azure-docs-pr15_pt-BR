<properties
   pageTitle="Habilitar ou desabilitar o monitoramento de máquina virtual Azure"
   description="Descreve como habilitar ou desabilitar o monitoramento de máquina virtual Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="enable-or-disable-azure-vm-monitoring"></a>Habilitar ou desabilitar o monitoramento de máquina virtual Azure

Esta seção descreve como habilitar ou desabilitar o monitoramento em máquinas virtuais em execução no Azure. Por padrão monitoramento estiver habilitado nas máquinas virtuais do Azure se implantado a partir do [portal do Azure](https://portal.azure.com) e monitoramento gráficos são fornecidos por padrão com um período de 1 minuto. Você pode habilitar ou desabilitar o monitoramento usando o portal ou a Interface de linha de comando do Azure para Mac, Linux e Windows (a CLI Azure). 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Ativar / desativar o monitoramento por meio do Portal do Azure
 
Você pode habilitar o monitoramento da sua máquina virtual do Azure, que fornece dados sobre sua instância nos períodos de 1 minuto. (aplicam alterações de armazenamento). Dados de diagnóstico detalhadas estarão disponíveis para a máquina virtual nos gráficos de portal ou por meio da API. Por padrão, portal Azure permite monitoramento, mas você pode desativá-lo, conforme descrito abaixo. Você pode habilitar o monitoramento enquanto a máquina virtual está em execução ou no estado de parado.

- Abra o portal em **Azure [https://portal.azure.com](https://portal.azure.com)**

- No painel de navegação esquerdo, clique em máquinas virtuais.

- Nas máquinas virtuais lista, selecione uma instância em execução ou parada. Blad de máquina virtual será aberta.

- Clique em "All settings".

- Clique em "Diagnóstico".

- Alterar status para ativado ou desativado. Você também pode escolher neste lâmina o nível de detalhes que você gostaria de habilitar para sua máquina virtual de monitoramento.

[Azure.Note] A opção diagnóstico na é o padrão quando você cria uma nova máquina virtual

![Ativar / desativar o monitoramento por meio do Portal Azure.][1]


## <a name="enable--disable-monitoring-with-azure-cli"></a>Ativar / desativar o monitoramento CLI do Azure
 
Habilitar o monitoramento para uma máquina virtual do Azure.

- Crie um arquivo nomeado como PrivateConfig.json com o seguinte conteúdo.
        {"storageAccountName": "a conta de armazenamento para receber dados", "storageAccountKey": "a chave da conta"}
- Execute o seguinte comando CLI do Azure.

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Você pode alterar da versão 2.0 para uma versão posterior, quando disponível. 

Para obter mais detalhes sobre como configurar o monitoramento métricas e amostras, visite o documento - * *[Usando Linux diagnóstico extensão Monitor Linux máquina virtual do desempenho e dados de diagnóstico](virtual-machines-linux-classic-diagnostic-extension.md).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

