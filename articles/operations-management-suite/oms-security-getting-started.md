<properties
   pageTitle="Guia de Introdução com a solução de auditoria e segurança do pacote de gerenciamento de operações | Microsoft Azure"
   description="Este documento ajuda você a começar a usar a segurança do pacote de gerenciamento de operações e recursos de solução de auditoria para monitorar seu nuvem híbrida."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="get-started-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>
 
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Introdução aos segurança do pacote de gerenciamento de operações e solução de auditoria
Este documento ajuda você a começar rapidamente com recursos de solução de segurança de pacote (OMS) do gerenciamento de operações e auditoria, orientando você através de cada opção.

## <a name="what-is-oms"></a>O que é OMS?
Pacote de gerenciamento de operações da Microsoft (OMS) é na nuvem solução de gerenciamento da Microsoft que ajuda você a gerenciar e proteger seu local e infraestrutura de nuvem. Para obter mais informações sobre OMS, leia o artigo do [Pacote de gerenciamento de operações](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>Painel de segurança de OMS e auditoria

A solução de segurança de OMS e auditoria fornece uma visualização abrangente da sua organização postura de segurança IT com consultas de pesquisa interna notáveis problemas que exigem sua atenção. O painel de **segurança e auditoria** é a tela inicial para tudo relacionados à segurança no OMS. Ele fornece alto nível percepção o estado de segurança de seus computadores. Ele também inclui a capacidade de visualizar todos os eventos do últimas 24 horas, 7 dias, ou qualquer outro intervalo de tempo personalizado. Para acessar o painel de **segurança e auditoria** , siga estas etapas:

1. No painel principal do **Pacote de gerenciamento de operações do Microsoft** clique em bloco de **configurações** no canto esquerdo.
2. Na lâmina **configurações** , em **soluções** , clique em **segurança e auditoria** opção.
3. No painel de **segurança e auditoria** aparecerão:

    ![Painel de segurança de OMS e auditoria](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Se você estiver acessando esse painel pela primeira vez e você não tiver dispositivos monitorados pelo OMS, os blocos não serão preenchidos com dados obtidos do agente. Depois de instalar o agente, pode levar algum tempo para popular, portanto, o que você vê inicialmente pode estar ausente alguns dados conforme eles ainda estão carregando na nuvem.  Nesse caso, é normal ver alguns blocos sem informações tangíveis. Leia [computadores com Windows conectar diretamente para OMS](https://technet.microsoft.com/library/mt484108.aspx) para obter mais informações sobre como instalar o agente OMS em um sistema do Windows e [computadores Linux conectar OMS](https://technet.microsoft.com/library/mt622052.aspx) para obter mais informações sobre como executar esta tarefa em um sistema Linux.

> [AZURE.NOTE] O agente irá coletar as informações com base nos eventos atuais que estão habilitados, por exemplo, nome do computador, IP endereço e nome de usuário. No entanto nenhum documento/arquivos, nome do banco de dados ou dados particulares são coletados.   

As soluções são um conjunto de regras de aquisição de lógica, visualização e os dados que atendem principais desafios dos clientes. Segurança e auditoria é uma solução, outras pessoas podem ser adicionadas separadamente. Leia o artigo [Adicionar soluções](https://technet.microsoft.com/library/mt674635.aspx) para obter mais informações sobre como adicionar uma nova solução.

No painel de segurança de OMS e auditoria está organizado em quatro categorias principais:

- **Domínios de segurança**: nesta área, você poderá ainda mais explorar os registros de segurança ao longo do tempo, acessar avaliação de malware, atualize a avaliação, segurança de rede, informações de identidade e acesso, computadores com eventos de segurança e rapidamente têm acesso ao painel da Central de segurança do Azure.
- **Problemas de notáveis**: esta opção permitirá que você identifique rapidamente o número de questões ativas e a gravidade desses problemas.
- **Detecções (visualização)**: permite que você identifique padrões de ataque visualizando alertas de segurança conforme eles ocorrem em relação a seus recursos.
- **Inteligência de ameaças**: permite que você identifique padrões de ataque visualizando o número total de servidores com o tráfego de IP mal-intencionado saída, o tipo de ameaças e um mapa que mostra onde esses IPs são provenientes. 
- **Consultas de segurança comuns**: essa opção fornece uma lista das consultas segurança mais comuns que você pode usar para monitorar seu ambiente. Quando você clica em uma das consultas, ele abre a lâmina de **pesquisa** com os resultados para a consulta.

> [AZURE.NOTE] Para obter mais informações sobre como o OMS mantém seus dados seguros, leia que como OMS protege seus dados.

## <a name="security-domains"></a>Domínios de segurança

Quando o monitoramento de recursos, é importante poder acessar rapidamente o estado atual do seu ambiente. No entanto também é importante sejam capazes de controlar eventos de volta que ocorreu no passado que pode levar a um melhor entendimento do que está acontecendo em seu ambiente em determinado ponto no tempo. 

> [AZURE.NOTE] retenção de dados é de acordo com o OMS preços plano. Para obter mais informações, visite o [Pacote de gerenciamento de operações do Microsoft](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx) preços página.

Cenários de investigação incidentes de resposta e judiciais diretamente irá beneficiar os resultados disponíveis no bloco **Registros de segurança ao longo do tempo** .

![Registros de segurança ao longo do tempo](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Quando você clica neste bloco, a lâmina de **pesquisa** será aberta, exibindo um resultado de consulta para **Eventos de segurança** (tipo = SecurityEvents) com dados baseados nos últimos sete dias, conforme mostrado abaixo:

![Registros de segurança ao longo do tempo](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

O resultado de pesquisa é dividido em dois painéis: no painel esquerdo fornece uma análise do número de eventos de segurança que foram encontrados, os computadores em que esses eventos foram encontrados, o número de contas que foram descobertas nesses computadores e os tipos de atividades. O painel direito fornece os resultados totais e um modo de exibição cronológico dos eventos de segurança com a atividade de eventos e o nome do computador. Você também pode clicar em **Mostrar mais** para exibir mais detalhes sobre este evento, como os dados do evento, a identificação do evento e a origem do evento.

> [AZURE.NOTE] Para obter mais informações sobre a consulta de pesquisa OMS, leia [OMS pesquisar referência](https://technet.microsoft.com/library/mt450427.aspx).

### <a name="antimalware-assessment"></a>Avaliação de antimalware

Essa opção permite que você identifique rapidamente computadores com proteção insuficiente e computadores que são comprometidos por um pedaço de malware. Status de avaliação de malware e ameaças detectadas nos servidores monitoradas sejam lidas e, em seguida, os dados são enviados para o serviço OMS na nuvem para processamento. Servidores com detectado ameaças e servidores com proteção insuficiente são mostrados no painel de avaliação de malware, que pode ser acessado depois de clicar no bloco **Antimalware avaliação** . 

![avaliação de malware](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Assim como qualquer outro bloco ativo disponível no painel de OMS, quando você clica nele, a lâmina de **pesquisa** será aberto com o resultado da consulta. Para esta opção, se você clicar na opção **Não relatando** em **Status de proteção**, você terá o resultado da consulta que mostra este única entrada que contém o nome do computador e sua ordem, conforme mostrado abaixo:

![resultado da pesquisa](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [AZURE.NOTE] *classificação* é um grau concedendo para refletir o status da proteção (ativado, desativado, atualizado, etc) e ameaças que são encontradas. Tendo que como um número ajuda a tornar agregações.

Se você clicar no nome do computador, você terá a exibição cronológica do status de proteção para este computador. Isso é muito útil para cenários em que você precisa compreender se o antimalware foi instalado uma vez e em algum momento, que ele foi removido.   

### <a name="update-assessment"></a>Avaliação de atualização 

Essa opção permite que você determine rapidamente a exposição geral a possíveis problemas de segurança e se ou como críticas essas atualizações são para seu ambiente. Solução de auditoria e segurança OMS apenas fornecem a visualização dessas atualizações, os dados reais oriundos de [Soluções de atualizações do sistema](https://technet.microsoft.com/library/mt484096.aspx), que é um módulo diferente dentro OMS. Veja um exemplo das atualizações:

![atualizações do sistema](./media/oms-security-getting-started/oms-getting-started-fig6.png)

> [AZURE.NOTE] Para obter mais informações sobre solução de atualizações, leia [atualizar servidores com a solução de atualizações do sistema](https://technet.microsoft.com/library/mt484096.aspx).

### <a name="identity-and-access"></a>Identidade e acesso

Identidade deve ser o plano de controle para sua empresa, protegendo sua identidade deve ser sua prioridade. Enquanto no passado havia perímetro ao redor de organizações e essas perímetro foram dentre os limites de defesa primários, atualmente com mais dados e mais aplicativos mudar para a nuvem a identidade se torna o perímetro novo. 

> [AZURE.NOTE] Atualmente, os dados baseados apenas nos dados de login de eventos de segurança (evento 4624 ID) no logon do Office 365 futura e dados do Azure AD também serão incluídos.

Monitorando suas atividades de identidade será capaz de executar ações proativas antes de um incidente leva local ou ações reativas para interromper uma tentativa de ataque. No painel de **identidade e acesso** fornece uma visão geral do seu estado de identidade, incluindo o número de tentativas para fazer logon, a conta do usuário que foram usados durante essas tentativas, contas que foram bloqueadas, contas com alterado ou redefinir a senha e atualmente número de contas que estiver conectado. 

Quando você clica no bloco **identidades e acesso** você verá no painel a seguir:

![identidade e acesso](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

As informações disponíveis neste painel imediatamente podem ajudá-lo para identificar uma atividade suspeita possível. Por exemplo, existem 338 tentativas de fazer logon como **administrador** e 100% dessas tentativas falha. Isso pode ser causado por um ataque de força bruta contra essa conta. Se você clicar nesta conta que você vai obter mais informações que podem ajudar a determinar o recurso de destino para esse ataque potencial:

![resultados da pesquisa](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

O relatório detalhado fornece informações importantes sobre este evento, incluindo: o computador de destino, o tipo de logon (nesse caso logon de rede), a atividade (este evento caso 4625) e um cronograma abrangente de cada tentativa. 

### <a name="computers"></a>Computadores

Neste bloco pode ser usado para acessar todos os computadores que possuem ativamente eventos de segurança. Quando você clica neste bloco, você verá a lista de computadores com eventos de segurança e o número de eventos em cada computador:

![Computadores](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

Você pode continuar sua investigação clicando em cada computador e revisar os eventos de segurança que foram sinalizados.

### <a name="azure-security-center"></a>O Centro de segurança do Azure

Neste bloco é basicamente um atalho para acessar o painel da Central de segurança do Azure. Leia o [guia de Introdução do Centro de segurança do Azure](../security-center/security-center-get-started.md) para obter mais informações sobre essa solução.

## <a name="notable-issues"></a>Problemas de notáveis

Sua intenção principal desse grupo de opções é fornecer uma visão geral dos problemas que você tem no seu ambiente, categorizando-los no crítico, aviso e informativo. O bloco de tipo de questão ativa é uma visualização desses problemas, mas não permite que você para explorar mais detalhes sobre eles, para que você precisa usar a parte inferior neste bloco que tem o nome do problema (nome), quantos objetos tinha isso acontecer (contagem) e como é essencial (GRAVIDADE).

![Problemas de notáveis](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Você pode ver que esses problemas já foram abordados em diferentes áreas de grupo de **Domínios de segurança** , que reforça a intenção deste modo de exibição: visualizar os problemas mais importantes no seu ambiente de um único local.

## <a name="detections-preview"></a>Detecções (prévia)

Sua intenção principal dessa opção é permitir IT para identificar rapidamente ameaças potenciais para seu ambiente via e a gravidade deste ameaças.

![Ameaças Intel](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Esta opção também pode ser usada durante uma investigação de resposta a incidente para realizar a avaliação e obter mais informações sobre o ataque.

> [AZURE.NOTE] Para obter mais informações sobre como usar o OMS para resposta de incidente, assista [como aproveitar o Centro de segurança do Azure & Microsoft operações Management Suite para uma resposta de incidente](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703).

## <a name="threat-intelligence"></a>Inteligência de ameaças

A nova seção de inteligência de ameaças da solução segurança e auditoria visualiza os padrões de ataque possíveis de várias maneiras: o número total de servidores com o tráfego de IP mal-intencionados saída, o tipo de ameaças e um mapa que mostra onde esses IPs são provenientes. Você pode interagir com o mapa e clique em IPs para obter mais informações.

Pinos amarelos no mapa indicam tráfego de entrada de IPs mal-intencionados. Não é incomum para servidores que estão expostos na internet para ver o tráfego mal-intencionado, mas recomendamos revisar essas tentativas para certificar-se de que nenhuma delas foi bem-sucedido. Esses indicadores são baseados em logs do IIS WireData e logs de Firewall do Windows.  

![Ameaças Intel](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Consultas de segurança comuns

Lista de consultas de segurança comuns disponíveis pode ser útil para acessar rapidamente as informações de recurso e personalizá-lo com base nas necessidades do seu ambiente. Estas consultas comuns são:

- Todas as atividades de segurança
- Atividades de segurança no computador "computer01.contoso.com" (substituir com seu próprio nome de computador)
- Atividades de segurança no computador "computer01.contoso.com" conta "Administrador" (substituir com seus próprios nomes de computador e conta)
- Faça logon no atividade por computador
- Contas quem terminadas antimalware da Microsoft em qualquer computador
- Computadores onde o processo de antimalware do Microsoft foi encerrado
- Computadores onde "hash.exe" foi executado (substituir com nome de processo diferente)
- Todos os nomes de processo que foram executados
- Faça logon no atividade por conta
- Contas que remotamente conectado no computador "computer01.contoso.com" (substituir com seu próprio nome de computador)

## <a name="see-also"></a>Consulte também

Neste documento, você introduzidas solução OMS segurança e auditoria. Para saber mais sobre a segurança do OMS, consulte os seguintes artigos:

- [Visão geral do pacote de gerenciamento (OMS) operações](operations-management-suite-overview.md)
- [Monitoramento e responder a alertas de segurança na segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-responding-alerts.md)
- [O monitoramento de recursos de segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-monitoring-resources.md)
