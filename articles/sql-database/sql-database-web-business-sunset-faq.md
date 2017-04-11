<properties
   pageTitle="Perguntas Frequentes do pôr do sol Azure Web de banco de dados do SQL e Business Edition | Microsoft Azure"
   description="Descubra quando os bancos de dados da Web do SQL Azure e Business serão descartados e saiba mais sobre os recursos e funcionalidades de novos níveis de serviço."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# <a name="web-and-business-edition-sunset-faq"></a>Perguntas Frequentes do pôr do sol da Web e Business Edition

Bancos de dados da Web do SQL e Business Azure agora são descartados. As camadas Basic, Standard, Premium e elástica substituem os bancos de dados da Web e Business retiring.

Para ajudá-lo com Atualizando bancos de dados da Web e Business, o serviço de banco de dados SQL recomenda uma camada e desempenho nível de serviço adequado (nível de preço) para cada banco de dados com base na sua carga de trabalho histórica.

**Para obter preços recomendações de nível:**

- [Atualização para V12 de banco de dados do SQL usando o portal do Azure](sql-database-upgrade-server-portal.md)
- [Atualização para V12 de banco de dados do SQL usando o PowerShell](sql-database-upgrade-server-powershell.md)
- [Alterar o nível de preço de um banco de dados da Web ou Business](sql-database-service-tier-advisor.md)



## <a name="why-does-the-azure-portal-show-my-web-and-business-edition-databases-as-retired"></a>Por que o portal do Azure Mostrar minha Web Business edition bancos de dados e como desativado?

Porque Web e Business bancos de dados de edição não estarão disponíveis após setembro de 2015, o portal de etiquetas Web Business bancos de dados e como desativado. O rótulo Descontinuado também fornece um lembrete que bancos de dados da Web e Business devem ser atualizados para padrão, Basic ou Premium. Para obter informações detalhadas sobre como atualizar bancos de dados da Web ou Business existentes para novos níveis de serviço, consulte [atualizar para V12 de banco de dados do SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="which-new-service-tier-is-the-best-choice-to-upgrade-my-existing-web-or-business-database-to"></a>Qual novo nível de serviço é a melhor opção para atualizar meu banco de dados existente da Web ou Business para?

Selecionar um novo serviço camada e desempenho nível apropriado para o seu banco de dados da Web ou Business existente depende os requisitos de recursos e desempenho específico para seu aplicativo.

Use as recomendações de camada preços descritas acima, ou para obter informações detalhadas para ajudá-lo selecionando um novo nível de serviço apropriado, consulte [atualizar para V12 de banco de dados do SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="why-is-microsoft-introducing-new-service-tiers"></a>Por que a Microsoft está introduzindo novos níveis de serviço?

Com base nos comentários do cliente, Azure SQL Database está introduzindo novos níveis de serviço para ajudar clientes mais facilmente suportar cargas de trabalho de banco de dados relacional. Os novos níveis foram projetados para oferecer o desempenho previsível entre uma variedade de sete níveis de simplificada para exigências de aplicativo de transações ativado. Além disso, os novos níveis oferecem uma variedade de recursos continuidade de negócios, um SLA atividade mais forte, tamanhos maiores de banco de dados para menos dinheiro e uma experiência aprimorada de cobrança.

## <a name="where-can-i-learn-more-about-the-new-service-tiers"></a>Onde posso saber mais sobre os novos níveis de serviço?

Para obter informações detalhadas sobre os novos níveis de serviço e o modelo de desempenho, consulte [níveis de serviço](sql-database-service-tiers.md). Para obter informações detalhadas de preços para os novos níveis de serviço, consulte [preços de banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="what-features-or-functionality-will-not-be-available-in-basic-standard-and-premium"></a>Quais recursos ou funcionalidade não estará disponível em Basic, Standard e Premium?

O recurso de agrupamentos será desativado com as edições de negócios e da Web. Os clientes que precisam de escala-out seus bancos de dados serão recomendados em vez disso, usar ou migrar para [Ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md) para [Banco de dados do SQL Azure](sql-database-elastic-scale-get-started.md), que simplifica a criação e gerenciamento de um aplicativo que usa fragmentação. Uma biblioteca de cliente .NET permite que aplicativos definir como os dados são mapeados para as solicitações OLTP fragmentos e rotas para bancos de dados apropriados. Para dar suporte a operações de gerenciamento que reconfigurar como os dados são distribuídos entre fragmentos, um modelo de serviço de nuvem Azure está incluído que você pode hospedar em sua própria assinatura do Azure. Além das [Ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md), Microsoft continuará a criar e publicar [orientações de padrões e práticas de fragmentação personalizada](https://msdn.microsoft.com/library/azure/dn764977.aspx) com base em descobertas dos contratos com clientes profunda. Novos clientes que precisam de escala funcionalidade devem fazer check-out de [Ferramentas de banco de dados elástica](sql-database-elastic-scale-get-started.md) e/ou contate o Microsoft Support para avaliar suas opções.

Microsoft também está mudando a experiência de cópia do banco de dados com bancos de dados Premium. Anteriormente como cota de banco de dados de premium era limitada, crie banco de dados... COMO uma cópia de na T-SQL criado um banco de dados Premium suspenso sem recursos reservados, que foi cobrada de acordo com a mesma taxa um banco de dados de negócios. Como cota de premium agora está disponível mais gratuitamente, suspenso Premium não é mais suportada. Cópias de banco de dados serão criadas com a mesma edição e o nível de desempenho como fonte e serão cobradas adequadamente. Os clientes podem escolher downgrade bancos de dados copiados para um nível de desempenho ou nível de serviço diferente de reduzir o custo se desejado. Bancos de dados existentes suspenso Premium serão convertidos para Business edition como parte nesta versão. Está previsto que a introdução de [Restaurar no momento](sql-database-recovery-using-backups.md#point-in-time-restore) serão reduzir a necessidade de fazer cópias de backup de bancos de dados.

## <a name="how-does-basic-standard-and-premium-improve-my-billing-experience"></a>Como Basic, Standard e Premium aumenta a minha experiência cobrança?

Básico, padrão, bancos de dados do SQL Azure de Premium são cobrados por hora e você tem a capacidade de dimensionar cada banco de dados para cima ou para baixo. Você será cobrado a uma taxa fixa com base no nível mais alto serviço camada e desempenho que escolher para cada hora. Além disso, os níveis de desempenho (exemplo: Basic, S1 e P2) são divididos na fatura para tornar mais fácil ver o número de dias de banco de dados/horas incorridos em um único mês para cada nível de desempenho. Bancos de dados da Web e Business continuam será cobrado usando unidades de banco de dados com base no tamanho do banco de dados. Visite a [página de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) para saber mais sobre os preços e as diferenças entre os níveis de serviço de novo.


## <a name="see-also"></a>Consulte também

[Banco de dados do SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Web e preços de negócios](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Níveis de serviço](sql-database-service-tiers.md)

[Atualizar para V12 de banco de dados do SQL Azure](sql-database-upgrade-server-portal.md)
