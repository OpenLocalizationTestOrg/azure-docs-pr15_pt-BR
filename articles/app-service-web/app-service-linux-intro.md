<properties 
    pageTitle="Introdução ao serviço de aplicativo no Linux | Microsoft Azure" 
    description="Saiba mais sobre o serviço de aplicativo no Linux." 
    keywords="serviço de aplicativo do Azure, linux, oss"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="introduction-to-app-service-on-linux"></a>Introdução ao serviço de aplicativo no Linux
Serviço de aplicativo no Linux está na visualização pública e oferece suporte para aplicativos de web em execução nativamente no Linux. 

## <a name="overview"></a>Visão geral ##
Clientes podem usar o serviço de aplicativo no Linux aos aplicativos da web de host nativamente no Linux para pilhas de aplicativos com suporte. A seção de recursos a seguir lista as pilhas de aplicativos com suporte no momento.

## <a name="features"></a>Recursos ##
Serviço de aplicativo no Linux atualmente oferece suporte as seguir pilhas de aplicativos

- Node
- PHP

Os clientes podem implantar seus aplicativos usando

- FTP.
- Gito local.
- GitHub ou BitBucket.

Para dimensionar de aplicativo


- Os clientes podem expandir seus aplicativos web para cima e para baixo, alterando o nível no seu plano de serviço de aplicativo. 
- Os clientes podem dimensionar seus aplicativos e executar seu aplicativo em várias instâncias dentro dos limites do seu SKU.

Kudu algumas das funcionalidades básicas trabalhará

- Ambiente.
- Implantações.
- Console básico.

## <a name="limitations"></a>Limitações ##

O portal de gerenciamento Azure apenas mostrará recursos atualmente com suporte para o serviço de aplicativo no Linux e ocultar o restante. Como nossa equipe habilitando recursos mais nós será manter refletir isso no portal de gerenciamento. Alguns recursos como integração de VNET e AAD / autenticação de terceiros ou extensões de site Kudu não funcionam no momento. Mas, como podemos obter essas trabalhando atualizaremos nosso blog sobre alterações e documentação.

Essa visualização pública atualmente só está disponível nas regiões a seguir

-   Oeste EUA.
-   Europa Oeste.
-   Sudeste Asiático.

Web app no Linux é suportado apenas nas dedicado planos de serviço de aplicativo e não tem um nível livre ou compartilhado. Além disso, os planos de serviço de aplicativo para regular e Linux web apps são mutuamente exclusivos, portanto não é possível criar um aplicativo da web de Linux em um plano de serviço de aplicativo não-Linux.

Aplicativo da Web no Linux deve ser criado em um grupo de recursos que não contenha não-Linux web apps na mesma região.

Devido a falta de reciclagem sobreposto dos aplicativos web, os clientes devem esperar que um tempo de inatividade pequeno em caso de um aplicativo web tem reiniciado. 

## <a name="next-steps"></a>Próximas etapas ##

Siga os links a seguir para começar a usar o serviço de aplicativo no Linux. Envie dúvidas e preocupações em [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Criar aplicativos Web no aplicativo de serviço no Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Usar a configuração de PM2 para Node nos aplicativos Web no Linux](./app-service-linux-using-nodejs-pm2.md)

