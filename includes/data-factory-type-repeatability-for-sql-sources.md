## <a name="repeatability-during-copy"></a>Capacidade de repetição durante a cópia

Quando copia dados ao Azure SQL do SQL Server de outros dados armazena um precisa lembre repetição para evitar resultados não intencionais. 

Ao copiar dados para o banco de dados do Azure SQL do SQL Server, atividade de cópia será por padrão ACRESCENTAR o conjunto de dados para a tabela de receptor por padrão. Por exemplo, quando copiar dados de uma fonte de arquivo CSV (dados de valores separados por vírgulas) que contém dois registros do banco de dados do Azure SQL do SQL Server, esta é a tabela aparência:
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

Suponha que você encontrou erros no arquivo de origem e atualizado a quantidade de busca câmara de 2 a 4 no arquivo de origem. Se você executar novamente na fatia de dados para esse período, você encontrará dois novos registros acrescentados ao banco de dados do Azure SQL do SQL Server. A seguir supõe nenhuma das colunas na tabela possui a restrição de chave primária.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Para evitar isso, você precisará especificar semântica UPSERT utilizando um do abaixo 2 mecanismos descritos a seguir.

> [AZURE.NOTE] Uma fatia pode ser reexecutar automaticamente em fábrica de dados do Azure de acordo com a política de repetição especificada.

### <a name="mechanism-1"></a>Mecanismo de 1

Você pode utilizar a propriedade **sqlWriterCleanupScript** para executar a ação de limpeza primeiro quando uma fatia é executada. 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

O script de limpeza poderia ser executado primeiro durante a cópia de uma determinado fatia que deseja excluir os dados da tabela SQL correspondente a essa fatia. A atividade subsequentemente irá inserir os dados na tabela do SQL. 

Se a fatia é agora execute novamente, em seguida, você encontrará que a quantidade é atualizada como desejado.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Suponha que o registro de arruela plana é removido do csv original. Em seguida, executar novamente na fatia produza o seguinte resultado: 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

Nada novo tinha ser realizadas. A atividade de cópia executou o script de limpeza para excluir os dados correspondentes para essa fatia. Em seguida, ele ler a entrada de csv (que então contido apenas 1 registro) e inseri-lo na tabela. 

### <a name="mechanism-2"></a>Mecanismo de 2
> [AZURE.IMPORTANT] sliceIdentifierColumnName não há suporte para depósito de dados do SQL Azure neste momento. 

Outro mecanismo para atingir repetição é fazendo com que uma coluna exclusiva (**sliceIdentifierColumnName**) na tabela de destino. Esta coluna seria usada pela fábrica de dados do Azure para garantir a que origem e destino fiquem sincronizados. Essa abordagem funciona quando há flexibilidade ao alterar ou definir o esquema de tabela do SQL de destino. 

Esta coluna seria usada por fábrica de dados do Azure para fins de repetição e no processo de fábrica de dados do Azure não fará alterações de esquema para a tabela. Para usar essa abordagem:

1.  Defina uma coluna de tipo binário (32) no destino tabela SQL. Deve haver sem restrições nessa coluna. Vamos nomeie esta coluna como 'ColumnForADFuseOnly' para este exemplo.
2.  Usá-lo a atividade de cópia da seguinte maneira:

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Azure Data Factory preencherá esta coluna de acordo com as sua necessidade de garantir a que origem e destino fiquem sincronizados. Os valores desta coluna não devem ser usados fora neste contexto pelo usuário. 

Semelhante ao mecanismo de 1, atividade de cópia será automaticamente primeiro limpar os dados da fatia determinada do destino tabela SQL e execute a atividade de cópia normalmente para inserir os dados de origem para destino para essa fatia. 
