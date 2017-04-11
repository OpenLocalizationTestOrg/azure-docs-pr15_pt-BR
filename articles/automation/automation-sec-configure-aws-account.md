<properties
   pageTitle="Configurar a autenticação com Amazon Web Services | Microsoft Azure"
   description="Este artigo descreve como criar e validar uma credencial AWS para runbooks na automação do Azure Gerenciando AWS recursos."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="autenticação de aws, configurar aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar Runbooks com Amazon Web Services
Automatizar tarefas comuns com recursos no Amazon Web Services (AWS) pode ser feito com a automação runbooks no Azure.  Você pode automatizar muitas tarefas no AWS usando automação runbooks exatamente como você pode com recursos no Azure.  Tudo o que é necessário são duas coisas:

* Uma assinatura de AWS e um conjunto de credenciais.  Especificamente sua chave de acesso AWS e chave secreta.  Para obter mais informações, consulte o artigo [Usando AWS credenciais](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Uma assinatura do Azure e a conta de automação.  Para obter mais informações sobre como configurar uma conta de automação do Azure, consulte o artigo [Configurar executar como conta do Azure](../automation/automation-sec-configure-azure-runas-account.md).  

Para autenticar com AWS, você deverá especificar um conjunto de credenciais AWS para autenticar seus runbooks executando de automação do Azure. Se você já tiver uma conta de automação criada e você deseja usá-lo para autenticar com AWS, você pode seguir as etapas na seção a seguir.  Se você quiser dedicada uma conta para recursos do runbooks direcionando AWS, você deve primeiro criar uma nova [conta de automação executar como](../automation/automation-sec-configure-azure-runas-account.md) (ignorar a opção para criar uma entidade de serviço) e, em seguida, siga as etapas abaixo.

## <a name="configure-automation-account"></a>Configurar conta de automação
De automação do Azure para se comunicar com AWS, primeiro será necessário recuperar suas credenciais AWS e armazená-los como ativos na automação do Azure.  Execute as seguintes etapas documentadas no documento AWS [Gerenciando teclas de acesso para sua conta de AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para criar uma tecla de acesso e copie a **Identificação de tecla de acesso** e uma **Tecla de acesso de segredo** (opcionalmente baixar o arquivo de chave para armazená-la em um lugar seguro).

Depois que você criou e copiados suas chaves de segurança AWS, você precisará criar um ativo de credencial com uma conta de automação do Azure para armazená-los com segurança e referência-los com seus runbooks.  Siga as etapas na seção **para criar uma nova credencial** no artigo [ativos de credencial na automação do Azure](../automation/automation-certificates.md/###To create a new credential with the Azure portal) e insira as seguintes informações:

1. Na caixa **nome** , digite **AWScred** ou um valor apropriado seguindo seus padrões de nomenclatura.  
2. Na caixa **nome de usuário** , digite sua **ID de acesso** e a **Tecla de acesso de segredo** na caixa **senha** e **Confirmar senha** .   

## <a name="next-steps"></a>Próximas etapas

- Reivew o artigo de solução de [implantação de automatizar de uma máquina virtual no Amazon Web Services](../automation/automation-scenario-aws-deployment.md) para aprender a criar runbooks para automatizar tarefas no AWS.
