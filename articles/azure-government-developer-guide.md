<properties 
    pageTitle="Guia de desenvolvedores do governo Azure" 
    description="Isso fornece uma comparação de recursos e orientações sobre como desenvolver aplicativos para o governo do Azure" 
    services="" 
    cloud="gov"
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  <a name="microsoft-azure-government-developer-guide"></a>Guia de desenvolvedor do Microsoft Azure governo 

<p> Microsoft Azure Government é uma física e instância isolada de rede do Microsoft Azure.  Este guia de desenvolvedores fornecerá detalhes sobre as diferenças que os desenvolvedores de aplicativo e administradores precisaria interagir e trabalhar com essas regiões separadas do Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>Neste tópico


+ [Visão geral](#Overview)
+ [Orientação para desenvolvedores](#Guidance)
+ [Recursos disponíveis atualmente no Microsoft Azure Government](#Features)
+ [Mapeamento de ponto de extremidade](#Endpoint)
+ [Próximas etapas](#next)


## <a name="Overview"></a>Visão geral

Microsoft Azure Government é uma instância separada do serviço do Microsoft Azure às necessidades de segurança e conformidade de agências federais dos EUA, estado e governos local e seus provedores de soluções. Governo Azure oferece físico e rede isolamento de implantações de governo fora dos EUA e fornece filtrado pessoal dos EUA. 

A Microsoft fornece um número de ferramentas para criar e implantar aplicativos de nuvem da Microsoft global Microsoft Azure (serviço "Global") e serviços de governo do Microsoft Azure.

Quando criar e implantar aplicativos para os desenvolvedores de serviços de governo do Azure, em vez de serviço Global, precisa saber as principais diferenças dos dois serviços.  Especificamente em torno de instalar e configurar o ambiente de programação, configurando pontos de extremidade, escrever aplicativos e implantá-los como os serviços do governo do Azure.

As informações neste documento resume as diferenças e complementam as informações disponíveis no site do(http://www.azure.com/gov "Governo do Azure") de [Governo do Azure]e a [Biblioteca técnica do Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") no MSDN. Informações oficiais também podem estar disponíveis em muitos outros locais como o [Microsoft Azure Trust Center] (https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center" /), [Centro de documentação do Azure](https://azure.microsoft.com/documentation/) e em [Azure Blogs] (https://azure.microsoft.com/blog/ "Azure Blogs" /). 

Esse conteúdo destina-se para parceiros e desenvolvedores que estão implantando ao Microsoft Azure governo.



## <a name="Guidance"></a>Orientação para desenvolvedores
Porque a maioria do conteúdo técnico que está disponível no momento supõe que aplicativos estão sendo desenvolvidos para o serviço Global, em vez do Microsoft Azure Government, é importante para garantir que os desenvolvedores estejam cientes das principais diferenças para aplicativos desenvolvidos para ser hospedada no governo do Azure.

- Primeiro, existem diferenças de recursos e serviços, isso significa que determinados recursos que estão em regiões específicas do serviço Global podem não estar disponíveis no Azure governamentais.

- Segundo, para recursos que são oferecidos no Azure governo, existem diferenças de configuração do serviço Global.  Portanto, você deve examinar seu código de amostra, configurações e etapas para garantir que está criando e executando dentro do ambiente de serviços de nuvem do Azure governamentais.


## <a name="Features"></a>Recursos disponíveis atualmente no Microsoft Azure Government
Governo Azure atualmente tem os seguintes serviços disponíveis no regiões conosco GOV IOWA e conosco GOV VIRGÍNIA:

- Máquinas virtuais
- Serviços de nuvem
- Armazenamento
- Do Active Directory
- Agendador
- Rede virtual
- Banco de dados SQL
- Arquivos do Azure
- Serviços de mídia
- Gerenciador de tráfego
- Barramento de serviço
- StorSimple
- Relacionada Cache
- Backup Azure
- Automação
- Rota expressa
- etc.

Outros serviços estão disponíveis e mais serviços serão adicionados em uma base contínua.  Para a lista mais atual dos serviços, consulte a [página de regiões](https://azure.microsoft.com/regions/#services) que destacará cada região disponíveis e seus serviços.  

Atualmente, nos GOV Iowa e conosco Virgínia GOV são os centros de dados do Azure governamentais de suporte.  Consulte a página de regiões acima para os centros de dados atual e serviços disponíveis.

## <a name="Endpoint"></a>Mapeamento de ponto de extremidade

Use a tabela a seguir para orientá-lo durante o mapeamento de pontos de extremidade Microsoft Azure e banco de dados do SQL públicos para pontos de extremidade específicos do governo do Azure.


Tipo de serviço|Azure público|Governo Azure
---|---|---
Portal de gerenciamento|Manage.WindowsAzure.com|Manage.WindowsAzure.US
Geral|*. no windows.net|*. usgovcloudapi.net
Principais|*. core.windows.net|*. core.usgovcloudapi.net
Calcular|*. cloudapp.net|*. usgovcloudapp.net
Armazenamento de blob|*. blob.core.windows.net|   *. blob.core.usgovcloudapi.net
Armazenamento de fila|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Armazenamento de tabela|*. table.core.windows.net|*. table.core.usgovcloudapi.net
Gerenciamento de serviço|Management.Core.Windows.NET|Management.Core.usgovcloudapi.NET
Banco de dados SQL|*. database.windows.net|*. database.usgovcloudapi.net
Ponto de extremidade de balanceamento de carga ARM|https://Management.Windows.NET|https://Management.usgovcloudapi.NET  

* Para autenticação de ARM via Azure AD, consulte [Autenticar solicitações de Gerenciador de recursos do Azure](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Próximas etapas

Se você estiver interessado em aprender mais e sobre o Azure governo aproveite alguns dos links abaixo.

- **[Inscrever-se para uma avaliação](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Adquirindo e acessando governo do Azure](http://azure.com/gov)**

- **[Visão geral do governo Azure](/azure-government-overview)**

- **[Blog do governo Azure](http://blogs.msdn.com/b/azuregov/)**

- **[Conformidade Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
