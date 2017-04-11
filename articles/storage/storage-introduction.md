<properties
    pageTitle="Introdução ao armazenamento | Microsoft Azure"
    description="Uma visão geral do armazenamento do Azure, armazenamento de dados on-line da Microsoft na nuvem. Saiba como usar a melhor solução de armazenamento de nuvem disponíveis em seus aplicativos."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Introdução ao Microsoft Azure armazenamento

## <a name="overview"></a>Visão geral

Armazenamento do Azure é a solução de armazenamento de nuvem para aplicativos modernos que contam com durabilidade, disponibilidade e escalabilidade para atender às necessidades dos seus clientes. Lendo este artigo, desenvolvedores, profissionais de TI e decisões de negócios tomadores Saiba sobre:

- O que é o armazenamento do Azure e como você pode tirar proveito em nuvem, móvel, servidor e aplicativos da área de trabalho
- Que tipos de dados que você pode armazenar com os serviços de armazenamento do Azure: blob de dados (objeto), dados de tabela NoSQL, mensagens de fila e compartilhamentos de arquivo.
- Como o acesso aos dados de armazenamento do Azure é gerenciado
- Como seus dados de armazenamento do Azure são feitos duráveis via redundância e replicação
- Onde Avançar para criar seu primeiro aplicativo de armazenamento do Azure

Para começar a trabalhar com o armazenamento do Azure rapidamente, consulte [Introdução ao armazenamento do Azure em cinco minutos](storage-getting-started-guide.md).

