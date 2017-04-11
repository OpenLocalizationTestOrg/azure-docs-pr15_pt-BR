<properties
    pageTitle="Configurar políticas de seção no domínio HDInsight | Microsoft Azure"
    description="Aprenda..."
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/25/2016"
    ms.author="saurinsh"/>

# <a name="configure-hive-policies-in-domain-joined-hdinsight-preview"></a>Configurar políticas de seção no domínio HDInsight (prévia)

Saiba como configurar políticas de Ranger Apache para seção. Neste artigo, você cria duas diretivas de Ranger para restringir o acesso a hivesampletable. O hivesampletable vem com clusters de HDInsight. Depois de configurar as políticas, você usar o driver ODBC e do Excel para se conectar à seção tabelas em HDInsight.


## <a name="prerequisites"></a>Pré-requisitos

- Um cluster de domínio HDInsight. Consulte [Configurar domínio HDInsight clusters](hdinsight-domain-joined-configure.md).
- Uma estação de trabalho com o Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 autônomo ou Office 2010 Professional Plus.


## <a name="connect-to-apache-ranger-admin-ui"></a>Conectar-se a interface de usuário de administrador de Ranger Apache

**Para se conectar a interface de usuário de administrador de Ranger**

1. Em um navegador, conecte a interface de usuário de administrador de Ranger. A URL é https://&lt;ClusterName >.azurehdinsight.net/Ranger/. 

    >[AZURE.NOTE] Ranger usa credenciais diferentes Hadoop cluster. Para evitar navegadores usando credenciais armazenadas em cache do Hadoop, use a nova janela do navegador inprivate para se conectar a interface do usuário do administrador do Ranger.
