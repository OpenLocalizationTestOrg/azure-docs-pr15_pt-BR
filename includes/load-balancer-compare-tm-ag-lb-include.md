## <a name="load-balancer-differences"></a>Diferenças de Balanceador de carga

Há diferentes opções para distribuir o tráfego de rede usando o Microsoft Azure. Essas opções funcionam de maneira diferente uns dos outros, tendo um recurso diferente definir e suporte cenários diferentes. Eles podem cada ser usados em isolamento ou combinando-os.

- **Balanceador de carga do Azure** funciona na camada de transporte (camada 4 na pilha de referência de rede OSI). Ele fornece o nível de rede de distribuição do tráfego nas instâncias de um aplicativo em execução no mesmo Azure data center.

- **Gateway do aplicativo** funciona na camada de aplicativo (camada 7 na pilha de referência de rede OSI). Ele atua como um serviço de proxy reverso, encerrando a conexão do cliente e encaminhar solicitações para pontos de extremidade de back-end.

- **Gerenciador de tráfego** funciona no nível do DNS.  Ele usa respostas DNS para direcionar o tráfego de usuário final para pontos de extremidade distribuídos globalmente. Clientes, em seguida, se conectar a esses pontos de extremidade diretamente.

A tabela a seguir resume os recursos oferecidos pelo cada serviço:

| Serviço | Balanceador de carga Azure | Gateway de aplicativo | Gerenciador de tráfego |
|---|---|---|---|
|Tecnologia| Nível de transporte (camada 4) | Nível de aplicativo (camada 7) | Nível DNS |
| Protocolos de aplicativos com suporte | Qualquer | HTTP e HTTPS |  Qualquer (um ponto de extremidade HTTP é necessário para monitoramento de ponto de extremidade) |
| Pontos de extremidade | Instâncias de função Azure VMs e serviços de nuvem | Qualquer endereço IP interno do Azure ou internet pública endereço IP | Azure VMs, serviços de nuvem, Azure Web Apps e pontos de extremidade externos |
| Suporte de Vnet | Pode ser usado para Internet opostos internos (Vnet) aplicativos e | Pode ser usado para Internet opostos internos (Vnet) aplicativos e |    Suporta apenas os aplicativos da Internet |
Monitoramento de ponto de extremidade | Suporte por meio de testes | Suporte por meio de testes | Suporte via GET HTTP/HTTPS | 

Azure balanceador de carga e rede de roteiro Application Gateway o tráfego para pontos de extremidade, mas eles têm diferentes cenários de uso para que o tráfego para tratar. A tabela a seguir ajuda a compreender a diferença entre os balanceadores de dois carga:

| Tipo | Balanceador de carga Azure | Gateway de aplicativo |
|---|---|---|
| Protocolos | TCP/UDP | HTTP / HTTPS |
| Reserva de IP | Com suporte | Sem suporte | 
| Modo de balanceamento de carga | 5-tuple(source IP, source port, destination IP, destination port, protocol type) | Round Robin<br>O roteamento baseado em URL | 
| Modo de balanceamento de carga (IP de origem / conjunta sessões) |  2-tupla (IP de origem e destino IP), 3-tupla (IP de origem, destino IP e porta). Pode expandir ou reduzir com base no número de máquinas virtuais | Afinidade baseada em cookies<br>O roteamento baseado em URL |
| Testes de integridade | Padrão: intervalo de sondagem - 15 segundos. Tirado de rotação: 2 falhas contínuas. Testes de suporta definidos pelo usuário | Intervalo de sondagem ocioso 30 segundos. Removidos após uma falha de único teste no modo ocioso ou 5 falhas consecutivas tráfego ao vivo. Testes de suporta definidos pelo usuário | 
| Transferindo SSL | Sem suporte | Com suporte | 
  