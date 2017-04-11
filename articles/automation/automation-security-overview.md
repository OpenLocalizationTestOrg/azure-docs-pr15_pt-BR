<properties
   pageTitle="Segurança de automação Azure | Microsoft Azure"
   description="Este artigo fornece uma visão geral de segurança de automação e os métodos de autenticação diferentes disponíveis para contas de automação na automação do Azure."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="segurança de automação, automação segura" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="magoedte" />

# <a name="azure-automation-security"></a>Segurança de automação Azure
Automação Azure permite que você automatize tarefas com recursos no Azure, local e com outros provedores de nuvem como Amazon Web Services (AWS).  Em ordem para um runbook executar suas ações necessárias, ele deve ter permissões para acessar os recursos com segurança com os direitos mínimos necessários na assinatura.  
Este artigo abordará os vários cenários de autenticação compatíveis com a automação do Azure e mostrará como começar a trabalhar com base no ambiente ou ambientes que necessárias para gerenciar.  

## <a name="automation-account-overview"></a>Visão geral de conta de automação
Quando você começa a automação do Azure pela primeira vez, você deve criar pelo menos uma conta de automação. Contas de automação permitem isolar seus recursos de automação (runbooks, ativos, configurações) de recursos contidos em outras contas de automação. Você pode usar contas de automação para separar os recursos em ambientes lógicos separadas. Por exemplo, você pode usar uma conta para desenvolvimento, outra para produção e outro para seu ambiente local.  Uma conta de automação do Azure é diferente da sua conta da Microsoft ou contas criadas em sua assinatura do Azure.

Os recursos de automação para cada conta de automação estão associados a uma única região Azure, mas contas de automação podem gerenciar recursos em qualquer região. O motivo principal para criar contas de automação em diferentes regiões seria se você tenha políticas que exigem dados e recursos sejam isolados para uma área específica.

>[AZURE.NOTE]Contas de automação e os recursos que elas contêm que são criados no portal do Azure, não podem ser acessados no portal de clássico do Azure. Se você quiser gerenciar essas contas ou seus recursos com o Windows PowerShell, você deve usar os módulos do Gerenciador de recursos do Azure.

Todas as tarefas que você executar contra recursos usando o Gerenciador de recursos do Azure e cmdlets do Azure no Azure automação devem autenticar Azure usando autenticação baseada em credenciais do Active Directory do Azure identidade organizacional.  Autenticação baseada em certificado foi o método de autenticação original com o modo de gerenciamento de serviço do Azure, mas ele era complicado para a configuração.  Autenticar no Azure com usuário Azure AD foi introduzido back em 2014 não apenas simplificam o processo para configurar uma conta de autenticação, mas também dá suporte a capacidade de autenticar não interativa no Azure com uma única conta de usuário que funcionam com o Gerenciador de recursos do Azure e recursos clássicos.   

Atualmente, quando você cria uma nova conta de automação no portal do Azure, ele cria automaticamente:

-  Executar como conta que cria um novo objeto de serviço do Azure Active Directory, um certificado e atribui o controle de acesso baseado em função de Colaborador (RBAC), que será usado para gerenciar recursos do Gerenciador de recursos usando runbooks.
-  Executar como conta clássica carregando um certificado de gerenciamento, que será usado para gerenciar gerenciamento de serviço do Azure ou recursos clássicos usando runbooks.  

Controle de acesso baseado em função está disponível com o Azure Resource Manager conceder ações permitidas para uma conta de usuário do Azure AD e executar como conta e autenticar esse serviço principal.  Leia o [controle de acesso baseado em função no artigo de automação do Azure](../automation/automation-role-based-access-control.md) para obter mais informações para ajudar a desenvolver seu modelo para gerenciar permissões de automação.  

Runbooks em execução em um operador de Runbook híbrido no seu data center ou contra computação serviços no AWS não pode usar o mesmo método que normalmente é usado para runbooks autenticação de recursos Azure.  Isso ocorre porque esses recursos estão executando fora do Azure e portanto, exigirão suas próprias credenciais de segurança definidos no automação para autenticar para os recursos que eles acessarão localmente.  

## <a name="authentication-methods"></a>Métodos de autenticação

A tabela a seguir resume os métodos de autenticação diferentes para cada ambiente compatíveis com a automação do Azure e o artigo descreve como configurar a autenticação para seus runbooks.

Método  |  Ambiente  | Artigo
----------|----------|----------
Conta de usuário do Azure AD | Gerenciador de recursos de Azure Azure gerenciamento e serviço | [Autenticar Runbooks com conta de usuário do Azure AD](../automation/automation-sec-configure-aduser-account.md)
Executar Azure como conta | Gerenciador de recursos do Azure | [Autenticar Runbooks com conta executar como do Azure](../automation/automation-sec-configure-azure-runas-account.md)
Executar clássico Azure como conta | Gerenciamento de serviço Azure | [Autenticar Runbooks com conta executar como do Azure](../automation/automation-sec-configure-azure-runas-account.md)
Autenticação do Windows | Data Center de local | [Autenticar Runbooks para trabalhadores de Runbook híbrido](../automation/automation-hybrid-runbook-worker.md)
AWS credenciais | Amazon Web Services | [Autenticar Runbooks com Amazon Web Services (AWS)](../automation/automation-sec-configure-aws-account.md)



