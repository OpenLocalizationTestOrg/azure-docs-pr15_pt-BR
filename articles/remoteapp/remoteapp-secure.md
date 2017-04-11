
<properties
    pageTitle="Proteger aplicativos e recursos no Azure RemoteApp | Microsoft Azure"
    description="Saiba como bloquear aplicativos e recursos no RemoteApp do Azure"
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



# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Proteger aplicativos e recursos no RemoteApp do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp fornece aos usuários acesso aos aplicativos do Windows gerenciados centralmente, que permite controlar o que seus usuários podem ou não fazer.  Isso é especialmente útil quando o usuário está se conectando através de um dispositivo não gerenciado (como seu Macbook pessoa) e você quiser controlar o acesso de usuário ou experiência.

Por exemplo, se você estiver usando o Active Directory para autenticação de usuário e deseja impedir que os usuários copiando dados fora de um aplicativo, você pode configurar uma política de grupo de área de trabalho remota para bloquear usuários copiem dados.

Outro exemplo é se você deseja bloquear o acesso à internet para um aplicativo específico no seu conjunto. Você pode criar uma regra de Firewall do Windows que bloqueia o acesso ao criar a imagem para o conjunto.

## <a name="implementation-options"></a>Opções de implementação

  Aqui estão as opções de implementação de chave, que podem ser usadas individualmente ou em conjunto, conforme necessário:

1.  Se sua coleção de RemoteApp for domínio associado, você pode aplicar qualquer [Política de grupo](https://technet.microsoft.com/library/cc725828.aspx) (com exceção do ocioso e desconectar tempo limite políticas descrito [aqui](../azure-subscription-service-limits.md)).
2.  Como alternativa à política de grupo (se o seu conjunto não seja integrado ao domínio ou não tem os privilégios direita no AD), você pode configurar [Políticas locais](https://technet.microsoft.com/library/cc775702.aspx) na sua imagem de modelo.  Observação que esse grupo políticas diretivas locais trunfo quando há um conflito.
3.  Algumas configurações do sistema operacional/aplicativo não são configuráveis via política, mas podem ser por meio de chave do registro usando a [ferramenta de RegEdit](./remoteapp-hybridtrouble.md) ao configurar sua imagem de modelo.
4.  Você pode usar o [Firewall do Windows](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) para controlar o acesso de rede de e para o computador onde o aplicativo é executado. Apenas certifique-se de que você não bloquear URLs e portas definidas aqui.
5.  Você pode usar o [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) para controlar quais aplicativos e arquivos que os usuários podem ser executados. Por exemplo, usuários experientes podem descobrir como executar aplicativos que você não publicou, mas que estão disponíveis na imagem usada para criar o conjunto - AppLocker pode bloquear isso.

## <a name="detailed-information"></a>Informações detalhadas

- As seguintes políticas RDS são provavelmente será mais útil:
    - [Dispositivo e redirecionamento de recursos](https://technet.microsoft.com/library/ee791794.aspx)
    - [Redirecionamento de impressora](https://technet.microsoft.com/library/ee791784.aspx)
    - [Perfis](https://technet.microsoft.com/library/ee791865.aspx).
- Observe que configurando redirecionamentos através do RemoteApp PowerShell módulo (como visto [aqui](./remoteapp-redirection.md)) depende de computador cliente para impor a política, então se segurança é o objetivo principal deverá para impor a política por meio da diretiva local de imagem de modelo ou política de grupo.
- [Políticas de Windows Server 2012 R2](https://technet.microsoft.com/library/hh831791.aspx).
- [Políticas do Office 2013](https://technet.microsoft.com/library/cc178969.aspx) (incluindo [como personalizar a barra de ferramentas do Office](https://technet.microsoft.com/library/cc179143.aspx)).
