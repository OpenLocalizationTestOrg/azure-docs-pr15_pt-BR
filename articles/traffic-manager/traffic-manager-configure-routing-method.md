<properties
    pageTitle="Configurar métodos de roteamento de tráfego Manager | Microsoft Azure"
    description="Este artigo explica como configura diferentes métodos de roteamento no Gerenciador de tráfego"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-traffic-manager-routing-methods"></a>Configurar métodos de roteamento de tráfego Manager

Gerenciador de tráfego Azure fornece três métodos de roteamento que controlam como o tráfego é roteado para pontos de extremidade do serviço disponíveis. O método de roteamento de tráfego é aplicado a cada consulta DNS recebida para determinar a extremidade que deve ser retornada na resposta DNS.

Há três métodos de roteamento de tráfego disponíveis no Gerenciador de tráfego:

- **Prioridade:** Selecione 'Priority' quando desejar usar um ponto de extremidade do serviço primário e fornecer backups caso o principal esteja indisponível.
- **Ponderada:** Selecione 'ponderada' quando você quiser distribuir o tráfego em um conjunto de pontos de extremidade, seja uniforme ou acordo com pesos, que você definir.
- **Desempenho:** Selecione 'Desempenho' quando você tem pontos de extremidade em diferentes locais geográficos e deseja que os usuários finais para usar o ponto de extremidade "mais próximo" em termos da menor latência de rede.

## <a name="configure-priority-routing-method"></a>Configurar o método de roteamento de prioridade

Independentemente do modo de site, sites do Azure já fornecem funcionalidade de failover para sites em um data center (também conhecido como uma região). Gerenciador de tráfego fornece failover para sites em diferentes dos data centers.

Um padrão comum para failover do serviço é enviar tráfego para um serviço principal e fornecer um conjunto de serviços de backup idênticos para failover. As etapas a seguir explicam como configurar esse failover prioridade com sites e serviços de nuvem Azure:

