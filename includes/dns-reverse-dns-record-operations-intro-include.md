## <a name="what-is-reverse-dns"></a>O que é reversa de DNS?

Os registros DNS convencionais habilitar um mapeamento de um nome DNS (como 'www.contoso.com') para um endereço IP (como 64.4.6.100).  DNS reverso permite a tradução de um endereço IP (64.4.6.100) para um nome ('www.contoso.com').

Os registros DNS reverso são usados em uma variedade de situações. Por exemplo, os registros DNS reverso são amplamente usados no combate spam de email, verificando o remetente de uma mensagem de email.  Servidor de email de recebimento irá recuperar o registro DNS reverso de endereço IP do servidor de envio e verificar se o host está autorizado a enviar email do domínio de origem. (Observe entretanto [computação Azure services não oferecem suporte para envio de emails para domínios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Reverso como o DNS funciona

Os registros DNS reverso são hospedados em especiais zonas DNS, conhecidas como zonas de 'ARPA'.  Essas zonas formam uma hierarquia DNS separada em paralelo com a hierarquia normal domínios como 'contoso.com' de hospedagem.

Por exemplo, o DNS gravar 'www.contoso.com' é implementado usando um registro DNS 'A' com nome 'www' na zona 'contoso.com'.  Este registro aponta para o endereço IP correspondente, neste caso 64.4.6.100.  A pesquisa reversa é implementada separadamente, usando um registro de 'PTR' denominado '100' na zona '6.4.64.in-addr.arpa' (Observe que os endereços IP são revertidos em zonas ARPA.)  Esse registro PTR, se ele foi configurado corretamente, aponta para o nome www.contoso.com.

Quando uma organização recebe um bloco de endereço IP, eles também adquirem o direito de gerenciar zona ARPA correspondente. As zonas ARPA correspondentes para os blocos de endereços IP usados pelo Azure são hospedadas e gerenciadas pela Microsoft. Seu ISP pode hospedar a zona ARPA para seus próprios endereços IP para você, ou pode permitir que você hospedar a zona ARPA em um serviço DNS de sua escolha, como o DNS do Azure.

>[AZURE.NOTE] Pesquisas diretas DNS e pesquisas DNS reversa são implementadas em paralelas, separadas hierarquias DNS. A consulta inversa para 'www.contoso.com' é **não** hospedado na zona 'contoso.com', em vez disso, ele é hospedado na zona ARPA para o bloco de endereço IP correspondente.

Para obter mais informações sobre o DNS reverso, consulte [Pesquisa inversa de DNS](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Suporte a Azure reversa de DNS

Azure suporta dois cenários separados relacionadas para inverter DNS:

1. Hospedando a zona ARPA correspondente ao seu bloco de endereço IP.
2. Permitindo que você configurar o registro DNS reverso para o endereço IP atribuído ao seu serviço Azure.

Para dar suporte a DNS antigo, Azure pode ser usado para hospedar suas zonas ARPA e gerenciar os registros PTR para cada pesquisa inversa de DNS.  O processo de criação da zona ARPA, configurando a delegação e configurar registros PTR é a mesma regulares zonas DNS.  As únicas diferenças são que a delegação deve ser configurada por meio de seu ISP em vez de seu registrador de DNS e apenas o tipo de registro PTR deve ser usado.

Para dar suporte a última, o Azure permite que você configure a pesquisa inversa para os endereços IP alocado ao seu serviço.  Esta pesquisa inversa é configurada pelo Azure como um registro PTR na zona ARPA correspondente.  Essas zonas ARPA, correspondente a todos os intervalos IP usados pelo Azure, são hospedadas pela Microsoft. **O restante deste artigo descreve esse cenário detalhadamente.**
