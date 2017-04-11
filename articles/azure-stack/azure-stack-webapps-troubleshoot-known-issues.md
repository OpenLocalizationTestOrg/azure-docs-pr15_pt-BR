<properties
    pageTitle="Aplicativos na pilha Azure - problemas conhecidos e solução de problemas da Web | Microsoft Azure"
    description="Orientações detalhadas para implantar Web Apps em pilha do Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="web-apps-resource-provider---known-issues-and-troubleshooting"></a>Provedor de recursos de aplicativos Web - problemas conhecidos e solução de problemas

> [AZURE.NOTE] As informações a seguir se aplica somente implantações de TP1 de pilha do Azure.

Se você tiver problemas durante a implantação ou trabalhar com Web Apps em pilha Azure consulte as orientações abaixo.

## <a name="azure-stack-web-apps-not-available-in-the-portal"></a>Azure pilha Web Apps não está disponível no portal

![Pilha Azure Web Apps criar novo aplicativo Web][1]

Depois de concluir o [registro do provedor de aplicativos Web do Azure pilha](azure-stack-webapps-deploy.md#register-the-newly-deployed-azure-stack-web-apps-provider-with-arm) se você não puder encontrar a Web + blade móvel, tente o seguinte:
* **Faça logout do portal** e **Feche o navegador** e, em seguida, em um **novo navegador janela faça logon no portal** e tente novamente.

Se você ainda não conseguir ver a web e móvel blade, tente o seguinte:

1.  Na **Máquina de Host do Azure pilha** no **Gerenciador Hyper-V** localize o **xRPVM** e **conectar-se** para a máquina virtual.
2.  Abra um **prompt de comando como administrador** e execute **IISRESET**
3.  Retornar para o **ClientVM** e abra uma **nova janela do navegador**, **Faça logon no portal** e tente novamente.

## <a name="deployment-fails-when-creating-a-web-app-in-a-new-resource-group"></a>Implantação Falha ao criar um aplicativo Web em um novo grupo de recursos

Na primeira visualização dos aplicativos da Web, **Todos os aplicativos Web** devem ser criados no mesmo grupo de recursos como o provedor de recursos de aplicativos Web (**AppService-LOCAL**).  Este é um problema conhecido e será resolvido em uma visualização futura.

## <a name="other-issues"></a>Outros problemas

Se você encontrar outros problemas com o Web Apps em pilha Azure envie no [Azure pilha fórum] (https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) onde membros da nossa equipe estão monitorando postagens.


<!--Image references-->
[1]: ./media/azure-stack-webapps-troubleshoot-known-issues/NewWebandMobile.png



<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
