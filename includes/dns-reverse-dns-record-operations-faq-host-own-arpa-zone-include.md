<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Perguntas Frequentes - hospedando seu fuso ARPA no DNS do Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Pode hospedar zonas ARPA para Meus blocos IP atribuído pelo ISP no Azure DNS?
Sim. Hospedar zonas ARPA seu próprio intervalos de IP no Azure DNS é totalmente suportada.

Simplesmente [criar a zona de DNS do Azure](dns-getstarted-create-dnszone.md)e trabalhar com seu ISP para [a zona de representante](dns-domain-delegation.md).  Você pode gerenciar os registros PTR para cada pesquisa inversa da mesma forma que outros tipos de registro.

Você também pode [Importar uma zona de pesquisa inversa existente usando a CLI do Azure](dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Quanto o custo de zona Meu ARPA hospedagem?
Hospedando a zona ARPA para seu IP atribuído pelo ISP bloco no Azure DNS cobrada de acordo com [taxas Azure DNS padrão](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Pode hospedar o zonas ARPA para endereços IPv4 e IPv6 no Azure DNS?
Sim.