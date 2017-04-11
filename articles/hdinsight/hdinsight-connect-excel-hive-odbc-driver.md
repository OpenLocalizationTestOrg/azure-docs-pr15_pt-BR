<properties
   pageTitle="Conectar o Excel ao Hadoop com o Driver ODBC seção | Microsoft Azure"
   description="Saiba como configurar e usar o driver ODBC seção da Microsoft para o Excel para consultar dados em um cluster de HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

#<a name="connect-excel-to-hadoop-with-the-microsoft-hive-odbc-driver"></a>Conectar o Excel ao Hadoop com o seção driver ODBC do Microsoft

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Solução de grande volume da Microsoft se integra componentes do Microsoft Business Intelligence (BI) clusters Apache Hadoop que foram implantados pelo Azure HDInsight. Um exemplo dessa integração é a capacidade de conexão do Excel para o seção data warehouse de um cluster de Hadoop em HDInsight usando o Driver de Microsoft seção abrir banco de dados ODBC (conectividade).

Também é possível conectar os dados associados a um cluster de HDInsight e outras fontes de dados, incluindo outros clusters do Hadoop (não-HDInsight), do Excel usando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e usar o Power Query, consulte [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query].

> [AZURE.NOTE] Enquanto as etapas neste artigo podem ser usadas com cluster um Linux ou HDInsight baseados no Windows, o Windows é exigido para a estação de trabalho do cliente.

**Pré-requisitos**:

Antes de começar este artigo, você deve ter o seguinte:

- **HDInsight um cluster**. Para criar uma, consulte [Introdução ao Azure HDInsight][hdinsight-get-started].
- **A estação de trabalho** com o Office 2013 Professional Plus, o Office 365 Pro Plus, o Excel 2013 autônomo ou o Office 2010 Professional Plus.


##<a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft seção

Baixar e instalar o Driver ODBC seção da Microsoft no [Centro de Download]do[hive-odbc-driver-download].

Este driver pode ser instalado em versões de 32 bits ou 64 bits do Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 e Windows Server 2012 e permitirá conexão Azurehdinsight (versão 1,6 e posterior) e Azure HDInsight emulador (v.1.0.0.0 e posterior). Você deve instalar a versão que corresponde à versão do aplicativo onde você usará o driver ODBC. Para este tutorial, o driver será usado do Office Excel.

##<a name="create-hive-odbc-data-source"></a>Criar fonte de dados ODBC de seção

As etapas a seguir mostram como criar uma fonte de dados ODBC seção.

1. No Windows 8 ou Windows 10, pressione a tecla do Windows para abrir a tela Iniciar e digite **fontes de dados**.
2. Clique em **Configurar dados ODBC fontes (32 bits)** ou **Configurar fontes de dados ODBC (64 bits)** , dependendo de sua versão do Office. Se você estiver usando o Windows 7, escolha **Fontes de dados ODBC (32 bits)** ou **Fontes de dados ODBC (64 bits)** em **Ferramentas administrativas**. Isso abrirá a caixa de diálogo **Administrador de fonte de dados ODBC** .

    ![Administrador de fonte de dados ODBC][img-hdi-simbahiveodbc-datasource-admin]

3. No DNS do usuário, clique em **Adicionar** para abrir o assistente **Criar nova fonte de dados** .
4. Selecione o **Driver ODBC seção da Microsoft**e clique em **Concluir**. Isso abrirá a caixa de diálogo **Microsoft seção ODBC Driver de configuração do DNS** .

