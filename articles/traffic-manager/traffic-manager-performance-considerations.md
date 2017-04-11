<properties
    pageTitle="Considerações de desempenho para o Gerenciador de tráfego do Azure | Microsoft Azure"
    description="Compreender o desempenho em Gerenciador de tráfego e como testar o desempenho do seu site ao usar o Gerenciador de tráfego"
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
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho para o Gerenciador de tráfego

Esta página explica considerações de desempenho usando o Gerenciador de tráfego. Considere o seguinte cenário:

Você tem as regiões WestUS e EastAsia instâncias do seu site. Uma das instâncias está falhando a verificação de integridade para o teste de Gerenciador de tráfego. Tráfego de aplicativo é direcionado para a região íntegra. Esse failover é esperado, mas desempenho pode ser um problema com base na latência do tráfego agora viajando para uma região distante.

## <a name="how-traffic-manager-works"></a>Como funciona o Gerenciador de tráfego

O impacto de desempenho somente Gerenciador de tráfego pode ter em seu site é a pesquisa de DNS inicial. Uma solicitação DNS para o nome do seu perfil do Gerenciador de tráfego é tratada pelo servidor DNS da Microsoft raiz que hospeda a zona de trafficmanager.net. Gerenciador de tráfego preenche e atualiza regularmente, com base na política do Gerenciador de tráfego e os resultados de teste de servidores da Microsoft DNS raiz. Portanto, mesmo durante a pesquisa de DNS inicial, nenhuma consulta DNS é enviada ao Gerenciador de tráfego.

Gerenciador de tráfego consiste em vários componentes: nome DNS servers, um serviço de API, a camada de armazenamento e um ponto de extremidade do serviço de monitoramento. Se um componente de serviço do Gerenciador de tráfego falhar, não há nenhum efeito sobre o nome DNS associado ao seu perfil do Gerenciador de tráfego. Os registros nos servidores DNS da Microsoft permanecem inalterados. No entanto, ponto de extremidade de monitoramento e atualização de DNS não ocorrer. Portanto, Gerenciador de tráfego não é capaz de atualizar o DNS para apontar para seu site de failover quando seu local principal falhar.

Resolução de nomes DNS é rápida e os resultados são armazenados em cache. A velocidade da pesquisa de DNS inicial depende dos servidores DNS que o cliente usa para resolução de nome. Normalmente, um cliente pode concluir uma pesquisa DNS dentro de cerca de 50 ms. Os resultados da pesquisa são armazenados em cache para a duração do DNS Time-to-live (TTL). O TTL Gerenciador de tráfego padrão é 300 segundos.

O tráfego não flui através do Gerenciador de tráfego. Uma vez concluída a pesquisa de DNS, o cliente tem um endereço IP para uma instância do seu site. O cliente conecta-se diretamente para o endereço e não passa através do Gerenciador de tráfego. A política do Gerenciador de tráfego que você escolher não tem nenhuma influência sobre o desempenho do DNS. No entanto, um desempenho roteamento-método pode afetar negativamente a experiência de aplicativo. Por exemplo, se sua política redireciona tráfego da América do Norte para uma instância hospedado na Ásia, a latência de rede para essas sessões pode ser um problema de desempenho.

## <a name="measuring-traffic-manager-performance"></a>Medir o desempenho do Gerenciador de tráfego

Há vários sites, que você pode usar para entender o desempenho e o comportamento de um perfil Gerenciador de tráfego. Muitos desses sites são gratuitos, mas podem ter limitações. Alguns sites oferecem aprimorado de monitoramento e relatórios de uma taxa.

As ferramentas nesses sites medida DNS latências e exibir os resolvidos endereços IP para locais de cliente em todo o mundo. Maioria dessas ferramentas não armazenar em cache os resultados DNS. Portanto, as ferramentas de mostram a pesquisa de DNS completa sempre que um teste é executado. Quando você testar do seu próprio cliente, você enfrentar somente o desempenho de pesquisa DNS completo uma vez durante a duração TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Ferramentas de amostra para medir o desempenho do DNS

- [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS oferece muitas ferramentas de desempenho. A ferramenta de comparação de DNS pode mostrar quanto tempo leva para resolver seu nome DNS e que comparação com outros provedores de serviços DNS.

- [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Uma das ferramentas mais simples é WebSitePulse. Insira a URL para ver o tempo de resolução DNS, primeiro Byte, último Byte e outras estatísticas de desempenho. Você pode escolher entre três locais de teste diferentes. Neste exemplo, verá que a primeira execução mostra que a pesquisa DNS leva sec 0.204.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Como os resultados são armazenados em cache, o segundo teste para o mesmo ponto de extremidade do Gerenciador de tráfego a pesquisa de DNS leva sec 0.002.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

- [Monitor de aplicativo sintéticos autoridade de certificação](https://asm.ca.com/en/checkit.php)

    Anteriormente conhecido como a ferramenta de site de seleção Watchmouse, este site mostram o tempo de resolução DNS de várias regiões geográficas simultaneamente. Insira a URL para ver o tempo de resolução DNS, a hora de conexão e a velocidade de vários locais geográficos. Use este teste para ver qual serviço hospedado é retornado para locais diferentes em todo o mundo.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

- [Pingdom](http://tools.pingdom.com/)

    Esta ferramenta fornece estatísticas de desempenho para cada elemento de uma página da web. A guia de página análise mostra a porcentagem de tempo gasto na pesquisa de DNS.

- [O que é meu DNS?](http://www.whatsmydns.net/)

    Este site faz uma pesquisa DNS de 20 diferentes locais e exibe os resultados em um mapa.

- [Aprofundar Interface da Web](http://www.digwebinterface.com)

    Este site mostra que informações de DNS, incluindo registros e CNAMEs mais detalhadas. Certifique-se de que você verifique a 'Colorir output' e 'Estatísticas' em Opções e selecione 'Todos' em servidores de nomes.

## <a name="next-steps"></a>Próximas etapas

[Sobre métodos de roteamento de tráfego do Gerenciador de tráfego](traffic-manager-routing-methods.md)

[Teste suas configurações do Gerenciador de tráfego](traffic-manager-testing-settings.md)

[Operações em Gerenciador de tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gerenciador de tráfego Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
