<properties
   pageTitle="Segurança de dados do Centro de segurança do Azure | Microsoft Azure"
   description="Este documento explica como os dados são gerenciados e protegidos na Central de segurança do Azure."
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
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-data-security"></a>Segurança de dados do Centro de segurança do Azure
Para ajudar os clientes impedir, detectar e responder a ameaças, o Centro de segurança do Azure coleta e processa dados sobre os recursos do Azure, incluindo informações de configuração, metadados, logs de eventos, falhar arquivos de despejo e muito mais. Podemos fazer compromissos fortes para proteger a privacidade e segurança de dados. Microsoft adere às diretrizes estritas de segurança e conformidade — da codificação à operação de um serviço. 

Este artigo explica como os dados são gerenciados e protegidos na Central de segurança do Azure.

## <a name="data-sources"></a>Fontes de dados

O Centro de segurança do Azure analisa dados das seguintes fontes:

- Serviços do Azure: Lê informações sobre a configuração dos serviços Azure você implantou comunicando-se com o provedor de recursos do serviço.
- Tráfego de rede: Leituras amostradas metadados de tráfego da infraestrutura da Microsoft, como origem/destino IP/porta, tamanho de pacote de rede e protocolo de rede.
- Soluções de parceiros: Coleta alertas de segurança de soluções de parceiros integradas, como firewalls e soluções de antimalware. Esses dados são armazenados em armazenamento de Central de segurança do Azure, localizado atualmente nos Estados Unidos.
- Suas máquinas virtuais: O Centro de segurança do Azure pode coletar informações de configuração e informações sobre eventos de segurança, como eventos do Windows e logs, logs do IIS, mensagens syslog e arquivos de despejo de falha de suas máquinas virtuais usando agentes de conjunto de dados de auditoria. Consulte a seção "Gerenciando a coleta de dados" abaixo para obter detalhes adicionais.  

Além disso, informações sobre alertas de segurança, recomendações e status de integridade de segurança são armazenadas no armazenamento do Centro de segurança do Azure, localizado atualmente nos Estados Unidos. Essas informações podem incluir informações de configuração relacionados e eventos de segurança coletados de suas máquinas virtuais conforme necessário para fornecer o alerta de segurança, recomendação ou status de integridade de segurança.

## <a name="data-protection"></a>Proteção de dados

**Diferenciação de dados**: dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados marcado por organização. Essa marcação persiste em todo o ciclo de vida de dados e ela é aplicada em cada camada do serviço. Além disso, os dados coletados de suas máquinas virtuais são armazenados em suas contas de armazenamento.

**Acesso a dados**: para fornecer recomendações de segurança e investigar possíveis ameaças de segurança, pessoal da Microsoft pode acessar informações coletadas ou analisados pelos serviços do Azure, incluindo arquivos de despejo de falha. Arquivos de despejo de falha e eventos de criação de processo acidentalmente podem incluir dados do cliente ou dados pessoais de suas máquinas virtuais. Podemos cumprir os [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Declaração de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), qual estado que Microsoft usarão não dados do cliente ou gerar informações dele finalidades comerciais publicidade ou semelhantes. Somente usamos dados do cliente conforme necessário para fornecer serviços Azure, incluindo fins compatíveis com fornecendo desses serviços. Manter todos os direitos de dados do cliente.

**Uso dos dados**: a Microsoft usa inteligência de ameaças vistos em vários locatários e padrões para aprimorar nossos recursos de detecção e prevenção; podemos fazê-lo de acordo com os compromissos de privacidade descritos na nossa [Política de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

**Local de dados**: uma conta de armazenamento está especificada para cada região onde máquinas virtuais estão funcionando. Isso permite armazenar dados na mesma região como a máquina virtual do qual os dados são coletados. Esses dados, incluindo arquivos de despejo de falha, serão armazenados constantemente na sua conta de armazenamento. O serviço também armazena informações sobre alertas de segurança, incluindo alertas de soluções de parceiros integradas, recomendações e status de integridade de segurança no armazenamento do Centro de segurança do Azure, localizado atualmente nos Estados Unidos.

## <a name="managing-data-collection-from-virtual-machines"></a>Gerenciamento do conjunto de dados de máquinas virtuais

Quando você optar por habilitar o Centro de segurança do Azure, a coleta de dados está ativada para cada uma das suas assinaturas. Você pode desativar a coleta de dados na seção "Política de segurança" do painel do Centro de segurança do Azure. Quando a coleta de dados está ativada, o Centro de segurança do Azure disposições o agente de monitoramento do Azure em todos os existentes com suporte máquinas virtuais e as novas que são criadas. A extensão de monitoramento de segurança do Azure procura relacionados à segurança de várias configurações e eventos em [Rastreamento de eventos do Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) rastreia. Além disso, o sistema operacional aumentará o log de eventos no decorrer da execução da máquina. Exemplos desses dados são: tipo de sistema operacional e versão, operacional logs do sistema (logs de eventos do Windows), que executam processos, nome da máquina, endereços IP, registrados em usuário e ID de locatário. O agente de monitoramento do Azure lê entradas de log de eventos e ETW rastreia e copiá-los à sua conta de armazenamento para análise. 

Uma conta de armazenamento é especificada para cada região no qual você tem máquinas virtuais executando, onde os dados coletados de máquinas virtuais em que a mesma região está armazenada. Isso facilita a manter dados na mesma área geográfica para fins de Soberania de dados e privacidade. Você pode configurar contas de armazenamento para cada região na seção "Política de segurança" do painel do Centro de segurança do Azure.

O agente de monitoramento do Azure também copia arquivos de despejo de falha para sua conta de armazenamento.  O Centro de segurança do Azure coleta efêmeras cópias de seus arquivos de despejo de falha e analisa evidências de tentativas de exploração e compromete bem-sucedido.  O Centro de segurança do Azure executa essa análise dentro da mesma região geográfica como a conta de armazenamento e exclui as cópias efêmeras quando análise for concluída.

Você pode desabilitar a coleta de dados de máquinas virtuais a qualquer momento, o que removerá quaisquer agentes de monitoramento instalado anteriormente pela Central de segurança do Azure.


## <a name="see-also"></a>Consulte também

Neste documento, você aprendeu como os dados são gerenciados e protegidos na Central de segurança do Azure. Para saber mais sobre o Centro de segurança do Azure, consulte:

- [Guia de operações e planejamento de centro de segurança do Azure](security-center-planning-and-operations-guide.md) — aprender a planejar e compreenda as considerações de design para adotar o Centro de segurança do Azure.
- [Monitoramento na Central de segurança do Azure de integridade de segurança](security-center-monitoring.md) — Aprenda a monitorar a integridade dos seus recursos Azure
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerenciar e responder a alertas de segurança
- [Monitoramento soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Aprenda a monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — localizar postagens no blog sobre segurança do Azure e conformidade