Para obter detalhes sobre ferramentas, bibliotecas e outros recursos para trabalhar com o armazenamento do Azure, consulte [Próximas etapas](#next-steps) abaixo.

## <a name="what-is-azure-storage"></a>O que é o armazenamento do Azure?

Computação permite novos cenários para aplicativos que exigem armazenamento scalable, durável e altamente disponível para seus dados – o que é exatamente por que a Microsoft desenvolveu Azure armazenamento em nuvem. Além de tornando possível para os desenvolvedores criem aplicativos de grande escala para dar suporte a novos cenários, armazenamento do Azure também fornece a base de armazenamento para máquinas virtuais do Azure, uma prova ainda mais sua eficiência.

Armazenamento do Azure é amplamente escalável, portanto, você pode armazenar e processo centenas de terabytes de dados para oferecer suporte os cenários de grande volume necessários análise científica, financeiro e aplicativos de mídia. Ou você pode armazenar as pequenas quantidades de dados necessários para um site de pequenas empresas. Sempre que se enquadram às suas necessidades, você paga apenas os dados que está armazenado. Armazenamento do Azure atualmente armazena dezenas de trilhões de objetos customer exclusivos e manipula milhões de solicitações por segundo em média.

Armazenamento do Azure é Elástico, portanto, você pode projetar aplicativos para um grande público global e redimensionar esses aplicativos conforme necessário - tanto em termos a quantidade de dados armazenados e o número de solicitações feitas em relação a ele. Você paga apenas para o que você usa, e somente quando você usá-lo.

Armazenamento do Azure usa um sistema de partição automático que equilibra automaticamente a carga seus dados com base no tráfego. Isso significa que como as exigências em aumentar seu aplicativo, o armazenamento do Azure aloca automaticamente os recursos apropriados para atendê-los.

Armazenamento do Azure é acessível a partir de qualquer lugar do mundo, de qualquer tipo de aplicativo, se ele está em execução na nuvem, na área de trabalho, em um servidor local ou em um celular ou tablet dispositivo. Você pode usar o armazenamento do Azure em cenários móveis onde o aplicativo armazena um subconjunto de dados no dispositivo e sincroniza com um conjunto completo de dados armazenados na nuvem.

Armazenamento do Azure suporta clientes usando um conjunto diverso de sistemas operacionais (inclusive Windows e Linux) e uma variedade de linguagens de programação (incluindo .NET, Java, Node, Python, Ruby, PHP e C++ e linguagens de programação móveis) para desenvolvimento conveniente. Armazenamento do Azure também expõe recursos de dados via APIs REST simples, o que estão disponíveis para qualquer cliente capaz de envio e recebimento de dados por meio de HTTP/HTTPS.

Armazenamento do Azure Premium oferece suporte de disco de alto desempenho e baixa latência para i/o intenso cargas de trabalho em máquinas virtuais do Azure. Com o armazenamento do Azure Premium, você pode anexar vários discos de dados persistentes a uma máquina virtual e configurá-los para atender às suas necessidades de desempenho. Cada disco de dados é feito por um disco SSD em armazenamento do Azure Premium máximo desempenho de e/s. Consulte [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md) para obter mais detalhes.

## <a name="introducing-the-azure-storage-services"></a>Apresentando os serviços de armazenamento do Azure

Armazenamento do Azure fornece os seguintes quatro serviços: Blob, armazenamento de tabela, fila do armazenamento e armazenamento de arquivos.

- Armazenamento de blob armazena dados de objeto não estruturados. Um blob pode ser qualquer tipo de texto ou dados binários, como um documento, arquivo de mídia ou instalador do aplicativo. Armazenamento de blob também é conhecido como armazenamento do objeto.
- Armazenamento de tabela armazena conjuntos de dados estruturados. Armazenamento de tabela é um armazenamento de dados de atributo key NoSQL, que permite desenvolvimento rápido e acesso rápido a grandes quantidades de dados.
- Armazenamento de fila fornece mensagens confiáveis para processamento de fluxo de trabalho e para comunicação entre os componentes de serviços de nuvem.
- Armazenamento de arquivos oferece armazenamento compartilhado para aplicativos herdados usando o protocolo SMB padrão. Azure máquinas virtuais e os serviços de nuvem podem compartilhar dados de arquivos em componentes de aplicativo por meio de compartilhamentos montados e aplicativos de locais podem acessar dados de arquivo em um compartilhamento por meio do serviço de arquivo API REST.

Uma conta de armazenamento do Azure é uma conta de segura que lhe dá acesso aos serviços do armazenamento do Azure. Sua conta de armazenamento fornece namespace exclusivo para seus recursos de armazenamento. A imagem abaixo mostra as relações entre os recursos de armazenamento do Azure em uma conta de armazenamento:

![Recursos de armazenamento do Azure](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Armazenamento de blob

Para usuários com grandes quantidades de dados de objeto não estruturados armazenar na nuvem, o armazenamento de Blob oferece uma solução econômica e escalável. Você pode usar o armazenamento de Blob para armazenar conteúdo, como:

- Documentos
- Dados sociais como fotos, vídeos, músicas e blogs
- Backups de arquivos, computadores, bancos de dados e dispositivos
- Imagens e texto para aplicativos web
- Dados de configuração para aplicativos em nuvem
- Dados grandes, como os logs e outros grandes conjuntos de dados

Cada blob é organizada em um contêiner. Contêineres também fornecem uma maneira útil para atribuir políticas de segurança para grupos de objetos. Uma conta de armazenamento pode conter qualquer número de contêineres, e um contêiner pode conter qualquer número de blobs, até o limite de capacidade de 500 TB da conta de armazenamento.  

Blob storage oferece três tipos de blobs, bloquear blobs, acrescentar blobs e blobs de página (discos).

- Bloco blobs são otimizados para streaming e armazenando objetos de nuvem e são uma boa opção para armazenar documentos, arquivos de mídia, backups etc.
- Acrescentar blobs são semelhantes aos bloco blobs, mas são otimizados para acrescentar operações. Um blob de acréscimo pode ser atualizado apenas adicionando um novo bloco de até o final. Acrescentar blobs são uma boa opção para cenários como log, onde os novos dados precisam ser gravados apenas para o final do blob.
- Blobs de página são otimizados para representar IaaS discos e suporte aleatório grava e pode ser até 1 TB em tamanho. Uma rede do Azure máquina virtual anexados IaaS disco é um VHD armazenado como um blob de página.

Para conjuntos de dados muito grandes onde restrições de rede fazer upload ou download de dados para o armazenamento de Blob durante a transmissão irreal, você pode entregar um disco rígido à Microsoft para importar ou exportar dados diretamente do data center. Consulte [usar o serviço de importação/exportação do Microsoft Azure para transferir dados para armazenamento de Blob](storage-import-export-service.md).

## <a name="table-storage"></a>Armazenamento de tabela

Aplicativos modernos geralmente exigem armazenamentos de dados com maior escalabilidade e flexibilidade que a geração anterior do software necessários. Armazenamento de tabela oferece armazenamento altamente disponível e amplamente escaláveis, para que seu aplicativo pode dimensionar automaticamente para atender à demanda do usuário. Armazenamento de tabela é o repositório de chave/atributo NoSQL da Microsoft – tem um design schemaless, tornando mais diferente do bancos de dados relacionais tradicionais. Com um armazenamento de dados schemaless, é fácil adaptar seus dados conforme as necessidades dos seus evolve de aplicativo. Armazenamento de tabela é fácil de usar, para que os desenvolvedores podem criar aplicativos rapidamente. Acesso a dados é rápido e econômico para todos os tipos de aplicativos.  Armazenamento de tabela normalmente é significativamente menor no custo SQL tradicional para semelhantes volumes de dados.

Armazenamento de tabela é uma loja de atributo key, significando que cada valor em uma tabela é armazenado com um nome de propriedade digitado. O nome da propriedade pode ser usado para filtrar e especificar critérios de seleção. Uma coleção de propriedades e seus valores compõem uma entidade. Como o armazenamento de tabela é schemaless, duas entidades na mesma tabela podem conter diferentes conjuntos de propriedades, e essas propriedades podem ser de diferentes tipos.

Você pode usar o armazenamento de tabela para armazenar flexíveis conjuntos de dados, como dados de usuário para aplicativos web, catálogos de endereços, informações de dispositivo e qualquer outro tipo de metadados que requer o seu serviço.  Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter qualquer número de tabelas, para cima até o limite de capacidade da conta de armazenamento.

Como Blobs e filas, os desenvolvedores podem gerenciar e tabela do access protocolos de armazenamento usando o resto padrão, no entanto, o armazenamento de tabela também dão suporte a um subconjunto do protocolo OData, simplificando consultando recursos avançados e habilitando JSON e AtomPub (baseado em XML) formatos.

Para aplicativos baseados na Internet de hoje, bancos de dados NoSQL como armazenamento de tabela oferecem uma alternativa popular bancos de dados relacionais tradicionais.

## <a name="queue-storage"></a>Armazenamento de fila

Criar aplicativos para escala, componentes de aplicativo geralmente são desassociados, para que eles possam ser redimensionados independentemente. Armazenamento de fila fornece uma solução de mensagens confiável para comunicação assíncrona entre componentes de aplicativo, se eles estiverem executando na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. Armazenamento de fila também dá suporte a gerenciamento de tarefas assíncronas e criar fluxos de trabalho do processo.

Uma conta de armazenamento pode conter qualquer número de filas. Uma fila pode conter qualquer número de mensagens, até o limite de capacidade da conta de armazenamento. Mensagens individuais podem ser até 64 KB em tamanho.

## <a name="file-storage"></a>Armazenamento de arquivos

Armazenamento de arquivos Azure oferece compartilhamentos de arquivos SMB baseado em nuvem, para que você pode migrar aplicativos herdados que dependem de compartilhamentos de arquivos para o Azure rapidamente e sem revisões dispendiosos. Com o armazenamento de arquivo do Azure, aplicativos em execução no Azure máquinas virtuais ou os serviços de nuvem podem montar um compartilhamento de arquivos na nuvem, tal como um aplicativo de desktop montagens um compartilhamento SMB típico. Qualquer número de componentes de aplicativo possa montar e acessar o compartilhamento de armazenamento de arquivos simultaneamente.

Como um compartilhamento de armazenamento de arquivos é um compartilhamento de arquivo SMB padrão, aplicativos em execução no Azure podem acessar dados no compartilhamento por meio do sistema de arquivo I/O APIs. Os desenvolvedores podem aproveitar, portanto, seu código existente e habilidades para migrar aplicativos existentes. Os profissionais de TI podem usar cmdlets do PowerShell para criar, montar e gerenciar compartilhamentos de arquivos de armazenamento como parte da administração dos aplicativos do Azure.

Como os outros serviços de armazenamento do Azure, o armazenamento de arquivos expõe uma API REST para acessar dados em um compartilhamento. Aplicativos no local podem chamar o API REST para acessar dados em um compartilhamento de arquivos de armazenamento de arquivos. Dessa forma, uma empresa pode optar por migrar alguns aplicativos legados do Azure e continuar a executar outras pessoas na sua própria organização. Observe que um compartilhamento de arquivo de montagem só é possível para aplicativos em execução no Azure; um aplicativo de locais só pode acessar o compartilhamento de arquivos por meio da API REST.

Aplicativos distribuídos também podem usar o armazenamento de arquivos para armazenar e compartilhar dados de aplicativo útil e desenvolvimento e ferramentas de teste. Por exemplo, um aplicativo pode armazenar arquivos de configuração e dados de diagnóstico como logs, métricas e falha descarta em um arquivo de armazenamento compartilhar para que fiquem disponíveis para várias máquinas virtuais ou funções. Desenvolvedores e administradores podem armazenar utilitários que precisam para criar ou gerenciar um aplicativo em um compartilhamento de armazenamento de arquivos que está disponível para todos os componentes, em vez de instalá-los em cada máquina virtual ou instância de função.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Acesso ao Blob, tabela, fila e recursos de arquivo

Por padrão, somente o proprietário da conta de armazenamento pode acessar recursos na conta de armazenamento. Para a segurança dos seus dados, todas as solicitações feitas em relação a recursos em sua conta devem ser autenticadas. Autenticação depende de um modelo de chave compartilhada. BLOBs também podem ser configurados para oferecer suporte à autenticação anônima.

Sua conta de armazenamento é atribuída duas chaves de acesso privado na criação que são usadas para autenticação. Ter duas chaves garante que seu aplicativo permanecerão disponível quando você gerar regularmente as chaves como uma prática de gerenciamento de chaves de segurança comuns.

Se você precisa permitir que usuários controlada acesso aos seus recursos de armazenamento, você pode criar uma assinatura de acesso compartilhado. Uma assinatura de acesso compartilhado (SAS) é um token que pode ser anexado a um URL que permita acesso delegado a um recurso de armazenamento. Qualquer pessoa que possua o token pode acessar o recurso que ele aponte para com as permissões que ele especifica, para o período de tempo que ele é válido. Começando com a versão 2015-04-05, o armazenamento do Azure suporta dois tipos de assinaturas de acesso compartilhado: SAS de serviço e SAS de conta.

O serviço SAS delegue acesso a um recurso em apenas um dos serviços de armazenamento: o serviço Blob, fila, tabela ou arquivo.

Uma conta SAS delegue acesso aos recursos em um ou mais dos serviços de armazenamento. Você pode delegar acesso a operações de nível de serviço que não estão disponíveis com um serviço SAS. Você também pode delegar acesso ao ler, gravar e excluir operações em contêineres de blob, tabelas, filas e compartilhamentos de arquivos que não são permitidos com um serviço SAS.

Por fim, você pode especificar que um contêiner e seus blobs ou um blob específico, estão disponíveis para acesso público. Quando você indica que um contêiner ou blob é pública, qualquer pessoa pode ler-anonimamente; Nenhuma autenticação é necessária.  BLOBs e contêineres públicos são úteis para expor recursos como mídia e documentos que são hospedados em sites.  Para diminuir a latência de rede para um público global, você pode armazenar em cache dados blob usados por sites com o CDN do Azure.

Consulte [Assinaturas usando de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md) para obter mais informações sobre assinaturas de acesso compartilhado. Consulte [Gerenciar acesso anônimo de leitura para contêineres e blobs](storage-manage-access-to-resources.md) e [autenticação para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx) para obter mais informações sobre acesso seguro a sua conta de armazenamento.

## <a name="replication-for-durability-and-high-availability"></a>Replicação para durabilidade e alta disponibilidade

Os dados em sua conta de armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade. Replicação copia seus dados, dentro do mesmo data center ou em um segundo data center, dependendo de qual opção de replicação que você escolher. Replicação protege seus dados e preserva seu aplicativo tempo em caso de falhas de hardware temporárias. Se seus dados forem replicados para um segundo data center, que também protege seus dados contra uma falha grave no local principal.

Replicação garante que sua conta de armazenamento atende o [Contrato de nível de serviço (SLA) para armazenamento](https://azure.microsoft.com/support/legal/sla/storage/) mesmo diante de falhas. Consulte que o SLA para obter informações sobre o armazenamento do Azure garante durabilidade e disponibilidade. 

Quando você cria uma conta de armazenamento, você pode selecionar uma das opções de replicação a seguir:  

- **Armazenamento localmente redundante (LRS).** Armazenamento localmente redundante mantém três cópias de seus dados. LRS é replicado três vezes em um único data center em uma única região. LRS protege seus dados contra falhas de hardware normal, mas não da falha de um único data center.  

    LRS é oferecida com um desconto. Para durabilidade máxima, recomendamos que você use armazenamento geográfica redundantes, descrito a seguir.


- **Armazenamento de zona redundantes (ZRS).** Armazenamento redundante de zona mantém três cópias de seus dados. ZRS está replicado três vezes em dois ou três instalações, dentro de uma única região ou entre duas regiões, fornecendo durabilidade maior que LRS. ZRS garante que os seus dados são duráveis dentro de uma única região.  

    ZRS fornece um nível maior de durabilidade que LRS; No entanto, para durabilidade máxima, recomendamos que você use armazenamento geográfica redundantes, descrito a seguir.  

    > [AZURE.NOTE] ZRS está disponível somente para blobs de bloco e só é suportado para as versões de 2014-02-14 e posterior.
    >
    > Depois que você criou sua conta de armazenamento e selecionada ZRS, você não pode convertê-lo para usar para qualquer outro tipo de replicação, ou vice-versa.

- **Armazenamento de localização geográfica redundantes (GRS)**. GRS mantém seis cópias de seus dados. Com GRS, seus dados são replicados três vezes dentro da região principal e também são replicados três vezes em uma região secundária centenas de milhas longe da região primária, fornecendo o nível mais alto de durabilidade. Em caso de falha na região primária, o armazenamento do Azure falharão à região secundário. GRS garante que os seus dados estiverem duráveis em duas regiões separadas.

    Para obter informações sobre pares primário e secundário por região, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

- **Acesso de leitura geográfica - redundantes (ar-GRS)**. Acesso de leitura geográfica-redundantes replica seus dados para um local geográfico secundário e também fornece acesso de leitura aos seus dados no local secundário. Armazenamento de localização geográfica redundantes de acesso de leitura permite que acessar seus dados do primário ou do local secundário, se um local fica indisponível. Armazenamento de localização geográfica redundantes de acesso de leitura é a opção padrão para sua conta de armazenamento por padrão quando criá-lo. 

    > [AZURE.IMPORTANT] Você pode alterar como seus dados são duplicados após a sua conta de armazenamento tiver sido criada, a menos que você especificou ZRS quando você criou a conta. Entretanto, observe que você pode provoca uma transferência de dados de uso único adicionais se você mudar de LRS para GRS ou ar GRS de custo.

Consulte [replicação de armazenamento do Azure](storage-redundancy.md) para obter detalhes adicionais sobre as opções de replicação de armazenamento.

Para informações sobre preços do replicação de conta de armazenamento, consulte [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services) para obter mais informações sobre quais serviços estão disponíveis em cada região.

Para obter detalhes arquitetônicos sobre durabilidade com o armazenamento do Azure, consulte [SOSP papel - armazenamento Azure: R altamente disponível armazenamento de serviço de nuvem com forte consistência](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).


## <a name="transferring-data-to-and-from-azure-storage"></a>Transferir dados para e do armazenamento do Azure

Você pode usar o utilitário de linha de comando AzCopy para copiar blob, arquivo e dados de tabela em sua conta de armazenamento ou em contas de armazenamento. Para obter mais informações, consulte [transferir dados com o utilitário de AzCopy de linha de comando](storage-use-azcopy.md) .

AzCopy baseia-se na parte superior de [Biblioteca de movimentação de dados do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), que está disponível atualmente no modo de visualização.

O serviço de importação/exportação do Azure fornece uma maneira de importar dados blob ou exportar dados blob de sua conta de armazenamento por meio de um disco rígido enviado para o Centro de dados do Azure. Para obter mais informações sobre o serviço de importação/exportação, consulte [usar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de Blob](storage-import-export-service.md).

## <a name="pricing"></a>Preços

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>Ferramentas, bibliotecas e APIs do armazenamento

Recursos de armazenamento Azure podem ser acessados por qualquer idioma que pode fazer solicitações HTTP/HTTPS. Além disso, o armazenamento do Azure oferece bibliotecas de programação para vários idiomas populares. Essas bibliotecas simplificam vários aspectos de trabalho com o armazenamento do Azure por manipular detalhes como invocação síncrona e assíncrona, lotes de operações, gerenciamento de exceções, tentativas automáticas, comportamento operacional e assim por diante. Bibliotecas estão disponíveis atualmente para os seguintes idiomas e plataformas, com outras pessoas no pipeline:

### <a name="azure-storage-data-services"></a>Serviços de dados de armazenamento do Azure

- [Serviços de armazenamento API REST](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Biblioteca de cliente de armazenamento para .NET, Windows Phone e tempo de execução do Windows](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Biblioteca de cliente de armazenamento para C++](https://github.com/Azure/azure-storage-cpp)
- [Biblioteca de cliente de armazenamento para Java/Android](/develop/java/)
- [Biblioteca de cliente de armazenamento para Node](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [Biblioteca de cliente de armazenamento para PHP](/develop/php/)
- [Biblioteca de cliente de armazenamento para Ruby](/develop/ruby/)
- [Biblioteca de cliente de armazenamento para Python](/develop/python/)
- [Cmdlets de armazenamento para PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Serviços de gerenciamento de armazenamento do Azure

- [Referência de API REST do provedor de recursos de armazenamento](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [Biblioteca de cliente de provedor de recursos de armazenamento para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [Cmdlets de provedor de recursos de armazenamento para PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [API do resto de gerenciamento do serviço de armazenamento (clássico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Serviços de movimentação de dados de armazenamento do Azure

- [API de restante do serviço de importação/exportação de armazenamento](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [Biblioteca de cliente de movimentação de dados de armazenamento para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários

- [Explorador de armazenamento do Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Ferramentas de cliente de armazenamento do Azure](storage-explorers.md)
- [Ferramentas e SDKs azure](https://azure.microsoft.com/tools/)
- [Emulador de armazenamento do Azure](http://www.microsoft.com/download/details.aspx?id=43709)
- [PowerShell Azure](../powershell-install-configure.md)
- [Utilitário de linha de comando AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o armazenamento do Azure, explore estes recursos:

### <a name="documentation"></a>Documentação

- [Documentação de armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>Para administradores

- [Usando o PowerShell Azure com o armazenamento do Azure](storage-powershell-guide-full.md)
- [Usando o Azure CLI com armazenamento do Azure](storage-azure-cli.md)

### <a name="for-net-developers"></a>Para desenvolvedores .NET

- [Introdução ao armazenamento de Blob do Azure usando .NET](storage-dotnet-how-to-use-blobs.md)
- [Introdução ao armazenamento de tabela do Azure usando .NET](storage-dotnet-how-to-use-tables.md)
- [Introdução ao armazenamento de fila do Azure usando .NET](storage-dotnet-how-to-use-queues.md)
- [Introdução ao armazenamento de arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>Para desenvolvedores Java/Android

- [Como usar o armazenamento de Blob do Java](storage-java-how-to-use-blob-storage.md)
- [Como usar o armazenamento de tabela do Java](storage-java-how-to-use-table-storage.md)
- [Como usar o armazenamento de filas de Java](storage-java-how-to-use-queue-storage.md)
- [Como usar o armazenamento de arquivos de Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>Para os desenvolvedores Node

- [Como usar o armazenamento de Blob do Node](storage-nodejs-how-to-use-blob-storage.md)
- [Como usar o armazenamento de tabela do Node](storage-nodejs-how-to-use-table-storage.md)
- [Como usar o armazenamento de fila do Node](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>Para desenvolvedores PHP

- [Como usar o armazenamento de Blob do PHP](storage-php-how-to-use-blobs.md)
- [Como usar o armazenamento de tabela do PHP](storage-php-how-to-use-table-storage.md)
- [Como usar o armazenamento de fila do PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Para desenvolvedores Ruby

- [Como usar o armazenamento de Blob do Ruby](storage-ruby-how-to-use-blob-storage.md)
- [Como usar o armazenamento de tabela do Ruby](storage-ruby-how-to-use-table-storage.md)
- [Como usar o armazenamento de fila do Ruby](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Para os desenvolvedores Python

- [Como usar o armazenamento de Blob do Python](storage-python-how-to-use-blob-storage.md)
- [Como usar o armazenamento de tabela do Python](storage-python-how-to-use-table-storage.md)
- [Como usar o armazenamento de fila do Python](storage-python-how-to-use-queue-storage.md)
- [Como usar o armazenamento de arquivos do Python](storage-python-how-to-use-file-storage.md)
