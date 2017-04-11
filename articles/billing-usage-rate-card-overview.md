<properties
   pageTitle="Obter ideias para seu consumo de recursos do Microsoft Azure | Microsoft Azure"
   description="Fornece uma visão conceitual do uso de cobrança do Azure e APIs RateCard, que são usados para fornecer ideias para consumo de recursos do Azure e tendências."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>Obter ideias para seu consumo de recursos do Microsoft Azure

Clientes e parceiros exigem a capacidade de prever com precisão e gerenciar seus custos do Azure.  Como eles movimentem de uma Capex a um modelo de Opex, eles também precisam a capacidade de fazer showback versus análise de devolução, bem como fornecem fidelidade de modo no estimativa e cobrança, especialmente para implantações de nuvem grande.

O uso de recursos do Azure e APIs de cartão de taxa discutido no endereço neste artigo essas necessidades, permitindo que novas ideias para seu consumo de recursos Azure.  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>Apresentando o uso do recurso Azure e APIs do RateCard

O uso de recursos do Azure e RateCard APIs são implementadas como um provedor de recursos, como parte da família de APIs expostas pelo Resource Manager do Azure.  

### <a name="azure-resource-usage-api-preview"></a>Uso do recurso Azure API (prévia)
Clientes e parceiros podem usar a API de uso de recursos do Azure para obter seus dados de consumo Azure previsto. Os recursos incluem:

