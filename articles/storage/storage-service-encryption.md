<properties
    pageTitle="Criptografia de serviço de armazenamento do Azure para dados inativos | Microsoft Azure"
    description="Use o recurso de criptografia de serviço de armazenamento do Azure para criptografar o seu armazenamento de Blob do Azure no lado do serviço quando armazenar os dados e descriptografar ao recuperar dados."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="robinsh"/>

# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Criptografia de serviço de armazenamento do Azure para dados inativos

Azure armazenamento de serviço de criptografia (SSE) para os dados em Rest ajuda você a proteger e proteger seus dados para atender a segurança organizacional e compromissos de conformidade. Com esse recurso, o armazenamento do Azure criptografa os seus dados antes de persistir ao armazenamento automaticamente e descriptografa antes de recuperação. A criptografia, descriptografia e gerenciamento de chaves são totalmente transparentes aos usuários.

As seguintes seções fornecem experiências orientações detalhadas sobre como usar os recursos de criptografia do serviço de armazenamento, bem como os cenários com suporte e usuário.

## <a name="overview"></a>Visão geral

Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que permitem que os desenvolvedores criem aplicativos seguros juntos. Dados podem ser protegidos em trânsito entre um aplicativo e Azure usando [Criptografia do lado do cliente](storage-client-side-encryption.md), HTTPs ou SMB 3.0. Criptografia de serviço de armazenamento fornece criptografia inativos, tratamento de criptografia, descriptografia e gerenciamento de chaves de forma totalmente transparente. Todos os dados são criptografados com 256 bits [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), dentre as codificações de bloco mais fortes disponíveis.

SSE funciona criptografando os dados quando ele é escrito para o armazenamento do Azure e pode ser usado para bloquear blobs, blobs de página e acrescentar blobs. Ele funciona para o seguinte:

-   Contas de armazenamento de finalidade geral e armazenamento de Blob
-   Armazenamento padrão e Premium 
-   Redundância de todos os níveis (LRS, ZRS, GRS, ar-GRS)
-   Contas de armazenamento do Azure Gerenciador de recursos (mas não clássico) 
-   Todas as regiões

