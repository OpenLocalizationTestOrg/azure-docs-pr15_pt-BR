<properties
   pageTitle="Híbrido Runbook trabalhador: Um trabalho runbook termina com um status de suspenso | Microsoft Azure"
   description="Erro de encerramento de trabalho sintomas causas e resoluções para híbrido Runbook trabalhador."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="magoedte" />

# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Operador de Runbook híbrido: Um trabalho runbook termina com um status de suspenso

## <a name="summary"></a>Resumo

Seu runbook está suspenso logo após tentar executá-la em três vezes. Há condições que podem interromper runbook seja concluída com êxito e a mensagem de erro relacionada não inclui informações adicionais indicando o porquê. Este artigo fornece etapas de solução de problemas para problemas relacionados a falhas na execução da runbook as híbrido Runbook trabalhador.

Se seu problema Azure não endereçado neste artigo, visite os fóruns do Azure no [MSDN e estouro de pilha](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou para [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Além disso, você pode enviar uma solicitação de suporte Azure selecionando **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Sintoma

Falha de execução de runbook e o erro retornado é, "ação de trabalho não pode ser executado 'Ativar', porque o processo interrompido inesperadamente. Ação de trabalho tentativa de 3 vezes."


## <a name="cause"></a>Causa

Há várias causas possíveis para o erro: 

  1. Trabalhador híbrido está atrás de um proxy ou firewall
  2. Trabalhador híbrida estiver em execução no computador tem menos de [requisitos](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) mínimos de hardware 
  3. O runbooks não pode autenticar com recursos locais


## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: Híbrido Runbook trabalhador está atrás de um proxy ou firewall

O computador que híbrido Runbook trabalhador está em execução está atrás de um servidor proxy ou firewall e acesso de rede de saída não pode ser permitido ou configurado corretamente.

### <a name="solution"></a>Solução

Verifique se o computador possui acesso de saída para *. cloudapp.net em portas 443, 9354 e 30199 30000. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: O computador tem requisitos de hardware mínimo menor que

Computadores que executam o trabalhador de Runbook híbrido devem atender aos requisitos mínimos de hardware antes designando-o para hospedar esse recurso. Caso contrário, dependendo a utilização de recursos de outros processos de plano de fundo e disputa causados por runbooks durante a execução, o computador irá se tornam mais utilizados e causar atrasos de trabalho de runbook ou tempos limite. 

### <a name="solution"></a>Solução 

Primeiro, confirme o computador designado para executar o recurso híbrido Runbook trabalhador atende aos requisitos mínimos de hardware.  Em caso afirmativo, monitore a utilização de CPU e memória para determinar qualquer correlação entre o desempenho de processos do operador de Runbook híbrido e Windows.  Se houver memória ou pressão da CPU, isso pode indicar a necessidade de atualizar ou adicionar processadores adicionais ou aumentar a memória para eliminar o gargalo de recurso e resolver o erro. Como alternativa, selecione um recurso de computação diferente que pode oferecer suporte a requisitos mínimos e a escala quando exigências de carga de trabalho indicam que um aumento é necessário.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: Runbooks não pode autenticar com recursos locais

### <a name="solution"></a>Solução

Verifique o log de eventos do **Microsoft SMA** para um evento correspondente com descrição *Win32 processo foi encerrado com código [4294967295]*.  A causa desse erro é ainda não configurado autenticação em seus runbooks ou especificou as credenciais executar como para o grupo de trabalho híbrido.  Examine [as permissões do Runbook](automation-hybrid-runbook-worker.md#runbook-permissions) para confirmar que você configurou corretamente autenticação para seus runbooks.  


 