4. Faça logon usando o nome de usuário de domínio de administrador de cluster e a senha:

    ![Home page do HDInsight domínio Ranger](./media/hdinsight-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Atualmente, Ranger só funciona com fio colorido e de seção.

## <a name="create-domain-users"></a>Criar usuários de domínio

[Configurar o domínio HDInsight clusters](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad), você criou hiveruser1 e hiveuser2. Você usará a conta de dois usuário neste tutorial.

## <a name="create-ranger-policies"></a>Criar políticas de Ranger

Nesta seção, você irá criar duas diretivas de Ranger para acessar hivesampletable. Você pode dar permissão select em um conjunto diferente de colunas. Ambos os usuários foram criados em [clusters de HDInsight configurar domínio](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).  Na próxima seção, você irá testar as duas diretivas no Excel.

**Para criar políticas de Ranger**

1. Abra a interface de usuário de administrador de Ranger. Consulte [conectar-se a interface de usuário de administrador do Apache Ranger](#connect-to-apache-ranager-admin-ui).
2. Clique em ** &lt;ClusterName > _hive**, sob a **seção**. Você deverá ver duas pré-configurar políticas.
3. Clique em **Adicionar nova política**e insira os seguintes valores:

    - Nome da política: leitura-hivesampletable-tudo
    - Seção de banco de dados: padrão
    - tabela: hivesampletable
    - Seção coluna: *
    - Selecione usuário: hiveuser1
    - Permissões: selecione

    ![Configurar política de Ranger seção HDInsight domínio](./media/hdinsight-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    >[AZURE.NOTE] Se um usuário de domínio não estiver preenchido em Selecionar usuário, aguarde alguns minutos para Ranger sincronizar com AAD.

4. Clique em **Adicionar** para salvar a política.
5. Repita as duas últimas etapas para criar outra política com as seguintes propriedades:

    - Nome da política: leitura-hivesampletable-devicemake
    - Seção de banco de dados: padrão
    - tabela: hivesampletable
    - Seção coluna: clientid, devicemake
    - Selecione usuário: hiveuser2
    - Permissões: selecione

## <a name="create-hive-odbc-data-source"></a>Criar fonte de dados ODBC de seção

As instruções podem ser encontradas na [fonte de dados ODBC de seção Criar](hdinsight-connect-excel-hive-odbc-driver.md).  

    Propriedade|Descrição
    ---|---
    Nome da fonte de dados|Dê um nome à sua fonte de dados
    Host|Insira &lt;HDInsightClusterName >. azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net
    Porta|Use <strong>443</strong>. (Esta porta foi alterada de 563 para 443.)
    Banco de dados|Use o <strong>padrão</strong>.
    Tipo de servidor de seção|Selecione a <strong>seção servidor 2</strong>
    Mecanismo|Selecione <strong>Serviço do Azure HDInsight</strong>
    Caminho HTTP|Deixe em branco.
    Nome de usuário|Insira hiveuser1@contoso158.onmicrosoft.com. Atualize o nome de domínio, se for diferente.
    Senha|Digite a senha para hiveuser1.
    </table>

Certifique-se clique em **Testar** antes de salvar a fonte de dados.


##<a name="import-data-into-excel-from-hdinsight"></a>Importar dados para o Excel a partir do HDInsight

Na seção anterior, você configurou duas diretivas.  hiveuser1 tem a permissão select em todas as colunas, e hiveuser2 tem a permissão select em duas colunas. Nesta seção, você representa os dois usuários para importar dados para o Excel.


1. Abra uma pasta de trabalho nova ou existente no Excel.
2. Na guia **dados** , clique em **De outras fontes de dados**e clique em **Assistente de Conexão de dados** para iniciar o **Assistente de Conexão de dados**.

    ! [Assistente de conexão de dados aberta] [img-hdi-simbahiveodbc.excel.dataconnection]

3. Selecione o **DSN ODBC** como fonte de dados e clique em **Avançar**.
4. De fontes de dados ODBC, selecione o nome da fonte de dados que você criou na etapa anterior e clique em **Avançar**.
5. Digite novamente a senha para o cluster no assistente e, em seguida, clique em **Okey**. Aguarde até que a caixa de diálogo **Selecionar banco de dados e tabela** abrir. Isso pode levar alguns segundos.
8. Selecione **hivesampletable**e clique em **Avançar**. 
8. Clique em **Concluir**.
9. Na caixa de diálogo **Importar dados** , você pode alterar ou especificar a consulta. Para fazer isso, clique em **Propriedades**. Isso pode levar alguns segundos. 
10. Clique na guia **definição** . O texto de comando é:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    Pelas políticas Ranger definida, o hiveuser1 tem permissão select em todas as colunas.  Portanto essa consulta funciona com credenciais do hiveuser1, mas essa consulta não não funciona com credenciais do hiveuser2.

    ! [Propriedades de Conexão] [img-hdi-simbahiveodbc-excel-connectionproperties]

11. Clique **Okey** para fechar a caixa de diálogo de propriedades de Conexão.
12. Clique **Okey** para fechar a caixa de diálogo **Importar dados** .  
13. Redigite a senha para hiveuser1 e clique em **Okey**. Leva alguns segundos antes de dados obtém importados para o Excel. Quando estiver pronto, você deverá ver 11 colunas de dados.

Para testar a segunda diretiva (leitura-hivesampletable-devicemake) que você criou na seção anterior

1. Adicione uma nova planilha no Excel.
2. Siga o procedimento último para importar os dados.  A única alteração que você fará é usar credenciais do hiveuser2 em vez do hiveuser1. Isso falhará porque hiveuser2 só tem permissão para ver duas colunas. Você deve receber o seguinte erro:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.

3. Siga o mesmo procedimento para importar dados. Desta vez, usar credenciais do hiveuser2 e também modificar a instrução select de:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    Para:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Quando estiver pronto, você deverá ver duas colunas de dados importados.

## <a name="next-steps"></a>Próximas etapas

- Para configurar um cluster de domínio HDInsight, consulte [Configurar domínio HDInsight clusters](hdinsight-domain-joined-configure.md).
- Para gerenciar clusters um domínio HDInsight, consulte [clusters de gerenciar domínio HDInsight](hdinsight-domain-joined-manage.md).
- Para executar consultas de seção usando SSH em clusters de domínio HDInsight, consulte [Usar SSH com baseado em Linux Hadoop em HDInsight do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).
- Para conectar seção utilizando JDBC seção, consulte [conectar ao seção em Azurehdinsight usando o driver JDBC seção](hdinsight-connect-hive-jdbc-driver.md)
- Para conectar o Excel ao Hadoop usando seção ODBC, consulte [Conectar o Excel ao Hadoop com a unidade de Microsoft seção ODBC](hdinsight-connect-excel-hive-odbc-driver.md)
- Para conectar o Excel ao Hadoop usando o Power Query, consulte [Conectar o Excel ao Hadoop usando o Power Query](hdinsight-connect-excel-power-query.md)