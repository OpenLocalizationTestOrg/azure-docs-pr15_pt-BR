
<properties
    pageTitle="Requisitos de aplicativo do Azure RemoteApp | Microsoft Azure"
    description="Saiba mais sobre os requisitos para os aplicativos que você deseja usar no RemoteApp do Azure"
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



# <a name="app-requirements"></a>Requisitos do aplicativo

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

RemoteApp Azure suporta streaming 32 bits ou 64 bits baseado no Windows aplicativos de uma imagem do Windows Server 2012 R2. A maioria dos 32 bits ou 64 bits baseado no Windows aplicativos existentes executados "assim mesmo" no Azure RemoteApp (serviços de área de trabalho remota ou anteriormente conhecidos como serviços de Terminal) ambiente. No entanto, há uma diferença entre executando e executando bem - alguns aplicativos funcionem corretamente e executam bem, enquanto outros não. As informações a seguir fornecem orientação para desenvolver aplicativos em um ambiente de serviços de área de trabalho remota e testar para garantir a compatibilidade.

Dica: Estamos trabalhando para criar alguns exemplos de trabalho de aplicativos para você. Você verá os novos tópicos que discutam usando o Microsoft Access, QuickBooks e App-V no RemoteApp.

## <a name="requirements"></a>Requisitos
Esses três requisitos, se seguidos, ajudam seu aplicativo executar bem no RemoteApp:

1.  Aplicativos que atendem a todos os [requisitos de certificação para aplicativos de desktop do Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) e de acordo com [as diretrizes de programação de serviços de área de trabalho remota](https://msdn.microsoft.com/library/aa383490.aspx) terá total compatibilidade com RemoteApp.
2.  Aplicativos nunca devem armazenar dados localmente na imagem ou instâncias de RemoteApp que podem ser perdidas.  Depois de criar um conjunto de RemoteApp, as instâncias são clonar têm estadas e devem conter apenas os aplicativos. Armazenar dados em uma fonte externa ou no perfil do usuário.
3.  Imagens personalizadas nunca devem conter dados que podem ser perdidos.  

## <a name="testing-your-apps"></a>Teste seus aplicativos
Use estas etapas para testar aplicativos:

1.  Instalar o Windows Server 2012 R2 e seu aplicativo
2.  Habilitar a área de trabalho remota
3.  Crie duas contas de usuário, UsuárioA e UserB, adicionando duas contas de usuário ao grupo de segurança de área de trabalho remota.
4.  Verificar a compatibilidade de várias sessões estabelecendo dois RDS sessões simultâneas ao PC ao iniciar o aplicativo.
5.  Validar o comportamento do aplicativo

## <a name="application-development-guidelines"></a>Diretrizes de desenvolvimento de aplicativos
Use as seguintes diretrizes para desenvolver aplicativos para RemoteApp.

### <a name="multiple-users"></a>Vários usuários

- Instalar um [aplicativo para um único usuário ](https://msdn.microsoft.com/library/aa380661.aspx)pode criar problemas em um ambiente multiusuário.
- Aplicativos devem [armazenar informações específicas do usuário](https://msdn.microsoft.com/library/aa383452.aspx) em locais específicos do usuário, separadamente do globais informações que se aplicam a todos os usuários.
- RemoteApp usa vários [namespaces para objetos de núcleo](https://msdn.microsoft.com/library/aa382954.aspx); um namespace global é usado principalmente pelos serviços nos aplicativos cliente/servidor.
- Não é seguro supor que o nome do computador ou o [endereço IP](https://msdn.microsoft.com/library/aa382942.aspx) atribuído ao computador estão associados um único usuário porque vários usuários podem estar conectados simultaneamente a um servidor de Host de sessão de área de trabalho remota (Host de sessão de RD).

### <a name="performance"></a>Desempenho
- Desative [efeitos gráficos](https://msdn.microsoft.com/library/aa380822.aspx) antes de adicionar seu aplicativo para o RemoteApp.
- Para maximizar a disponibilidade de CPU para todos os usuários, desative [tarefas em segundo plano](https://msdn.microsoft.com/library/aa380665.aspx) ou criar tarefas de plano de fundo eficiente que não são recursos.
- Você deve ajustar e Saldo [uso de segmento](https://msdn.microsoft.com/library/aa383520.aspx) do aplicativo para um ambiente multiusuário, vários processadores.
- Para otimizar o desempenho, é recomendável para aplicativos para [detectar](https://msdn.microsoft.com/library/aa380798.aspx) se estão sendo executados em uma sessão de cliente.
