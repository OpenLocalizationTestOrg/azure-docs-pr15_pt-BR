<properties
   pageTitle="Usar aprendizado de máquina Azure com o SQL Data Warehouse | Microsoft Azure"
   description="Tutorial para usar aprendizado de máquina do Azure com depósito de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Usar aprendizado de máquina Azure com o SQL Data Warehouse

Azure aprendizado de máquina é um serviço de análise de previsão totalmente gerenciado que você pode usar para criar modelos de previsão contra seus dados no depósito de dados do SQL e, em seguida, publicar como serviços web pronto para consumir. Você pode aprender as Noções básicas de análise de previsão e lendo [Introdução ao aprendizado de máquina no Azure][]de aprendizado de máquina.  Você pode, em seguida, saiba como criar, treinar, pontuação e testar um modelo de aprendizado de máquina usando a [criar experimentar tutorial][].

Neste artigo, você aprenderá como fazer o seguinte usando o [Azure Studio de aprendizado de máquina][]:

- Leia os dados de seu banco de dados para criar, treinar e pontuação de um modelo de previsão
- Gravar dados em seu banco de dados


## <a name="read-data-from-sql-data-warehouse"></a>Ler dados de depósito de dados do SQL

Podemos lerá os dados da tabela Produtos no banco de dados AdventureWorksDW.

### <a name="step-1"></a>Etapa 1

Iniciar um novo experimento clicando em + novo na parte inferior da janela Studio de aprendizado de máquina, selecione EXPERIMENTO e selecione experimentar em branco. Selecione o nome de experimento padrão na parte superior da tela e renomeie-a para algo significativo, por exemplo, previsão de preço de bicicleta.

### <a name="step-2"></a>Etapa 2

Procure o módulo do leitor na paleta de conjuntos de dados e módulos no lado esquerdo da tela experimento. Arraste o módulo à tela experimento.
![][drag_reader]

### <a name="step-3"></a>Etapa 3

Selecione o módulo do leitor e preencha o painel de propriedades.

1. Selecione o banco de dados do SQL Azure como fonte de dados.
2. Nome do servidor de banco de dados: digite o nome do servidor. Você pode usar o [portal do Azure][] para encontrá-los.

![][server_name]

3. Nome do banco de dados: digite o nome de um banco de dados no servidor que você acabou de especificado.
4. Nome de conta de usuário do servidor: digite o nome de usuário de uma conta que tenha permissões de acesso para o banco de dados.
5. Senha de conta de usuário do servidor: fornecer a senha da conta de usuário especificado.
6. Aceitar qualquer certificado do servidor: Use esta opção (menos segura) se desejar ignorar revisando o certificado de site antes de ler seus dados.
7. Consulta de banco de dados: insira uma instrução SQL que descreve os dados que você deseja ler. Nesse caso, estamos lerá dados da tabela de produto usando a seguinte consulta.


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Etapa 4

1. Execute o experimento clicando em executar em tela experimento.
2. Quando o experimento for concluído, o módulo do leitor terá uma marca de seleção verde para indicar que foi concluída com êxito. Observe também o concluído executando o status no canto superior direito.

![][run]

3. Para ver os dados importados, clique na porta de saída na parte inferior do dataset automóvel e selecione Visualizar.


## <a name="create-train-and-score-a-model"></a>Criar, treinar e pontuação de um modelo

Agora você pode usar este conjunto de dados para:

- Criar um modelo: processar dados e definir recursos
- Treinar o modelo: escolher e aplicar um algoritmo de aprendizagem
- Pontuação e testar o modelo: prever novo preço de bicicleta


![][model]

Para saber mais sobre como criar, treinar, pontuação de teste e uma modelo de uso de aprendizado de máquina a [criar experimentar tutorial][].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Gravar dados depósito de dados do SQL Azure

Podemos gravará o conjunto de resultados para tabela ProductPriceForecast no banco de dados AdventureWorksDW.

### <a name="step-1"></a>Etapa 1

Procure o módulo do gravador na paleta de conjuntos de dados e módulos no lado esquerdo da tela experimento. Arraste o módulo à tela experimento.

![][drag_writer]

### <a name="step-2"></a>Etapa 2

Selecione o módulo gravador e preencha o painel de propriedades.

1. Selecione o banco de dados do SQL Azure como o destino de dados.
2. Nome do servidor de banco de dados: digite o nome do servidor. Você pode usar o [portal do Azure][] para encontrá-los.
3. Nome do banco de dados: digite o nome de um banco de dados no servidor que você acabou de especificado.
4. Nome de conta de usuário do servidor: digite o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
5. Senha de conta de usuário do servidor: fornecer a senha da conta de usuário especificado.
6. Aceitar qualquer certificado do servidor (inseguro): Selecione esta opção se você não deseja exibir o certificado.
7. Separado por vírgula lista de colunas a serem salvos: forneça uma lista de colunas do dataset ou resultado que você deseja gerar uma saída.
8. Nome de tabela de dados: especifique o nome da tabela de dados.
9. Lista separada por vírgulas de colunas de tabela de dados: especifique os nomes de coluna para usar na nova tabela. Os nomes de coluna podem ser diferentes na fonte do dataset, mas você deve listar o mesmo número de colunas aqui definidas para a tabela de saída.
10. Número de linhas gravadas por operação do SQL Azure: você pode configurar o número de linhas que são gravadas em um banco de dados SQL em uma operação.

![][writer_properties]

### <a name="step-3"></a>Etapa 3

1. Execute o experimento clicando em executar em tela experimento.
2. Quando termina o experimento, todos os módulos terá uma marca de seleção verde para indicar que eles concluída com êxito.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento de depósito de dados do SQL][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Visão geral de desenvolvimento de depósito de dados do SQL]: ./sql-data-warehouse-overview-develop.md
[Criar experimento tutorial]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introdução à máquina aprendizagem no Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Studio de aprendizado de máquina Azure]: https://studio.azureml.net/Home
[Portal do Azure]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
