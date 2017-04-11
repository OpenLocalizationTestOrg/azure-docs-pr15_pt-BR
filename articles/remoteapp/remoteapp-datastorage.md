
<properties
    pageTitle="Nunca armazenar dados confidenciais no imagens personalizadas para Azure RemoteApp | Microsoft Azure"
    description="Saiba mais sobre as diretrizes para armazenar dados em imagens personalizadas no RemoteApp do Azure"
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


# <a name="never-store-sensitive-data-on-custom-images"></a>Nunca armazenar dados confidenciais no imagens personalizadas

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Quando você hospeda seu próprio aplicativo no Azure RemoteApp, a primeira etapa é criar uma imagem personalizada. Usamos dessa imagem personalizada para criar instâncias de máquina virtual que servir seus aplicativos para seus usuários. A imagem personalizada deve conter apenas aplicativos e dados nunca confidenciais que podem ser perdidos, como bancos de dados SQL, arquivos de pessoal ou arquivos de dados especiais como arquivos de empresa do QuickBooks. Todos os dados confidenciais devem residem externo ao Azure RemoteApp em um servidor de arquivo, outra VM do Azure, ou no SQL Azure. A imagem apenas deve hospedar o aplicativo que conecta-se à fonte de dados e apresenta os dados. Verifique os [requisitos para imagens do Azure RemoteApp](remoteapp-imagereqs.md) para obter mais informações. 

Para compreender por que você não deve armazenar dados confidenciais, você precisa compreender como funciona o Azure RemoteApp. Quando um conjunto é criado ou atualizado, nos bastidores vários clones ou cópias da imagem são criados. Todas essas instâncias de máquina virtual são réplicas exatas da imagem personalizada. Quando os usuários iniciar aplicativos eles estão conectados a uma dessas instâncias de máquina virtual. Mas a mesma instância não garantimos e não deve importar porque eles são não-persistente. As instâncias de máquina virtual que hospeda os aplicativos são não-persistente e podem ser destruído ou excluídos com base, por exemplo, durante a atualização do conjunto. 

Depois que o conjunto está provisionado e usuários iniciarem conectando ao VMs, dados do usuário são persistentes e protegido porque ele é salvo no armazenamento separado dentro de um VHD que chamamos de um [disco de perfil de usuário (UPD)](remoteapp-upd.md), que é o perfil de usuário no c:\users\<userprofile >. Quando um aplicativo é iniciado, o UPD é montado e tratado como um perfil de usuário local pelo sistema operacional. Leia mais sobre como [Azure RemoteApp salva as configurações e dados do usuário](remoteapp-upd.md).

Dados de exemplo que não devem residem na imagem:

- Dados para os usuários acessem compartilhados
- Banco de dados do SQL ou do QuickBooks DB
- Todos os dados em D:\

Dados de exemplo que podem residem no perfil padrão sejam copiados para UPD todos os usuários:

- Arquivos de configuração por usuário
- Scripts que usuários precisarão preservados em sua UPD

Pontos-chave:

- Nunca armazenar dados confidenciais que podem ser perdidos na imagem ao criar uma imagem personalizada.
- Dados confidenciais sempre devem residem em um servidor de arquivo separado, separada do Azure máquina virtual, na nuvem e sempre externos para as instâncias de máquina virtual que hospeda seus aplicativos dentro do Azure RemoteApp. 
- Dados de usuário são salvos e persistir no disco de perfil de usuário (UPD)


