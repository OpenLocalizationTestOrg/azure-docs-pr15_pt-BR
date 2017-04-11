<properties
 pageTitle="Solucionar problemas de implantação de serviço de nuvem | Microsoft Azure"
 description="Há alguns problemas comuns que pode ocorrer ao implantar um serviço de nuvem do Azure. Este artigo fornece soluções para algumas delas."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-deployment-problems"></a>Solucionar problemas de implantação de serviço de nuvem

Quando você implanta um pacote de aplicativo de serviço de nuvem para o Azure, você pode obter informações sobre a implantação do painel de **Propriedades** no portal do Azure. Você pode usar os detalhes nesse painel para ajudá-lo a solucionar problemas com o serviço de nuvem, e você pode fornecer essas informações para dar suporte a Azure ao abrir uma nova solicitação de suporte.

Você pode encontrar o painel de **Propriedades** da seguinte maneira:

* No portal do Azure, clique a implantação do seu serviço de nuvem, clique em **todas as configurações**e, em seguida, clique em **Propriedades**.
* No portal de clássico do Azure, a implantação do seu serviço de nuvem, clique em **Painel de controle**, localizado no canto inferior direito da página (em **rapidamente**). Lembre-se de que não há nenhum rótulo "Propriedades" no painel.

> [AZURE.NOTE] Você pode copiar o conteúdo do painel de **Propriedades** na área de transferência clicando no ícone no canto superior direito do painel.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: não é possível acessar o meu site, mas minha implantação é iniciada e todas as instâncias de função estão prontas

O link de URL do site mostrado no portal não inclui a porta. A porta padrão para sites é 80. Se seu aplicativo está configurado para executar em uma porta diferente, você deve adicionar o número da porta correta para a URL ao acessar o site.

1. No portal do Azure, clique na implantação do seu serviço de nuvem.
2. No painel **Propriedades** do portal do Azure, verifique as portas para as instâncias de função (em **Pontos de extremidade de entrada**).
3. Se a porta não for 80, adicione o valor de porta correta para a URL quando você acessar o aplicativo. Para especificar uma porta não padrão, digite a URL, seguida por dois-pontos (:), seguido do número de porta, sem espaços.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: Minha instâncias de função reciclagem sem me fazer nada

Serviço de reparo ocorre automaticamente quando Azure detecta nós de problema e, portanto, move instâncias de função para novos nós. Quando isso acontecer, você poderá ver suas instâncias de função reciclagem automaticamente. Para descobrir se o reparo de serviço ocorreu:

1. No portal do Azure, clique na implantação do seu serviço de nuvem.
2. No painel **Propriedades** do portal do Azure, examine as informações e determinar se o reparo de serviço ocorreu durante o tempo que você observou as funções reciclagem.

Funções também serão Lixeira aproximadamente uma vez por mês durante atualizações de SO de convidado e host-OS.  
Para obter mais informações, consulte o postagem de blog [Função instância reiniciado devido à atualizações de sistema operacional](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: não é possível fazer uma troca de VIP e receber um erro

Uma troca de VIP não é permitida se uma atualização de implantação estiver em andamento. Atualizações de implantação podem ocorrer automaticamente quando:

* Um novo sistema operacional convidado está disponível e você está configurado para atualizações automáticas.
* Serviço de reparo ocorre.

Para descobrir se uma atualização automática está impedindo você fazendo uma troca de VIP:

1. No portal do Azure, clique na implantação do seu serviço de nuvem.
2. No painel **Propriedades** do portal do Azure, examine o valor de **Status**. Se estiver **pronto**, verifique a **última operação** para ver se um aconteceu recentemente que podem impedir a troca de VIP.
3. Repita as etapas 1 e 2 para a implantação de produção.
4. Se uma atualização automática estiver em andamento, aguarde termine antes de tentar fazer a troca de VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Uma instância da função é loop entre iniciado, ao inicializar, ocupado e interrompido

Esta condição pode indicar um problema com o seu arquivo de código, pacote ou configuração do aplicativo. Nesse caso, você deve ser capaz de ver o status alterando a cada poucos minutos e o portal do Azure pode dizer algo como **Reciclagem**, **ocupado**ou **ao inicializar**. Isso indica que não há algo errado com o aplicativo que está mantendo a instância da função execução.

Para obter mais informações sobre como solucionar esse problema, consulte o blog postar [Dados de diagnóstico de computação do Azure PaaS](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) e [problemas comuns que causam funções a Lixeira](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problema: O meu aplicativo parou de funcionar

1. No portal do Azure, clique na instância de função.
2. No painel **Propriedades** do portal do Azure, considere as seguintes condições para resolver o problema:
   * Se a instância de função recentemente parou (você pode verificar o valor de **Anular contagem**), a implantação pode estar atualizando. Aguarde para ver se a instância da função continuará funcionando por conta própria.
   * Se a instância de função for **ocupado**, verifique o código do seu aplicativo para ver se o evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) é processado. Talvez seja necessário adicionar ou corrigir algum código que manipula esse evento.
   * Percorra os dados de diagnósticos e cenários de solução de problemas na postagem do blog [Calcular dados de diagnóstico de PaaS Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

>[AZURE.WARNING] Se você Lixeira seu serviço de nuvem, você redefinir as propriedades para a implantação, apagando efetivamente as informações para o problema original.

## <a name="next-steps"></a>Próximas etapas

Ler mais [artigos de solução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços de nuvem.

Para saber como solucionar problemas de função de serviço de nuvem usando dados de diagnóstico do Azure PaaS computador, consulte [série de blog de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
