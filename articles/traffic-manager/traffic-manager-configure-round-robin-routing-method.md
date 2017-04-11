<properties
   pageTitle="Configurar o Gerenciador de tráfego round robin método de roteamento de tráfego | Microsoft Azure"
   description="Este artigo ajudará você a configurar round robin balanceamento de carga para seus pontos de extremidade do Gerenciador de tráfego."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-round-robin-routing-method"></a>Método de roteamento de configurar Round Robin

Um padrão de comum método de roteamento do tráfego é fornecer um conjunto de pontos de extremidade idênticos, que incluem serviços de nuvem e sites, e enviar tráfego para cada de forma alternada. As etapas a seguir descrevem como configurar o Gerenciador de tráfego para realizar este tipo de método de roteamento de tráfego. Para obter mais informações sobre os métodos de roteamento de tráfego diferente, consulte [sobre o Gerenciador de tráfego métodos de roteamento de tráfego](traffic-manager-routing-methods.md).

>[AZURE.NOTE] Sites Azure já fornecem funcionalidade para sites em um data center (também conhecido como uma região) de balanceamento de carga de round robin. Gerenciador de tráfego permite especificar o método de roteamento de tráfego alternada para sites em diferentes dos data centers.

## <a name="routing-traffic-equally-round-robin-across-a-set-of-endpoints"></a>Em um conjunto de pontos de extremidade de roteamento tráfego igualmente (round robin):

1. No portal do clássico Azure, no painel esquerdo, clique no ícone do **Gerenciador de tráfego** para abrir o painel Gerenciador de tráfego. Se você ainda não tiver criado o perfil do Gerenciador de tráfego, consulte [Gerenciar perfis do Gerenciador de tráfego](traffic-manager-manage-profiles.md) para as etapas para criar um perfil de Gerenciador de tráfego básico.
2. No portal do clássico Azure, no painel Gerenciador de tráfego, localize o perfil do Gerenciador de tráfego que contém as configurações que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
3. Na página para seu perfil, clique em **pontos de extremidade** na parte superior da página e verificar se os pontos de extremidade do serviço que você deseja incluir em sua configuração estão presentes. Para obter etapas adicionar ou remover pontos de extremidade, consulte [Gerenciar pontos de extremidade no Gerenciador de tráfego](traffic-manager-endpoints.md).
4. Na sua página de perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. **Método de roteamento de tráfego configurações**, verifique se o método de roteamento de tráfego **Round Robin**. Se não estiver, clique em **repetição alternada** na lista suspensa.
6. Verificar se as **Configurações de monitoramento** estão configurados adequadamente. Monitoramento garante que os pontos de extremidade que estiverem offline não são enviados tráfego. Para monitorar os pontos de extremidade, especifique um caminho e nome do arquivo. Observe que uma barra "/" é uma entrada válida para o caminho relativo e indica que o arquivo está no diretório raiz (padrão). Para obter mais informações sobre o monitoring, consulte [Sobre o Gerenciador de tráfego monitoramento](traffic-manager-monitoring.md).
7. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
8. Teste as alterações em sua configuração. Para obter mais informações, consulte [Configurações do Gerenciador de tráfego de teste](traffic-manager-testing-settings.md).
9. Depois que o perfil do Gerenciador de tráfego estiver configuração e trabalhando, edite o registro DNS no seu servidor DNS autoritativo para apontar o seu nome de domínio de empresa para o nome de domínio do Gerenciador de tráfego. Para obter mais informações sobre como fazer isso, consulte [ponto um domínio de Internet da empresa para um domínio do Gerenciador de tráfego](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Próximas etapas


[Aponte o domínio da Internet uma empresa para um domínio do Gerenciador de tráfego](traffic-manager-point-internet-domain.md)

[Métodos de roteamento de tráfego Manager](traffic-manager-routing-methods.md)

[Configurar o método de roteamento de failover](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)

[Gerenciador de-desabilitar, habilitar o tráfego ou excluir um perfil](disable-enable-or-delete-a-profile.md)

[Gerenciador de tráfego - desativar ou ativar um ponto extremo](disable-or-enable-an-endpoint.md)

