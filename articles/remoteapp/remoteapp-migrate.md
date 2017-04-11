
<properties
    pageTitle="Migrar dados de usuário do Azure RemoteApp | Microsoft Azure"
    description="Saiba como migrar seus dados de usuário e sair do Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Como migrar dados dentro e fora do Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Você pode usar muitas ferramentas e diferentes métodos para transferir [dados de usuário](remoteapp-upd.md) dentro e fora do Azure RemoteApp. Aqui estão alguns métodos:

- Copiar e colar usando o compartilhamento de área de transferência
- Copiar arquivos e dados para um servidor de arquivos
- Copiar arquivos para o OneDrive for Business através de um navegador
- Copiar arquivos usando o redirecionamento

>[AZURE.NOTE] 
> Não é possível habilitar o OneDrive para agentes de sincronização do consumidor ou de negócios - elas [não são suportados](remoteapp-onedrive.md) no Azure RemoteApp.

## <a name="use-copy-and-paste-in-file-explorer"></a>Use copiar e colar no Explorador de arquivos

Copiar e colar usando a área de transferência está habilitado em RemoteApp implantações [por padrão](remoteapp-redirection.md). Isso permite aos usuários copiar arquivos entre seus aplicativos de PC e RemoteApp locais. Muitas vezes, por meio de curso normal dos usando aplicativos no RemoteApp, usuários salvou arquivos em seus UPDs - movendo que dados fora RemoteApp são fácil:

1. [Publicar o Explorador de arquivos como um aplicativo](remoteapp-publish.md) em um conjunto de RemoteApp. (Observe que se trata de uma tarefa administrativa.)
2. Direcione seus usuários para iniciar o aplicativo do Explorador de arquivos que você publicou e usá-lo para copiar e colar arquivos em seu UPD e proveito deles.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Carregar arquivos e dados de um servidor de arquivos usando a cópia do arquivo de rede padrão

Organizações com frequência usam os servidores de arquivo para armazenar dados gerais. Se você souber o nome do servidor ou local, seus usuários podem procurar na rede local para o servidor e copie seus arquivos, assim como faziam acima. Novamente, você desejará publicar o Explorador de arquivos para o RemoteApp e compartilhá-lo com os usuários.

>[AZURE.NOTE] 
> O servidor de arquivos deve estar na rede roteável que RemoteApp foi implantado em.

## <a name="copy-files-to-onedrive-for-business"></a>Copiar arquivos para o OneDrive for Business
Embora você não pode ativar o OneDrive for agente de sincronização de negócios no RemoteApp, você ainda pode copiar arquivos do seu UPD onedrive for Business através de um navegador. 

1. Publicar File Explorer RemoteApp e, em seguida, diga aos usuários para acessar os arquivos por meio desse aplicativo. 
2. É mais fácil transferir arquivos se eles são compactados, para que os usuários devem criar um arquivo. zip que contém todos os arquivos para mover-se para o OneDrive for Business.
3. Peça aos usuários para o portal do Office 365 e, em seguida, vá para OneDrive e carregue o arquivo. zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copiar arquivos usando o redirecionamento de unidade

Se você tiver habilitado o [redirecionamento de unidade](remoteapp-redirection.md), você já tiver criado uma unidade mapeada para seus usuários. Nesse caso, podem seus arquivos na unidade redirecionada zip e, em seguida, salvá-los em seu PC local.