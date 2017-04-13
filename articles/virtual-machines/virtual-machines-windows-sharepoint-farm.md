<properties
    pageTitle="Criar server farms SharePoint | Microsoft Azure"
    description="Crie rapidamente um novo farm do SharePoint 2013 ou 2016 do SharePoint no Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="josephd"/>

# <a name="create-sharepoint-server-farms"></a>Criar farms do SharePoint server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo clássico.

## <a name="sharepoint-2013-farms"></a>Farms do SharePoint 2013

Com o Microsoft Azure marketplace do portal, você pode criar rapidamente pré-configurado farms do SharePoint Server 2013. Isso pode salvar você bastante tempo quando precisar de um farm do SharePoint básico ou alta disponibilidade para um ambiente de desenvolvimento/teste ou se você estiver avaliando o SharePoint Server 2013 como uma solução de colaboração para sua organização.

> [AZURE.NOTE] O item de **Farm do SharePoint Server** em do Azure Marketplace do portal do Azure foi removido. Ele foi substituído com o **Farm do SharePoint 2013 não-HA** e o **Farm do SharePoint 2013 HA** itens.

Farm do SharePoint básico consiste em três máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Você pode usar essa configuração do farm para uma instalação simplificada para desenvolvimento de aplicativos do SharePoint ou sua avaliação pela primeira vez do SharePoint 2013.

Para criar o farm do SharePoint (servidor de três) básico:

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Clique em **implantar**.
3. No painel de **Farm do SharePoint 2013 não-HA** , clique em **criar**.
4. Especificar as configurações nas etapas do painel **Criar Farm do SharePoint 2013 não-HA** e clique em **criar**.

Farm do SharePoint alta disponibilidade consiste em nove máquinas virtuais nesta configuração.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Você pode usar essa configuração do farm para testar maiores cargas de cliente, alta disponibilidade externo do site do SharePoint e grupos de disponibilidade do SQL Server AlwaysOn um farm do SharePoint. Você também pode usar esta configuração para desenvolvimento de aplicativos do SharePoint em um ambiente de alta disponibilidade.

Para criar o farm do SharePoint alta disponibilidade (servidor de nove):

1. Clique [aqui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Clique em **implantar**.
3. No painel de **Farm do SharePoint 2013 HA** , clique em **criar**.
4. Especifique as configurações em sete etapas do painel **Criar Farm do SharePoint 2013 HA** e clique em **criar**.

> [AZURE.NOTE] Você não pode criar o **Farm do SharePoint 2013 não-HA** ou **Farm do SharePoint 2013 HA** com uma avaliação gratuita do Azure.

O portal do Azure cria ambas essas farms em uma rede virtual somente na nuvem com uma presença na web voltado para a Internet. Não há nenhuma conexão de VPN ou rota expressa-to-site novamente com a rede de sua organização.

> [AZURE.NOTE] Quando você cria o basic ou farms do SharePoint de alta disponibilidade usando o portal de Azure, você não pode especificar um grupo de recursos existentes. Para contornar essa limitação, crie esses farms com o Azure PowerShell. Para obter mais informações, consulte [criar SharePoint 2013 desenvolvimento/teste farms com PowerShell do Azure](https://technet.microsoft.com/library/mt743093.aspx#powershell).

## <a name="sharepoint-2016-farms"></a>Farms do SharePoint 2016

Consulte [Este artigo](https://technet.microsoft.com/library/mt723354.aspx) para obter as instruções criar o farm do SharePoint Server 2016 único servidor seguinte.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Gerenciando os farms do SharePoint

Você pode administrar os servidores desses farms através de conexões de área de trabalho remota. Para obter mais informações, consulte [logon na máquina virtual](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

Do site do SharePoint de Administração Central, você pode configurar meus sites, aplicativos do SharePoint e outros tipos de funcionalidade. Para obter mais informações, consulte [Configurar o SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Próximas etapas

- Descubra o [SharePoint configurações](https://technet.microsoft.com/library/dn635309.aspx) adicionais em serviços de infraestrutura Azure.
