<properties
   pageTitle="Visão geral de segurança de armazenamento do Azure | Microsoft Azure"
   description=" Armazenamento do Azure é a solução de armazenamento de nuvem para aplicativos modernos que contam com durabilidade, disponibilidade e escalabilidade para atender às necessidades dos seus clientes. Este artigo fornece uma visão geral do núcleo recursos de segurança do Azure que podem ser usados com o armazenamento do Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# <a name="azure-storage-security-overview"></a>Visão geral de segurança de armazenamento do Azure

Armazenamento do Azure é a solução de armazenamento de nuvem para aplicativos modernos que contam com durabilidade, disponibilidade e escalabilidade para atender às necessidades dos seus clientes. Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança:

- A conta de armazenamento pode ser protegida usando o controle de acesso baseado em função e Azure Active Directory.
- Dados podem ser protegidos em trânsito entre um aplicativo e Azure usando criptografia do lado do cliente, HTTPS ou SMB 3.0.
- Dados podem ser definidos para ser criptografados automaticamente quando gravados ao armazenamento do Azure usando criptografia de serviço de armazenamento.
- Sistema operacional e dados discos usados por máquinas virtuais podem ser definidos para ser criptografados usando criptografia de disco do Azure.
- Acesso delegado para os objetos de dados em armazenamento Azure pode ser concedido usando assinaturas de acesso compartilhado.
- O método de autenticação usado por alguém quando acessam armazenamento pode ser controlado por meio de análises de armazenamento.

Para uma visão mais detalhada segurança no armazenamento do Azure, consulte o [guia de segurança de armazenamento do Azure](../storage/storage-security-guide.md). Este guia fornece detalhamento os recursos de segurança de armazenamento do Azure como chaves de conta de armazenamento, criptografia de dados em trânsito e em restante e a análise de armazenamento.

Este artigo fornece uma visão geral dos recursos de segurança do Azure que podem ser usados com o armazenamento do Azure. Links são fornecidas para artigos que fornecem detalhes de cada recurso isso você pode saber mais.

Aqui estão os principais recursos a serem abordados neste artigo:

- Controle de acesso baseado em função
- Acesso delegado a objetos de armazenamento
- Criptografia em trânsito
- Criptografia no restante/criptografia do serviço de armazenamento
- Criptografia de disco Azure
- Azure cofre chave

## <a name="role-based-access-control-rbac"></a>Controle de acesso baseado em função (RBAC)

Você pode proteger sua conta de armazenamento com controle de acesso baseado em função (RBAC). Restringindo o acesso com base nos princípios de segurança [precisa saber](https://en.wikipedia.org/wiki/Need_to_know) e [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é fundamental para as organizações que deseja aplicar políticas de segurança para acesso a dados. Esses direitos de acesso são concedidos atribuindo a função RBAC adequada aos grupos e aplicativos em um determinado escopo. Você pode usar [funções RBAC internas](../active-directory/role-based-access-built-in-roles.md), como colaborador da conta de armazenamento, atribuir privilégios aos usuários.

Saiba Mais:

- [Controle de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Acesso delegado a objetos de armazenamento

Uma assinatura de acesso compartilhado (SAS) fornece acesso delegado a recursos em sua conta de armazenamento. As associações de segurança significa que você pode conceder que permissões a objetos em sua conta de armazenamento de limitadas de um cliente por um determinado período de tempo e com um conjunto de permissões especificado. Você pode conceder essas permissões limitadas sem precisar compartilhar suas chaves de acesso de conta. As associações de segurança é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com as associações de segurança, o cliente só precisa passar as associações de segurança para o método ou construtor apropriado.

Saiba Mais:

- [Compreendendo o modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [Criar e usar um com o armazenamento de Blob](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Criptografia em trânsito
Criptografia em trânsito é um mecanismo de proteção de dados quando ele é transmitido em redes. Com o armazenamento do Azure, você pode proteger dados usando:

- [Criptografia de nível de transporte](../storage/storage-security-guide.md#encryption-in-transit), como HTTPS ao transferir dados em ou reduzir o armazenamento do Azure.
- [Criptografia de fios](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como criptografia SMB 3.0 para compartilhamentos de arquivos do Azure.
- [Criptografia do lado do cliente](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para criptografar os dados antes de serem transferido para o armazenamento e descriptografar os dados após transferido sem armazenamento.

Saiba mais sobre criptografia do lado do cliente:

- [Criptografia do lado do cliente para armazenamento do Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Série de controles de segurança na nuvem: criptografar dados em trânsito](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Criptografia inativos

Para muitas organizações, a [criptografia de dados no restante](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória na direção de privacidade de dados, conformidade e Soberania de dados. Há três recursos Azure que fornecem criptografia de dados que é "at rest":

- [Criptografia de serviço de armazenamento](../storage/storage-security-guide.md#encryption-at-rest) permite que você solicite que o serviço de armazenamento automaticamente criptografar dados ao escrevê-lo ao armazenamento do Azure.
- [Criptografia do lado do cliente](../storage/storage-security-guide.md#client-side-encryption) também fornece o recurso de criptografia inativos.
- [Criptografia de disco do Azure](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) permite criptografar o sistema operacional discos e dados usados por uma máquina virtual de IaaS.

Saiba mais sobre criptografia de serviço de armazenamento:

- [Criptografia de serviço de armazenamento do Azure](https://azure.microsoft.com/services/storage/) está disponível para [Armazenamento de Blob do Azure](https://azure.microsoft.com/services/storage/blobs/). Para obter detalhes sobre outros tipos de armazenamento do Azure, consulte [arquivo](https://azure.microsoft.com/services/storage/files/), [disco (armazenamento Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [tabela](https://azure.microsoft.com/services/storage/tables/)e [fila](https://azure.microsoft.com/services/storage/queues/).
- [Criptografia de serviço de armazenamento do Azure para dados inativos](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Criptografia de disco Azure

Azure criptografia de disco para máquinas virtuais (VMs) ajuda você a endereço organizacional requisitos de segurança e conformidade criptografando seus discos de máquina virtual (inclusive as unidades de inicialização e de dados) com chaves e políticas de que controle no [Azure chave cofre](https://azure.microsoft.com/services/key-vault/).

Criptografia de disco para VMs funciona para sistemas operacionais Linux e Windows. Ele também usa chave cofre para ajudá-lo a proteger, gerenciar e auditoria de uso de suas chaves de criptografia de disco. Todos os dados de seus discos de máquina virtual está criptografado inativos usando a tecnologia de criptografia padrão da indústria em suas contas de armazenamento do Azure. A solução de criptografia de disco para Windows baseia-se a [Criptografia de unidade de BitLocker da Microsoft](https://technet.microsoft.com/library/cc732774.aspx)e a solução Linux se baseia [dm crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Saiba Mais:

- [Criptografia de disco Azure para Windows e máquinas virtuais de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure cofre chave

Criptografia de disco Azure usa [Cofre de chave do Azure](https://azure.microsoft.com/services/key-vault/) para ajudá-lo a controlar e gerenciar chaves de criptografia de disco e senhas na sua assinatura de chave cofre, garantindo que todos os dados na máquina virtual discos são criptografados inativos em seu armazenamento do Azure. Você deve usar chave cofre para chaves e o uso de política de auditoria.

Saiba Mais:

- [O que é Azure chave cofre?](../key-vault/key-vault-whatis.md)
- [Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md)
