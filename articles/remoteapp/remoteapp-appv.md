<properties
    pageTitle="Usando aplicativos de App-V com o Azure RemoteApp | Microsoft Azure"
    description="Saiba como usar aplicativos de App-V no Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="using-app-v-apps-in-azure-remoteapp"></a>Usando aplicativos de App-V no Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Você pode usar os aplicativos de App-V em uma coleção de híbrido Azure RemoteApp, que requer a associação de domínio.

Antes de começar, certifique-se de instalar o cliente App-V 5.1 com as últimas atualizações. Você precisará criar uma [imagem personalizada](remoteapp-create-custom-image.md) que inclui o cliente App-V.  

É fácil usar sua infraestrutura de App-V existente com o Azure RemoteApp. Como um conjunto de híbrida é implantado em um VNET do Azure que tenha acesso ao seu controlador de domínio e as VMs são domínio associado, você pode aproveitar seu aplicativo-v infraestrutura e implantação métodos existentes ao aplicativo de App-V de host easyily no Azure RemoteApp. Aqui estão algumas considerações que você deve estar ciente, com base no tipo de implantação de App-V que você possui no momento:

| Opções de configuração |                       | Positivo                                                               | Negativo                                                                                              |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Método de entrega       | Streaming (sob demanda) | Aplicativo é sempre a mais recente e atualizado                                     | Latência da primeira vez                                                                                    |
|                       | Montado               | Mais rápido; aplicativo já está presente na máquina virtual                              | Aumento dos - leva (limite de 127 GB) de espaço de imagem                                                           |
| Armazenamento local de aplicativo  | Conteúdo compartilhado        | Aplicativo é executada na memória da instância RemoteApp do Azure                         | Consome memória e boa conexão para streaming server (arquivo) onde o aplicativo está localizado                      |
|                       | Disco (em cache)         | Execução rápida. Aplicativo não depende da disponibilidade da fonte de conteúdo | Aumento dos - leva (limite de 127 GB) de espaço de imagem                                                           |
| -Alvo             | Usuário                  | Exige infraestrutura de App-V completo autônomo                          |                                                                                                       |
|                       | Global (máquina)      |  Pré-publicar ou de destino usando a publicação do servidor                         |  Precisa atualizar sua imagem Azure se desejar atualizar o aplicativo (grande). Ocupa espaço na imagem. |

 Depois de criar sua imagem personalizada e seu conjunto de híbrido, publicar seu aplicativo, atribuir aos usuários e aproveitar seus aplicativos existentes do App-V hospedados no Azure RemoteApp entregues em qualquer dispositivo em qualquer lugar.
