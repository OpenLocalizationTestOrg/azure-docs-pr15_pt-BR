<properties
   pageTitle="Uso do Microsoft Azure e habilitar de APIs RateCard Cloudyn para fornecer ITFM para clientes | Microsoft Azure"
   description="Fornece uma perspectiva exclusiva de parceiro de cobrança do Microsoft Azure Cloudyn, em suas experiências integrar as APIs de cobrança do Azure seus produtos.  Isso é especialmente útil para clientes do Azure e Cloudyn interessadas em usando/experimentar Cloudyn para serviços do Azure."
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

# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>Uso do Microsoft Azure e APIs RateCard habilitar Cloudyn fornecer ITFM para clientes

Cloudyn, um parceiro de desenvolvimento do Microsoft e um fornecedor líder de recursos de gerenciamento de nuvem, foi escolhido para obter uma visualização particular das novas APIs de RateCard e uso de recursos do Microsoft Azure.  A API de uso fornece acesso aos dados de consumo Azure previsto para uma assinatura. A API RateCard fornece informações sobre preços completas de todos os serviços do Azure, para clientes não - empresariais contrato EA. Integrado juntas, essas APIs fornecem uma base de informações completas para entrada em ferramentas de gerenciamento de TI financeiro (ITFM) como aqueles fornecidos pelo Cloudyn.

## <a name="introduction"></a>Introdução

A "multiplicação" chamada de dados da API do uso com dados da API RateCard (preço de uso [unidades] [$unit] = uso detalhado e custo) cria mais granulares, precisas e confiáveis informações disponíveis para cobrança Azure hoje.

![Visão geral ITFM][1]

