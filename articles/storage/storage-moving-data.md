<properties
    pageTitle="Mover dados para e do armazenamento do Azure | Microsoft Azure"
    description="Este artigo fornece uma visão geral dos diferentes métodos para mover dados de e para o armazenamento do Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="micurd"/>

# <a name="moving-data-to-and-from-azure-storage"></a>Mover os dados para e do armazenamento do Azure

Se você desejar mover dados locais para o armazenamento do Azure (ou vice-versa), há várias maneiras de fazer isso. A abordagem que funciona melhor para você dependerá seu cenário. Este artigo fornece uma descrição rápida das ofertas apropriadas para cada um deles e cenários diferentes.

## <a name="building-applications"></a>Criação de aplicativos

Se você estiver criando um aplicativo, desenvolvendo contra a API REST ou uma das nossas muitas bibliotecas de cliente é uma ótima maneira de mover dados e armazenamento do Azure.

Armazenamento do Azure fornece bibliotecas de cliente avançado para .NET, iOS, Java, Android, Universal Windows plataforma (UWP), Xamarin, C++, Node, PHP, Ruby e Python. As bibliotecas de cliente oferecem recursos avançados como lógica de repetição, log e carregamentos paralelos. Você também pode desenvolver diretamente com a API REST, que pode ser chamado por qualquer idioma que faz solicitações HTTP/HTTPS.

Consulte [Introdução ao armazenamento de Blob do Azure](storage-dotnet-how-to-use-blobs.md) para saber mais.

Além disso, também oferecemos a [Biblioteca de movimentação de dados de armazenamento do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) que é uma biblioteca projetada para alto desempenho cópia de dados para e do Azure. Consulte nossa biblioteca de movimentação de dados [documentação](https://github.com/Azure/azure-storage-net-data-movement) para saber mais. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Rapidamente a exibição/interagindo com seus dados

Se você quiser uma maneira fácil de exibir seus dados de armazenamento do Azure tendo também a capacidade de carregar e baixar seus dados, em seguida, considere usar um Gerenciador de armazenamento do Azure.

Confira a nossa lista de [Gerenciadores de armazenamento do Azure](storage-explorers.md) para saber mais.

## <a name="system-administration"></a>Administração do sistema

Se você exige ou é mais confortáveis com um utilitário de linha de comando (por exemplo, administradores de sistema), aqui estão algumas opções para você considerar:

### <a name="azcopy"></a>AzCopy

AzCopy é um utilitário de linha de comando do Windows projetado para alto desempenho copiando dados de e armazenamento do Azure. Você também pode copiar dados dentro de uma conta de armazenamento, ou entre contas de armazenamento diferentes.

Para saber mais, consulte [transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md) .

### <a name="azure-powershell"></a>PowerShell Azure

PowerShell Azure é um módulo que fornece cmdlets para gerenciar os serviços do Azure. É um shell de linha de comando baseado em tarefas e a linguagem de script projetado especialmente para administração do sistema.

Consulte [Usando o PowerShell do Azure com o armazenamento do Azure](storage-powershell-guide-full.md) para saber mais.

### <a name="azure-cli"></a>CLI Azure

CLI Azure fornece um conjunto de código-fonte aberto, entre plataformas comandos para trabalhar com os serviços do Azure. CLI Azure está disponível no Windows, OSX e Linux.

Consulte [utilizando a CLI Azure com o armazenamento do Azure](storage-azure-cli.md) para saber mais.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Mover grandes quantidades de dados com uma rede lenta

Um dos maiores desafios associados à movimentação grandes quantidades de dados é o tempo de transferência. Se quiser obter dados de/para o armazenamento do Azure sem se preocupar sobre custos de redes ou escrever código, Azure importação/exportação é uma solução apropriada.

Consulte [Importação/exportação do Azure](storage-import-export-service.md) para saber mais.

## <a name="backing-up-your-data"></a>Fazer backup de seus dados

Se você simplesmente precisar fazer backup dos dados para o armazenamento do Azure, o Backup do Azure é a maneira para ir. Esta é uma solução poderosa para fazer backup de dados locais e VMs do Azure.

Consulte [Backup do Azure](../backup/backup-introduction-to-azure-backup.md) para saber mais.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Acessar seus dados locais e da nuvem

Se precisar de uma solução para acessar seus dados locais e da nuvem, você deve considerar usando a solução de armazenamento em nuvem do Azure híbrido, StorSimple. Essa solução consiste em um dispositivo StorSimple físico que inteligente frequentemente armazena dados usados no SSDs, ocasionalmente os dados usados em unidades de disco rígido e dados inativos/backup/arquivamento no armazenamento do Azure.

Consulte [StorSimple](../storsimple/storsimple-overview.md) para saber mais.

## <a name="recovering-your-data"></a>Recuperando seus dados

Quando você tiver cargas de trabalho local e aplicativos, você precisará de uma solução que permite que sua empresa continue a ser executado em caso de um desastre. Recuperação de Site Azure manipula replicação, failover e recuperação de máquinas virtuais e servidores físicos. Dados duplicados serão armazenados em armazenamento do Azure, permitindo que você eliminar a necessidade de uma análise no local secundário.

Consulte [Recuperação de Site do Azure](../site-recovery/site-recovery-overview.md) para saber mais.
