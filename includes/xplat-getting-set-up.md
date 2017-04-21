<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli"></a>Usando CLI Azure

As etapas a seguir o ajudarão a usar o Azure CLI facilmente com a versão mais recente e a assinatura correta. Se você precisar instalar CLI do Azure e conectá-lo primeiro à sua conta, consulte a [Interface de linha do Azure (Azure comando)](xplat-cli-install.md).

### <a name="step-1-update-azure-cli-version"></a>Etapa 1: Versão de atualização do Azure CLI

Para usar Azure CLI comandos fundamental com modo de gerenciamento de serviço, você deve ter uma versão recente se possível. Para verificar sua versão, digite `azure --version`. Você verá algo como:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se você quiser atualizar sua versão do Azure CLI, consulte [CLI do Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Etapa 2: Configurar a conta do Azure e assinatura

Quando você tiver conectado o CLI Azure com a conta que você deseja usar, você pode ter mais de uma assinatura. Nesse caso, você deve examinar as assinaturas disponíveis para sua conta digitando `azure account list`e selecione a assinatura que você deseja usar, digitando `azure account set <subscription id or name> true` onde _nome ou id da assinatura_ é a id da assinatura ou o nome da assinatura que você gostaria de trabalhar com na sessão atual. Você verá algo semelhante ao seguinte:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Se você ainda não tiver uma conta do Azure, mas você tiver uma assinatura a assinatura do MSDN, você pode obter gratuito créditos Azure ativando seus [benefícios de assinante MSDN aqui](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , ou você podem usar a conta gratuita. Dois funcionarão para acesso Azure.
