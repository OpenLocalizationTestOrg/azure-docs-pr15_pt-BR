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


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>Publicar aplicativos em redes separadas e locais usando grupos de conector - Public Preview

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure portal clássico](active-directory-application-proxy-connectors.md)


Grupos de conector são úteis para vários cenários, incluindo:

- Sites com vários data centers interligados. Nesse caso, você deseja manter tantos tráfego dentro do data center possível porque links de data center cruzada são caro e lenta. Você pode implantar conectores em cada data center para servir somente os aplicativos que residem dentro do data center. Essa abordagem minimiza links de data center cruzado e fornece uma experiência totalmente transparente aos usuários.
- Gerenciando aplicativos instalados em redes isoladas que não fazem parte da rede corporativa principal. Você pode usar grupos de conector instalar conectores dedicados em redes isoladas para isolar também aplicativos à rede.
- Para aplicativos instalados IaaS para acesso à nuvem, grupos de conector fornecem um serviço comum para proteger o acesso a todos os aplicativos. Grupos de conector não criar dependência adicional em sua rede corporativa ou a experiência do aplicativo de fragmento. Conectores podem ser instalados em cada data center nuvem e servem somente os aplicativos que residem nesta rede. Você pode instalar vários conectores para alcançar alta disponibilidade.
- Suporte para vários florestas ambientes em que os conectores específicos podem ser implantados por floresta e definidos para servir aplicativos específicos.
- Grupos de conector podem ser usados em sites de recuperação de dados para detectar ou failover ou como backup para o site principal.
- Grupos de conector também podem ser usados para servir várias empresas de um locatário único.

## <a name="prerequisite-create-your-connectors"></a>Pré-requisito: Criar seu conectores
Para agrupar seus conectores, você tem que garantir que você [instalou vários conectores](active-directory-application-proxy-enable.md). Quando você instala um novo conector, ela automaticamente ingressa no grupo de conector **padrão** .

## <a name="step-1-create-connector-groups"></a>Etapa 1: Criar grupos de conector
Você pode criar grupos de conector quantas como você deseja. Criação de grupo de conector é realizada no [portal do Azure](https://portal.azure.com).

1. Selecione **Active Directory do Azure** para ir para o painel de gerenciamento de seu diretório. A partir daí, selecione **aplicativos corporativos** > **proxy do aplicativo**.

2. Selecione o botão de **Grupos de conector** . A lâmina de novo grupo de conector é exibida.

3. Dê um nome para seu novo grupo de conector e, em seguida, use o menu suspenso para selecionar os conectores pertencem a este grupo.

4. Selecione **Salvar** quando seu grupo de conector for concluído.

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Etapa 2: Atribuir aplicativos a seus grupos de conector
A última etapa é configurar cada aplicativo para o grupo de conector que servirá-lo.

1. No painel de gerenciamento de seu diretório, selecione **aplicativos corporativos** > **todos os aplicativos** > o aplicativo que você deseja atribuir a um grupo de conector > **Proxy do aplicativo**.
2. Em **grupo de conector**, use o menu suspenso para selecionar o grupo que você deseja que o aplicativo para usar.
3. Selecione **Salvar** para aplicar a alteração.


## <a name="see-also"></a>Consulte também

- [Habilitar o Proxy de aplicativo](active-directory-application-proxy-enable.md)
- [Ativar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Solucionar problemas com o Proxy de aplicativo](active-directory-application-proxy-troubleshoot.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
