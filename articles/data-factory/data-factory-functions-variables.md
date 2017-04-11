<properties 
    pageTitle="Funções de fábrica de dados e variáveis do sistema | Microsoft Azure" 
    description="Fornece uma lista de funções de fábrica de dados do Azure e variáveis de sistema" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"
    services="data-factory"
/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---functions-and-system-variables"></a>Dados Azure Factory - funções e variáveis de sistema
Este artigo fornece informações sobre funções e variáveis suportados pelo fábrica de dados do Azure.
  
## <a name="data-factory-system-variables"></a>Variáveis do sistema de fábrica de dados

Nome da variável | Descrição | Escopo do objeto | Escopo JSON e casos de uso
------------- | ----------- | ------------ | ------------------------
WindowStart | Início do intervalo de tempo para executar a janela de atividade atual | atividade | <ol><li>Especifica consultas de seleção de dados. Consulte os artigos de conector referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) .</li><li>Passe parâmetros para seção script (exemplo mostrado acima).</li>
WindowEnd | Fim do intervalo de tempo para executar a janela de atividade atual | atividade | mesmo que acima
SliceStart | Início do intervalo de tempo para fatia dados produzido | atividade<br/>conjunto de dados | <ol><li>Especifica nomes de arquivo e caminhos de pasta dinâmico enquanto trabalha com [Azure Blob](data-factory-azure-blob-connector.md) e [conjuntos de dados do sistema de arquivos](data-factory-onprem-file-system-connector.md).</li><li>Especifica dependências de entrada com funções de fábrica de dados no conjunto de entradas de atividade.</li></ol>
SliceEnd | Fim do intervalo de tempo para produzidos de fatia de dados atual | atividade<br/>conjunto de dados | mesmo que acima. 

> [AZURE.NOTE] Atualmente fábrica de dados requer que o agendamento especificado na atividade exatamente corresponder o agendamento especificado na disponibilidade do conjunto de dados de saída. Isso significa que WindowStart, WindowEnd e SliceStart e SliceEnd sempre são mapeados para o mesmo período de tempo e uma fatia de saída simples.
 
## <a name="data-factory-functions"></a>Funções de fábrica de dados 

Você pode usar funções em fábrica de dados junto com acima variáveis do sistema mencionada para as seguintes finalidades:

1.  Especificando consultas de seleção de dados (consulte os artigos de conector referenciados pelo artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) .

    A sintaxe para chamar uma função de fábrica de dados é: ** $$ ** para consultas de seleção de dados e outras propriedades na atividade e conjuntos de dados.  
2. Especificar dependências de entrada com funções de fábrica de dados na atividade entradas conjunto (consulte exemplo acima).

    $$ não é necessária para especificar expressões de dependência de entrada.   

No exemplo a seguir, a propriedade de **sqlReaderQuery** em um arquivo JSON é atribuída a um valor retornado pela função **Text.Format** . Este exemplo também usa uma variável de sistema chamada **WindowStart**, que representa a hora de início da atividade executar janela.
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>Funções

As tabelas a seguir listam todas as funções em fábrica de dados do Azure:

