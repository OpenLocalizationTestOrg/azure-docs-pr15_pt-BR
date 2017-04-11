<properties 
   pageTitle="Versão do StorSimple 8000 notas de versão | Microsoft Azure"
   description="Descreve os novos recursos, questões abertas e soluções alternativas disponíveis para o julho de 2014 lançamento do Microsoft Azure StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>Notas de versão de lançamento do StorSimple 8000 série - julho de 2014 

## <a name="overview"></a>Visão geral

As notas de versão de acompanhamento identificam as questões abertas críticas para a série de 8000 StorSimple lançamento de disponibilidade geral (GA) de julho de 2014 do Microsoft Azure StorSimple. Esta versão corresponde à versão do software 6.3.9600.17215.  

A menos que especificado em contrário, essas notas de versão se aplicam a todos os modelos do dispositivo StorSimple. As notas são atualizadas continuamente; como exigindo a solução de problemas críticos são descobertos, eles são adicionados. Antes de implantar sua solução Microsoft Azure StorSimple, considere as seguintes informações.  

## <a name="known-issues-in-this-release"></a>Problemas conhecidos nesta versão
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.  
 
| Não. | Recurso | Problema | Comentários/solução alternativa | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Redefinição de fábrica | Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple podem estar presas e exibir esta mensagem: **Redefinir para fábrica está em andamento (fase 8)**. Isso acontece se você pressionar CTRL + C enquanto o cmdlet está em andamento. | Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, contate o Microsoft Support para as próximas etapas. | Sim | Não |
| 2 | Quorum de disco | Em casos raros, se a maioria dos discos no compartimento EBOD de um dispositivo 8600 estiver desconectada, resultando em quorum sem disco, então o pool de armazenamento será offline. Ela permanecerá offline, mesmo se os discos estão reconectados. | Você precisará reiniciar o dispositivo. Se o problema persistir, contate o Microsoft Support para as próximas etapas. | Sim | Não |
| 3 | Falhas de instantâneo de nuvem | Em casos raros, um instantâneo de nuvem pode falhar com o erro de **limite de backup máximo atingido**. Isso ocorre se você exceder 255 clones onlinehttps no mesmo dispositivo, do mesmo volume original que foi excluído. | | Sim | Sim |
| 4 | Identificação do controlador incorreto | Quando uma substituição de controlador é executada, controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada do nó ponto, a identificação do controlador pode aparecer inicialmente como ID. do controlador de ponto Em casos raros, esse comportamento também pode ser visto após a reinicialização do sistema. | Nenhuma ação é necessária. Essa situação se resolverá após a substituição de controlador é concluída. | Sim | Não |
| 5 | Dispositivo monitorando gráficos | No serviço StorSimple Manager, os gráficos de monitoramento do dispositivo não funcionam quando básica ou a autenticação NTLM está habilitada na configuração do servidor proxy para o dispositivo. | Modificar a configuração de proxy da web para o dispositivo registrado com o serviço do Gerenciador de StorSimple para que a autenticação está definida como nenhum. Para fazer isso, execute do Windows PowerShell para o cmdlet Set-HcsWebProxy StorSimple. | Sim | Sim |
| 6 | Contas de armazenamento | Usando o serviço de armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados. | | Sim | Sim |
| 7 | Failback | Não há suporte para um failback dentro de 24 horas de recuperação de dados (DR). | | Sim | Não |
| 8 | Failover de dispositivo | Não há suporte para vários failovers de um contêiner de volume do dispositivo de origem mesmo para diferentes dispositivos de destino. Failover de um único dispositivo inativo em vários dispositivos fará os contêineres de volume na primeira falha sobre dispositivo perdem a propriedade de dados. Após o failover, esses contêineres de volume aparecerá ou se comportam de forma diferente quando você vê-los no portal de clássico do Azure. | | Sim | Não |
| 9 | Instalação | Durante StorSimple adaptador de instalação do SharePoint, você precisa fornecer um IP de dispositivo concluir com êxito a instalação. | | Sim | Não |
| 10 | Interfaces de rede | Interfaces de rede dados 2 e 3 de dados foram trocadas no software. | Se você precisa configurar essas interfaces, contate Microsoft Support. | Sim | Não |


 