5. Digite ou selecione os valores a seguir:

    Propriedade|Descrição
    ---|---
    Nome da fonte de dados|Dê um nome à sua fonte de dados
    Host|Insira &lt;HDInsightClusterName >. azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net
    Porta|Use <strong>443</strong>. (Esta porta foi alterada de 563 para 443.)
    Banco de dados|Use o <strong>padrão</strong>.
    Tipo de servidor de seção|Selecione a <strong>seção servidor 2</strong>
    Mecanismo|Selecione <strong>Serviço do Azure HDInsight</strong>
    Caminho HTTP|Deixe em branco.
    Nome de usuário|Insira o nome de usuário de cluster HDInsight. Este é o nome de usuário criada durante o processo de provisionamento de cluster. Se você usou a opção criar rápida, o nome de usuário padrão é <strong>admin</strong>.
    Senha|Insira a senha de usuário de cluster HDInsight.
    </table>

    Há alguns parâmetros importantes a serem consideradas quando você clica em **Opções avançadas**:

    Parâmetro|Descrição
    ---|---
    Usar consulta nativa|Quando for selecionada, o driver ODBC não tentará converter TSQL em HiveQL. Você deverá usá-lo somente se você estiver 100% se que você estiver enviando puro HiveQL instruções. Quando a conexão com o SQL Server ou banco de dados do SQL Azure, você deve deixá-la desmarcada.
    Linhas buscadas por bloco|Quando se busca uma grande quantidade de registros, ajuste esse parâmetro pode ser necessário para garantir o desempenho ideal.
    Comprimento de coluna padrão da cadeia, comprimento de coluna binária, escala da coluna Decimal|O tipo de dados comprimentos e precisions pode afetar como dados são retornados. Eles fará com que informações incorretas deverá ser retornada devido a perda de precisão e/ou truncamento.


    ![Opções avançadas][img-HiveOdbc-DataSource-AdvancedOptions]

6. Clique em **Testar** para testar a fonte de dados. Quando a fonte de dados está configurada corretamente, ele mostra *testes concluída com êxito!*.
7. Clique **Okey** para fechar a caixa de diálogo de teste. A nova fonte de dados agora deve estar listada do **Administrador de fonte de dados ODBC**.
8. Clique em **Okey** para sair do assistente.

##<a name="import-data-into-excel-from-hdinsight"></a>Importar dados para o Excel a partir do HDInsight

As etapas a seguir descrevem a maneira de importar dados de uma tabela de seção em uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou nas etapas acima.

1. Abra uma pasta de trabalho nova ou existente no Excel.
2. Na guia **dados** , clique em **De outras fontes de dados**e clique em **Assistente de Conexão de dados** para iniciar o **Assistente de Conexão de dados**.

    ![Assistente de conexão de dados aberta][img-hdi-simbahiveodbc.excel.dataconnection]

3. Selecione o **DSN ODBC** como fonte de dados e clique em **Avançar**.
4. De fontes de dados ODBC, selecione o nome da fonte de dados que você criou na etapa anterior e clique em **Avançar**.
5. Digite novamente a senha para o cluster no assistente e clique em **teste** para verificar a configuração novamente, se necessário.
6. Clique **Okey** para fechar a caixa de diálogo de teste.
7. Clique em **Okey**. Aguarde até que a caixa de diálogo **Selecionar banco de dados e tabela** abrir. Isso pode levar alguns segundos.
8. Selecione a tabela que você deseja importar e clique em **Avançar**. O *hivesampletable* é um exemplo de tabela de seção que vem com clusters de HDInsight.  Você pode escolher se você ainda não tiver criado um. Para obter mais informações sobre executar consultas de seção e criar tabelas de seção, consulte [Usar seção com HDInsight][hdinsight-use-hive].
8. Clique em **Concluir**.
9. Na caixa de diálogo **Importar dados** , você pode alterar ou especificar a consulta. Para fazer isso, clique em **Propriedades**. Isso pode levar alguns segundos.
10. Clique na guia **definição** e, em seguida, acrescentar **limite 200** a instrução select seção na caixa de texto **texto de comando** . A modificação limita o conjunto de registros retornado para 200.

    ![Propriedades de Conexão][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Clique **Okey** para fechar a caixa de diálogo de propriedades de Conexão.
12. Clique **Okey** para fechar a caixa de diálogo **Importar dados** .  
13. Digite a senha novamente e clique em **Okey**. Leva alguns segundos antes de dados obtém importados para o Excel.

##<a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como usar o driver ODBC do Microsoft seção para recuperar dados do HDInsight Service para o Excel. Da mesma forma, você pode recuperar dados do HDInsight Service no banco de dados do SQL. Também é possível carregar dados em um HDInsight Service. Para saber mais, consulte:

- [Analisar dados de atrasos de voos usando HDInsight][hdinsight-analyze-flight-data]
- [Carregar dados ao HDInsight][hdinsight-upload-data]
- [Usar Sqoop com HDInsight] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
