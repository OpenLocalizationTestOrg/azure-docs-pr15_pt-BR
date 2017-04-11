<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Perguntas Frequentes - reversa de DNS para seu endereço de IP atribuído Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto reverter custo de registros DNS?
Elas são gratuitas!  Não há nenhum custo adicional para os registros DNS reverso ou consultas.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>Os registros DNS reverso do meu endereço IP público atribuído Azure resolverá da internet?
Sim. Assim que você definir a propriedade DNS reversa para seu endereço IP público, Azure gerencia todas as delegações DNS e zonas DNS necessárias para garantir que o registro DNS reverso resolve para todos os usuários de internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Um registro DNS reverso padrão será criado para meus endereços IP públicos?
Não. DNS reverso é um recurso opt-in. Sem registros DNS reverso padrão são criados se você optar por não configurá-los.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>O que é o formato para o nome de domínio totalmente qualificado (FQDN)?
FQDNs são especificadas na ordem de frente e devem ser terminadas por um ponto (por exemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>O que acontece se as verificações de validação para o DNS reverso eu especificou falharem?
Onde a validação de DNS reverso verifica falhar, a operação de gerenciamento de serviço falhará. Corrija o valor DNS reverso conforme necessário e tente novamente.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Pode gerenciar o DNS reverso do meu site do Azure?
Não há suporte para o DNS reverso para sites do Azure. DNS inversa há suporte para máquinas virtuais do Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>Pode configurar vários registros DNS reverso para meu endereço IP público?
Não. Azure oferece suporte a um único registro DNS reverso para cada endereço IP público. No entanto, cada endereço IP público pode ter seu próprio registro DNS reverso.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>Pode configurar os registros DNS reverso para um endereço de IP IPv6 público?
Não.  No momento, os registros DNS reverso são suportados para endereços IPv4 públicos IP apenas.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>Pode configurar um registro DNS reverso para meu endereço IP público sem ter um DomainNameLabel especificado?
Não. Para aproveitar os registros DNS reverso para seus endereços IP públicos, especifique a propriedade DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar emails para domínios externos da meus serviços de computação Azure?
Não. [Serviços de computação azure não dão suporte a enviar emails para domínios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
