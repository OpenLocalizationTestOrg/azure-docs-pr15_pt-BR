<properties 
    pageTitle="Tarefas permitidas em diferentes estados ou status nos serviços do BizTalk | Microsoft Azure" 
    description="As ações/operações permitidas em status MABS diferente: parar, iniciar, reinicie, suspender, continuar, excluir, dimensionar, atualizar configuração e fazendo backup" 
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



# <a name="biztalk-services-service-state-chart"></a>BizTalk serviços: Gráfico de estado de serviço
Dependendo do estado atual do serviço BizTalk, há operações que você pode ou não é possível executar o serviço de BizTalk.

Por exemplo, você provisionar um novo serviço BizTalk no portal de clássico do Azure. Quando ele for concluído com êxito, o serviço de BizTalk está em estado ativo. No estado ativo, você pode parar o serviço BizTalk. Se parar for bem-sucedida, o serviço BizTalk vai para um estado interrompido. Se parar falhar, o serviço BizTalk vai para um estado de StopFailed. No estado StopFailed, você pode reiniciar o serviço BizTalk. Se você tentar uma operação que não é permitida, como o currículo serviço BizTalk, ocorre o seguinte erro:

**Operação não permitida**

Para provisionar um BizTalk Service, acesse [Serviços BizTalk: provisionamento usando o portal do Azure clássico](http://go.microsoft.com/fwlink/p/?LinkID=302280).

As tabelas a seguir listam as operações ou ações que podem ser concluídas quando o BizTalk Service está em um estado específico. Um ✔ significa que a operação é permitida enquanto estiver nesse estado. Uma entrada em branco significa que a operação não pode ser executada enquanto estiver nesse estado.

## <a name="start-stop-restart-suspend-resume-and-delete-operations"></a>Iniciar, parar, reinicie, suspender, currículo e excluir operações
<table border="1">
<tr>
        <th colspan="15">Operação ou ação</th>
</tr>

<tr>
        <th rowspan="18">Estado do serviço BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Iniciar</th>
        <th>Parar</th>
        <th>Reiniciar</th>
        <th>Suspender</th>
        <th>Currículo</th>
        <th>Excluir</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ativa</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Desativado</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspenso</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Interrompido</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Falha na atualização do serviço</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## <a name="scale-update-configuration-and-backup-operations"></a>Escala, configuração de atualização e operações de Backup
<table border="1">
<tr>
        <th colspan="15">Operação ou ação</th>
</tr>

<tr>
        <th rowspan="18">Estado do serviço BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Escala</th>
        <th>Configuração de atualização</th>
        <th>Backup</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ativa</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Desativado</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspenso</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Interrompido</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Falha na atualização do serviço</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## <a name="see-also"></a>Consulte também
- [Serviços do BizTalk: Portal de clássico do Azure usando de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços BizTalk: Guias de painel, o Monitor e a escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk serviços: Desenvolvedor, Basic, Standard e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk serviços: Backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços do BizTalk: otimização](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk serviços: Nome do emissor e chave emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Como faço para começar a usar o SDK dos serviços do BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 