Para habilitar ou desabilitar a criptografia de serviço de armazenamento para uma conta de armazenamento, faça logon no [portal do Azure](https://azure.portal.com) e selecione uma conta de armazenamento. Na lâmina configurações, procure a seção de serviço Blob conforme mostrado nesta captura de tela e clique em criptografia.

![Opção de criptografia de portal captura de tela mostrando](./media/storage-service-encryption/image1.png)

Depois de clicar na configuração de criptografia, você pode habilitar ou desabilitar criptografia de serviço de armazenamento.

![Propriedades de criptografia de portal captura de tela mostrando](./media/storage-service-encryption/image2.png)

##<a name="encryption-scenarios"></a>Cenários de criptografia

Criptografia de serviço de armazenamento pode ser habilitada em um nível de conta de armazenamento. Ele oferece suporte para os seguintes cenários de cliente:

-   Criptografia de blobs do bloco, acrescentar blobs e blobs de página.

-   Criptografia de arquivados VHDs e trouxe para Azure do local de modelos.

-   Criptografia de discos de sistema operacional e dados subjacentes para VMs IaaS criados usando seu VHDs.

SSE tem as seguintes limitações:

-   Não há suporte para criptografia de contas de armazenamento clássico.

-   Não há suporte para criptografia de armazenamento clássico contas migradas para contas de armazenamento do Gerenciador de recursos.

-   Os dados existentes - SSE só criptografa dados recém-criado após a criptografia estiver habilitada. Se por exemplo você cria uma nova conta de armazenamento do Gerenciador de recursos, mas não ativar a criptografia e, em seguida, você pode carregar blobs ou VHDs arquivados para essa conta de armazenamento e ative SSE, esses blobs não serão criptografadas, a menos que eles são reconfigurados ou copiados.

-   Suporte de Marketplace - ativar criptografia de VMs criada do Marketplace usando o [portal do Azure](https://portal.azure.com), PowerShell e CLI do Azure. A imagem base VHD permanecerá não criptografada; Entretanto, todas as gravações feitas após a máquina virtual girado para cima serão criptografadas.

-   Tabela, filas, e dados de arquivos não serão criptografados.

##<a name="getting-started"></a>Guia de Introdução

###<a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Etapa 1: [criar uma nova conta de armazenamento](storage-create-storage-account.md).

###<a name="step-2-enable-encryption"></a>Etapa 2: Ative a criptografia.

Você pode habilitar a criptografia usando o [portal do Azure](https://portal.azure.com).

> [AZURE.NOTE] Se você quiser programaticamente habilitar ou desabilitar a criptografia de serviço de armazenamento em uma conta de armazenamento, você pode usar a [API REST de provedor de recursos do Azure armazenamento](https://msdn.microsoft.com/library/azure/mt163683.aspx), a [Biblioteca de cliente de provedor de recursos de armazenamento para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](../powershell-install-configure.md)ou a [CLI do Azure](storage-azure-cli.md).

###<a name="step-3-copy-data-to-storage-account"></a>Etapa 3: Copiar dados para a conta de armazenamento

Se você habilitar SSE em uma conta de armazenamento e, em seguida, escrever blobs a essa conta de armazenamento, os blobs serão criptografados. Qualquer blobs já localizados nessa conta de armazenamento não serão criptografadas até que eles são reconfigurados. Você pode copiar os dados da conta de armazenamento de um a um com SSE criptografado, ou até mesmo habilitar SSE e copiar os blobs de um contêiner para outro para certeza de que os dados anteriores estão criptografados. Você pode usar qualquer uma das seguintes ferramentas para fazer isso.

#### <a name="using-azcopy"></a>Usando AzCopy

AzCopy é um utilitário de linha de comando do Windows projetado para copiar dados para e do armazenamento de Blob do Microsoft Azure, arquivo e tabela usando comandos simples com um desempenho ideal. Você pode usar isso para copiar sua blobs de uma conta de armazenamento para outro que tenha SSE habilitado. 

Para saber mais, visite [transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md).

#### <a name="using-the-storage-client-libraries"></a>Usando as bibliotecas de cliente do armazenamento

Você pode copiar dados blob para e do armazenamento de blob ou entre contas de armazenamento usando nosso conjunto sofisticado de bibliotecas de cliente de armazenamento, incluindo .NET, C++, Java, Android, Node, PHP, Python e Ruby.

Para saber mais, visite nossa [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Usando o Gerenciador de armazenamento

Você pode usar o Gerenciador de armazenamento para criar contas de armazenamento, carregar e baixar dados, exibir conteúdo de blobs e navegar pelos diretórios. Você pode usar um destes procedimentos para carregar blobs em sua conta de armazenamento com criptografia ativada. Com alguns gerenciadores de armazenamento, você também pode copiar dados de armazenamento de blob existente para um contêiner diferente na conta de armazenamento ou uma nova conta de armazenamento que tem SSE habilitado.

Para saber mais, visite [Gerenciadores de armazenamento do Azure](storage-explorers.md).

###<a name="step-4-query-the-status-of-the-encrypted-data"></a>Etapa 4: Consultar o status dos dados criptografados

Foi implantada uma versão atualizada de bibliotecas do cliente de armazenamento do que permite que você consultar o estado de um objeto para determinar se ele está criptografado ou não. Exemplos serão adicionados a esse documento no futuro próximo.

Enquanto isso, você pode chamar [Obter propriedades da conta](https://msdn.microsoft.com/library/azure/mt163553.aspx) para verificar se a conta de armazenamento tem criptografia ativada ou exibir as propriedades da conta de armazenamento no portal do Azure.

##<a name="encryption-and-decryption-workflow"></a>Criptografia e descriptografia de fluxo de trabalho

Aqui está uma breve descrição do fluxo de trabalho criptografia/descriptografia:

-   O cliente permite a criptografia na conta de armazenamento.

-   Quando o cliente grava novos dados (colocar Blob, colocar bloco, página colocar, etc.) ao armazenamento de Blob; todas as gravações são criptografadas com 256 bits [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), dentre as codificações de bloco mais fortes disponíveis.

-   Quando o cliente precisa acessar dados (obter Blob, etc.), os dados são automaticamente decodificados antes de retornar ao usuário.

-   Se criptografia estiver desabilitada, novas gravações não são criptografadas e dados criptografados existentes permanecem criptografados até reescrito pelo usuário. Enquanto a criptografia estiver habilitada, gravações ao armazenamento de Blob serão criptografadas. Não altera o estado dos dados com o usuário alternar entre ativar/desativar a criptografia para a conta de armazenamento.

-   Todas as chaves de criptografia são armazenadas, criptografadas e gerenciadas pela Microsoft.

##<a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Perguntas frequentes sobre criptografia de serviço de armazenamento de dados inativos

**P: posso ter uma conta existente do armazenamento clássico. Pode habilitar SSE nele?**

R: não, SSE só é suportado em contas de armazenamento do Gerenciador de recursos.

**P: como pode criptografar dados na minha conta de armazenamento clássico?**

R: você pode criar uma nova conta de armazenamento do Gerenciador de recursos e copiar os dados usando [AzCopy](storage-use-azcopy.md) de sua conta de armazenamento clássico existente para sua conta de armazenamento recém-criado do Gerenciador de recursos. 

Outra opção é migrar sua conta de armazenamento clássico para uma conta de armazenamento gerenciar o recurso. Para obter mais informações, consulte [Plataforma com suporte de IaaS recursos de migração do clássico ao Gerenciador de recursos](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/).

**P: posso ter uma conta de armazenamento do Gerenciador de recursos existentes. Pode habilitar SSE nele?**

R: Sim, mas apenas recentemente gravados blobs serão criptografados. Ele não voltar e criptografar dados que já estava presentes. 

**P: gostaria de criptografar os dados atuais em uma conta de armazenamento do Gerenciador de recursos existentes?**

R: você pode habilitar SSE a qualquer momento em uma conta de armazenamento do Gerenciador de recursos. No entanto, bolhas que já foram presentes não serão criptografadas. Para criptografar esses blobs, você poderá copiá-los para outro nome ou outro contêiner e remova as versões não criptografadas.

**P: Estou usando o armazenamento de Premium; Posso usar o SSE?**

R: Sim, SSE é suportado em armazenamento de Premium e de armazenamento padrão.

**P: se eu criar uma nova conta de armazenamento e habilitar SSE, depois de criar uma nova máquina virtual usando essa conta de armazenamento, isso significa que minha máquina virtual está criptografado?**

R: Sim. Qualquer disco criado que usam a nova conta de armazenamento será criptografado, desde que eles são criados após SSE está habilitado. Se a máquina virtual foi criada usando o Azure Market Place, a imagem base VHD permanecerá não criptografada; Entretanto, todas as gravações feitas após a máquina virtual girado para cima serão criptografadas.

**P: é possível criar novas contas de armazenamento com SSE habilitado usando o PowerShell do Azure e Azure CLI?**

R: Sim.

**P: como muito mais armazenamento do Azure custa se SSE está habilitado?**

R: não há nenhum custo adicional.

**P: quem gerencia as chaves de criptografia?**

R: as teclas são gerenciadas pela Microsoft.

**P: posso usar Meus próprio chaves de criptografia?**

R: estamos trabalhando em fornecendo recursos para clientes trazer suas próprias chaves de criptografia.

**P: é possível revogar o acesso às chaves de criptografia?**

R: não neste momento; as teclas são totalmente gerenciadas pela Microsoft.

**P: é SSE habilitado por padrão ao criar uma nova conta de armazenamento?**

R: SSE não está habilitado por padrão; Você pode usar o portal do Azure para ativá-lo. Você pode também programaticamente ativar esse recurso usando a API REST de provedor de recursos de armazenamento.

**P: como este é diferente da criptografia de unidade do Azure?**

R: esse recurso é usado para criptografar dados no armazenamento de Blob do Azure. A criptografia de disco do Azure é usada para criptografar discos de dados e sistema operacional em VMs IaaS. Para obter mais detalhes, visite o nosso [Guia de segurança de armazenamento](storage-security-guide.md).

**P: o que acontece se habilitar SSE e, em seguida, entrar e ativar a criptografia de disco do Azure nos discos?**

R: isso funcionará perfeitamente. Seus dados serão criptografados por ambos os métodos.

**P: minha conta de armazenamento está configurada para ser replicados geográfica redundante. Se eu ativar SSE, minha cópia redundante também será criptografada?**

R: Sim, todas as cópias da conta de armazenamento estão criptografadas, e todas as opções de redundância – armazenamento localmente redundantes (LRS), armazenamento redundante de zona (ZRS), armazenamento redundante geográfica (GRS) e acesso de leitura redundante geográfica armazenamento (ar-GRS) – são compatíveis.

**P: não é possível habilitar criptografia na minha conta de armazenamento.**

R: é uma conta de armazenamento do Gerenciador de recursos? Não há suporte para contas de armazenamento clássico. 

**P: SSE só é permitido em regiões específicas?**

R: o SSE está disponível em todas as regiões. 

**P: como contatar alguém se eu tiver problemas ou quiser fornecer comentários?**

R: entre em contato com [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) por problemas relacionados à criptografia de serviço de armazenamento.

##<a name="next-steps"></a>Próximas etapas

Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que permitem que os desenvolvedores criem aplicativos seguros juntos. Para obter mais detalhes, visite o [Guia de segurança de armazenamento](storage-security-guide.md).
