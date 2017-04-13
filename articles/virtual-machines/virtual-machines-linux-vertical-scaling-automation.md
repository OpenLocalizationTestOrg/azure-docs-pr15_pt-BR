<properties
    pageTitle="Dimensionar verticalmente Azure máquina virtual com a automação do Azure | Microsoft Azure"
    description="Como dimensionar verticalmente uma máquina Virtual Linux em resposta ao monitoramento alertas com a automação do Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/29/2016"
    ms.author="singhkay"/>

# <a name="vertically-scale-azure-virtual-machine-with-azure-automation"></a>Dimensionar verticalmente Azure máquina virtual com a automação do Azure

Dimensionamento vertical é o processo de aumentar ou diminuir os recursos de uma máquina em resposta à carga de trabalho. No Azure, isso pode ser feito alterando o tamanho da máquina Virtual. Isso pode ajudar nos seguintes cenários

- Se a máquina Virtual não está sendo usado com frequência, você pode redimensioná-lo para baixo até um tamanho menor para reduzir os custos mensais
- Se a máquina Virtual está vendo uma carga de pico, ela pode ser redimensionada para um tamanho maior para aumentar sua capacidade

A estrutura de tópicos para ver as etapas fazer isso é como abaixo

1. Configuração do Azure automação para acessar suas máquinas virtuais
2. Importar a escala Vertical do Azure automação runbooks para sua assinatura
3. Adicionar uma webhook ao seu runbook
4. Adicionar um alerta para sua máquina Virtual

> [AZURE.NOTE] Devido o tamanho da máquina Virtual primeiro, os tamanhos que ele pode ser dimensionado, pode estar limitado devido a disponibilidade dos outros tamanhos no cluster em que máquina Virtual atual é implantado. No runbooks de automação publicados usados neste artigo podemos cuidam da ocorrência e dimensionar somente dentro do abaixo pares de tamanho de máquina virtual. Isso significa que uma máquina Virtual de Standard_D1v2 será não inesperadamente aumentadas para Standard_G5 ou reduzido para Basic_A0.

>| Tamanhos de máquina virtual dimensionamento par |   |
|---|---|
|  Basic_A0 |  Basic_A4 |
|  Standard_A0 | Standard_A4 |
|  Standard_A5 | Standard_A7  |
|  Standard_A8 | Standard_A9  |
|  Standard_A10 |  Standard_A11 |
|  Standard_D1 |  Standard_D4 |
|  Standard_D11 | Standard_D14  |
|  Standard_DS1 |  Standard_DS4 |
|  Standard_DS11 | Standard_DS14  |
|  Standard_D1v2 |  Standard_D5v2 |
|  Standard_D11v2 |  Standard_D14v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configuração do Azure automação para acessar suas máquinas virtuais

A primeira coisa que você precisa fazer é criar uma conta de automação do Azure que hospedará o runbooks usado para dimensionar as instâncias de máquina virtual escala definida. Recentemente, o serviço de automação introduziu o recurso de "Executar como conta" que torna a configuração de capital de serviço para execução automática a runbooks em nome do usuário muito fácil. Você pode ler mais sobre isso no artigo abaixo:

* [Autenticar Runbooks com conta executar como do Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar a escala Vertical do Azure automação runbooks para sua assinatura

Os que são necessários para dimensionar verticalmente sua máquina Virtual runbooks já publicados na Galeria de Runbook de automação do Azure. Você precisará importá-los para sua assinatura. Você pode aprender como importar runbooks lendo o artigo a seguir.

* [Galerias de runbook e módulo de automação do Azure](../automation/automation-runbook-gallery.md)

Os runbooks que precisam ser importados são mostradas na imagem abaixo

![Importar runbooks](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar uma webhook ao seu runbook

Assim que você importou os runbooks que você precisará adicionar um webhook runbook para que ele pode ser acionado por um alerta de uma máquina Virtual. Os detalhes da criação de um webhook para seu Runbook podem ser lidos aqui

* [Azure webhooks de automação](../automation/automation-webhooks.md)

Verifique se que você copiar a webhook antes de fechar a caixa de diálogo webhook, pois você precisará isso na próxima seção.

## <a name="add-an-alert-to-your-virtual-machine"></a>Adicionar um alerta para sua máquina Virtual

1. Selecione configurações de máquina Virtual
2. Selecione "Regras de alerta"
3. Selecione "Adicionar alerta"
4. Selecione uma métrica para acionar o alerta em
5. Selecione uma condição, que, quando atendidas irão causar o alerta acionar
6. Selecione um limite para a condição na etapa 5. para ser atendida
7. Selecione um período no qual o serviço de monitoramento verificará para a condição e limite nas etapas 5 e 6
8. Cole a webhook que você copiou na seção anterior.

![Adicionar um alerta para máquina Virtual 1](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![Adicionar alerta à máquina Virtual 2](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)