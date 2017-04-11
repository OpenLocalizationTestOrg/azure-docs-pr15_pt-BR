<properties
   pageTitle="Habilitar ou desabilitar um ponto de extremidade do Gerenciador de tráfego | Microsoft Azure"
   description="Este artigo ajudará desabilitar ou habilitar seus pontos de extremidade de perfil do Gerenciador de tráfego."
   services="traffic-manager"
   documentationCenter="na"
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

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Habilitar ou desabilitar um ponto de extremidade do Gerenciador de tráfego

Você também pode desativar pontos de extremidade individuais que fazem parte de um perfil do Gerenciador de tráfego. Pontos de extremidade incluem sites e serviços de nuvem. Desabilitar um ponto de extremidade deixa-lo como parte do perfil, mas o perfil atua como se o ponto de extremidade não está incluído nele. Esta ação é muito útil para remover temporariamente um ponto de extremidade que está no modo de manutenção ou sendo redistribuído. Depois que o ponto de extremidade estiver em funcionamento novamente, ele pode ser habilitado

>[AZURE.NOTE] **Desabilitar um ponto de extremidade tem nada fazer com seu estado de implantação no Azure. Um ponto de extremidade eficaz, permanecerá para cima e capaz de receber o tráfego mesmo quando desativado no Gerenciador de tráfego. Além disso, a desabilitação de um ponto de extremidade em um perfil não afeta seu status em outro perfil.**

## <a name="to-disable-an-endpoint"></a>Para desabilitar um ponto extremo

1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
1. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade incluídas em sua configuração.
1. Clique na extremidade que você deseja desabilitar e clique em **Desativar** na parte inferior da página.
1. Tráfego interromperá fluindo para o ponto de extremidade com base no DNS Time-to-Live (TTL) configurado para o nome de domínio do Gerenciador de tráfego. Você pode alterar o TTL da página de configuração do perfil do Gerenciador de tráfego.

## <a name="to-enable-an-endpoint"></a>Para habilitar um ponto extremo


1. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações de ponto de extremidade que você deseja modificar e clique na seta à direita do nome do perfil. Isso abrirá a página de configurações para o perfil.
1. Na parte superior da página, clique em **pontos de extremidade** para exibir os pontos de extremidade incluídas em sua configuração.
1. Clique na extremidade que você deseja habilitar e clique em **Habilitar** na parte inferior da página.
1. Tráfego iniciará fluindo para o serviço como ditado pelo perfil.

## <a name="next-steps"></a>Próximas etapas

[Gerenciador de-desabilitar, habilitar o tráfego ou excluir um perfil](disable-enable-or-delete-a-profile.md)

[Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)

[Considerações de desempenho do Gerenciador de tráfego](traffic-manager-performance-considerations.md)