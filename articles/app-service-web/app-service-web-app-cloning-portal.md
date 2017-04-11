<properties
    pageTitle="Clonagem de aplicativo Web usando o Portal do Azure"
    description="Saiba como clonar seus aplicativos da Web para novos aplicativos Web usando o Portal do Azure."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Aplicativo de serviço de aplicativo Azure clonagem usando o Portal do Azure#

O recurso de clonagem do [Azure aplicativo de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) permite que você facilmente clonar aplicativos da web existente para um aplicativo recém-criado em uma região diferente ou na mesma região. Isso permitirá aos clientes implantar um número de aplicativos em diferentes regiões rapidez e facilidade.

Clonagem de aplicativo é atualmente suporte apenas para os planos de serviço de aplicativo de nível premium. O novo recurso usa as mesmas limitações como recurso de Backup de aplicativos Web, consulte [fazer backup de um aplicativo web no serviço de aplicativo do Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>Clonagem um aplicativo existente ##

O aplicativo web deve estar executando no modo **Premium** , para que você crie um clonar para o aplicativo web.

1. No [Portal do Azure](https://portal.azure.com/), abra blade do web app.
2. Clique em **Ferramentas**. Na lâmina **Ferramentas** , clique em **Aplicativo clonar**.

    ![][1]

    > [AZURE.NOTE]
    > Se o web app não ainda estiver no modo **Premium** , você receberá uma mensagem indicando os modos suportados para clonagem do aplicativo. Neste ponto, você tem a opção de selecionar **Atualizar**.
    
3. Na lâmina **Clonar aplicativo** forneça um nome do novo aplicativo web, grupo de recursos e plano de serviço de aplicativo. Também o usuário poderá optar por clonar um número de configurações de fonte de aplicativo web ou não.

    ![][2]

4. Após clicar em **criar** a plataforma começarão a funcionar sobre como criar um clonar do aplicativo web fonte.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonagem um aplicativo existente para um ambiente de serviço de aplicativo##

Na lâmina **Clonar aplicativo** cliente terá a opção de escolher um pool de aplicativos em um ambiente de serviço de aplicativo existente.

## <a name="current-restrictions"></a>Restrições atuais ##

Este recurso está atualmente no modo de visualização, estamos trabalhando para adicionar novos recursos ao longo do tempo, a lista a seguir estão as restrições conhecidas o suporte atual de aplicativo clonagem no Portal do Azure:

- Configurações do Gerenciador de tráfego Azure não são duplicadas
- Configurações de escala automática não são duplicadas
- Configurações de agendamento de backup não são duplicadas
- Configurações de VNET não são duplicadas
- Obtenção de informações de aplicativo não está automaticamente configuradas do aplicativo da web de destino
- Configurações de Auth fácil não são duplicadas
- Extensão de kudu não são duplicado
- Dica regras não são duplicadas
- Conteúdo do banco de dados não são duplicado


### <a name="references"></a>Referências ###
- [Clonagem de aplicativo Web usando o PowerShell](app-service-web-app-cloning.md)
- [Fazer backup de um aplicativo web no serviço de aplicativo do Azure](web-sites-backup.md)
- [Como criar um ambiente de serviço de aplicativo](app-service-web-how-to-create-an-app-service-environment.md)
- [Criar um aplicativo web em um ambiente de serviço de aplicativo](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [Introdução ao ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png