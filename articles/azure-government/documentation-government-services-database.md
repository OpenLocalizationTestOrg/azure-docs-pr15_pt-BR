<properties
    pageTitle="Documentação do governo Azure | Microsoft Azure"
    description="Isso fornece uma comparação de recursos e orientações sobre como desenvolver aplicativos para o governo do Azure"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/18/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-databases"></a>Bancos de dados do governo Azure

##  <a name="sql-database"></a>Banco de dados SQL

Consulte a [Documentação do público de banco de dados do SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/) e o<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centro de segurança da Microsoft para o mecanismo de banco de dados SQL</a> para orientação adicional sobre configuração de visibilidade de metadados e práticas recomendadas de proteção.

### <a name="variations"></a>Variações

Banco de dados do SQL V12 estará disponível no governo do Azure.

O endereço do SQL Azure Servers no Azure governo é diferente:

Tipo de serviço|Azure público|Governo Azure
---|---|---
Banco de dados SQL|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Considerações

As informações a seguir identificam o limite de governo do Azure para SQL Azure:

| Dados regulamentadas/controlados permitidos | Dados regulamentadas/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados armazenados e processados em SQL do Microsoft Azure podem conter dados regulamentadas governo do Azure. Você deve usar ferramentas de banco de dados para transferir dados de dados regulamentadas governo do Azure. | Metadados SQL Azure não é permitido para conter dados de exportação controlada. Esses metadados incluem todos os dados de configuração inseridos quando criar e manter seu produto de armazenamento.  Não digite dados regulamentadas/controlados nos seguintes campos: nome, nome da assinatura, grupos de recursos, o nome do servidor, logon de administrador do servidor, nomes de implantação, nomes de recursos, marcas de recurso de banco de dados

## <a name="azure-redis-cache"></a>Cache relacionada Azure

Para obter detalhes sobre esse serviço e como usá-lo, consulte a [documentação de pública do Azure relacionada Cache](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="variations"></a>Variações

As URLs para acessar e gerenciar Azure relacionada Cache do governo do Azure são diferentes:

Tipo de serviço|Azure público|Governo Azure
---|---|---
Ponto de extremidade de cache|*. redis.cache.windows.net|*. redis.cache.usgovcloudapi.net
Portal do Azure|https://portal.Azure.com|https://portal.Azure.US

>[AZURE.NOTE] Todos os seus scripts e código precisa considerar os ambientes e pontos de extremidade apropriados. Para obter mais informações, consulte [como conectar à nuvem de governo do Azure](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


### <a name="considerations"></a>Considerações

As informações a seguir identificam o limite de governo do Azure para Azure relacionada Cache:

| Dados regulamentadas/controlados permitidos | Dados regulamentadas/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados armazenados e processados em Cache relacionada do Azure podem conter dados regulamentadas governo do Azure. | Metadados de Cache relacionada Azure não é permitido para conter dados de exportação controlada. Não digite dados regulamentadas/controlados nos seguintes campos: nome, nome VNET, nome da assinatura, grupos de recursos, marcas de recurso, propriedades relacionada em Cache.  

##  <a name="next-steps"></a>Próximas etapas

Para informações complementares e atualizações assinar o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure governamentais.</a>
