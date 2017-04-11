<properties
   pageTitle="Guia de solução de problemas de Central de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda a solucionar problemas do Centro de segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-troubleshooting-guide"></a>Guia de solução de problemas do Centro de segurança do Azure
Este guia é para profissionais de TI (tecnologia) de informação, analistas de segurança de informações e administradores de nuvem cujas organizações estiver usando o Centro de segurança do Azure e precisam solucionar que problemas relacionados à Central de segurança.

## <a name="troubleshooting-guide"></a>Guia de solução de problemas
Este guia explica como solucionar problemas relacionados à Central de segurança. A maioria da solução de problemas concluído no Centro de segurança ocorrerá examinando primeiro os registros de [Log de auditoria](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) para o componente com falha. Por meio de logs de auditoria, você pode determinar:

- Quais operações foram tiradas local
- Quem iniciou a operação
- Quando a operação ocorreu
- O status da operação
- Os valores das outras propriedades que podem ajudá-lo a operação de pesquisa

O log de auditoria contém todas as operações de gravação (colocar, POST, DELETE) executadas em seus recursos, no entanto, ele não inclui operações de leitura (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Solução de problemas de instalação do agente monitoramento no Windows

A Central de segurança monitoramento agente é usada para executar a coleta de dados. Depois de coleta de dados está habilitada e o agente está instalado corretamente na máquina de destino, esses processos devem ser em execução:

- ASMAgentLauncher.exe - monitoramento agente do Azure 
- ASMMonitoringAgent.exe - extensão Azure monitoramento de segurança
- ASMSoftwareScanner.exe – Gerenciador de digitalização do Azure

A extensão de monitoramento de segurança do Azure procura vários configuração relevantes de segurança e coleta logs de segurança na máquina virtual. O Gerenciador de verificação será usado como um scanner de patch.

Se a instalação for realizada com êxito, você deve ver uma entrada semelhante ao abaixo em Logs de auditoria para o destino máquina virtual:

![Logs de auditoria](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

Você também pode obter mais informações sobre o processo de instalação lendo os logs de agente, localizados em *%systemdrive%\windowsazure\logs* (exemplo: C:\WindowsAzure\Logs).

> [AZURE.NOTE] Se o agente de centro de segurança do Azure é inadequada, você precisará reiniciar o destino máquina virtual, desde que não tem o comando parar e iniciar o agente.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Solução de problemas de instalação do agente monitoramento no Linux
Quando a solução de problemas de instalação do agente de máquina virtual em um sistema Linux, você deve garantir que a extensão foi baixada/var/biblioteca/waagent /. Você pode executar o comando a seguir para verificar se ele foi instalado:

`cat /var/log/waagent.log` 

Outros arquivos de log que você possa examinar para finalidade de solução de problemas são: 

- /var/log/mdsd.Err
- / var/log/azure /

Em um sistema de trabalho, você deve ver uma conexão para o processo de mdsd TCP 29130. Este é o syslog se comunicar com o processo de mdsd. Você pode validar esse comportamento executando o comando a seguir:

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>Contatar o suporte da Microsoft

Alguns problemas podem ser identificados usando as diretrizes fornecidas neste artigo, as outras pessoas, que você também pode encontrar documentadas no [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)público Central de segurança. Porém se você precisa de mais solução de problemas, você pode abrir uma nova solicitação de suporte usando o Portal do Azure conforme mostrado abaixo: 

![Suporte da Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Consulte também

Neste documento, você aprendeu como configurar políticas de segurança na Central de segurança do Azure. Para saber mais sobre o Centro de segurança do Azure, consulte o seguinte:

- [Guia de operações e planejamento de centro de segurança do Azure](security-center-planning-and-operations-guide.md) — aprender a planejar e compreenda as considerações de design para adotar o Centro de segurança do Azure.
- [Monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md) — Aprenda a monitorar a integridade dos seus recursos Azure
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerenciar e responder a alertas de segurança
- [Monitoramento soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — localizar postagens no blog sobre segurança do Azure e conformidade
