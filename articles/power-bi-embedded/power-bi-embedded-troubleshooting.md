<properties
   pageTitle="Solução de problemas de visualização incorporada do Microsoft Power BI"
   description="Solução de problemas de visualização incorporada do Microsoft Power BI"
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

# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Solução de problemas de visualização incorporada do Microsoft Power BI
Este artigo fornece respostas para solucionar problemas de **Power BI inserida**.

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>Configuração de cadeias de caracteres de conexão do SQL Server
Para definir uma cadeia de conexão do SQL Server, você precisa seguir um formato específico. Abaixo está um exemplo de sequência de conexão para o SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Para saber mais sobre cadeias de caracteres de conexão do SQL Server, consulte os seguintes artigos:

-   [Cadeias de caracteres de Conexão do SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-   [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>Definir credenciais
No caso em que você tiver as credenciais para um desenvolvimento ambiente de teste, como o nome de usuário e senha, você precisará atualizar credenciais que correspondam a uma solução de produção.

## <a name="see-also"></a>Consulte também
- [Começar a usar o exemplo](power-bi-embedded-get-started-sample.md)
- [O que é inserida do Power BI](power-bi-embedded-what-is-power-bi-embedded.md)
