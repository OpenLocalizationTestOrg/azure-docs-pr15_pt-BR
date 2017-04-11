<properties
   pageTitle="Configurar o método de roteamento de tráfego do Gerenciador de tráfego failover | Microsoft Azure"
   description="Este artigo ajudará você a configurar o método de roteamento de tráfego de failover no Gerenciador de tráfego"
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

# <a name="configure-failover-routing-method"></a>Configurar o método de roteamento de Failover

Muitas vezes uma organização deseja fornecer confiabilidade para seus serviços. Ele faz isso, fornecendo serviços de backup caso seu serviço primário falhar. Um padrão comum para failover do serviço é fornecer um conjunto de serviços idênticos e enviar tráfego para um serviço principal, enquanto mantém uma lista configurada de um ou mais serviços de backup. Você pode configurar esse tipo de backup com sites e serviços de nuvem Azure seguindo os procedimentos abaixo.

Observe que o Azure sites já oferece failover tráfego roteamento funcionalidade do método para sites em um data center (também conhecido como uma região), independentemente do modo de site. Gerenciador de tráfego permite especificar o método de roteamento de tráfego failover para sites em diferentes dos data centers.

## <a name="to-configure-failover-traffic-routing-method"></a>Para configurar o método de roteamento de tráfego de failover:

1. No portal do clássico Azure, no painel esquerdo, clique no ícone do **Gerenciador de tráfego** para abrir o painel Gerenciador de tráfego. Se você ainda não tiver criado o perfil do Gerenciador de tráfego, consulte [Gerenciar perfis do Gerenciador de tráfego](traffic-manager-manage-profiles.md) para as etapas para criar um perfil de Gerenciador de tráfego básico.
2. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
3. Na sua página de perfil, clique em **pontos de extremidade** na parte superior da página e verifique se que a ambos os serviços de nuvem e sites (pontos de extremidade) que você deseja incluir em sua configuração estão presentes. Para obter etapas adicionar ou remover pontos de extremidade, consulte [Gerenciar pontos de extremidade no Gerenciador de tráfego](traffic-manager-endpoints.md).
4. Na sua página de perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. **Configurações de método de roteamento de tráfego**, verifique se o método de roteamento de tráfego **Failover**. Se não estiver, clique em **Failover** na lista suspensa.
6. Para **Lista de prioridade de Failover**, ajuste a ordem de failover para seus pontos de extremidade. Quando você selecionar o método de roteamento de tráfego de **Failover** , a ordem dos pontos de extremidade selecionados é importante. O principal ponto de extremidade está visível. Use as setas e para baixo para alterar a ordem, conforme necessário. Para obter informações sobre como definir a prioridade de failover usando o Windows PowerShell, consulte [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verificar se as **Configurações de monitoramento** estão configurados adequadamente. Monitoramento garante que os pontos de extremidade que estiverem offline não são enviados tráfego. Para monitorar os pontos de extremidade, especifique um caminho e nome do arquivo. Observe que uma barra "/" é uma entrada válida para o caminho relativo e indica que o arquivo está no diretório raiz (padrão). Para obter mais informações sobre o monitoring, consulte [Gerenciador de tráfego monitoramento](traffic-manager-monitoring.md).
8. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
9. Teste as alterações em sua configuração. Consulte [Configurações do Gerenciador de tráfego de teste](traffic-manager-testing-settings.md) para obter mais informações.
10. Depois que o perfil do Gerenciador de tráfego estiver configuração e trabalhando, edite o registro DNS no seu servidor DNS autoritativo para apontar o seu nome de domínio de empresa para o nome de domínio do Gerenciador de tráfego. Para obter mais informações sobre como fazer isso, consulte [ponto um domínio de Internet da empresa para um domínio do Gerenciador de tráfego](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Próximas etapas

[Aponte o domínio da Internet uma empresa para um domínio do Gerenciador de tráfego](traffic-manager-point-internet-domain.md)

[Métodos de roteamento de tráfego Manager](traffic-manager-routing-methods.md)

[Configurar o método de roteamento de repetição alternada](traffic-manager-configure-round-robin-routing-method.md)

[Configurar o método de roteamento de desempenho](traffic-manager-configure-performance-routing-method.md)

[Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)

[Gerenciador de-desabilitar, habilitar o tráfego ou excluir um perfil](disable-enable-or-delete-a-profile.md)

[Gerenciador de tráfego - desativar ou ativar um ponto extremo](disable-or-enable-an-endpoint.md)

