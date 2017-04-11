<properties 
    pageTitle="Saiba mais sobre a limitação nos serviços do BizTalk | Microsoft Azure" 
    description="Saiba mais sobre a otimização de limites e resultando comportamentos de tempo de execução para Serviços BizTalk. A limitação baseia-se no uso de memória e o número de mensagens. MABS, WABS" 
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





# <a name="biztalk-services-throttling"></a>Serviços do BizTalk: otimização

Os serviços do Azure BizTalk implementa a otimização de serviço com base em duas condições: uso de memória e o número de mensagens simultâneos processamento. Este tópico lista os limites de otimização e descreve o comportamento de tempo de execução quando ocorre uma condição de otimização.

## <a name="throttling-thresholds"></a>A otimização de limites

A tabela a seguir apresenta a limitação de origem e de limites:

||Descrição|Limite baixo|Limite alto|
|---|---|---|---|
|Memória|% do total do sistema memória disponível/PageFileBytes. <p><p>PageFileBytes disponível total é de aproximadamente 2 vezes a RAM do sistema.|60%|70%|
|Processamento de mensagem|Número de mensagens processando simultaneamente|40 * número de cores|100 * número de cores|

Quando um limite alto é alcançado, serviços do Azure BizTalk começa a aceleração. A limitação paradas quando o limite mínimo é atingido. Por exemplo, seu serviço está usando a memória do sistema de 65%. Nessa situação, o serviço não aceleração. Seu serviço inicia usando a memória do sistema de 70%. Nessa situação, o serviço acelera e continua até que o serviço usa memória do sistema (limite baixo) 60% de aceleração.

Os serviços do Azure BizTalk rastreia o status de otimização (estado normal versus estado limitado) e a duração de otimização.


## <a name="runtime-behavior"></a>Comportamento de tempo de execução

Quando os serviços do Azure BizTalk entra em um estado limitação, ocorre o seguinte:

- A aceleração é por instância de função. Por exemplo:<br/>
RoleInstanceA é a otimização. Não é a otimização de RoleInstanceB. Nessa situação, mensagens no RoleInstanceB são processadas conforme esperado. Mensagens em RoleInstanceA são descartadas e falharem com o seguinte erro:<br/><br/>
**Servidor está ocupado. Tente novamente.**<br/><br/>
- Quaisquer fontes de recepção não poll ou fazer o download de uma mensagem. Por exemplo:<br/>
Um pipeline recebe mensagens de uma fonte externa de FTP. Obtém a instância de função fazendo a recepção em um estado de otimização. Nessa situação, o pipeline interrompe baixar mensagens adicionais até a instância da função parar a otimização.
- Uma resposta é enviada ao cliente para que o cliente pode reenviar a mensagem.
- Você deve esperar até que a otimização for resolvido. Especificamente, você deve esperar até que o limite mínimo é atingido.

## <a name="important-notes"></a>Observações importantes
- A otimização não pode ser desabilitada.
- Limites de otimização não pode ser modificada.
- A limitação é implementada todo o sistema.
- O servidor de banco de dados do SQL Azure também tem a otimização de interno.

## <a name="additional-azure-biztalk-services-topics"></a>Tópicos de serviços do Azure BizTalk adicionais

-  [Instalar os serviços do BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriais: Os serviços do Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Como faço para começar a usar o SDK dos serviços do BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Serviços BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Consulte também
- [BizTalk serviços: Desenvolvedor, Basic, Standard e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços do BizTalk: Portal de clássico do Azure usando de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços do BizTalk: Gráfico de Status de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Serviços BizTalk: Guias de painel, o Monitor e a escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk serviços: Backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk serviços: Nome do emissor e chave emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 
