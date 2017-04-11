<properties
   pageTitle="Práticas recomendadas para atualizações de Software no Microsoft Azure IaaS | Microsoft Azure"
   description="Artigo fornece um conjunto de práticas recomendadas para atualizações de software em um ambiente do Microsoft Azure IaaS.  Destina-se para profissionais de TI e analistas de segurança que lidam com alterar controle, gerenciamento de ativos e de atualização de software diariamente, inclusive aqueles responsável por esforços de segurança e conformidade da sua organização."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Práticas recomendadas para atualizações de software no Microsoft Azure IaaS

Antes de mergulhar qualquer tipo de discussão em práticas recomendadas para um ambiente do Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) , é importante entender quais são os cenários que terá você Gerenciando atualizações de software e as responsabilidades. O diagrama abaixo deverá ajudá-lo a compreender esses limites:

![Modelos de nuvem e responsabilities](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

A coluna mais à esquerda mostra sete responsabilidades (definidas nas seções a seguir) que considere as organizações que contribuem com a segurança e privacidade de um ambiente de computação.
 
Classificação de dados e responsabilidade e proteção de ponto de extremidade da & cliente são as responsabilidades que estão somente no domínio de clientes e responsabilidades físico, Host e rede estão no domínio dos provedores de serviço de nuvem nos modelos de PaaS e o SaaS. 

As responsabilidades restantes são compartilhadas entre clientes e provedores de serviços de nuvem. Algumas responsabilidades exigem o CSP e o cliente gerenciar e administrar a responsabilidade juntas, inclusive auditoria de seus domínios. Por exemplo, considere a possibilidade de identidade e gerenciamento de acesso ao usar os serviços do Azure Active Directory; a configuração de serviços como autenticação multifator é até o cliente, mas garantir efetiva funcionalidade é responsabilidade do Microsoft Azure.

> [AZURE.NOTE] Para obter mais informações sobre as responsabilidades compartilhadas na nuvem, leia [Responsabilidades compartilhada para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) 

Aplicam esses mesmos princípios em um cenário de híbrido onde sua empresa estiver usando o Azure IaaS VMs que se comunicar com os recursos de local conforme mostrado no diagrama abaixo.

![Cenário de típico híbrida com o Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>Avaliação inicial

Mesmo se sua empresa já estiver usando um sistema de gerenciamento de atualização e você já tiver diretivas de atualização de software no lugar, é importante frequentemente reveja avaliações anteriores de política e atualizá-los com base nas necessidades atuais. Isso significa que você precisa estar familiarizado com o estado atual dos recursos em sua empresa. Para chegar a esse estado, você precisa saber:

-   Os computadores físicos e virtuais de sua empresa.

-   Sistemas operacionais e versões executados em cada um desses computadores físicos e virtuais.

-   Atualizações de software atualmente instaladas em cada computador (versões de service pack, atualizações de software e outras modificações).

-   A função cada computador executa em sua empresa.

-   Os aplicativos e programas em execução em cada computador.

-   A propriedade e informações de contato para cada computador.

-   Apresentam os ativos no seu ambiente e seu valor relativo para determinar quais áreas precisam de mais atenção e proteção.

-   Problemas conhecidos de segurança e os processos de sua empresa tem no lugar de identificação novos problemas de segurança ou alterações no nível de segurança.

-   Contramedidas que foram implantadas para proteger o seu ambiente.

Você deve atualizar essas informações regularmente e ele deve estar disponível para envolvidos no seu processo de gerenciamento de atualização de software.

## <a name="establish-a-baseline"></a>Estabelecer uma linha de base

Uma parte importante do processo de gerenciamento de atualização de software está criando instalações padrão iniciais das versões de sistema operacional, aplicativos e hardware para computadores de sua empresa; Estes são chamados linhas de base. Uma linha de base é a configuração de um produto ou sistema estabelecido em um ponto específico no tempo. Um aplicativo ou linha de base do sistema operacional, por exemplo, fornece a capacidade de recriar um computador ou serviço para um estado específico.

Linhas de base fornecem a base para encontrar e corrigir problemas em potencial e simplificam o processo de gerenciamento de atualização de software, reduzindo o número de atualizações de software que você deve implantar em sua empresa e aumentando a capacidade de monitorar a conformidade.

Depois de executar a auditoria inicial de sua empresa, você deve usar as informações obtidas de auditoria para definir uma linha de base operacional para os componentes de TI dentro de seu ambiente de produção. Um número de linhas de base pode ser necessário, dependendo dos diferentes tipos de hardware e software implantado em produção.

Por exemplo, alguns servidores exigem uma atualização de software para impedir que deslocamento quando inserirem o processo de desligamento quando executando o Windows Server 2012. Uma linha de base para esses servidores deve incluir essa atualização de software.

Em grandes organizações, muitas vezes é útil dividir os computadores de sua empresa em categorias de ativo e manter cada categoria em uma linha de base padrão, usando as mesmas versões de software e atualizações de software. Você pode usar essas categorias ativo priorizar uma distribuição de atualização de software.

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>Assinar os serviços de notificação de atualização de software apropriado

Depois de executar uma auditoria inicial do software em uso na sua empresa, você deve determinar o melhor método para receber notificações de novas atualizações de software para cada produto de software e versão. Dependendo do produto de software, o melhor método de notificação será notificações por email, sites da Web ou publicações de computador.

Por exemplo, o Centro de resposta de segurança da Microsoft (MSRC) responde para todas as questões de segurança sobre produtos da Microsoft e fornece o serviço de boletim de segurança do Microsoft, uma notificação de email gratuito de vulnerabilidades recentemente identificadas e atualizações de software lançadas para lidar com essas vulnerabilidades. Você pode assinar este serviço em http://www.microsoft.com/technet/security/bulletin/notify.mspx.

## <a name="software-update-considerations"></a>Considerações de atualização de software

Depois de executar uma auditoria inicial do software em uso na sua empresa, você deve determinar os requisitos para configurar seu sistema de gerenciamento de atualização de software, que depende do sistema de gerenciamento de atualização de software que você está usando. Para WSUS ler [Práticas recomendadas com serviços de atualização do Windows Server](https://technet.microsoft.com/library/Cc708536), para System Center, leia [planejamento para atualizações de Software no Gerenciador de configuração](https://technet.microsoft.com/library/gg712696).

No entanto, há algumas considerações gerais e práticas recomendadas que você pode aplicar, independentemente da solução que você está usando, conforme mostrado nas seções a seguir.

### <a name="setting-up-the-environment"></a>Configurando o ambiente

Considere as seguintes práticas durante o planejamento para configurar o software ambiente de gerenciamento de atualização:

-   **Criar conjuntos de atualização de software de produção com base em critérios estáveis**: em geral, usando critérios estáveis para criar conjuntos de seu inventário de atualização de software e distribuição ajudará a simplificar todas as etapas do processo de gerenciamento de atualização de software. Os critérios estáveis podem incluir a versão do sistema operacional de cliente instalado e nível do service pack, a função do sistema ou organização de destino.

-   **Criar conjuntos de pré-produção que incluem computadores de referência**: A coleção de pré-produção deve incluir configurações representantes de versões do sistema operacional, linha de software de negócios e outros softwares em execução em sua empresa.

Você também deve considerar onde o servidor de atualização de software ficarão localizado, se ele estará na infraestrutura do Azure IaaS na nuvem ou se ele será local. Isso é uma decisão importante porque você precisa avaliar a quantidade de tráfego entre recursos locais e infraestrutura Azure. Leia [conectar uma rede local para uma rede virtual do Windows Azure](https://technet.microsoft.com/library/Dn786406.aspx) para obter mais informações sobre como conectar sua infraestrutura de locais no Azure.

As opções de design que determinam onde o servidor de atualização ficarão localizado também variam de acordo com sua infraestrutura atual e o sistema de atualização de software que você está usando no momento. Para WSUS leia [Implantar o Windows Server Update Services em sua organização](https://technet.microsoft.com/library/hh852340.aspx) e para o Gerenciador de configuração do System Center leia [planejamento de Sites e hierarquias no Gerenciador de configuração](https://technet.microsoft.com/library/Gg712681.aspx).

### <a name="backup"></a>Backup

Backups regulares são importantes não apenas para a atualização gerenciamento plataforma de software em si, mas também para os servidores que serão atualizados. Organizações com um [processo de gerenciamento de mudanças](https://technet.microsoft.com/library/cc543216.aspx) no lugar exigirão IT para justificar os motivos por que o servidor precisa ser atualizado, o tempo de inatividade estimado e o impacto possível. Para garantir que você tenha uma reversão configuração no lugar em caso de falha de uma atualização, certifique-se de fazer backup do sistema regularmente.

Algumas opções de backup do Azure IaaS incluem:

-   [Proteção de carga de trabalho IaaS Azure usando o Gerenciador de proteção de dados](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Fazer backup Azure máquinas virtuais](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>Monitoramento

Você deve executar relatórios regulares para monitorar o número de ausente ou instalado atualizações ou atualizações com status incompletos, para cada atualização de software que está autorizado. Da mesma forma, relatar atualizações de software que ainda não autorizado pode facilitar decisões mais fáceis de implantação.

Você também deve considerar as seguintes tarefas:

-   Conduza uma auditoria das atualizações de segurança aplicáveis e instaladas para todos os computadores em sua empresa.

-   Autorize e implantar as atualizações nos computadores adequados.

-   Controlar o estoque e atualizar status da instalação e o andamento de todos os computadores em sua empresa.

Além disso, em gerais considerações que foram explicados neste artigo, você também deve considerar cada produto melhor do praticar, por exemplo: se você tiver uma máquina virtual no Azure com o SQL Server, certifique-se de que você está seguindo a recomendação de atualizações de software para o produto.

## <a name="next-steps"></a>Próximas etapas

Use as diretrizes descritas neste artigo para ajudá-lo a determinar as melhores opções para atualizações de software para máquinas virtuais dentro do Azure IaaS. Existem muitas semelhanças entre práticas recomendadas de atualização de software em um data center tradicional versus Azure IaaS, portanto é recomendável avaliar suas políticas de atualização de software atual para incluir VMs do Azure e incluir as práticas recomendadas relevantes deste artigo em seu processo de atualização de software geral.
