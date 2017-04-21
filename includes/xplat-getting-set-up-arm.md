<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli-with-azure-resource-manager-arm"></a>Usando CLI Azure com Azure Resource Manager (ARM)

Antes de poder usar a CLI Azure com modelos e comandos do Gerenciador de recursos para implantar o Azure recursos e cargas de trabalho usando grupos de recursos, será necessário uma conta com o Azure (obviamente). Se você não tiver uma conta, você pode obter uma [avaliação gratuita de Azure aqui](https://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Se você ainda não tiver uma conta do Azure, mas você tiver uma assinatura a assinatura do MSDN, você pode obter gratuito créditos Azure ativando seus [benefícios de assinante MSDN aqui](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , ou você podem usar a conta gratuita. Dois funcionarão para acesso Azure.

### <a name="step-1-verify-the-azure-cli-version"></a>Etapa 1: Verificar a versão CLI do Azure

Para usar Azure CLI comandos fundamental e modelos ARM, você precisa ter pelo menos versão 0.8.17. Para verificar sua versão, digite `azure --version`. Você verá algo como:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se você precisa atualizar sua versão do Azure CLI, consulte [CLI do Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-verify-you-are-using-a-work-or-school-identity-with-azure"></a>Etapa 2: Verifique se você estiver usando um trabalho ou escola identidade com o Azure

Você somente pode usar o modo de comando BRAÇO se você estiver usando um [locatário do Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) ou um [Nome Principal do serviço](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Eles também são chamados *ids organizacionais*.)

Para ver se você tiver um, faça logon em digitando `azure login` e usando o seu trabalho ou escola de nome de usuário e senha quando solicitado. Se você tiver um, você verá algo semelhante ao seguinte:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
  	|info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Se você não vir isso, você deve criar um novo locatário (ou serviço principal) com sua identidade de conta da Microsoft. (Isso é geralmente o caso com assinaturas do MSDN pessoais ou assinaturas de avaliação gratuitas.) Para criar um trabalho ou escola id de sua conta do Azure criada com uma id da Microsoft, consulte [associar um diretório de AD Azure com uma nova assinatura do Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Se você acha que você já deve ter uma id organizacional, talvez você precise conversar com a pessoa que criou a conta para você.

### <a name="step-3-choose-your-azure-subscription"></a>Etapa 3: Escolher sua assinatura do Azure

Se você tiver apenas uma assinatura na sua conta do Azure, CLI Azure associa próprio essa assinatura por padrão. Se você tiver mais de uma assinatura, você precisa selecionar a assinatura que você deseja usar, digitando `azure account set <subscription id or name> true` onde _nome ou id da assinatura_ é a id da assinatura ou o nome da assinatura que você gostaria de trabalhar com na sessão atual.

Você verá algo semelhante ao seguinte:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### <a name="step-4-place-your-azure-cli-in-the-arm-mode"></a>Etapa 4: Colocar seu CLI Azure no modo BRAÇO

Para usar o modo de gerenciamento de recursos do Azure (ARM) com a CLI do Azure, digite `azure config mode arm`. Você verá algo semelhante ao seguinte:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] Você pode alternar de volta para usar comandos de gerenciamento do serviço Azure digitando `azure config mode asm`.
