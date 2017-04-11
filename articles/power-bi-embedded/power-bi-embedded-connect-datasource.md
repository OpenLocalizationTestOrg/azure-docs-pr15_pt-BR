<properties
   pageTitle="Inseridos do Microsoft Power BI - conectando a uma fonte de dados"
   description="Power BI inserida, conectar-se a fontes de dados"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="connect-to-a-data-source"></a>Conectar a uma fonte de dados

Com o **Power BI inserida**, você pode inserir relatórios em seu próprio aplicativo. Quando você incorpora um relatório do Power BI em seu aplicativo, o relatório se conecta aos dados subjacentes **importando** uma cópia dos dados ou **conectar-se diretamente** à fonte de dados usando o **DirectQuery**.

Aqui estão as diferenças entre usar **importação** e **DirectQuery**.

|Importar | DirectQuery
|---|---
|Tabelas, colunas *e os dados* serão importadas ou copiadas para o conjunto de dados do relatório. Para ver as alterações que ocorreram os dados subjacentes, você deve atualizar ou importar, um completo atual dataset novamente.|Somente *tabelas e colunas* são importadas ou copiado para o conjunto de dados do relatório. Você sempre pode exibir os dados mais atuais.
Com o Power BI incorporados, você pode usar DirectQuery com fontes de dados, mas não em fontes de dados locais neste momento.

## <a name="benefits-of-using-directquery"></a>Benefícios do uso de DirectQuery

Há duas vantagens principais ao usar **DirectQuery**:

   -    **DirectQuery** permite que você criar visualizações sobre conjuntos de dados muito grandes, onde caso contrário seria inviável para importar primeiro todos os dados.
   -    Alterações nos dados de base pode exigir uma atualização de dados e para alguns relatórios, a necessidade de exibir dados atuais exigem transferências de dados grandes, tornando novamente importando dados inviável. Por outro lado, **DirectQuery** relatórios sempre usar dados atual.

## <a name="limitations-of-directquery"></a>Limitações de DirectQuery

   Existem algumas limitações ao uso **DirectQuery**:

   -    Todas as tabelas devem ser de um banco de dados.
   -    Se a consulta é muito complexa, ocorrerá um erro. Para corrigir o erro, você deve refatorar a consulta para que ela é menos complexa. Se o quuery deve ser complexa, você precisará importar os dados em vez de usar **DirectQuery**.
   -    Filtragem de relação está limitado a uma única direção, ao invés de ambas as direções.
   -    Você não pode alterar o tipo de dados de uma coluna.
   -    Por padrão, as limitações são colocadas em expressões DAX permitidas em medidas. Consulte [DirectQuery e medidas](#measures).

<a name="measures"/>
## <a name="directquery-and-measures"></a>DirectQuery e medidas

Para garantir consultas enviadas a fonte de dados subjacente desempenho aceitável, limitações são impostas em medidas. Quando usando o **Power BI Desktop**, os usuários avançados pode optar por ignorar essa limitação escolhendo **arquivo > Opções e configurações > Opções**. Na caixa de diálogo **Opções** , escolha **DirectQuery**e selecione a opção **Permitir medidas irrestritas no modo DirectQuery**. Quando essa opção estiver marcada, qualquer expressão DAX que é válido para uma medida pode ser usado. Os usuários devem estar cientes; No entanto, que algumas expressões que executam muito bem quando a importação dos dados pode resultar em consultas muito lentas com a fonte de back-end quando no modo **DirectQuery** . 

## <a name="see-also"></a>Consulte também
- [Introdução ao Microsoft Power BI inserida](power-bi-embedded-get-started.md)
- [Área de trabalho do Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
