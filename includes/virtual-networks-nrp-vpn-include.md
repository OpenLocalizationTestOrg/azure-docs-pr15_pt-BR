## <a name="vpn-gateway"></a>Gateway VPN 
Um recurso de gateway VPN permite que você crie uma conexão segura entre o Centro de dados local e o Azure. Um recurso de gateway VPN pode ser configurado de três maneiras diferentes:
 
- **Aponte para o Site** – você pode acessar seus recursos Azure hospedados em um VNET usando um cliente VPN de qualquer computador com segurança. 
- **Conexão de vários local** – você pode conectar com segurança de seus centros de dados locais a recursos executados em um VNET. 
- **VNET para VNET** – você pode conectar com segurança em VNETS Azure dentro da mesma região, ou regiões para construir cargas de trabalho com redundância geográfica.

Chaves propriedades de um gateway VPN incluem:
 
- **Tipo de gateway** - dinamicamente roteados ou um gateway roteado estático. 
- **Prefixo do Pool de endereço do cliente de VPN** – endereços IP para serem atribuídos a clientes conectando-se em um ponto para configuração do site.