Categoria | Função | Parâmetros | Descrição
-------- | -------- | ---------- | ----------- 
Tempo | AddHours(X,Y) | X: DateTime <br/><br/>Y: int | Adiciona Y horas ao tempo determinado X. <br/><br/>Exemplo: 5/9/2013 12:00:00 PM + 2 horas = 9/5/2013 2:00:00 PM
Tempo | AddMinutes(X,Y) | X: DateTime <br/><br/>Y: int | Adiciona Y minutos para X.<br/><br/>Exemplo: 15/9/2013 12:00:00 PM + 15 minutos = 9/15/2013 12:15:00 PM
Tempo | StartOfHour(X) | X: Datetime | Obtém a hora de início para a hora representada pelo componente de hora de X. <br/><br/>Exemplo: StartOfHour de 9/15/2013 05:10:23 PM é 15/9/2013 05:00:00 PM
Data | AddDays(X,Y) | X: DateTime<br/><br/>Y: int | Adiciona dias de Y para X.<br/><br/>Exemplo: 15/9/2013 12:00:00 PM + 2 dias = 9/17/2013 12:00:00 PM
Data | AddMonths(X,Y) | X: DateTime<br/><br/>Y: int | Adiciona meses de Y X.<br/><br/>Exemplo: 15/9/2013 12:00:00 PM + 1 mês = 10/15/2013 12:00:00 PM 
Data | AddQuarters(X,Y) | X: DateTime <br/><br/>Y: int | Adiciona Y * 3 meses para X.<br/><br/>Exemplo: 15/9/2013 12:00:00 PM + 1 trimestre = 12/15/2013 12:00:00 PM
Data | AddWeeks(X,Y) | X: DateTime<br/><br/>Y: int | Adiciona Y * 7 dias para X<br/><br/>Exemplo: 15/9/2013 12:00:00 PM + 1 semana = 9/22/2013 12:00:00 PM
Data | AddYears(X,Y) | X: DateTime<br/><br/>Y: int | Adiciona anos de Y a X.<br/><br/>Exemplo: 15/9/2013 12:00:00 PM + 1 ano = 9/15/2014 12:00:00 PM
Data | Day(X) | X: DateTime | Obtém o componente do dia de X.<br/><br/>Exemplo: Dia de 15/9/2013 12:00:00 PM é 9. 
Data | DayOfWeek(X) | X: DateTime | Obtém o dia do componente de semana de X.<br/><br/>Exemplo: DayOfWeek de 15/9/2013 12:00:00 PM é domingo.
Data | DayOfYear(X) | X: DateTime | Obtém o dia do ano representada pelo componente de ano de X.<br/><br/>Exemplos:<br/>1/12/2015: dia 335 de 2015<br/>31/12/2015: dia 365 de 2015<br/>31/12/2016: dia 366 da 2016 (salto ano)
Data | DaysInMonth(X) | X: DateTime | Obtém os dias do mês representado pelo componente de mês do parâmetro X.<br/><br/>Exemplo: DiasNoMês de 9/15/2013 são 30 pois há 30 dias do mês de setembro.
Data | EndOfDay(X) | X: DateTime | Obtém a data e hora que representa o final do dia (componente do dia) de X.<br/><br/>Exemplo: EndOfDay de 9/15/2013 05:10:23 PM é 15/9/2013 11:59:59 PM.
Data | EndOfMonth(X) | X: DateTime | Obtém o final do mês representado pelo componente de mês do parâmetro X. <br/><br/>Exemplo: EndOfMonth de 9/15/2013 05:10:23 PM é 30/9/2013 11:59:59 PM (tempo de data que representa o final do mês de setembro)
Data | StartOfDay(X) | X: DateTime | Obtém o início do dia são representado pelo componente do dia do parâmetro X.<br/><br/>Exemplo: StartOfDay de 9/15/2013 05:10:23 PM é 15/9/2013 12:00:00 AM.
DateTime | From(X) | X: cadeia de caracteres de | Analise cadeia X para uma data hora.
DateTime | Ticks(X) | X: DateTime | Obtém as escalas propriedade do parâmetro X. Uma escala é igual a 100 nanossegundos. O valor desta propriedade representa o número de escalas decorridos desde 12:00:00 meia-noite, 1 de janeiro, 0001. 
Texto | Format(X) | X: variável de cadeia de caracteres de | Formata o texto.

#### <a name="textformat-example"></a>Exemplo de Text.Format

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

Consulte [Personalizar data e cadeias de caracteres de formato de hora](https://msdn.microsoft.com/library/8kb3ddd4.aspx) tópico que descreve as opções de formatação diferentes que você pode usar (por exemplo: AA versus AAAA). 

> [AZURE.NOTE] Ao usar uma função dentro de outra função, você não precisa usar **$$** prefixo para a função interna. Por exemplo: $$Text.Format ('PartitionKey eq \\' my_pkey_filter_value\\' e RowKey ge \\' {0:yyyy-MM-dd hh}\\', Time.AddHours (SliceStart, -6)). Neste exemplo, observe que **$$** prefixo não é usado para a função **Time.AddHours** . 
  

