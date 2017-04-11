<properties
   pageTitle="Teste de caneta | Microsoft Azure"
   description="O artigo fornece uma visão geral da processo de (teste) de testes de penetração e como executar teste contra seus aplicativos em execução no Azure infraestrutura."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="pen-testing"></a>Teste de caneta

Uma das excelentes coisas sobre como usar o Microsoft Azure de teste de aplicativo e implantação é que você não precisa reunir uma infraestrutura de locais para desenvolver, testar e implantar seus aplicativos. Todos os a infraestrutura é resolvida pelos serviços de plataforma do Microsoft Azure. Você não precisa se preocupar requisição, aquisição e "posicionamento no rack e empilhamento" seu próprio hardware local.

Isso é ótimo –, mas você ainda precisa certificar-se de que você executar a segurança normal auditoria detalhada. Uma das coisas que você precisa fazer é penetração testar os aplicativos que você implanta no Azure.

Você já deve saber que a Microsoft realiza [testes de penetração de nosso ambiente Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Isso ajuda a melhorar a nossa plataforma e orienta nossas ações em termos de melhorar os controles de segurança, introduzindo novos controles de segurança e aprimorar nossos processos de segurança.

Podemos não caneta testar seu aplicativo para você, mas entendemos que você desejará e precisa executar testes em seus próprios aplicativos de caneta. Que é uma boa coisa, porque quando você aumentar a segurança de seus aplicativos, você ajuda a tornar a todo o ecossistema Azure mais segura.

Quando você caneta testar seus aplicativos, ele pode parecer com um ataque conosco. Estamos [continuamente monitorar](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) atacar padrões e iniciará um processo de resposta a incidente se for necessário. Ele não ajudá-lo e ele não ajude-nos se podemos disparar uma resposta incidente devido a seu próprio conclusão caneta de auditoria teste.

O que fazer?

Quando você estiver pronto para caneta teste seus aplicativos hospedados Azure, é necessário para nos informar. Depois que nós sabemos que você vai executar testes específicos, nós não inadvertidamente desligar (como bloqueando o endereço IP que você está testando do), desde que os testes estão em conformidade com o Azure caneta teste termos e condições.
Você pode executar os testes padrão incluem:

- Testes em seus pontos de extremidade para descobrir as [10 vulnerabilidades de projeto de segurança de aplicativo de Web aberta (OWASP) superior](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [Teste de difusão](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) seus pontos de extremidade
- A [verificação de portas](https://en.wikipedia.org/wiki/Port_scanner) seus pontos de extremidade

Um tipo de teste que você não pode executar é qualquer tipo de ataque de [Negação de serviço (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Isso inclui iniciando um ataque em si, ou executar testes relacionados que podem determinar, demonstram ou simular qualquer tipo de ataque DoS.

Pronto para começar com a caneta que está testando seus aplicativos hospedados no Microsoft Azure? Se afirmativo, vá diretamente para a [Visão geral de teste de penetração](https://security-forms.azure.com/penetration-testing/terms) da página (e clique no botão Criar uma solicitação de teste na parte inferior da página. Você encontrará também obter mais informações sobre a caneta testes termos e condições e links úteis sobre como você pode relatar falhas de segurança relacionadas ao Azure ou qualquer outro serviço da Microsoft.
