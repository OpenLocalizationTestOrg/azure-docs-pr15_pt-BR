## <a name="repeatability-during-copy"></a>Capacidade de repetição durante a cópia

Ao copiar dados de e para lojas relacionais, você precisa lembre repetição para evitar resultados não intencionais. 

Uma fatia pode ser executado novamente automaticamente em fábrica de dados do Azure de acordo com a política de repetição especificada. Recomendamos que você defina uma política de Repetir para se proteger contra falhas temporárias. Portanto repetição é um aspecto importante para cuidam de durante a movimentação de dados. 

**Como uma fonte:**

> [AZURE.NOTE] Os exemplos a seguir são para SQL Azure, mas são aplicáveis a qualquer armazenamento de dados que ofereça suporte a conjuntos de dados retangulares. Você pode precisar ajustar o **tipo** de fonte e a propriedade de **consulta** (por exemplo: consulta em vez de sqlReaderQuery) para os dados armazenar.   

Geralmente, ao ler de lojas relacionais, convém ler apenas os dados correspondentes a esse fatia. Uma maneira de fazer isso seria usando as variáveis WindowStart e WindowEnd disponíveis no Azure dados fábrica. Leia sobre as variáveis e funções no Azure Data Factory aqui no artigo [agendamento e execução](../articles/data-factory/data-factory-scheduling-and-execution.md) . Exemplo: 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

Essa consulta lê os dados de 'MyTable' que está no intervalo de duração de fatia. Reexecutar dessa fatia também sempre seria garantir que esse comportamento. 

Em outros casos, talvez você queira ler a tabela inteira (suponha para mover de uma vez só) e pode definir o sqlReaderQuery da seguinte maneira:

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    
