<properties
    pageTitle="Usando o Outlook no Azure RemoteApp | Microsoft Azure" 
    description="Saiba como configurar e usar o Outlook no Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Usando o Microsoft Outlook no Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

RemoteApp Azure suporta O365 do Microsoft Outlook. Leia mais sobre como [o Office funciona no Azure RemoteApp](remoteapp-officesubscription.md). Há algumas configurações recomendadas do Outlook quando usada em RemoteApp do Azure.

## <a name="cached-mode"></a>Modo de cache
Modo de cache é uma configuração recomendada quando usando o Outlook no Azure RemoteApp. Quando você configurar uma conta do Outlook 2013 para usar o modo cache do Exchange, o Outlook 2013 funciona de uma cópia local de correio do usuário do Microsoft Exchange que está armazenado em um arquivo de dados offline (OST arquivo) no computador do usuário, junto com o catálogo de Endereços Offline (OAB). A caixa de correio em cache e OAB são atualizados periodicamente do serviço do Office 365. Leia mais sobre [as diferenças entre o modo de cache e online](https://technet.microsoft.com/library/jj683103.aspx).

O usuário pode selecionar o **Modo cache do Exchange** ou **Modo Online** durante a configuração de conta ou alterando as configurações de conta. Você também pode implantar um modo ou outro usando a ferramenta de personalização do Office (OCT) ou a política de grupo.  

Leia as [instruções passo a passo sobre como habilitar o modo em cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Pesquisa
No Azure RemoteApp, usando a pesquisa dentro do Outlook tem limitações. RemoteApp Azure usa VMs em pool para acomodar sessões de usuário. Indexação de pesquisa depende da identificação de máquina, que é diferente para VMs diferentes. É possível que, sempre que um usuário entra em Azure RemoteApp, eles são direcionados para uma nova VM. Isso significa que, se podemos Habilitar pesquisa local, o indexador será executado toda vez que a identificação da máquina muda (quando o usuário está em uma máquina virtual diferente). Dependendo do tamanho da. Arquivo OST, o indexador pode levar muito tempo para concluir e usar os recursos necessários para outros aplicativos. Pesquisa não apenas seria lenta, mas não pode produzir resultados. Usando um perfil de conta do modo Online seria contornar esse problema, mas o desempenho geral seria afetado devido a falta de um cache local (consulte o link acima para obter mais informações sobre a diferença entre o modo de cache e online). Infelizmente, pesquisa indexados/local não pode ser desabilitada e pesquisa on-line não pode ser ativada por padrão no Outlook 2013.
