A tabela a seguir lista os requisitos para PolicyBased e RouteBased VPN gateways. Esta tabela se aplica ao gerente de recursos e modelos de implantação clássico. Para o modelo clássico, gateways PolicyBased VPN são os mesmos gateways estáticos e gateways baseados em rota são os mesmos gateways dinâmicos.


|   | **Gateway VPN PolicyBased Basic** | **Gateway VPN RouteBased Basic** | **Gateway VPN RouteBased padrão**   | **Gateway VPN RouteBased alto desempenho** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Conectividade to-Site (S2S)**  | Configuração PolicyBased VPN        | Configuração RouteBased VPN  | Configuração RouteBased VPN     | Configuração RouteBased VPN    |
| Conectividade de **ponto-a-Site (P2S**)      | Sem suporte   | Com suporte (pode coexistência com S2S)  | Com suporte (pode coexistência com S2S)  | Com suporte (pode coexistência com S2S) |
| **Método de autenticação**                 |    Chave pré-compartilhada  | Chave pré compartilhada para conectividade de S2S, certificados para conectividade de P2S | Chave pré compartilhada para conectividade de S2S, certificados para conectividade de P2S | Chave pré compartilhada para conectividade de S2S, certificados para conectividade de P2S |
| **Número máximo de conexões de S2S**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Número máximo de conexões de P2S**       | Sem suporte                  | 128                                                                   | 128                               | 128                              |
|**Suporte a roteamento ativo (BGP)**           | Sem suporte                  | Sem suporte                                                         | Com suporte                     | Com suporte                   |
 
