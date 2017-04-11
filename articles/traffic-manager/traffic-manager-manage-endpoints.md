<properties
    pageTitle="Gerenciar pontos de extremidade no Gerenciador de tráfego do Azure | Microsoft Azure"
    description="Este artigo ajudará você a adicionar, remover, habilitar e desabilitar pontos de extremidade do Gerenciador de tráfego do Azure."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="add-disable-enable-or-delete-endpoints"></a>Adicionar, desativar, ativar ou excluir pontos de extremidade

O recurso de aplicativos Web do serviço de aplicativo do Azure já fornece failover e funcionalidade de roteamento de tráfego alternada para sites em um data center, independentemente do modo de site. Gerenciador de tráfego Azure permite especificar failover e roteamento de tráfego de alternada para serviços de nuvem e sites em diferentes dos data centers. A primeira etapa necessária para fornecer essa funcionalidade é adicionar o ponto de extremidade de site ou serviço de nuvem ao Gerenciador de tráfego.

>[AZURE.NOTE]  Este artigo explica como usar o portal clássico. O portal de clássico Azure apenas dá suporte para a criação e a atribuição de serviços de nuvem e aplicativos Web, como pontos de extremidade. O novo [portal Azure](https://portal.azure.com) é a interface preferida.

Você também pode desativar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de tráfego. Desabilitar um ponto de extremidade deixa-lo como parte do perfil, mas o perfil atua como se o ponto de extremidade não está incluído nele. Esta ação é útil para remover temporariamente um ponto de extremidade que está no modo de manutenção ou sendo redistribuído. Depois que o ponto de extremidade estiver em funcionamento novamente, ele pode ser habilitado.

>[AZURE.NOTE] Desabilitar um ponto de extremidade tem nada fazer com seu estado de implantação no Azure. Um ponto de extremidade eficaz, permanece para cima e capaz de receber o tráfego mesmo quando desativado no Gerenciador de tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Para adicionar um ponto de extremidade de site ou serviço de nuvem

1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar. Para abrir a página de configurações, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade que já fazem parte da sua configuração.
3. Na parte inferior da página, clique em **Adicionar** para acessar a página **Adicionar pontos de extremidade do serviço** . Por padrão, a página lista os serviços de nuvem em **Pontos de extremidade do serviço**.
4. Para serviços de nuvem, selecione os serviços de nuvem na lista para adicioná-los como pontos de extremidade para este perfil. Limpar o nome de serviço de nuvem remove da lista de pontos de extremidade.
5. Sites, clique na lista suspensa **Tipo de serviço** e, em seguida, selecione **Web app**.
6. Selecione os sites na lista para adicioná-los como pontos de extremidade para este perfil. Limpar o nome de site removerá a lista de pontos de extremidade. Você pode selecionar apenas um site por Azure Data Center (também conhecido como uma região). Quando você seleciona o primeiro site, os outros sites no mesmo data center ficam indisponíveis para seleção. Observe também que somente padrão sites estão listadas.
7. Após selecionar os pontos de extremidade para este perfil, clique na marca de seleção na parte inferior direita para salvar suas alterações.

>[AZURE.NOTE] Depois de adicionar ou remover um ponto de extremidade de um perfil usando o método de roteamento de tráfego de *Failover* , na lista de prioridade de failover podem não ser ordenados eles maneira desejada. Você pode ajustar a ordem da lista de prioridade de Failover na página configuração. Para obter mais informações, consulte [Configurar Failover roteamento de tráfego](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para desabilitar um ponto extremo

1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar. Para abrir a página de configurações, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade incluídas em sua configuração.
3. Clique na extremidade que você deseja desabilitar e clique em **Desativar** na parte inferior da página.
4. Os clientes continuam a enviar tráfego para o ponto de extremidade para a duração de Time to Live (TTL). Você pode alterar o TTL na página Configuração do perfil do Gerenciador de tráfego.

## <a name="to-enable-an-endpoint"></a>Para habilitar um ponto extremo

1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar. Para abrir a página de configurações, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade incluídas em sua configuração.
3. Clique na extremidade que você deseja habilitar e clique em **Habilitar** na parte inferior da página.
4. Os clientes estão sendo direcionados para o ponto de extremidade habilitado, conforme indicado pelo perfil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Para excluir um ponto de extremidade de site ou serviço de nuvem

1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar. Para abrir a página de configurações, clique na seta à direita do nome do perfil.
2. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade que já fazem parte da sua configuração.
3. Na página pontos de extremidade, clique no nome do ponto de extremidade que você deseja excluir do perfil.
4. Na parte inferior da página, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar perfis do Gerenciador de tráfego](traffic-manager-manage-profiles.md)
* [Configurar métodos de roteamento](traffic-manager-configure-routing-method.md)
* [Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)
* [Considerações de desempenho do Gerenciador de tráfego](traffic-manager-performance-considerations.md)
* [Operações em Gerenciador de tráfego (referência de API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
