<properties
    pageTitle="Exemplo de dados em tabelas do Azure HDInsight seção | Microsoft Azure"
    description="Para baixo amostragem dados em tabelas de seção de Azurehdinsight (Hadopop)"
    services="machine-learning,hdinsight"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />

# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo na seção do Azure HDInsight tabelas

Neste artigo, vamos descrevem como amostra down dados armazenados nas tabelas do Azure HDInsight seção usando consultas de seção. Cobriremos três métodos de amostragem popularmente usados:

* Amostragem aleatória uniforme
* Amostragem aleatória por grupos
* Amostragem stratified

**Por que seus dados de exemplo?**
Se o conjunto de dados que você planeja analisar for grande, geralmente é uma boa ideia amostra busca os dados para reduzi-la para um tamanho menor, mas representante e mais gerenciável. Isso facilita a compreensão de dados, exploração e engenharia de recurso. Sua função no processo de ciência de dados de equipe é permitir criação de protótipo rápida das funções de processamento de dados e modelos de aprendizado de máquina.

No **menu** abaixo links para tópicos que descrevem como dados de exemplo do vários ambientes de armazenamento.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarefa de amostragem é uma etapa do [Processo de ciência de dados da equipe (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="how-to-submit-hive-queries"></a>Como enviar consultas de seção
Consultas de seção podem ser enviadas no console de linha de comando do Hadoop no nó principal do cluster Hadoop. Para fazer isso, faça logon em nó principal do cluster Hadoop, abra o console de linha de comando do Hadoop e enviar as consultas de seção de lá. Para obter instruções sobre como enviar consultas de seção no console de linha de comando do Hadoop, veja [como enviar consultas de seção](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="uniform"></a>Amostragem aleatória uniforme
Amostragem aleatória uniforme significa que cada linha no conjunto de dados tem a mesma chance de sendo de amostra. Isso pode ser implementado adicionando um aleatório de campo extra () ao conjunto de dados na consulta interna "select" e na consulta externa "select" essa condição nesse campo aleatório.

Aqui está uma consulta de exemplo:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Aqui, `<sample rate, 0-1>` Especifica a proporção de registros que os usuários desejam retirar uma amostra.

## <a name="group"></a>Amostragem aleatória por grupos

Quando dados categóricos amostragem, talvez você queira incluir ou excluir todas as instâncias de algum valor específico de uma variável categórica. Este é o que significa "amostragem por grupo".
Por exemplo, se você tiver uma variável categórica "Estados", que possui valores NY MA, CA, NJ, PA, etc, você deseja registros do mesmo estado estar sempre juntas, elas são amostra ou não.

Aqui está uma consulta de exemplo que amostras por grupo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Amostragem stratified

Amostragem aleatória é stratified relacionadas com uma variável categórica quando as amostras obtidas têm valores de que categorias que estão na mesma proporção como a população de pai do qual os exemplos foram obtidos. Usando o mesmo exemplo acima, suponha que seus dados tiverem população sub por estados, diga NJ tem 100 observações, NY tem 60 observações e WA tem 300 observações. Se você especificar a taxa de amostragem stratified seja 0,5, em seguida, a amostra obtida deve ter aproximadamente 50, 30 e 150 observações de NJ, NY e WA respectivamente.

Aqui está uma consulta de exemplo:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
            rank() over (partition by state order by rand()) as state_rank
        from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obter informações sobre os métodos de amostragem mais avançados que estão disponíveis na seção, consulte [LanguageManual amostragem](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
