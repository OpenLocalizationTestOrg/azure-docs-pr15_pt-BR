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
    ms.date="09/30/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-data-and-storage"></a>Dados do azure governo e armazenamento

##  <a name="azure-storage"></a>Armazenamento do Azure

Para obter detalhes sobre esse serviço e como usá-lo, consulte a [documentação de público de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/).

### <a name="variations"></a>Variações

As URLs para contas de armazenamento no Azure governo são diferentes:

Tipo de serviço|Azure público|Governo Azure
---|---|---
Armazenamento de blob|*. blob.core.windows.net|*. blob.core.usgovcloudapi.net
Armazenamento de fila|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Armazenamento de tabela|*. table.core.windows.net| *. table.core.usgovcloudapi.net

>[AZURE.NOTE] Todos os seus scripts e o código precisa de conta para pontos de extremidade apropriados.  Consulte [Configurar cadeias de caracteres de Conexão de armazenamento do Azure](../storage-configure-connection-string.md#creating-a-connection-string-to-the-explicit-storage-endpoint). 

Para obter mais informações sobre APIs consulte o <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Construtor de conta de armazenamento de nuvem</a>.

O sufixo do ponto de extremidade para usar nessas sobrecargas é core.usgovcloudapi.net 

### <a name="considerations"></a>Considerações

As informações a seguir identificam o limite de governo do Azure para o armazenamento do Azure:

| Dados regulamentadas/controlados permitidos | Dados regulamentadas/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dados inseridos, armazenados e processadas dentro de um armazenamento do Azure produto pode conter dados de exportação controlada. Autenticadores estáticos, como senhas e PINs de cartão inteligente para obter acesso a componentes de plataforma Windows Azure. Chaves particulares de certificados usados para gerenciar os componentes de plataforma Windows Azure. Outros segurança informações/segredos, como certificados, chaves de criptografia, chaves mestre e chaves de armazenamento armazenadas nos serviços do Azure. | Azure metadados de armazenamento não é permitido para conter dados de exportação controlada. Esses metadados incluem todos os dados de configuração inseridos quando criar e manter seu produto de armazenamento.  Não digite dados regulamentadas/controlados nos seguintes campos: grupos de recursos, nomes de implantação, nomes de recursos, marcas de recurso  

##  <a name="premium-storage"></a>Armazenamento de Premium

Para obter detalhes sobre esse serviço e como usá-lo, consulte [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).

###  <a name="variations"></a>Variações

Armazenamento de Premium estará disponível em Virgínia a USGov. Isso inclui máquinas virtuais de série DS. 

### <a name="considerations"></a>Considerações

Aplicam as mesmas considerações de dados de armazenamento listadas acima para contas de armazenamento premium. 

##  <a name="sql-database"></a>Banco de dados SQL

Consulte a [Documentação do público de banco de dados do SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/) e o<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centro de segurança da Microsoft para o mecanismo de banco de dados SQL</a> para orientação adicional sobre configuração de visibilidade de metadados e práticas recomendadas de proteção.

### <a name="variations"></a>Variações

Banco de dados do SQL V12 estará disponível no governo do Azure.

O endereço do SQL Azure Servers no Azure governo é diferente:

Tipo de serviço|Azure público|Governo Azure
---|---|---
Banco de dados SQL|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Considerações

As informações a seguir identificam o limite de governo do Azure para o armazenamento do Azure:

| Dados regulamentadas/controlados permitidos | Dados regulamentadas/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados armazenados e processados em SQL do Microsoft Azure podem conter dados regulamentadas governo do Azure. Você deve usar ferramentas de banco de dados para transferir dados de dados regulamentadas governo do Azure. | Metadados SQL Azure não é permitido para conter dados de exportação controlada. Esses metadados incluem todos os dados de configuração inseridos quando criar e manter seu produto de armazenamento.  Não digite dados regulamentadas/controlados nos seguintes campos: nome, nome da assinatura, grupos de recursos, o nome do servidor, logon de administrador do servidor, nomes de implantação, nomes de recursos, marcas de recurso de banco de dados

##  <a name="next-steps"></a>Próximas etapas

Para informações complementares e atualizações assinar o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure governamentais.</a>
