<properties
   pageTitle="Solucionando problemas de erros de cliente Docker no Windows usando o Visual Studio | Microsoft Azure"
   description="Solucione problemas encontrados ao usar o Visual Studio para criar e implantar aplicativos web em Docker no Windows usando Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# <a name="troubleshooting-visual-studio-docker-development"></a>Visual Studio Docker desenvolvimento de solução de problemas

Ao trabalhar com o Visual Studio Tools para Docker Preview, talvez você encontre alguns problemas devido a natureza de visualização.
A seguir estão alguns problemas comuns e resoluções.


## <a name="unable-to-validate-volume-mapping"></a>Não é possível validar o mapeamento de volume
Mapeamento de volume é necessário para compartilhar o código fonte e binários do seu aplicativo com a pasta de aplicativo no contêiner.  Mapeamentos de volume específico estão contidos os arquivos docker compose.dev.debug.yml e docker compose.dev.release.yml. Como arquivos forem alterados em sua máquina host, os contêineres refletirão essas alterações em uma estrutura de pasta semelhante.

Para habilitar o mapeamento de volume, abra **configurações …** do ícone da bandeja "moby" Docker para Windows e selecione a guia **Compartilhado unidades** .  Certifique-se de que a letra da unidade que hospeda seu projeto, bem como a letra da unidade em que reside % USERPROFILE % são compartilhados nelas e, em seguida, clicando em **Aplicar**.

Para testar se mapeamento de volume estiver funcionando, depois que as unidades foram compartilhadas, reconstruir e F5 de dentro do Visual Studio ou tente o seguinte um comando de prompt:

*Em um prompt de comando do Windows*

*[Observação: Isso pressupõe que sua pasta de usuários está localizada na unidade "C" e que ele foi compartilhado.  Atualizar conforme necessário, se você tiver compartilhado uma unidade diferente]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*No contêiner de Linux*

```
/ # ls
```

Você deve ver uma listagem da pasta usuários/público de diretório.
Se nenhum arquivos são exibidos e sua pasta de /c/Users/Public não está vazia, mapeamento de volume não está configurado corretamente. 

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Alterar para o diretório de fenda espacial para ver o conteúdo do `/c/Users/Public` diretório:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Observação:** *Ao trabalhar com Linux VMs, o sistema de arquivos de contêiner diferencia maiusculas de minúsculas.*

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Construir: "PrepareForBuild" Falha inesperada da tarefa.

Microsoft.DotNet.Docker.CommandLine.ClientException: Erro ao tentar se conectar:

Verifique se que o host de docker padrão está em execução. Abra um prompt de comando e execute:

```
docker info
```

Se isso retornará um erro tente iniciar o aplicativo de área de trabalho **Docker para Windows** .  Se o aplicativo da área de trabalho estiver em execução, em seguida, o ícone de **moby** na bandeja do deve estar visível. Clique com o botão direito no ícone de bandeja e abra **configurações**.  Clique na guia **Redefinir** e, em seguida, **Reiniciar Docker...**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Atualizar manualmente de versão 0.31 para 0,40


1. Fazer backup do projeto
1. Exclua os seguintes arquivos do projeto:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Fechar a solução e remova as seguintes linhas do arquivo .xproj:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Reabrir a solução
1. Remova as seguintes linhas do arquivo Properties\launchSettings.json:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Remova os seguintes arquivos relacionados a Docker de project.json no publishOptions:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Desinstalar a versão anterior e instale ferramentas de Docker 0,40 e, em seguida, **Adicionar -> suporte de Docker** novamente no menu de contexto para seu aplicativo de Console ou da Web do ASP.Net Core. Isso adicionará os novos artefatos Docker necessários volta ao seu projeto. 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Uma caixa de diálogo de erro ocorre ao tentar **Adicionar -> suporte de Docker** ou depurar um aplicativo de núcleo do ASP.NET em um contêiner de (F5)

Ocasionalmente observamos depois de desinstalar e instalar extensões, MEF (Managed Extensibility Framework) cache do Visual Studio pode se tornar corrompida. Quando isso ocorre, ele pode causar o erro várias caixas de diálogo ao adicionar o suporte de Docker e/ou tentar executar ou depurar (F5) seu aplicativo de núcleo do ASP.NET. Como solução temporária, execute as seguintes etapas para excluir e recriar o cache MEF.

1. Feche todas as instâncias do Visual Studio
1. Abrir %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Exclua as seguintes pastas
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Abrir o Visual Studio
1. Tentar o cenário novamente 
