<properties
    pageTitle="Copiar ou mover dados para armazenamento com AzCopy | Microsoft Azure"
    description="Use o utilitário AzCopy para mover ou copiar dados de ou blob, tabela e o conteúdo do arquivo. Copiar dados para o armazenamento Azure de arquivos locais ou copiar dados dentro ou entre contas de armazenamento. Migre facilmente seus dados para o armazenamento do Azure."
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
    ms.date="09/02/2016"
    ms.author="micurd"/>

# <a name="transfer-data-with-the-azcopy-command-line-utility"></a>Transferir dados com o utilitário de AzCopy de linha de comando

## <a name="overview"></a>Visão geral

AzCopy é um utilitário de linha de comando do Windows projetado para copiar dados para e do armazenamento de Blob do Microsoft Azure, arquivo e tabela usando comandos simples com um desempenho ideal. Você pode copiar dados de um objeto para outro dentro de sua conta de armazenamento, ou entre contas de armazenamento.

> [AZURE.NOTE] Este guia pressupõe que você já esteja familiarizado com [O armazenamento do Azure](https://azure.microsoft.com/services/storage/). Caso contrário, a documentação de [Introdução ao armazenamento do Azure](storage-introduction.md) de leitura será útil. Mais importante, você precisará [criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para começar a usar o AzCopy.

## <a name="download-and-install-azcopy"></a>Baixe e instale o AzCopy

### <a name="windows"></a>Windows

Baixe a [versão mais recente do AzCopy](http://aka.ms/downloadazcopy).

### <a name="maclinux"></a>Mac/Linux

AzCopy não está disponível para sistemas operacionais Mac/Linux. No entanto, o Azure CLI é uma alternativa adequada para copiar dados para e do armazenamento do Azure. Leia [utilizando a CLI Azure com o armazenamento do Azure](storage-azure-cli.md) para saber mais.

## <a name="writing-your-first-azcopy-command"></a>Escrever seu primeiro comando AzCopy

A sintaxe básica para comandos AzCopy é:

    AzCopy /Source:<source> /Dest:<destination> [Options]

Abra uma janela de comando e navegue até o diretório de instalação do AzCopy em seu computador - onde o `AzCopy.exe` executável está localizado. Se desejar, você pode adicionar o local de instalação do AzCopy ao caminho do sistema. Por padrão, AzCopy é instalado `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

Os exemplos a seguir demonstram uma variedade de cenários para copiar dados para e do Microsoft Azure Blobs, arquivos e tabelas. Consulte a seção de [Parâmetros de AzCopy](#azcopy-parameters) para obter uma explicação detalhada dos parâmetros usados em cada amostra.

## <a name="blob-download"></a>Blob: baixar

### <a name="download-single-blob"></a>Baixe o único blob

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

Observe que, se a pasta `C:\myfolder` não existir, AzCopy irá criá-lo e baixar `abc.txt ` na nova pasta.

### <a name="download-single-blob-from-secondary-region"></a>Baixe o único blob da região secundário

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Observe que você deve ter acesso de leitura geográfica-redundantes habilitado.

### <a name="download-all-blobs"></a>Baixar todos os blobs

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Considere que os seguintes blobs residem no contêiner especificado:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de download, o diretório `C:\myfolder` incluirão os seguintes arquivos:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Se você não especificar a opção `/S`, sem blobs serão baixados.

### <a name="download-blobs-with-specified-prefix"></a>Baixar blobs com o prefixo especificado

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Suponha que os seguintes blobs residem no contêiner especificado. Todos os blobs começando com o prefixo `a` serão baixados:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Após a operação de download, a pasta `C:\myfolder` incluirão os seguintes arquivos:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

O prefixo aplica-se ao diretório virtual, que a primeira parte do nome do blob de formulários. No exemplo mostrado acima, o diretório virtual não coincidir com o prefixo especificado, para que ele não foi baixado. Além disso, se a opção `\S` não for especificada, AzCopy não será baixado qualquer blobs.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Definir a hora da última modificação dos arquivos exportados mesma os blobs de origem

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

Você também pode excluir blobs da operação de download com base em sua hora da última modificação. Por exemplo, se você deseja excluir blobs cuja última modificação tempo é a mesma ou mais recente do que o arquivo de destino, adicione a `/XN` opção:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

Ou se você deseja excluir blobs cuja última modificação tempo é a mesma ou mais antigas do que o arquivo de destino, adicione a `/XO` opção:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## <a name="blob-upload"></a>Blob: carregar

### <a name="upload-single-file"></a>Carregar arquivo único

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

Se o contêiner de destino especificada não existir, AzCopy irá criá-lo e carregue o arquivo nela.

### <a name="upload-single-file-to-virtual-directory"></a>Carregar arquivo único no diretório virtual

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Se o diretório virtual especificado não existir, AzCopy carregará o arquivo para incluir o diretório virtual em seu nome (*por exemplo*, `vd/abc.txt` no exemplo acima).

### <a name="upload-all-files"></a>Carregar todos os arquivos

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Especificando opção `/S` carrega o conteúdo da pasta especificada para repetidamente de armazenamento de Blob, significando que todas as subpastas e seus arquivos serão carregados também. Por exemplo, suponha que os seguintes arquivos residem na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de carregamento, o contêiner incluirão os seguintes arquivos:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se você não especificar a opção `/S`, AzCopy não serão carregadas recursiva. Após a operação de carregamento, o contêiner incluirão os seguintes arquivos:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-files-matching-specified-pattern"></a>Carregar arquivos correspondência padrão especificado

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Assumir os seguintes arquivos residem na pasta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Após a operação de carregamento, o contêiner incluirão os seguintes arquivos:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Se você não especificar a opção `/S`, AzCopy só serão carregadas bolhas que não residem em um diretório virtual:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especificar o tipo de conteúdo de MIME de um blob de destino

Por padrão, AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. Começando com a versão 3.1.0, você pode especificar explicitamente o tipo de conteúdo por meio da opção `/SetContentType:[content-type]`. Esta sintaxe define o tipo de conteúdo para todos os blobs em uma operação de carregamento.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Se você especificar `/SetContentType` sem um valor, em seguida, AzCopy definirá cada blob ou tipo de conteúdo do arquivo de acordo com a sua extensão de arquivo.

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## <a name="blob-copy"></a>Blob: cópia

### <a name="copy-single-blob-within-storage-account"></a>Copiar blob único dentro de conta de armazenamento

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

Quando você copia um blob dentro de uma conta de armazenamento, uma operação de [cópia do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é executada.

### <a name="copy-single-blob-across-storage-accounts"></a>Copiar único blob entre contas de armazenamento

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Quando você copia um blob entre contas de armazenamento, uma operação de [cópia do servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) é executada.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copie o único blob da região secundário à região primária

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

Observe que você deve ter acesso de leitura geográfica-redundantes habilitado.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copiar único blob e respectivos instantâneos entre contas de armazenamento

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Após a operação de cópia, o contêiner de destino incluirá o blob e respectivos instantâneos. Considerando que o blob no exemplo acima tem dois instantâneos, o contêiner incluirá o blob e instantâneos a seguir:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Copiar sincronia blobs entre contas de armazenamento

AzCopy por padrão copia dados entre dois pontos de extremidade de armazenamento assíncrona. Portanto, a operação de cópia será executado em segundo plano usando capacidade de pouca largura de banda com nenhum SLA em termos como rapidamente um blob será copiado e AzCopy verificará periodicamente o status de cópia até que a cópia seja concluída ou falha.

O `/SyncCopy` opção garante que a operação de cópia obterá velocidade consistente. AzCopy realiza a cópia sincronizada baixando os blobs para copiar da fonte especificada para a memória local e, em seguida, carregando-os para o destino de armazenamento de Blob.

    AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`pode gerar custo adicional egresso comparado a cópia assíncrona, a abordagem recomendada é usar essa opção em uma máquina virtual do Azure que está na mesma região como sua conta de armazenamento de origem para evitar o custo de saída.

## <a name="file-download"></a>Arquivo: baixar

### <a name="download-single-file"></a>Baixar o arquivo único

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se a fonte especificada é um compartilhamento de arquivo Azure, então você deverá especificar o nome do arquivo exato (*por exemplo,* `abc.txt`) para baixar um único arquivo ou especificar opção `/S` para baixar todos os arquivos em repetidamente o compartilhamento. Tentar especificar um padrão de arquivo e a opção `/S` juntos resultará em um erro.

### <a name="download-all-files"></a>Baixar todos os arquivos

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Observe que as pastas vazias não serão baixadas.

## <a name="file-upload"></a>Arquivo: carregar

### <a name="upload-single-file"></a>Carregar arquivo único

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### <a name="upload-all-files"></a>Carregar todos os arquivos

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Observe que as pastas vazias não serão carregadas.

### <a name="upload-files-matching-specified-pattern"></a>Carregar arquivos correspondência padrão especificado

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a name="file-copy"></a>Arquivo: cópia

### <a name="copy-across-file-shares"></a>Copiar em compartilhamentos de arquivos

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### <a name="copy-from-file-share-to-blob"></a>Copiar de compartilhamento de arquivo para blob

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Observe que não é suportado copiando assíncrona de armazenamento de arquivos para Blob de página.

### <a name="copy-from-blob-to-file-share"></a>Copiar de blob para compartilhamento de arquivos

    AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### <a name="synchronously-copy-files"></a>Sincronia copiar arquivos

Você pode especificar o `/SyncCopy` opção para copiar dados de armazenamento de arquivos ao armazenamento de arquivos, de armazenamento de arquivos ao armazenamento de Blob e de armazenamento de Blob ao armazenamento de arquivos de sincronia, AzCopy faz isso baixando os dados de origem para a memória local e carregue-o novamente para destino.

    AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Ao copiar de armazenamento de arquivos ao armazenamento de Blob, o tipo de blob padrão é blob de bloco, o usuário pode especificar a opção `/BlobType:page` para alterar o tipo de blob de destino.

Observe que `/SyncCopy` pode gerar adicional egresso comparando com cópia assíncrona de custo, a abordagem recomendada é usar essa opção na VM Azure que está na mesma região como sua conta de armazenamento de origem para evitar o custo de saída.

## <a name="table-export"></a>Tabela: exportar

### <a name="export-table"></a>Exportar tabela

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy grava um arquivo de manifesto para a pasta de destino especificada. O arquivo de manifesto é usado no processo de importação para localizar os arquivos de dados necessários e executar a validação de dados. O arquivo de manifesto usa a seguinte convenção de nomenclatura por padrão:

    <account name>_<table name>_<timestamp>.manifest

Usuário também pode especificar a opção `/Manifest:<manifest file name>` para definir o nome de arquivo de manifesto.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### <a name="split-export-into-multiple-files"></a>Exportação de divisão em vários arquivos

    AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy usa um *índice de volume* nos nomes de arquivo de dados dividido para distinguir vários arquivos. O índice de volume consiste em duas partes, um *índice de chave de intervalo de partição* e *Dividir o índice do arquivo*. Ambos os índices são baseados em zero.

O índice de chave de intervalo de partição será 0 se o usuário não especificar a opção `/PKRS`.

Por exemplo, suponha que AzCopy gera dois arquivos de dados após o usuário Especifica opção `/SplitSize`. Os nomes de arquivo de dados resultante podem ser:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Observe que o mínimo possível de valor para a opção `/SplitSize` é de 32MB. Se o destino especificado é armazenamento de Blob, AzCopy será dividir o arquivo de dados, uma vez que seus tamanhos atinge o limite de tamanho de blob (200GB), independentemente de se a opção `/SplitSize` foi especificado pelo usuário.

### <a name="export-table-to-json-or-csv-data-file-format"></a>Exportar tabela para o formato de arquivo de dados JSON ou CSV

AzCopy por padrão Exporta tabelas para arquivos de dados JSON. Você pode especificar a opção `/PayloadFormat:JSON|CSV` para exportar tabelas como JSON ou CSV.

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Ao especificar o formato de carga CSV, AzCopy também gerará um arquivo de esquema com extensão de arquivo `.schema.csv` para cada arquivo de dados.

### <a name="export-table-entities-concurrently"></a>Exportar entidades tabela simultaneamente

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy iniciará operações simultâneas para exportar entidades quando o usuário Especifica opção `/PKRS`. Cada operação exporta um intervalo de chave de partição.

Observe que o número de operações simultâneas também é controlado pela opção `/NC`. AzCopy usa o número de processadores core como o valor padrão de `/NC` ao copiar entidades de tabela, mesmo se `/NC` não foi especificada. Quando o usuário Especifica opção `/PKRS`, AzCopy usa o menor dos dois valores - partição chave intervalos versus implicitamente ou explicitamente especificados operações simultâneas - para determinar o número de operações simultâneas para iniciar. Para obter mais detalhes, digite `AzCopy /?:NC` na linha de comando.

### <a name="export-table-to-blob"></a>Exportar tabela para blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy irá gerar um arquivo de dados JSON no contêiner blob com a seguinte convenção de nomenclatura:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

O arquivo de dados JSON gerado segue o formato de carga para metadados mínimo. Para obter detalhes sobre este formato de carga, consulte [Formato de carga para operações de serviço de tabela](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Observe que, ao exportar tabelas para blobs, AzCopy será baixar as entidades de tabela para arquivos de dados temporários locais e carregue essas entidades o blob. Esses arquivos de dados temporários são colocados na pasta de arquivo de diário com o caminho padrão "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", você pode especificar a opção/z [pasta de arquivo diário] para alterar o diário local da pasta de arquivo e, portanto, alterar o local de arquivos de dados temporários. Tamanho dos arquivos de dados temporários é decidido pelo tamanho dos entidades sua tabela e o tamanho especificado com a opção /SplitSize, embora o arquivo de dados temporários no disco local será excluído instantaneamente quando ele foi carregado para o blob, verifique se que você tem espaço suficiente no disco local para armazenar esses arquivos de dados temporários antes de serem excluídas.

## <a name="table-import"></a>Tabela: importação

### <a name="import-table"></a>Importação de tabela

    AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

A opção `/EntityOperation` indica como inserir entidades na tabela. Valores possíveis são:

- `InsertOrSkip`: Ignora uma entidade existente ou insere uma nova entidade se ele não existir na tabela.
- `InsertOrMerge`: Mescla uma entidade existente ou insere uma nova entidade se ele não existir na tabela.
- `InsertOrReplace`: Substitui uma entidade existente ou insere uma nova entidade se ele não existir na tabela.

Observe que você não pode especificar opção `/PKRS` no cenário de importação. Ao contrário de cenário de exportação, em que você deve especificar opção `/PKRS` para iniciar operações simultâneas, AzCopy por padrão iniciará operações simultâneas quando você importa uma tabela. O número padrão de operações simultâneas iniciado é igual ao número de processadores core; No entanto, você pode especificar um número diferente de simultâneas com a opção `/NC`. Para obter mais detalhes, digite `AzCopy /?:NC` na linha de comando.

Observe que AzCopy só oferece suporte a importação para JSON, não CSV. AzCopy não importa de tabela de JSON criados pelo usuário de suporte e arquivos de manifesto. Ambos os arquivos devem ser de uma exportação de tabela AzCopy. Para evitar erros, não modifique o JSON exportado ou arquivo de manifesto.

### <a name="import-entities-to-table-using-blobs"></a>Importar entidades à tabela usando blobs

Suponha um contêiner de Blob contém o seguinte: arquivo A JSON que representa uma tabela do Azure e seu arquivo de manifesto que acompanha.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Você pode executar o seguinte comando para importar entidades em uma tabela usando o arquivo manifesto no contêiner blob:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## <a name="other-azcopy-features"></a>Outros recursos de AzCopy

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copiar apenas os dados que não existem no destino

O `/XO` e `/XN` parâmetros permitem que você exclua os recursos de fonte mais antigos ou mais recente do sendo copiada, respectivamente. Se desejar copiar os recursos de fonte que não existem no destino, você pode especificar ambos os parâmetros no comando AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Observação: Isso não é suportado quando de origem ou de destino é uma tabela.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Use um arquivo de resposta para especificar parâmetros de linha de comando

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

Você pode incluir qualquer parâmetros de linha de comando AzCopy em um arquivo de resposta. AzCopy processa os parâmetros no arquivo como se eles foram especificados na linha de comando, executar uma substituição direta com o conteúdo do arquivo.

Suponha que um arquivo de resposta chamado `copyoperation.txt`, que contém as linhas a seguir. Cada parâmetro AzCopy pode ser especificado em uma única linha

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou em linhas separadas:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy falhará se você dividir o parâmetro entre duas linhas, como mostrado aqui para os `/sourcekey` parâmetro:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Usar vários arquivos de resposta para especificar os parâmetros de linha de comando

Suponha que um arquivo de resposta chamado `source.txt` que especifica um contêiner de fonte:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

E um arquivo de resposta nomeado `dest.txt` que especifica uma pasta de destino no sistema de arquivos:

    /Dest:C:\myfolder

E um arquivo de resposta nomeado `options.txt` que especifica opções para AzCopy:

    /S /Y

Para chamar AzCopy com esses arquivos de resposta, todos os quais residem em um diretório `C:\responsefiles`, use este comando:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy processa este comando como faria se você incluiu todos os parâmetros individuais na linha de comando:

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### <a name="specify-a-shared-access-signature-sas"></a>Especificar uma assinatura de acesso compartilhado (SAS)

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

Você também pode especificar uma SAS no contêiner URI:

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### <a name="journal-file-folder"></a>Pasta de arquivo de diário

Sempre que você emitir um comando para AzCopy, ele verifica existência de um arquivo de diário na pasta padrão, ou se existe em uma pasta que você especificou via essa opção. Se o arquivo de diário não existir em qualquer lugar, AzCopy trata a operação como nova e gera um novo arquivo de diário.

Se o arquivo de diário existir, AzCopy verificará se a linha de comando que você entrada corresponde a linha de comando no arquivo de diário. Se as duas linhas de comando corresponder, AzCopy continua a operação incompleta. Se eles não corresponderem, você será solicitado a substituir o arquivo de diário para iniciar uma nova operação, ou cancelar a operação atual.

Se você quiser usar o local padrão para o arquivo de diário:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Se você omitir opção `/Z`, ou especificar opção `/Z` sem o caminho da pasta, como mostrado acima, AzCopy cria o arquivo do diário no local padrão, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se o arquivo de diário já existir, AzCopy continua a operação baseada no arquivo de diário.

Se você quiser especificar um local personalizado para o arquivo de diário:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Este exemplo cria o arquivo de diário se ele ainda não exista. Se ele existir, AzCopy continua a operação baseada no arquivo de diário.

Se você quiser retomar uma operação de AzCopy:

    AzCopy /Z:C:\journalfolder\

Este exemplo continua a última operação, que pode ter falhado para ser concluída.

### <a name="generate-a-log-file"></a>Gerar um arquivo de log

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Se você especificar opção `/V` sem fornecer um caminho de arquivo de log detalhado, AzCopy cria o arquivo de log no local padrão, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Caso contrário, você pode criar um arquivo de log em um local personalizado:

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Observe que, se você especificar um caminho relativo acompanhando opção `/V`, tais como `/V:test/azcopy1.log`, e em seguida, log detalhado é criado no diretório de trabalho atual em uma subpasta chamada `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especificar o número de operações simultâneas para iniciar

Opção `/NC` Especifica o número de operações de cópia simultânea. Por padrão, AzCopy inicia um determinado número de operações simultâneas para aumentar a produtividade de transferência de dados. Para operações de tabela, o número de operações simultâneas é igual ao número de processadores que tiver. Para operações de Blob e do arquivo, o número de operações simultâneas é igual a 8 vezes o número de processadores que tiver. Se você estiver executando AzCopy em uma rede de baixa largura de banda, você pode especificar um número menor para /NC evitar falha causada pela concorrência de recurso.

### <a name="run-azcopy-against-azure-storage-emulator"></a>Executar AzCopy emulador de armazenamento do Azure

Você pode executar AzCopy contra o [Emulador de armazenamento do Azure](storage-use-emulator.md) para Blobs:

    AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

Tabelas e:

    AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## <a name="azcopy-parameters"></a>Parâmetros de AzCopy

Parâmetros para AzCopy são descritos abaixo. Você também pode digitar um dos seguintes comandos na linha de comando para obter ajuda usando AzCopy:

- Para obter ajuda detalhada de linha de comando para AzCopy:`AzCopy /?`
- Para obter ajuda detalhada com qualquer parâmetro AzCopy:`AzCopy /?:SourceKey`
- Para obter exemplos de linha de comando:`AzCopy /?:Samples`

### <a name="sourcesource"></a>/ Origem: "fonte"

Especifica os dados de origem do qual deseja copiar. A fonte pode ser um diretório de sistema de arquivos, um contêiner de blob, um diretório virtual do blob, um compartilhamento de arquivos de armazenamento, um diretório de arquivos de armazenamento ou uma tabela do Azure.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="destdestination"></a>/ Destino: "destino"

Especifica o destino para copiar para. O destino pode ser um diretório de sistema de arquivos, um contêiner de blob, um diretório virtual do blob, um compartilhamento de arquivos de armazenamento, um diretório de arquivos de armazenamento ou uma tabela do Azure.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="patternfile-pattern"></a>/ Padrão: "padrão do arquivo"

Especifica um padrão de arquivo que indica quais arquivos para copiar. O comportamento do parâmetro /Pattern é determinado pela localização dos dados de origem e a presença da opção de modo recursiva. Modo recursivo especificado via opção /S.

Se a fonte especificada for um diretório no sistema de arquivos, em seguida, curingas padrão estão em vigor e o padrão de arquivo fornecido é comparado com arquivos dentro do diretório. Se a opção que /s for especificado, então AzCopy também corresponde ao padrão especificado em relação a todos os arquivos em todas as subpastas sob o diretório.

Se a fonte especificada for um contêiner de blob ou diretório virtual, caracteres curinga não é aplicada. Se a opção que /s for especificado, em seguida, AzCopy interpretará o padrão de arquivo especificado como um prefixo blob. Se a opção que /s não for especificada, em seguida, AzCopy corresponde ao padrão de arquivo com nomes de blob exata.

Se a fonte especificada for um compartilhamento de arquivo Azure, você deve especificar o nome do arquivo exato (por exemplo, abc.txt) para copiar um único arquivo, ou especificar opção /S para copiar todos os arquivos repetidamente o compartilhamento. Tentativa especificar um padrão de arquivo e a opção /S juntos resultará em um erro.

AzCopy usa correspondência diferencia maiusculas de minúsculas quando o /Source é um contêiner de blob ou diretório virtual blob e usa a correspondência de maiusculas e minúsculas em todos os outros casos.

O padrão de arquivo usado quando nenhum padrão do arquivo é especificado é *.* para um local de sistema de arquivos ou um prefixo vazio para um local de armazenamento do Azure. Especificar vários padrões de arquivo não é suportada.

**Aplicável a:** BLOBs, arquivos

### <a name="destkeystorage-key"></a>/ DestKey: "armazenamento-chave"

Especifica a chave da conta de armazenamento para o recurso de destino.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="destsassas-token"></a>/ DestSAS: "token de sas"

Especifica uma assinatura de acesso compartilhado (SAS) com permissões de leitura e gravação para o destino (se aplicável). Envolvem as associações de segurança com aspas duplas, pois ela pode contém caracteres especiais de linha de comando.

Se o recurso de destino for um contêiner de blob, compartilhamento de arquivos ou tabela, você pode especificar essa opção seguida o token SAS, ou você pode especificar as associações de segurança como parte do contêiner de blob de destino, compartilhamento de arquivo ou URI da tabela, sem essa opção.

Se a origem e destino forem ambos os blobs, o blob de destino deve residem na mesma conta de armazenamento como o blob de origem.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="sourcekeystorage-key"></a>/ SourceKey: "armazenamento-chave"

Especifica a chave da conta de armazenamento para o recurso de fonte.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="sourcesassas-token"></a>/ SourceSAS: "token de sas"

Especifica uma assinatura de acesso compartilhado com permissões de leitura e a lista para a fonte (se aplicável). Envolvem as associações de segurança com aspas duplas, pois ela pode contém caracteres especiais de linha de comando.

Se o recurso de fonte é um contêiner de blob e uma tecla nem uma SAS é fornecida, então o contêiner de blob serão lidos via acesso anônimo.

Se a fonte for um compartilhamento de arquivo ou tabela, uma tecla ou uma SAS deve ser fornecida.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="s"></a>/S

Especifica o modo recursivo para operações de cópia. No modo recursivo, AzCopy irá copiar todos os blobs ou arquivos que correspondem ao padrão de arquivo especificado, inclusive aqueles em subpastas.

**Aplicável a:** BLOBs, arquivos

### <a name="blobtypeblock--page--append"></a>/ BlobType: "bloco" | "página" | "acrescentar"

Especifica se o blob de destino é um blob de bloco, um blob de página ou um blob de acréscimo. Esta opção é aplicável somente quando você estiver carregando um blob. Caso contrário, será gerado um erro. Se o destino é um blob e essa opção não for especificada, por padrão, AzCopy cria um blob de bloco.

**Aplicável a:** BLOBs

### <a name="checkmd5"></a>/ CheckMD5

Calcula um hash MD5 para dados baixados e verifica se o hash MD5 armazenado no blob ou o hash calculado corresponde a propriedade de conteúdo-MD5 do arquivo. A verificação de MD5 está desativada por padrão, portanto, você deve especificar essa opção para executar a verificação de MD5 ao fazer o download de dados.

Observe que o armazenamento do Azure não garante que o hash MD5 armazenado para o blob ou o arquivo é atualizado. É responsabilidade do cliente para atualizar o MD5 sempre que o blob ou o arquivo é modificado.

AzCopy sempre define a propriedade de conteúdo-MD5 para um arquivo ou blob do Microsoft Azure após carregar para o serviço.  

**Aplicável a:** BLOBs, arquivos

### <a name="snapshot"></a>/ Instantâneo

Indica se transferir instantâneos. Essa opção só é válida quando a fonte for um blob.

Os instantâneos blob transferidos são renomeados neste formato: .extension blob-nome (instantâneo-hora)

Por padrão, os instantâneos não são copiados.

**Aplicável a:** BLOBs

### <a name="vverbose-log-file"></a>/ V: [--arquivo de log detalhado]

Mensagens de status detalhado de saídas em um arquivo de log.

Por padrão, o arquivo de log detalhado é chamado AzCopyVerbose.log em `%LocalAppData%\Microsoft\Azure\AzCopy`. Se você especificar um local de arquivo existente para essa opção, o log detalhado será anexado a esse arquivo.  

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="zjournal-file-folder"></a>/ Z [diário-pasta de arquivo]

Especifica uma pasta de arquivo de diário para retomar uma operação.

AzCopy sempre oferece suporte reiniciando se uma operação foi interrompida.

Se essa opção não for especificada ou se ele for especificado sem um caminho de pasta, AzCopy criará o arquivo do diário no local padrão, que é % LocalAppData%\Microsoft\Azure\AzCopy.

Sempre que você emitir um comando para AzCopy, ele verifica existência de um arquivo de diário na pasta padrão, ou se existe em uma pasta que você especificou via essa opção. Se o arquivo de diário não existir em qualquer lugar, AzCopy trata a operação como nova e gera um novo arquivo de diário.

Se o arquivo de diário existir, AzCopy verificará se a linha de comando que você entrada corresponde a linha de comando no arquivo de diário. Se as duas linhas de comando corresponder, AzCopy continua a operação incompleta. Se eles não corresponderem, você será solicitado a substituir o arquivo de diário para iniciar uma nova operação, ou cancelar a operação atual.

O arquivo de diário é excluído após a conclusão bem-sucedida da operação.

Observe que não é suportado retomar uma operação de um arquivo de diário criado por uma versão anterior do AzCopy.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="parameter-file"></a>/@:"parameter-file"

Especifica um arquivo que contém parâmetros. AzCopy processa os parâmetros no arquivo como se eles foram especificados na linha de comando.

Em um arquivo de resposta, você pode especificar vários parâmetros em uma única linha ou especificar cada parâmetro em sua própria linha. Observe que um parâmetro individual não pode abranger várias linhas.

Arquivos de resposta podem incluir linhas de comentários que começam com o símbolo #.

Você pode especificar vários arquivos de resposta. Entretanto, observe que AzCopy não oferece suporte a arquivos de resposta aninhados.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="y"></a>/Y

Elimina todos os prompts de confirmação de AzCopy.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="l"></a>/L

Especifica uma operação de listagem Nenhum dado é copiado.

AzCopy interpretará o uso dessa opção como uma simulação para executar a linha de comando sem esta opção /L e contar quantos objetos serão copiados, você pode especificar a opção /V ao mesmo tempo para verificar quais objetos serão copiados no log detalhado.

O comportamento dessa opção também é determinado pelo local dos dados de origem e a presença recursiva modo opção /S e arquivo padrão da opção de /Pattern.

AzCopy exige a permissão de leitura e lista deste local de origem ao usar essa opção.

**Aplicável a:** BLOBs, arquivos

### <a name="mt"></a>/MT

Define a hora da última modificação do arquivo baixado para ser igual a blob de origem ou do arquivo.

**Aplicável a:** BLOBs, arquivos

### <a name="xn"></a>/XN

Exclui um recurso de fonte mais recente. O recurso não será copiado se a hora da última modificação da origem é a mesma ou mais recente que destino.

**Aplicável a:** BLOBs, arquivos

### <a name="xo"></a>/XO

Exclui um recurso de fonte mais antigo. O recurso não será copiado se a hora da última modificação da origem é a mesma ou mais antigo que destino.

**Aplicável a:** BLOBs, arquivos

### <a name="a"></a>/A

Carrega somente os arquivos que possuem o atributo de arquivamento definido.

**Aplicável a:** BLOBs, arquivos

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Carrega somente os arquivos que possuem qualquer um dos atributos especificados.

Atributos disponíveis incluem:

- R = arquivos somente leitura
- A = arquivos prontos para arquivamento
- S = arquivos de sistema
- H = arquivos ocultos
- C = arquivos compactados
- N = arquivos Normal
- E = arquivos criptografados
- T = arquivos temporários
- O = Arquivos Offline
- Posso = arquivos não-indexados

**Aplicável a:** BLOBs, arquivos

### <a name="xarashcnetoi"></a>/ XA: [RASHCNETOI]

Exclui os arquivos com qualquer um dos atributos especificados.

Atributos disponíveis incluem:

- R = arquivos somente leitura
- A = arquivos prontos para arquivamento
- S = arquivos de sistema
- H = arquivos ocultos
- C = arquivos compactados
- N = arquivos Normal
- E = arquivos criptografados
- T = arquivos temporários
- O = Arquivos Offline
- Posso = arquivos não-indexados

**Aplicável a:** BLOBs, arquivos

### <a name="delimiterdelimiter"></a>/ Delimitador: "delimitador"

Indica o caractere delimitador usado para delimitar diretórios virtuais em um nome de blob.

Por padrão, usa AzCopy / como o caractere delimitador. No entanto, AzCopy suporta usando qualquer caractere comum (como @, # ou %)) como um delimitador. Se você precisar incluir um dos seguintes caracteres especiais na linha de comando, coloque o nome de arquivo com aspas duplas.

Essa opção só é aplicável para baixá-lo blobs.

**Aplicável a:** BLOBs

### <a name="ncnumber-of-concurrent-operations"></a>/ NC: "número-de-simultânea-operações"

Especifica o número de operações simultâneas.

AzCopy por padrão inicia um determinado número de operações simultâneas para aumentar a produtividade de transferência de dados. Observe que grande número de operações simultâneas em um ambiente de baixa largura de banda pode sobrecarregar a conexão de rede e impedir que as operações de totalmente concluir. Controle operações simultâneas com base na largura de banda de rede disponível real.

O limite superior para operações simultâneas é 512.

**Aplicável a:** Tabelas de BLOBs, arquivos,

### <a name="sourcetypeblob--table"></a>/ SourceType: "Blob" | "Tabela"

Especifica que o `source` recurso é um disponíveis no ambiente de desenvolvimento local, em execução no emulador de armazenamento de blob.

**Aplicável a:** BLOBs, tabelas

### <a name="desttypeblob--table"></a>/ DestType: "Blob" | "Tabela"

Especifica que o `destination` recurso é um disponíveis no ambiente de desenvolvimento local, em execução no emulador de armazenamento de blob.

**Aplicável a:** BLOBs, tabelas

### <a name="pkrskey1key2key3"></a>/ PKRS: "CHAVE1 CHAVE2 # #key3 #..."

Divide o intervalo de chave de partição para habilitar a exportação de dados de tabela em paralelo, o que aumenta a velocidade da operação de exportação.

Se essa opção não for especificada, o AzCopy usa um único thread para exportar entidades de tabela. Por exemplo, se o usuário Especifica /PKRS: "aa #bb", então AzCopy inicia três operações simultâneas.

Cada operação exporta um dos três intervalos de chave de partição, conforme mostrado abaixo:

  [primeira partição-chave, aa)

  [aa, bb)

  [bb, última-partição-key]

**Aplicável a:** Tabelas

### <a name="splitsizefile-size"></a>/ SplitSize: "tamanho do arquivo"

Especifica o arquivo exportado dividir tamanho em MB, o valor mínimo permitido é 32.

Se essa opção não for especificada, AzCopy será exportar dados da tabela de arquivo único.

Se os dados da tabela são exportados para um blob e o tamanho do arquivo exportado atingir o limite de 200 GB de tamanho de blob, AzCopy dividirá o arquivo exportado, mesmo se essa opção não for especificada.

**Aplicável a:** Tabelas

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Especifica o comportamento de importação de dados de tabela.

- InsertOrSkip - ignora uma entidade existente ou insere uma nova entidade se ele não existir na tabela.

- InsertOrMerge - mescla uma entidade existente ou insere uma nova entidade se ele não existir na tabela.

- InsertOrReplace - substitui uma entidade existente ou insere uma nova entidade se ele não existir na tabela.

**Aplicável a:** Tabelas

### <a name="manifestmanifest-file"></a>/ Manifesto: "arquivo de manifesto"

Especifica o arquivo de manifesto para a tabela exportar e importar a operação.

Isso é opcional durante a operação de exportação, AzCopy irá gerar um arquivo de manifesto com nome predefinido se essa opção não for especificada.

Esta opção é necessária durante a operação de importação para localizar os arquivos de dados.

**Aplicável a:** Tabelas

### <a name="synccopy"></a>/ SyncCopy

Indica se deseja copiar sincronia blobs ou arquivos entre dois pontos de extremidade de armazenamento do Azure.

AzCopy por padrão usa cópia assíncrona do lado do servidor. Especifica essa opção para fazer uma cópia sincronizada, o que faz o download de arquivos ou blobs para memória local e, em seguida, carrega-los para o armazenamento do Azure.

Você pode usar esta opção ao copiar arquivos em armazenamento de Blob, dentro de armazenamento de arquivos ou do armazenamento de Blob para armazenamento de arquivos ou vice-versa.

**Aplicável a:** BLOBs, arquivos

### <a name="setcontenttypecontent-type"></a>/ SetContentType: "tipo de conteúdo"

Especifica o tipo de conteúdo MIME blobs de destino ou arquivos.

AzCopy define o tipo de conteúdo para um arquivo ou blob aplicativo/octeto-fluxo por padrão. Você pode definir o tipo de conteúdo para todos os blobs ou arquivos especificando explicitamente um valor para essa opção.

Se você especificar esta opção sem um valor, AzCopy definirá cada blob ou tipo de conteúdo do arquivo de acordo com a sua extensão de arquivo.

**Aplicável a:** BLOBs, arquivos

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" | "CSV"

Especifica o formato de arquivo de dados exportados a tabela.

Se essa opção não for especificada, por padrão, o AzCopy Exportar arquivo de dados de tabela no formato JSON.

**Aplicável a:** Tabelas

## <a name="known-issues-and-best-practices"></a>Problemas conhecidos e práticas recomendadas

### <a name="limit-concurrent-writes-while-copying-data"></a>Limite de gravações simultâneas ao copiar dados

Quando você copia blobs ou arquivos com AzCopy, tenha em mente que outro aplicativo esteja modificando os dados enquanto você estiver copiando-lo. Se possível, certifique-se de que os dados que você está copiando não está sendo modificados durante a operação de cópia. Por exemplo, ao copiar um VHD associado a uma máquina virtual Azure, certifique-se de que nenhum outro aplicativo no momento está escrevendo no VHD. Uma boa maneira de fazer isso é concedendo o recurso a ser copiada. Como alternativa, você pode criar um instantâneo do VHD primeiro e, em seguida, copie o instantâneo.

Se você não pode impedir que outros aplicativos gravando blobs ou arquivos enquanto eles estão sendo copiados, em seguida, tenha em mente que no momento em que o trabalho for concluído, os recursos copiados podem não ter mais paridade completa com os recursos de fonte.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Execute uma instância de AzCopy em um computador.
AzCopy foi projetado para maximizar a utilização de seu recurso de máquina para acelerar a transferência de dados, recomendamos que você execute somente uma instância de AzCopy em um computador e especifique a opção `/NC` se precisar de operações mais simultâneas. Para obter mais detalhes, digite `AzCopy /?:NC` na linha de comando.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Habilitar algoritmos de MD5 compatíveis com FIPS para AzCopy quando você "usar FIPS compatível com algoritmos para criptografia, hash e assinatura".
AzCopy por padrão usa a implementação MD5 .NET para calcular o MD5 ao copiar objetos, mas há alguns requisitos de segurança que precisam AzCopy para habilitar a configuração de MD5 compatível com FIPS.

Você pode criar um arquivo App `AzCopy.exe.config` com propriedade `AzureStorageUseV1MD5` e coloque-aside com AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

• De propriedade "AzureStorageUseV1MD5" True - o valor padrão, AzCopy usarão .NET MD5 implementação.
• False – AzCopy usará o algoritmo de MD5 compatível com FIPS.

Observe que algoritmos compatíveis com FIPS é desabilitado por padrão no seu computador Windows, digite secpol. msc na sua janela Executar e marque esta opção na configuração de segurança -> Segurança -> Diretiva Local opções -> criptografia do sistema: usar compatível com algoritmos FIPS para criptografia, hash e assinatura.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o armazenamento do Azure e AzCopy, consulte os seguintes recursos.

### <a name="azure-storage-documentation"></a>Documentação de armazenamento do Azure:

- [Introdução ao armazenamento do Azure](storage-introduction.md)
- [Como usar o armazenamento de Blob do .NET](storage-dotnet-how-to-use-blobs.md)
- [Como usar o armazenamento de arquivos do .NET](storage-dotnet-how-to-use-files.md)
- [Como usar o armazenamento de tabela do .NET](storage-dotnet-how-to-use-tables.md)
- [Como criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md)

### <a name="azure-storage-blog-posts"></a>Azure postagens de blog de armazenamento:
- [Apresentando visualização de biblioteca de movimentação de dados de armazenamento do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: Apresentando cópia sincronizada e tipo de conteúdo personalizado](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: Anunciando disponibilidade geral da AzCopy 3.0 plus versão de visualização do AzCopy 4.0 com suporte a arquivos e tabela](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: Otimizado para cenários de cópia grande escala](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy: Suporte para armazenamento de localização geográfica redundantes de acesso de leitura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Transferir dados com modo reinicializável e token SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Usando conta entre copiar Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Baixando/carregando arquivos para Blobs do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
