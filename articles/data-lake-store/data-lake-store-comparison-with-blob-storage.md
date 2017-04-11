<properties
   pageTitle="Comparação de armazenamento de Lucerne de dados do Azure com o Azure armazenamento de Blob | Microsoft Azure"
   description="Comparação de armazenamento de Lucerne de dados do Azure com o Azure armazenamento de Blob"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Comparando armazenamento de Lucerne de dados do Azure e armazenamento de blob do Microsoft Azure

A tabela neste artigo resume as diferenças entre o armazenamento de Lucerne de dados do Azure e armazenamento de Blob do Azure ao longo de alguns aspectos chaves grande do processamento de dados. Armazenamento de Blob do Azure é uma finalidade geral, o armazenamento de objeto scalable que destina-se uma ampla variedade de cenários de armazenamento. Azure Data Lucerne Store é um repositório de escala hyper que é otimizado para cargas de trabalho de análise de dados grande.

|    | Armazenamento de Lucerne de dados do Azure  | Armazenamento de blob do Microsoft Azure  |
|----|-----------------------|--------------------|
| Finalidade  | Armazenamento otimizado para cargas de trabalho de análise de dados grande                                                                          | Objeto de finalidade geral armazenar para uma ampla variedade de cenários de armazenamento                                                                                |
| Casos de uso                                   | Lote, interativo, streaming análises e dados de aprendizado de máquina como arquivos de log, IoT dados, clique em fluxos, grandes conjuntos de dados | Qualquer tipo de dados de texto ou binário, como aplicativo final, dados de backup, armazenamento de mídia para dados de finalidade geral e streaming de volta |
| Conceitos-chave                                | Conta de armazenamento de Lucerne dados contém pastas, que por sua vez contém dados armazenados como arquivos | Conta de armazenamento tem contêineres, que por sua vez tem dados na forma de blobs |
| Estrutura | Sistema de arquivos hierárquico                                                                                                    | Armazenamento de objeto com namespace simples  |
| API   | API REST em HTTPS | API REST sobre HTTP/HTTPS                                                                                                                            |
| API de servidor                             | [API REST compatível com o WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx)                                                                                                 | [Armazenamento de blob do Microsoft Azure API REST](https://msdn.microsoft.com/library/azure/dd135733.aspx)                                                                                                                         |
| Cliente do sistema de arquivo Hadoop                   | Sim                                                                                                                         | Sim                                                                                                                                                 |
| Operações de dados - autenticação            | Com base em [identidades do Active Directory do Azure](../active-directory/active-directory-authentication-scenarios.md) | Com base em segredos compartilhados - [Teclas de acesso de conta](../storage/storage-create-storage-account.md#manage-your-storage-account) e [Chaves de assinatura de acesso compartilhado](../storage/storage-dotnet-shared-access-signature-part-1.md).                                                                       |
| Operações de dados - protocolo de autenticação     | OAuth 2.0. Chamadas devem conter um JWT válido (JSON Web Token) emitido por Active Directory do Azure                     | Código de autenticação de mensagem baseado em hash (HMAC). Chamadas devem conter um hash SHA-256 codificado na Base 64 sobre uma parte da solicitação HTTP. |
| Operações de dados - autorização               | Listas de controle de acesso do POSIX (ACLs).  ACLs com base no Azure Active Directory identidades podem ser definidas o nível de arquivo e pasta. | Nível de conta autorização – usar [As teclas de acesso de conta](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>Para conta, contêiner ou blob autorização - usar [Chaves de assinatura de acesso compartilhado](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| Operações de dados - auditoria                    | Disponível. Veja [aqui](data-lake-store-diagnostic-logs.md) para obter informações.                                                                                                                   | Disponível                                                                                                                                           |
| Dados de criptografia inativos                     | Transparente, do lado do servidor (em breve)<ul><li>Com chaves de serviços gerenciados</li><li>Com teclas gerenciado pelo cliente no Azure KeyVault</li></ul>| <ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves de serviços gerenciados</li><li>Com teclas gerenciado pelo cliente no Azure KeyVault (em breve)</li></ul><li>Criptografia do lado do cliente</li></ul> |
| Operações de gerenciamento (por exemplo, conta criar) | [Controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) (RBAC) fornecida pelo Azure para gerenciamento de conta                                                                       | [Controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) (RBAC) fornecida pelo Azure para gerenciamento de conta                                                                                               |
| SDKs de desenvolvedor                              | .NET, Java, Node                                                                                                         | .NET, Java, Python, Node, C++, Ruby                                                                                                              |
| Desempenho da carga de análise              | Desempenho otimizado para cargas de trabalho de análise paralelos. Alta taxa de transferência e IOPS.                                           | Não otimizado para cargas de trabalho de análise                                                                                                               |
| Limites de tamanho                                 | Não há limites no número de arquivos, tamanhos de arquivo ou tamanhos de conta                                                                   | Limites específicos documentadas [aqui](../azure-subscription-service-limits.md#storage-limits)                                                                                                                     |
| Redundância geográfica                              | Localmente redundante (várias cópias de dados em uma região Azure)                                                             | Localmente redundantes (LRS), globalmente redundantes (GRS), acesso de leitura globalmente redundantes (ar-GRS). Veja [aqui](../storage/storage-redundancy.md) para obter mais informações |
| Estado do serviço                               | Demonstração pública                                                                                                              | Disponibilidade geral                                                                                                                                 |
| Disponibilidade regional  | Veja [aqui](https://azure.microsoft.com/regions/#services)| Veja [aqui](https://azure.microsoft.com/regions/#services) |
| Preço                                       |     Consulte [preços](https://azure.microsoft.com/pricing/details/data-lake-store/)| Consulte [preços](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Lucerne de repositório de dados](data-lake-store-overview.md)
- [Introdução ao armazenamento de Lucerne de dados](data-lake-store-get-started-portal.md)



