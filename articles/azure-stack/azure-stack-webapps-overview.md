<properties
    pageTitle="Visão geral de aplicativos da Web de pilha Azure | Microsoft Azure"
    description="Visão geral dos aplicativos Web na pilha Azure"
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
    
# <a name="azure-stack-web-apps-overview"></a>Visão geral do Azure pilha Web Apps
    
> [AZURE.NOTE] As informações a seguir se aplica somente implantações de TP1 de pilha do Azure.

Azure pilha Web Apps é o primeiro elemento a oferta de serviço de aplicativo do Azure trouxe para pilha do Azure. O instalador do Azure pilha Web Apps criará uma instância de cada um dos cinco tipos de função necessários e também criará um servidor de arquivos. Embora seja possível adicionar mais instâncias para cada um dos tipos de função, lembre-se de que não há muito espaço para VMs em Technical Preview 1. Os recursos atuais Azure pilha Web Apps são principalmente os recursos de foundation que são necessários para gerenciar aplicativos da web sistema e host.

![Pilha Azure serviço de aplicativo Web Apps no Azure empilhar Portal][1]

## <a name="limitations-of-the-technical-preview"></a>Limitações do Technical Preview

Não há nenhum suporte para as versões de visualização de serviço de aplicativo do Azure pilha. Não coloque cargas de trabalho de produção sobre este release preview. Também há nenhuma atualização entre versões de visualização de serviço de aplicativo do Azure pilha. Principais finalidades dessas versões de visualização são para mostrar o que estamos fornecendo e para obter comentários. 

## <a name="what-is-an-app-service-plan"></a>O que é um plano de serviço de aplicativo?

O provedor de recursos do Azure pilha Web Apps usa o mesmo código que usa o recurso de Azure Web Apps no serviço de aplicativo do Azure. Como resultado, alguns conceitos comuns estão patrimônio descrevendo. Em aplicativos Web, o contêiner de preços para aplicativos web é chamado o plano de serviço de aplicativo. Ele representa o conjunto de dedicado máquinas virtuais usadas para manter seus aplicativos. Dentro de uma determinada assinatura, você pode ter vários planos de serviço de aplicativo. Isso também é verdadeiro nos aplicativos Web do Azure pilha. 

No Azure, há trabalhadores compartilhados e dedicados. Um trabalhador compartilhado oferece suporte à hospedagem de aplicativo de alta densidade web vários locatários e há apenas um conjunto de operadores de compartilhado. Servidores dedicados somente são usados por um locatário e vêm em três tamanhos: pequeno, médio e grande. Às necessidades dos clientes locais sempre não podem ser descritas usando esses termos. No Azure pilha Web Apps, administradores de provedor de recursos podem definir os níveis de trabalho que desejam tornar disponíveis tal forma que eles tenham vários conjuntos de trabalhadores compartilhados ou diferentes conjuntos de trabalhadores dedicados com base em sua necessidades de hospedagem na web exclusiva. Usando as definições de nível de trabalho, ela podem definir seus próprios preços SKUs.

## <a name="portal-features"></a>Recursos de portal

Como também acontece com back-end, o Azure pilha Web Apps usa a mesma interface de usuário que usa Azure Web Apps. Alguns recursos serão desabilitados e não ainda são funcionais na pilha do Azure. Isso ocorre porque expectativas específicas do Azure ou serviços que esses recursos exigem não estão disponíveis ainda na pilha do Azure. 

## <a name="next-steps"></a>Próximas etapas

- [Antes de começar com o Azure pilha Web Apps](azure-stack-webapps-before-you-get-started.md)
- [Instalar o provedor de recursos de aplicativos Web](azure-stack-webapps-deploy.md)

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md), como o [provedor de recursos do SQL Server](azure-stack-sql-rp-deploy-short.md) e o [provedor de recursos de MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-overview/AppService_Portal.png
