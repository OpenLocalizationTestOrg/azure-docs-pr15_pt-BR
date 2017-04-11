<properties
    pageTitle="Trabalhando com conectores de Proxy de aplicativo do Azure AD | Microsoft Azure"
    description="Discute como criar e gerenciar grupos de conectores na Proxy de aplicativo do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicativos em redes separadas e locais usando grupos de conector

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure portal clássico](active-directory-application-proxy-connectors.md)


Grupos de conector são úteis para vários cenários, incluindo:

- Sites com vários data centers interligados. Nesse caso, você deseja manter tantos tráfego dentro do data center possível porque links de data center cruzada são geralmente caro e lenta. Você pode implantar conectores em cada data center para servir somente os aplicativos que residem dentro do data center. Essa abordagem minimiza links de data center cruzado e fornece uma experiência totalmente transparente aos usuários.
- Gerenciando aplicativos instalados em redes isoladas que não fazem parte da rede corporativa principal. Você pode usar grupos de conector instalar conectores dedicados em redes isoladas para isolar também aplicativos à rede.
- Para aplicativos instalados IaaS para acesso à nuvem, grupos de conector fornecem um serviço comum para proteger o acesso a todos os aplicativos. Grupos de conector não criar dependência adicional em sua rede corporativa ou a experiência do aplicativo de fragmento. Conectores podem ser instalados em cada data center nuvem e servem somente os aplicativos que residem nesta rede. Você pode instalar vários conectores para alcançar alta disponibilidade.
- Suporte para vários florestas ambientes em que os conectores específicos podem ser implantados por floresta e definidos para servir aplicativos específicos.
- Grupos de conector podem ser usados em sites de recuperação de dados para detectar ou failover ou como backup para o site principal.
- Grupos de conector também podem ser usados para servir várias empresas de um locatário único.

## <a name="prerequisite-create-your-connectors"></a>Pré-requisito: Criar seu conectores
Para agrupar seus conectores, você tem para tornar-se você [instalou vários conectores](active-directory-application-proxy-enable.md)e esse nome você-los e, em seguida, agrupá-los. Por fim, você precisa atribuí-las aos aplicativos específicos.

## <a name="step-1-create-connector-groups"></a>Etapa 1: Criar grupos de conector
Você pode criar grupos de conector quantas como você deseja. Criação de grupo de conector é realizada no portal de clássico do Azure.

1. Selecione seu diretório e clique em **Configurar**.  
    ![Proxy do aplicativo, configurar a captura de tela - clique em gerenciar grupos de conector](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. Em Proxy de aplicativo, clique em **Gerenciar grupos de conector** e criar um novo grupo de conector dando um nome para o grupo.  
    ![Captura de tela - nome do novo grupo de grupos de conector de proxy de aplicativo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Etapa 2: Atribuir conectores seus grupos
Depois que os grupos de conector são criados, mova os conectores para o grupo apropriado.

1. Em **Proxy de aplicativo**, clique em **Gerenciar conectores**.
2. Em **grupo**, selecione o grupo desejado para cada conector. Os conectores pode demorar até 10 minutos se tornem ativas no novo grupo.  
    ![Aplicativo proxy conectores captura de tela - grupo seleto menu suspenso](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Etapa 3: Atribuir aplicativos a seus grupos de conector
A última etapa é configurar cada aplicativo para o grupo de conector que servirá-lo.

1. No portal do clássico Azure, no seu diretório, selecione o aplicativo que você deseja atribuir ao grupo e clique em **Configurar**.
2. Em **grupo de conector**, selecione o grupo que você deseja que o aplicativo para usar. Essa alteração é aplicada imediatamente.  
    ![Aplicativo proxy conector grupo captura de tela - grupo seleto menu suspenso](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## <a name="see-also"></a>Consulte também

- [Habilitar o Proxy de aplicativo](active-directory-application-proxy-enable.md)
- [Ativar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Solucionar problemas com o Proxy de aplicativo](active-directory-application-proxy-troubleshoot.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