Consumir essas APIs fornece informações importantes sobre o uso de clientes e custos, permitindo Cloudyn para analisar as contas do cliente de maneira simple e programação e para realizar várias tarefas ITFM para seus clientes.

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Integração Cloudyn com as APIs de uso e RateCard
A API RateCard requer vários parâmetros de entrada, como informações de região, moeda e localidade – mas o mais importante é OfferDurableID, que especifica o tipo do Azure oferecendo o cliente está usando (pré-pago, planos de compromisso herdados de 6 e 12 meses, MSDN ofertas, MPN ofertas, ofertas promocionais e outros). O OfferDurableID pode ser encontrada no [uso do Azure e o portal de cobrança](https://account.windowsazure.com/Subscriptions), na "oferecer ID" para a assinatura determinada.

Após o registro para serviços de [Cloudyn para Azure](https://www.cloudyn.com/microsoft-azure/) , os clientes podem adicionar código OfferDurableID, que permite Cloudyn retirar suas informações de preços relevantes por meio da API RateCard.  Informações sobre os diferentes tipos de ofertas podem ser encontradas um a página [Detalhes do Microsoft Azure oferecem](https://azure.microsoft.com/support/legal/offer-details/) .

![Visão geral do mecanismo de Cloudyn ITFM][2]

Cloudyn usa o uso e a APIs RateCard, além da API de desempenho do Azure, criar camadas adicionais de visualização, análise, alerta, relatórios, gerenciamento de custos e recomendações viáveis, fornecendo clientes Azure uma ferramenta ITFM de nuvem corporativos confiável.

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Casos de uso de Cloudyn ITFM habilitados por integração de uso e API de RateCard
Comuns Cloudyn ITFM use casos habilitados por uso e as APIs RateCard incluem:

+ **Análise de custo** - permite nuvem custos seja quebrado para baixo até qualquer dimensão identificação nativo (provedor de serviço, conta, região etc.). O uso do Azure e RateCard APIs torná-la uma tarefa fácil, fornecendo o detalhamento mais granular de uso e dados por conta, que é, em seguida, agrupado e filtrado por Cloudyn e apresentado ao usuário, em um formulário de gráfico ou tabela de custo.

![Gráfico de pizza de análise de custo][3]

+ **Custo de alocação 360** - habilita finanças e gerentes de TI para descobrir a divisão de custo real, drivers e tendências de sua implantação de nuvem. Ela ainda mais permite aos gerentes associar facilmente despesas de implantação unidades de negócios, departamentos, regiões e mais, fornecendo precedentes ideias para custos de nuvem e facilitando showbacks e os estornos enterprise. O uso do Azure e RateCard APIs servem como entrada para o mecanismo de alocação de custo do Cloudyn, que complementa as APIs definindo métodos e lógica de negócios para alocar recursos marcados ou untaggable.

![Gráfico de alocação de custos 360][4]

+ **Dimensionamento de econômica** - fornece recomendações de dimensionamento à direita para máquinas virtuais subutilizadas, reduzindo despesas do cliente em máquinas muito grandes ou provisionamento excessivo. Isso é feito examinando máquina virtual CPU e métricas de RAM (via API de desempenho), horas de tempo de execução (via API de uso) e o custo (via API de RateCard). Cloudyn, em seguida, fornece recomendações de dimensionamento à direita com base em recursos de CPU ou RAM subutilizados (desempenho) e calcula economia estimada multiplicando o delta de preço (RateCard) entre as VMs pela (uso) de utilização-tempo real da máquina subutilizada.

![Dimensionamento econômico][5]

+ **Nuvem portabilidade recomendações** - fornece consultoria financeira em nuvem portabilidade. Ela examina custos atuais de um usuário dos recursos de nuvem que são implantados em fornecedores de nuvem principais e compara o custo de uma implantação equivalente no Azure. Ele fornece granular, por recurso, financeira baseado portabilidade recomendações para Azure. Após avaliar a implantação equivalente necessária no Azure (baseado em Preferências de usuário e métricas de desempenho), Cloudyn usa a API RateCard para avaliar o custo da implantação equivalente no Azure.

+ **Relatórios de desempenho** - habilitado por desempenho do Azure API, esses relatórios fornecem uma matriz de recursos de utilização de CPU e memória RAM para recomendações de otimização. Abaixo está um exemplo de relatório de utilização de instância, apresentando detalhamento da instância por utilização média da CPU.

![Relatórios de desempenho][6]

+ **Gerente de categoria** : um poderoso recurso no Cloudyn que traz ordem para recursos de nuvem desorganizada. Ele fornece aos usuários a liberdade de criar suas próprias categorias exclusivas (marcas) para medir eficaz e relatórios que seja alinhado com práticas comerciais. Além disso, os usuários podem facilmente regulamentar e categorizar marcação inconsistente (isto é, erros de digitação e outras discrepâncias) e detectar automaticamente os recursos marcados para atribuição de custo precisos.

![Gerenciador de categoria][7]

## <a name="video"></a>Vídeo

Aqui está um vídeo curto que mostra como um cliente do Azure pode usar Cloudyn do Azure e as APIs de cobrança do Azure, para obter ideias de seus dados de consumo Azure.

> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## <a name="next-steps"></a>Próximas etapas

+ Inicie uma avaliação gratuita do [Cloudyn do Azure](https://www.cloudyn.com/microsoft-azure/) para ver como você pode obter transparência de custo com a análise e relatórios personalizados para sua implantação de nuvem do Microsoft Azure.
+ Para obter uma visão geral do uso de recursos do Azure e RateCard APIs, consulte [obter ideias para seu consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Confira a [Referência da API REST Azure cobrança](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais informações sobre as duas APIs, que fazem parte do conjunto de APIs fornecidas pelo Resource Manager do Azure.
+ Se você gostaria de mergulhar diretamente em código de exemplo, confira nossa Microsoft Azure cobrança API amostras de código em [Amostras de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Saiba Mais
+ Para saber mais sobre ofertas do Microsoft Azure Enterprise Agreement (EA), visite [licenciamento do Azure para a empresa] (https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Consulte o artigo de [Visão geral do Gerenciador de recursos do Azure](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Gerenciador de recursos do Azure.
+ Para obter informações adicionais sobre o pacote de ferramentas necessárias para ajudar a compreender nuvem gastos, consulte o artigo Gartner [Guia de mercado para ferramentas de gerenciamento de TI financeiro (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png
