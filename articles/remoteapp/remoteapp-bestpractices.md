<properties
    pageTitle="Práticas recomendadas do Azure RemoteApp | Microsoft Azure"
    description="Práticas recomendadas para configurar e usar RemoteApp do Azure."
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

# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Práticas recomendadas para configurar e usar RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

As informações a seguir podem ajudá-lo a configurar e usar o Azure RemoteApp produtiva.

## <a name="connectivity"></a>Conectividade


- Sempre use a versão mais recente do cliente. Usar clientes antigos pode resultar em problemas de conectividade e outras experiências degradadas. Habilitando atualizações automáticas do aplicativo para o seu dispositivo garantirá que o cliente mais recente sempre está instalado.
- Sempre use a conexão de internet mais estável e confiável disponível para você.  
- Use somente com suporte a conexões de proxy para um desempenho ideal de conectividade.  Não há suporte para o proxy SOCKS.

## <a name="applications"></a>Aplicativos


- Salve e feche aplicativos RemoteApp quando terminar com o aplicativo. Não fechar o aplicativo pode resultar em perda de dados.
- Valide aplicativos personalizados antes de utilizá-los em RemoteApp do Azure. Isso inclui garantindo que eles trabalhar em uma plataforma de várias sessões e não consumam recursos desnecessários como memória e CPU que talvez diminui o outro usuário na mesma coleção. Para obter informações, baixe e revisar as [Práticas recomendadas de compatibilidade de aplicativos para os serviços de área de trabalho remota](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## <a name="configuration-and-management"></a>Configuração e gerenciamento


- Manter as imagens de modelo atualizado, instalando atualizações de software e outras correções críticas conforme necessário. Isso garante que, como Azure RemoteApp autoescalas para atender a capacidade de cada instância está corrigida.  
- Verifique se que sua implantação de serviços de Federação do Active Directory (AD FS) é seguro e confiável. Caso contrário, autenticação de cliente pode falhar, impedindo que os usuários acessem RemoteApp do Azure.
- Configure imagens de modelo com aplicativos instalados, funções ou recursos de modo que eles sejam sem estado. Eles não devem confiar em todas as instâncias de máquinas virtuais em um serviço de RemoteApp sendo em um estado persistente.
    - Armazene todos os dados de usuário em perfis de usuário ou outros locais de armazenamento externos para o serviço, como local de arquivo compartilhamentos ou OneDrive.
    - Armazene dados compartilhados em locais de armazenamento externos para o serviço, como local de arquivo compartilhamentos ou OneDrive.
    - Configure as configurações do sistema na imagem de modelo em vez de em máquinas virtuais individuais em um serviço.
    - Desabilitar atualizações automáticas de software para aplicativos publicados - em vez disso aplicá-los manualmente para a imagem de modelo e testá-las antes de implantar do modelo.