1. No portal do clássico Azure, no painel esquerdo, clique no ícone do **Gerenciador de tráfego** para abrir o painel Gerenciador de tráfego.
2. No painel Gerenciador de tráfego no portal de clássico do Azure, localize o perfil do Gerenciador de tráfego que contém as configurações que você deseja modificar. Para abrir a página de configurações de perfil, clique na seta à direita do nome do perfil.
3. Na sua página de perfil, clique em **pontos de extremidade** na parte superior da página. Verificar se os serviços de nuvem e os sites que você deseja incluir em sua configuração estão presentes.
4. Na parte superior para abrir a página de configuração, clique em **Configurar** .
5. **Configurações de método de roteamento de tráfego**, verifique se o método de roteamento de tráfego **Failover**. Se não estiver, clique em **Failover** na lista suspensa.
6. Para **Lista de prioridade de Failover**, ajuste a ordem de failover para seus pontos de extremidade. Quando você selecionar o método de roteamento de tráfego de **Failover** , a ordem dos pontos de extremidade selecionados é importante. O principal ponto de extremidade está visível. Use as setas e para baixo para alterar a ordem, conforme necessário. Para obter informações sobre como definir a prioridade de failover usando o Windows PowerShell, consulte [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verificar se as **Configurações de monitoramento** estão configurados adequadamente. Monitoramento garante que os pontos de extremidade que estiverem offline não são enviados tráfego. Para monitorar os pontos de extremidade, especifique um caminho e nome do arquivo. A barra "/" é uma entrada válida para o caminho relativo e indica que o arquivo está no diretório raiz (padrão).
8. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
9. Teste as alterações em sua configuração.
10. Depois que o perfil do Gerenciador de tráfego estiver funcionando, edite o registro DNS no seu servidor DNS autoritativo para apontar o seu nome de domínio de empresa para o nome de domínio do Gerenciador de tráfego.

## <a name="configure-weighted-routing-method"></a>Configurar o método de roteamento ponderado

Um padrão de comum método de roteamento do tráfego é fornecer um conjunto de pontos de extremidade idênticos, que incluem serviços de nuvem e sites, e enviar tráfego para cada de forma alternada. As etapas a seguir descrevem como configurar esse tipo de método de roteamento de tráfego.

>[AZURE.NOTE] Sites de Azure já fornecem funcionalidade para sites em um data center (também conhecido como uma região) de balanceamento de carga de round robin. Gerenciador de tráfego permite especificar o método de roteamento de tráfego alternada para sites em diferentes dos data centers.

1. No portal do clássico Azure, no painel esquerdo, clique no ícone do **Gerenciador de tráfego** para abrir o painel Gerenciador de tráfego.
2. No painel Gerenciador de tráfego, localize o perfil do Gerenciador de tráfego que contém as configurações que você deseja modificar. Para abrir a página de configurações de perfil, clique na seta à direita do nome do perfil.
3. Na página para seu perfil, clique em **pontos de extremidade** na parte superior da página e verificar se os pontos de extremidade do serviço que você deseja incluir em sua configuração estão presentes.
4. Na sua página de perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. **Método de roteamento de tráfego configurações**, verifique se o método de roteamento de tráfego **Round Robin**. Se não estiver, clique em **repetição alternada** na lista suspensa.
6. Verificar se as **Configurações de monitoramento** estão configurados adequadamente. Monitoramento garante que os pontos de extremidade que estiverem offline não são enviados tráfego. Para monitorar os pontos de extremidade, especifique um caminho e nome do arquivo. A barra "/" é uma entrada válida para o caminho relativo e indica que o arquivo está no diretório raiz (padrão).
7. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
8. Teste as alterações em sua configuração.
9. Depois que o perfil do Gerenciador de tráfego estiver funcionando, edite o registro DNS no seu servidor DNS autoritativo para apontar o seu nome de domínio de empresa para o nome de domínio do Gerenciador de tráfego.

## <a name="configure-performance-traffic-routing-method"></a>Configurar o método de roteamento de tráfego de desempenho

O método de roteamento de tráfego de desempenho permite direcionar o tráfego para o ponto de extremidade com a menor latência de rede do cliente. Normalmente, o data center com o menor latência é o mais próximo na distância geográfica. Este método de roteamento de tráfego não conta em tempo real alterações na configuração de rede ou carregar.

1. No portal do clássico Azure, no painel esquerdo, clique no ícone do **Gerenciador de tráfego** para abrir o painel Gerenciador de tráfego.
2. No painel Gerenciador de tráfego, localize o perfil do Gerenciador de tráfego que contém as configurações que você deseja modificar. Para abrir a página de configurações de perfil, clique na seta à direita do nome do perfil.
3. Na página para seu perfil, clique em **pontos de extremidade** na parte superior da página e verificar se os pontos de extremidade do serviço que você deseja incluir em sua configuração estão presentes.
4. Na página para seu perfil, clique em **Configurar** na parte superior para abrir a página de configuração.
5. **Configurações de método de roteamento de tráfego**, verifique se o método de roteamento de tráfego * *desempenho*. Se não estiver, clique * *desempenho** na lista suspensa.
6. Verificar se as **Configurações de monitoramento** estão configurados adequadamente. Monitoramento garante que os pontos de extremidade que estiverem offline não são enviados tráfego. Para monitorar os pontos de extremidade, especifique um caminho e nome do arquivo. A barra "/" é uma entrada válida para o caminho relativo e indica que o arquivo está no diretório raiz (padrão).
7. Depois de concluir as alterações de configuração, clique em **Salvar** na parte inferior da página.
8. Teste as alterações em sua configuração.
9. Depois que o perfil do Gerenciador de tráfego estiver funcionando, edite o registro DNS no seu servidor DNS autoritativo para apontar o seu nome de domínio de empresa para o nome de domínio do Gerenciador de tráfego.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar perfis do Gerenciador de tráfego](traffic-manager-manage-profiles.md)
* [Métodos de roteamento de tráfego Manager](traffic-manager-routing-methods.md)
* [Configurações do Gerenciador de tráfego de teste](traffic-manager-testing-settings.md)
* [Aponte o domínio da Internet uma empresa para um domínio do Gerenciador de tráfego](traffic-manager-point-internet-domain.md)
* [Gerenciar pontos de extremidade do Gerenciador de tráfego](traffic-manager-manage-endpoints.md)
* [Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)
