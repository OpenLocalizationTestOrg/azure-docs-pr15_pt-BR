<properties 
    pageTitle="Gerenciar um aplicativo web no serviço de aplicativo do Azure" 
    description="Links para recursos de gerenciamento de um aplicativo web no serviço de aplicativo do Azure." 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="rachelap"/>

# <a name="manage-a-web-app-in-azure-app-service"></a>Gerenciar um aplicativo web no serviço de aplicativo do Azure

Este tópico contém links para recursos de gerenciamento de um aplicativo web no [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Gerenciamento inclui todas as tarefas que mantém seu aplicativo web do funcionamento. 

Sobre o ciclo de vida de um aplicativo web, você executará tarefas de gerenciamento diferentes, como mover de implantação inicial para atualizações, manutenção e operação normal.

Muitas tarefas de gerenciamento de aplicativo da web podem ser executadas no Portal do Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Antes de implantar seu aplicativo web para produção

### <a name="choose-a-tier"></a>Escolha um nível

Serviço de aplicativo do Azure é oferecido em cinco níveis: livre, compartilhado, Basic, Standard e Premium. Para obter informações sobre os recursos e preços para cada camada, consulte [detalhes de preços](/pricing/details/app-service/). 

- [Planos de serviço de aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) permitem agrupar vários aplicativos web sob o mesmo nível.
- Você sempre pode [Alternar níveis](web-sites-scale.md) depois de criar seu aplicativo web.

### <a name="configuration"></a>Configuração

Use o [Portal do Azure](https://portal.azure.com/) para definir várias opções de configuração. Para obter detalhes, consulte [Configurar web apps em um serviço de aplicativo do Azure](web-sites-configure.md). Aqui está uma lista de verificação rápida:

- Selecione **versões de tempo de execução** para .NET, PHP, Java ou Python, se necessário.
- Habilite **WebSocket** se seu aplicativo web usa o protocolo WebSocket. (Isso inclui aplicativos que usam [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](web-sites-nodejs-chat-app-socketio.md).)
- Você está executando trabalhos de web contínua? Em caso afirmativo, ative **Sempre**.
- Defina o **documento padrão**, como index.

Além destas configurações básicas, talvez você queira configurar o seguinte:

- Criptografia de **Camada de soquete seguro (SSL)** . Para usar SSL com um nome de domínio personalizado, você deve obter um certificado SSL e configurar seu aplicativo web para usá-lo. Consulte [Habilitar HTTPS para um aplicativo web no serviço de aplicativo do Azure](web-sites-configure-ssl-certificate.md).
- **Nome de domínio personalizado.** Seu aplicativo web tem automaticamente um subdomínio em azurewebsites.net. Você pode associar um nome de domínio personalizado, como contoso.com. Consulte [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure](web-sites-custom-domain-name.md).

Configuração de idiomas específicos:

- **PHP**: [Configurar PHP nos aplicativos Web do serviço de aplicativo do Azure](web-sites-php-configure.md).
- **Python**: [Configurando Python ao Azure serviço de aplicativo Web Apps](web-sites-python-configure.md)


## <a name="while-your-web-app-is-running"></a>Enquanto seu aplicativo web está em execução

Enquanto seu aplicativo web é executado, você deseja certificar-se de que ele está disponível e que ele pode ser expandido para atender o tráfego de usuário. Talvez você também precise solucionar problemas de erros.

### <a name="monitoring"></a>Monitoramento

- Por meio do Portal do Azure, você pode [Adicionar métricas de desempenho](web-sites-monitor.md) como uso da CPU e o número de solicitações de cliente.
- [Escala de seu aplicativo web](web-sites-scale.md) em resposta ao tráfego. Dependendo de sua camada, você pode dimensionar o número de VMs e/ou o tamanho das instâncias de máquina virtual. Nos níveis padrão e Premium, você pode também configurar auto-dimensionamento, para que seu aplicativo web automaticamente, escalas em uma programação fixa ou em resposta ao carregar.  
 
### <a name="backups"></a>Backups

- Configurar [backups automáticos](web-sites-backup.md) de seu aplicativo web. Saiba mais sobre backups [neste](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/)vídeo.
- Saiba mais sobre as opções de [recuperação de banco de dados](../sql-database/sql-database-business-continuity.md) do Azure SQL Database.

### <a name="troubleshooting"></a>Solução de problemas

- Se algo der errado, você pode [Solucionar problemas no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), usando os logs de diagnóstico e depuração ao vivo na nuvem. 
- Fora do Visual Studio, há várias maneiras para coletar logs de diagnóstico. Consulte [Ativar o log para web apps em um serviço de aplicativo do Azure de diagnósticos](web-sites-enable-diagnostic-log.md).
- Para aplicativos de Node, consulte [como depurar um aplicativo da web Node no serviço de aplicativo do Azure](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Restaurando dados

- [Restaurar](web-sites-restore.md) um aplicativo web que foi backup anteriormente.


## <a name="when-you-update-your-web-app"></a>Quando você atualizar seu aplicativo web

Se você não ativou backups automáticos, você pode criar um [backup manual](web-sites-backup.md).

Considere usar [transferidos implantação](web-sites-staged-publishing.md). Essa opção permite publicar atualizações em uma implantação de teste que executa o lado a lado com sua implantação de produção. 

Se você usar os serviços de equipe do Visual Studio, você pode configurar contínua implantação do controle de origem:

- [Usando o controle de versão do Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [Usando gito](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  
