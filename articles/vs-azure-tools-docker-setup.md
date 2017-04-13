<properties
   pageTitle="Configurar um Host Docker com VirtualBox | Microsoft Azure"
   description="Instruções passo a passo para configurar uma instância de Docker padrão usando Docker máquina e VirtualBox"
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
   ms.author="mlearned" />

# <a name="configure-a-docker-host-with-virtualbox"></a>Configurar um Host Docker com VirtualBox

## <a name="overview"></a>Visão geral
Este artigo orienta você a configurar uma instância de Docker padrão usando Docker máquina e VirtualBox. Se você estiver usando o [Docker para Windows beta](http://beta.docker.com/), essa configuração não é necessária.

## <a name="prerequisites"></a>Pré-requisitos
As seguintes ferramentas precisam estar instalado.

- [Caixa de ferramentas de docker](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configurando o cliente Docker com o Windows PowerShell

Para configurar um cliente de Docker, simplesmente abrir o Windows PowerShell e execute as seguintes etapas:

1. Crie uma instância de host padrão docker.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Verifique se que a instância padrão está configurado e em execução. (Você deve ver uma ocorrência chamada 'default' em execução.

    ```PowerShell
    docker-machine ls 
    ```
        
    ![saída de máquina docker ls][0]
 
1. Definir o padrão como o host atual e configure seu shell.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Exiba os contêineres de Docker ativos. Na lista deve estar vazia.

    ```PowerShell
    docker ps
    ```

    ![saída do docker ps][1]
 
> [AZURE.NOTE]Cada vez que você reiniciar sua máquina de desenvolvimento, você precisará reiniciar seu host docker local.
> Para fazer isso, execute o seguinte comando no prompt de comando: `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
