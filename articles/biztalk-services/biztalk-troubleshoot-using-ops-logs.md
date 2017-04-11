<properties 
    pageTitle="Solucionar problemas de Serviços BizTalk usando logs de operação | Microsoft Azure" 
    description="Solucionar problemas de Serviços BizTalk usando logs de operação. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Serviços do BizTalk: Solucionar problemas usando os logs de operação

## <a name="what-are-the-operation-logs"></a>Quais são os Logs de operação
Logs de operação é um recurso de serviços de gerenciamento disponível no portal do clássico Azure que permite exibir registros históricos de operações executadas em seus serviços Azure, incluindo Serviços BizTalk. Isso permite exibir dados históricos relacionados a operações de gerenciamento na sua assinatura do BizTalk Service a partir de 180 dias.

> [AZURE.NOTE]Este recurso só captura logs para operações de gerenciamento nos serviços do BizTalk, como quando o serviço foi iniciado, feito para cima, e assim por diante. Essas operações são controladas independentemente se elas são executadas a partir do portal clássico Azure ou usando as [APIs REST do BizTalk serviço](http://msdn.microsoft.com/library/azure/dn232347.aspx). Para obter uma lista completa das operações que são controladas usando os serviços de gerenciamento, consulte [Operações controladas usando Azure Management Services](#bizops).<br/><br/>
Isso não captura os logs de atividades relacionadas ao tempo de execução de BizTalk Service (como mensagem processado pelo pontes, etc.). Para ver esses registros, use o modo de exibição de controle a partir do portal de Serviços BizTalk. Para obter mais informações, consulte [Controle de mensagens](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## <a name="view-biztalk-services-operation-logs"></a>Exibir Logs de operação dos serviços do BizTalk
1. No portal do Azure clássico, selecione **Os serviços de gerenciamento**e selecione a guia de **Logs de operação** .
2. Você pode filtrar os registros com base nos parâmetros diferentes como assinatura, intervalo de datas, tipo de serviço (por exemplo, Serviços BizTalk), nome do serviço ou o status da operação (êxito, falha).
3. Selecione a marca de seleção para exibir a lista filtrada. A imagem abaixo mostra atividades relacionadas a testbiztalkservice:  ![exibir logs de operação][ViewLogs] 
4. Para ler mais sobre como uma operação específica, selecione a linha e clique em **detalhes** na barra de tarefas na parte inferior.


## <a name="bizops"></a>Operações controladas usando os serviços de gerenciamento do Azure
A tabela a seguir lista as operações que são controladas usando os serviços de gerenciamento do Azure:

Nome da operação | Tarefa
--- | ---
CreateBizTalkService | Operação para criar um novo BizTalk Service
DeleteBizTalkService | Operação para excluir um BizTalk Service
RestartBizTalkService | Operação reiniciar uma BizTalk Service
StartBizTalkService | Operação para iniciar um BizTalk Service
StopBizTalkService | Operação de parar uma BizTalk Service
DisableBizTalkService | Operação para desativar um BizTalk Service
EnableBizTalkService | Operação para habilitar um BizTalk Service
BackupBizTalkService | Operação de backup de um BizTalk Service
RestoreBizTalkService | Operação para restaurar um BizTalk Service de backup especificado
SuspendBizTalkService | Operação para suspender um BizTalk Service
ResumeBizTalkService | Operação retomar uma BizTalk Service
ScaleBizTalkService | Operação dimensionar um BizTalk Service para cima ou para baixo
ConfigUpdateBizTalkService | Operação de atualização a configuração de um BizTalk Service
ServiceUpdateBizTalkService | Operação atualizar ou fazer downgrade um BizTalk Service para uma versão diferente
PurgeBackupBizTalkService | Operação de limpar backups do BizTalk Service fora do período de retenção


## <a name="see-also"></a>Consulte também
- [Serviço BizTalk backup](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurar o serviço BizTalk de Backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk serviços: Desenvolvedor, Basic, Standard e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Serviços do BizTalk: Portal de clássico do Azure usando de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Serviços do BizTalk: Gráfico de Status de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Serviços BizTalk: Guias de painel, o Monitor e a escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Serviços do BizTalk: otimização](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk serviços: Nome do emissor e chave emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Como faço para começar a usar o SDK dos serviços do BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 