- **Controle de acesso baseado em função do azure** - clientes e parceiros pode configurar suas políticas de acesso no [portal do Azure](https://portal.azure.com) ou por meio de [cmdlets do PowerShell do Azure](powershell-install-configure.md) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Active Directory do Azure para autenticação. O chamador também deve ser adicionado à função Leitor, proprietário ou colaborador para obter acesso a dados de uso para uma assinatura Azure específica.

- **Por hora ou agregações diária** - chamadores podem especificar se desejam que seus dados de uso Azure em compartimentos de memória diários ou classificações por hora. O padrão é diário.

- **Metadados da instância fornecido (inclui marcas de recurso)** – detalhes de nível de instância como o uri do recurso totalmente qualificado (/subscriptions/ {id da assinatura} / …), junto com o recurso marcas de recursos e informações de grupo serão fornecidas na resposta. Isso ajudar os clientes de forma determinista e alocar programaticamente uso pelas marcas, para casos de uso como charging cruzado.

- **Metadados de recurso fornecidos** - detalhes do recurso como o nome do medidor, medidor categoria, medidor subcategoria, unidade e região também serão passados na resposta, dar os chamadores um melhor entendimento do que foi consumida. Estamos também trabalhando para alinhar terminologia de metadados do recurso em portal do Azure, uso Azure CSV, EA CSV e outras experiências público, para permitir que os clientes correlação dados em experiências de cobrança.

- **Uso para todos os tipos de oferta** – dados de uso serão acessíveis para todos oferecem tipos incluindo pré-pago, MSDN, compromisso monetário, crédito monetário e EA entre outros.

### <a name="azure-resource-ratecard-api-preview"></a>Recurso Azure RateCard API (prévia)
Clientes e parceiros podem usar a API do Azure recurso RateCard para obter a lista de recursos Azure disponíveis, juntamente com estimado preços informações para cada. Os recursos incluem:

- **Controle de acesso baseado em função do azure** - clientes e parceiros pode configurar suas políticas de acesso no [portal do Azure](https://portal.azure.com) ou por meio de [cmdlets do PowerShell do Azure](powershell-install-configure.md) para especificar quais usuários ou aplicativos podem obter acesso aos dados RateCard. Os chamadores devem usar tokens padrão do Active Directory do Azure para autenticação. O chamador também deve ser adicionado à função Leitor, proprietário ou colaborador para obter acesso a dados de uso para uma assinatura Azure específica.

- **Suporte para pré-pago, MSDN, compromisso monetário e crédito monetário oferece (EA sem suporte)** - este API fornece informações de taxa de nível de oferta Azure, versus nível de assinatura.  O chamador desta API deve passar as informações de oferta para obter taxas e detalhes do recurso.  Como EA ofertas personalizou taxas por inscrição, estamos incapaz de oferecer as taxas EA neste momento.

## <a name="scenarios"></a>Cenários

Aqui estão alguns dos cenários que são feitos possíveis com a combinação de uso e as APIs RateCard:

- **Azure passam durante o mês** - os clientes podem usar o uso e RateCard APIs em combinação para obter ideias melhores para seu nuvem passam durante o mês, analisando as classificações de horários e diárias das estimativas de uso e encargo.

- **Configurar alertas** – clientes e parceiros pode configurar alertas baseados em monetário ou recurso em seu consumo de nuvem obtendo o consumo previsto e estimativa de encargo utilizando o uso e a API de RateCard.

- **Fatura Predict** – clientes e parceiros possam acessar seu consumo previsto e nuvem gasto e aplicar algoritmos de aprendizado de máquina para prever qual seria sua fatura no final do ciclo de cobrança.

- **Análise de custo de pré-consumo** – cliente pode também usar a API RateCard prever quanto sua fatura seria se estivessem mover as cargas de trabalho para o Azure, por fornecendo desejado números de uso. Se os clientes têm cargas de trabalho existentes em outros nuvens ou nuvens privadas, eles também podem mapear seu uso com o Azure passam de taxas para obter uma melhor estimativa das sua Azure estimado. Isso fornece um modo de exibição aprimorado do que pode ser obtido através do [Azure Calculadora de preços](https://azure.microsoft.com/pricing/calculator/), como (por exemplo) nossos parceiros de cobrança fornecem a capacidade de oferta de tabela dinâmica e comparar/contraste entre os tipos de oferta diferentes além pré-pago, incluindo compromisso monetário e crédito monetário. As APIs também fornecem a capacidade de custo estimativa por região, permitindo que o tipo de hipóteses necessária para tomar decisões de implantação, como recursos implantando em controladores de domínio diferentes do mundo podem ter um impacto direto em custo total.

- **Testes de hipóteses** -

    - Clientes e parceiros podem determinar se seria mais econômico para executar as cargas de trabalho em outra região, ou em outra configuração do recurso Azure. Recurso Azure custos podem ser diferentes com base no Azure região em que estão sendo executados e isso permite que clientes e parceiros obter as otimizações de custo.

    - Clientes e parceiros também podem determinar se outro tipo de oferta Azure oferece uma melhor taxa em um recurso Azure.

## <a name="partner-solutions"></a>Soluções de parceiros

[Uso do Microsoft Azure e RateCard APIs habilitar Cloudyn para fornecer ITFM para clientes que](billing-usage-rate-card-partner-solution-cloudyn.md) descreve a experiência de integração oferecida pelo parceiro do Azure cobrança API [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  Este artigo fornece cobertura detalhada das suas experiências, incluindo um vídeo curto que mostra como um cliente do Azure pode usar Cloudyn e APIs de cobrança do Azure para obtenção de informações de ganhos de seus dados de consumo Azure.

[Cruiser de nuvem e a integração de API de cobrança do Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) descreve como [da nuvem Cruiser Express para pacote Azure](http://www.cloudcruiser.com/partners/microsoft/) funciona diretamente no portal de WAP, permitindo que os clientes perfeitamente gerenciar os aspectos operacionais e financeiros da seu Microsoft Azure privado ou hospedado público em nuvem de uma única interface de usuário.   

## <a name="next-steps"></a>Próximas etapas
+ Confira a [Referência da API REST Azure cobrança](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais detalhes sobre as duas APIs, que fazem parte do conjunto de APIs fornecidas pelo Resource Manager do Azure.
+ Se você gostaria de mergulhar diretamente em código de exemplo, confira nossa Microsoft Azure cobrança API amostras de código em [Amostras de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Saiba Mais
+ Consulte o artigo de [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Gerenciador de recursos do Azure.
+ Para obter informações adicionais sobre o pacote de ferramentas necessárias para ajudar a compreender nuvem gastos, consulte o artigo Gartner [Guia de mercado para ferramentas de gerenciamento de TI financeiro (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).
