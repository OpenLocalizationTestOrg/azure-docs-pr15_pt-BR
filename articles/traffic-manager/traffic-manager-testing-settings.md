<properties
    pageTitle="Teste as configurações do Gerenciador de tráfego | Microsoft Azure"
    description="Este artigo ajudará você a testar configurações do Gerenciador de tráfego"
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

# <a name="test-your-traffic-manager-settings"></a>Teste suas configurações do Gerenciador de tráfego

Para testar as configurações do Gerenciador de tráfego, você precisa ter vários clientes, em vários locais, do qual você pode executar os testes. Em seguida, exiba os pontos de extremidade no seu perfil do Gerenciador de tráfego para baixo uma de cada vez.

* Defina o valor de TTL do DNS baixa para que as alterações se propagar rapidamente (por exemplo, 30 segundos).
* Conheça os endereços IP dos seus serviços de nuvem Azure e sites no perfil que você está testando.
* Use ferramentas que permitem resolver um nome DNS para um endereço IP e exibir esse endereço.

Você está verificando que os nomes DNS resolver para endereços IP dos pontos de extremidade no seu perfil. Resolva os nomes de maneira consistente com o método de roteamento de tráfego definido no perfil do Gerenciador de tráfego. Você pode usar as ferramentas como **nslookup** ou **aprofundar** para resolver nomes DNS.

Os exemplos a seguir ajudam você a testar seu perfil do Gerenciador de tráfego.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Verifique o perfil do Gerenciador de tráfego usando nslookup e ipconfig no Windows

1. Abra um comando ou o prompt do Windows PowerShell como administrador.
2. Tipo de `ipconfig /flushdns` para liberar o cache de resolução DNS.
3. Tipo de `nslookup <your Traffic Manager domain name>`. Por exemplo, o comando a seguir verifica o nome de domínio com o prefixo *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Um resultado típico mostra as seguintes informações:

    * O nome DNS e o endereço IP do servidor DNS sendo acessado para resolver esse nome de domínio do Gerenciador de tráfego.
    * O nome de domínio do Gerenciador de tráfego digitado na linha de comando após "nslookup" e o endereço IP à qual o domínio do Gerenciador de tráfego resolve. O segundo endereço IP é aquele importante para verificar. Ele deve corresponder a um endereço IP (VIP) virtual público por um dos serviços de nuvem ou sites no perfil do Gerenciador de tráfego que você está testando.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Como testar o método de roteamento de tráfego de failover

1. Deixe todos os pontos de extremidade para cima.
2. Usando um único cliente, solicite a resolução DNS para o seu nome de domínio de empresa usando o nslookup ou um utilitário semelhante.
3. Verifique se o endereço IP resolvido corresponde o principal ponto de extremidade.
4. Trazer o seu principal ponto de extremidade ou remova o arquivo de monitoramento para que o Gerenciador de tráfego considera que o aplicativo está desativado.
5. Aguarde o DNS Time-to-Live (TTL) do perfil do Gerenciador de tráfego de mais mais dois minutos. Por exemplo, se o TTL do DNS é 300 segundos (5 minutos), você deve esperar sete minutos.
6. Libere seu DNS cliente cache e solicitação de resolução DNS usando nslookup. No Windows, você pode liberar o cache DNS com o comando ipconfig /flushdns..
7. Certifique-se de que o endereço IP resolvido corresponde ao seu ponto de extremidade secundário.
8. Repita o processo, colocar cada ponto de extremidade alternadamente. Confirme se o DNS retorna o endereço IP do ponto de extremidade próxima na lista. Quando todos os pontos de extremidade para baixo, você deve obter o endereço IP do ponto de extremidade principal novamente.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Como testar o método de roteamento de tráfego ponderada

1. Deixe todos os pontos de extremidade para cima.
2. Usando um único cliente, solicite a resolução DNS para o seu nome de domínio de empresa usando o nslookup ou um utilitário semelhante.
3. Certifique-se de que o endereço IP resolvido corresponda a um dos seus pontos de extremidade.
4. Liberar o cache de cliente DNS e repita as etapas 2 e 3 para cada ponto de extremidade. Você deve ver os endereços IP diferentes retornados para cada um dos seus pontos de extremidade.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Como testar o método de roteamento de tráfego de desempenho

Para testar efetivamente um método de roteamento de tráfego de desempenho, você deve ter clientes localizados em diferentes partes do mundo. Você pode criar clientes em diferentes regiões Azure que podem ser usados para testar seus serviços. Se você tiver uma rede global, você pode entrar em clientes em outras partes do mundo e executar os testes a partir daí remotamente.

Como alternativa, há livre pesquisa DNS baseado na web e aprofundar serviços disponíveis. Algumas dessas ferramentas oferecem a capacidade de verificar a resolução de nomes DNS de vários locais em todo o mundo. Fazer uma pesquisa em "Pesquisa de DNS" para obter exemplos. Serviços de terceiros como Gomez ou discurso podem ser usados para confirmar que os perfis são distribuindo tráfego conforme esperado.

## <a name="next-steps"></a>Próximas etapas

* [Sobre métodos de roteamento de tráfego do Gerenciador de tráfego](traffic-manager-routing-methods.md)
* [Considerações de desempenho do Gerenciador de tráfego](traffic-manager-performance-considerations.md)
* [Solução de problemas Gerenciador de tráfego degradado estado](traffic-manager-troubleshooting-degraded.md)




