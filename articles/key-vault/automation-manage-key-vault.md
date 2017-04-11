<properties
    pageTitle="Gerenciar cofre de chave do Azure usando automação Azure | Microsoft Azure"
    description="Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar Cofre de chave do Azure."
    services="Key-Vault, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-key-vault-using-azure-automation"></a>Gerenciando Cofre de chave do Azure usando automação do Azure

Este guia apresentará o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento dos suas chaves e senhas no Azure chave cofre.

## <a name="what-is-azure-automation"></a>O que é automação Azure?

[Automação do Azure](../automation/automation-intro.md) é um serviço Azure para simplificar o gerenciamento de nuvem por meio de automação de processos e configuração de estado desejado. Usando o Azure automação, manual, repetido, demorada e propensa tarefas podem ser automatizadas para aumentar a confiabilidade, a eficiência e tempo de valor para sua organização.

Automação Azure fornece um mecanismo de execução do fluxo de trabalho altamente confiável e altamente disponível que se expande para atender às suas necessidades. No Azure automação, processos podem ser inicializados manualmente, sistemas de terceiros 3º ou em intervalos agendados para que tarefas acontecem exatamente quando necessário.

Reduzir a sobrecarga operacional e liberar IT e DevOps funcionários se concentrar no trabalho que adiciona o valor de negócios, movendo suas tarefas de gerenciamento de nuvem para serem executados automaticamente por automação do Azure.


## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Como o Azure automação pode ajudar Gerenciar cofre de chave do Azure?

Compartimento de chave pode ser gerenciado na automação do Azure usando os [AzureRM chave cofre cmdlets] (https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) e [cmdlets do Azure clássico chave cofre](https://msdn.microsoft.com/library/azure/dn868052.aspx). O módulo Azure para gerenciamento clássico Cofre de chave está disponível automaticamente no Azure automação e você pode importar o [módulo AzureRM KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) para automação do Azure, para que você possa realizar muitas das suas tarefas de gerenciamento de chave cofre dentro do serviço. Você também pode emparelhar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure, automatizar tarefas complexas em serviços do Azure e 3º sistemas de terceiros.

Com os cmdlets do Azure chave cofre, você pode executar estas tarefas entre outros: 

- Criar e configurar um cofre chave
- Criar ou importar uma chave
- Criar ou atualizar um segredo
- Atualizar atributos de uma chave
- Obter uma chave ou secreta
- Excluir uma chave ou secreta

Aqui estão alguns exemplos do uso do PowerShell para gerenciar Cofre de chave:  

* [Azure cofre chave - passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Instalar e configurar um cofre chave Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os fundamentos de automação do Azure e como ele pode ser usado para gerenciar Cofre de chave do Azure, siga estes links para saber mais sobre a automação do Azure.

* Consulte o Azure automação de [Introdução Tutorial](../automation/automation-first-runbook-graphical.md).
* Consulte os [scripts do PowerShell do Azure chave cofre](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).
