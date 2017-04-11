<properties
   pageTitle="Configurar o método de roteamento de tráfego de desempenho | Microsoft Azure"
   description="Este artigo ajudará você a configurar o método de roteamento de tráfego de desempenho no Gerenciador de tráfego"
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

# <a name="configure-performance-traffic-routing-method"></a>Configurar o método de roteamento de tráfego de desempenho

Para rotear o tráfego para serviços de nuvem e sites (pontos finais) que estão localizadas em diferentes dos data centers em todo o mundo (também conhecido como regiões), você pode direcionar o tráfego para o ponto de extremidade com a latência mais baixa do cliente solicitando. Normalmente, o data center com o menor latência corresponde a mais próximo na distância geográfica. O método de roteamento de tráfego de desempenho permitirá a distribuição com base em menor latência, mas não pode levar em alterações em tempo real de conta na configuração de rede ou carregar. Para obter mais informações sobre os métodos de roteamento de tráfego diferente que fornece Gerenciador de tráfego do Azure, consulte [métodos de roteamento de tráfego de sobre o Gerenciador de tráfego](traffic-manager-routing-methods.md).

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>Rotear o tráfego com base em menor latência em um conjunto de pontos de extremidade:

1. No portal do clássico Azure, no painel esquerdo, clique no ícone do **Gerenciador de tráfego** para abrir o painel Gerenciador de tráfego. Se você ainda não tiver criado o perfil do Gerenciador de tráfego, consulte [Gerenciar perfis do Gerenciador de tráfego](traffic-manager-manage-profiles.md) para ver as etapas para criar um perfil de Gerenciador de tráfego básico.
2. No portal do clássico Azure, no painel Gerenciador de tráfego, localize o perfil do Gerenciador de tráfego que contém as configurações que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
3. Na página para seu perfil, clique em **pontos de extremidade** na parte superior da página e verificar se os pontos de extremidade do serviço que você deseja incluir em sua configuração estão presentes. Para obter etapas adicionar ou remover pontos de extremidade do seu perfil, consulte [Gerenciar pontos de extremidade no Gerenciador de tráfego](traffic-manager-endpoints.md).
4. Na página para seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. **Configurações de método de roteamento de tráfego**, verifique se o método de roteamento de tráfego * *desempenho*. Se não estiver, clique * *desempenho** na lista suspensa.
6. Verificar se as **Configurações de monitoramento** estão configurados adequadamente. Monitoramento garante que os pontos de extremidade que estiverem offline não são enviados tráfego. Para monitorar os pontos de extremidade, especifique um caminho e nome do arquivo. Observe que uma barra "/" é uma entrada válida para o caminho relativo e indica que o arquivo está no diretório raiz (padrão). Para obter mais informações sobre o monitoring, consulte [Sobre o Gerenciador de tráfego monitoramento](traffic-manager-monitoring.md).
7. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
8. Teste as alterações em sua configuração. Para obter mais informações, consulte [Configurações do Gerenciador de tráfego de teste](traffic-manager-testing-settings.md).
9. Depois que o perfil do Gerenciador de tráfego estiver configuração e trabalhando, edite o registro DNS no seu servidor DNS autoritativo para apontar o seu nome de domínio de empresa para o nome de domínio do Gerenciador de tráfego. Para obter mais informações sobre como fazer isso, consulte [ponto um domínio de Internet da empresa para um domínio do Gerenciador de tráfego](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Próximas etapas


[Aponte o domínio da Internet uma empresa para um domínio do Gerenciador de tráfego](traffic-manager-point-internet-domain.md)

[Métodos de roteamento de tráfego Manager](traffic-manager-routing-methods.md)

[Configurar o método de roteamento de failover](traffic-manager-configure-failover-routing-method.md)

[Configurar o método de roteamento de repetição alternada](traffic-manager-configure-round-robin-routing-method.md)

[Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)

[Gerenciador de-desabilitar, habilitar o tráfego ou excluir um perfil](disable-enable-or-delete-a-profile.md)

[Gerenciador de tráfego - desativar ou ativar um ponto extremo](disable-or-enable-an-endpoint.md)

