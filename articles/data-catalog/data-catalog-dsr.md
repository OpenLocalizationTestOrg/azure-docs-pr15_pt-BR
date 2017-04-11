<properties
   pageTitle="Azure catálogo de dados com suporte a fontes de dados | Microsoft Azure"
   description="Especificação de fontes de dados com suporte no momento."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-supported-data-sources"></a>Azure catálogo de dados com suporte a fontes de dados

Os usuários do catálogo de dados do Azure podem publicar metadados usando uma API pública, um clique-uma vez registro ferramenta, inserindo manualmente informações diretamente ao catálogo de dados web ou portal. A grade a seguir resume todas as fontes compatíveis com o catálogo de hoje e os recursos de publicação para cada um.  Também listados estão as ferramentas de dados externos que cada fonte pode iniciar de nossa experiência de portal "abrir-in". A segunda grade no artigo tem mais técnica especificação de cada propriedades de conexão de fontes de dados.


## <a name="list-of-supported-data-sources"></a>Lista de fontes de dados com suporte

<table>

    <tr>
       <td><b>Objeto de origem de dados</b></td>
       <td><b>API</b></td>
       <td><b>Entrada manual</b></td>
       <td><b>Ferramenta de registro</b></td>
       <td><b>Ferramentas de abrir</b></td>
       <td><b>Anotações</b></td>
    </tr>

    <tr>
      <td>Diretório de armazenamento de Lucerne de dados do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo de armazenamento de Lucerne de dados do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Armazenamento do Azure Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Diretório de armazenamento do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de armazenamento do Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Diretório HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de seção</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição de seção</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição de MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de banco de dados Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição de banco de dados Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Outros (ativo genérico)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de depósito de dados do SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, PowerBI, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de depósito de dados do SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, PowerBI, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimensão do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>KPI do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Medida do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Relatório do Reporting Services do SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Navegador</font></td>
      <td><font size=2>Somente servidores de modo nativo. Não há suporte para o modo do SharePoint.</font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, PowerBI, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição do SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ferramentas de dados do Excel, PowerBI, SQL Server</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição do SAP Hana</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2>Modos de exibição de cálculo e exibições analíticas Modos de exibição do atributo não são suportados.</font></td>
    </tr>

    <tr>
      <td>Tabela Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição de Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do sistema</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Diretório FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo de FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Relatório de http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ponto de extremidade de http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Conjunto de entidade de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Função de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição de PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de exibição do SAP Hana</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Objeto de SalesForce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Lista do SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Se você precisa de suporte para fontes adicionais, envie uma solicitação de recurso usando o [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Especificação de referência da fonte de dados
> [AZURE.NOTE] Coluna de "Estrutura DSL" no seguintes somente listas conexão propriedades da tabela para o conjunto de propriedades de "address" que são usadas pelo catálogo de dados do Azure (isto é, bolsa de propriedade de "address" pode conter outras propriedades de conexão da fonte de dados que catálogo de dados do Azure persistir, mas não use.)
<table>
    <tr>
       <td><b>Tipo de fonte</b></td>
       <td><b>Tipo de ativo</b></td>
       <td><b>Tipos de objeto</b></td>
       <td><b>Estrutura DSL<b></td>
    </tr>
    <tr>
      <td>Armazenamento de Lucerne de dados do Azure</td>
      <td>Contêiner</td>
      <td>Lucerne de dados</td>
      <td>
        <font size=2>protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento de Lucerne de dados do Azure</td>
      <td>Tabela</td>
      <td>Pasta, arquivo</td>
      <td>
        <font size=2>protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Contêiner</td>
      <td>Contêiner</td>
      <td>
        <font size=2>protocolo: blobs do azure <br>autenticação: {tecla de acesso do azure} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contêiner</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>BLOB, diretório</td>
      <td>
        <font size=2>protocolo: blobs do azure <br>autenticação: {tecla de acesso do azure} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contêiner <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Contêiner</td>
      <td>Contêiner</td>
      <td>
        <font size=2>protocolo: tabelas do azure <br>autenticação: {tecla de acesso do azure} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta</font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2>protocolo: tabelas do azure <br>autenticação: {tecla de acesso do azure} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contêiner</td>
      <td>Cluster virtual</td>
      <td>
        <font size=2>protocolo: cosmos <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabela</td>
      <td>Modo de exibição de fluxo, conjunto de fluxo,</td>
      <td>
        <font size=2>protocolo: cosmos <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhum, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Relatório</td>
      <td>Relatório, Dashboard</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhum, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2>protocolo: db2 <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados</font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Tabela</td>
      <td>Tabela, modo de exibição</td>
      <td>
        <font size=2>protocolo: db2 <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de arquivos</td>
      <td>Tabela</td>
      <td>Arquivo</td>
      <td>
        <font size=2>protocolo: arquivo <br>autenticação: {nenhum, windows básico,} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;caminho</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabela</td>
      <td>Pasta, arquivo</td>
      <td>
        <font size=2>protocolo: ftp <br>autenticação: {nenhum, windows básico,} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de arquivo distribuído Hadoop</td>
      <td>Contêiner</td>
      <td>Cluster</td>
      <td>
        <font size=2>protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Sistema de arquivo distribuído Hadoop</td>
      <td>Tabela</td>
      <td>Pasta, arquivo</td>
      <td>
        <font size=2>protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Seção</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2>protocolo: seção <br>autenticação: {hdinsight, básico, username, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Seção</td>
      <td>Tabela</td>
      <td>Tabela, modo de exibição</td>
      <td>
        <font size=2>protocolo: seção <br>autenticação: {hdinsight, básico, username, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhum, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Relatório</td>
      <td>Relatório, Dashboard</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhum, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Tabela</td>
      <td>Ponto final, arquivo</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhum, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2>protocolo: mysql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabela</td>
      <td>Tabela, modo de exibição</td>
      <td>
        <font size=2>protocolo: mysql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contêiner</td>
      <td>Contêiner de entidade</td>
      <td>
        <font size=2>protocolo: odata <br>autenticação: {nenhum, básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabela</td>
      <td>Conjunto de entidade, função</td>
      <td>
        <font size=2>protocolo: odata <br>autenticação: {nenhum, básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;recurso</font>
      </td>
    </tr>
    <tr>
      <td>Banco de dados Oracle</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2>protocolo: oracle <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados</font>
      </td>
    </tr>
    <tr>
      <td>Banco de dados Oracle</td>
      <td>Tabela</td>
      <td>Tabela, modo de exibição</td>
      <td>
        <font size=2>protocolo: oracle <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2>protocolo: postgresql <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tabela</td>
      <td>Tabela, modo de exibição</td>
      <td>
        <font size=2>protocolo: postgresql <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhum, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Relatório</td>
      <td>Relatório, Dashboard</td>
      <td>
        <font size=2>protocolo: http <br>autenticação: {nenhum, basic, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Tabela</td>
      <td>Mashup de dados</td>
      <td>
        <font size=2>protocolo: power query <br>autenticação: {oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>SalesForce</td>
      <td>Tabela</td>
      <td>Objeto</td>
      <td>
        <font size=2>protocolo: com a equipe de vendas <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;classe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome do item</font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>Contêiner</td>
      <td>Servidor</td>
      <td>
        <font size=2>protocolo: sap-hana-sql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor</font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>Tabela</td>
      <td>Modo de exibição</td>
      <td>
        <font size=2>protocolo: sap-hana-sql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabela</td>
      <td>Lista</td>
      <td>
        <font size=2>protocolo: lista do sharepoint <br>autenticação: {básica, do windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Depósito de dados do SQL</td>
      <td>Comando</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Depósito de dados do SQL</td>
      <td>TableValuedFunction</td>
      <td>Função com valor de tabela</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Depósito de dados do SQL</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados</font>
      </td>
    </tr>
    <tr>
      <td>Depósito de dados do SQL</td>
      <td>Tabela</td>
      <td>Tabela, modo de exibição</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Comando</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Função com valor de tabela</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabela</td>
      <td>Tabela, modo de exibição</td>
      <td>
        <font size=2>protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size=2>protocolo: do analysis services <br>autenticação: {windows, básico, anônimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>protocolo: do analysis services <br>autenticação: {windows, básico, anônimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2>protocolo: do analysis services <br>autenticação: {windows, básico, anônimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {medida}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>Tabela</td>
      <td>Dimensão</td>
      <td>
        <font size=2>protocolo: do analysis services <br>autenticação: {windows, básico, anônimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {dimensão}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Tabular</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size=2>protocolo: do analysis services <br>autenticação: {windows, básico, anônimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Tabular</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2>protocolo: do analysis services <br>autenticação: {windows, básico, anônimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Tabular</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2>protocolo: do analysis services <br>autenticação: {windows, básico, anônimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {medida}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Tabular</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2>protocolo: do analysis services <br>autenticação: {windows, básico, anônimo, nenhum} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {tabela}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contêiner</td>
      <td>Servidor</td>
      <td>
        <font size=2>protocolo: do reporting services <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Relatório</td>
      <td>Relatório</td>
      <td>
        <font size=2>protocolo: do reporting services <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;caminho <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versão: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2>protocolo: teradata <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabela</td>
      <td>Tabela, modo de exibição</td>
      <td>
        <font size=2>protocolo: teradata <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objeto</font>
      </td>
    </tr>
    <tr>
      <td>Serviços de dados do SQL Server Master</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size="2">protocolo: mssql mds <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Versão</font>
      </td>
    </tr>
    <tr>
      <td>Serviços de dados do SQL Server Master</td>
      <td>Tabela</td>
      <td>Entidade</td>
      <td>
        <font size="2">protocolo: mssql mds <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Versão <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;entidade</font>
      </td>
    </tr>
    <tr>
      <td>Outro (não uma das opções acima)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>protocolo: genérico de ativos <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;idasset</font>
      </td>
    </tr>
</table>
