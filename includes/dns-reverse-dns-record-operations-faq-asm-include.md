<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Perguntas Frequentes - reversa de DNS para seu endereço de IP atribuído Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto reverter custo de registros DNS?
Elas são gratuitas!  Não há nenhum custo adicional para os registros DNS reverso ou consultas.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Meus registros DNS reverso resolverá da internet?
Sim. Assim que você definir a propriedade DNS reversa de seu serviço de nuvem, Azure gerencia todas as delegações DNS e zonas DNS necessárias para garantir que o registro DNS reverso resolve para todos os usuários de internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>Um registro DNS reverso padrão será criado para meus serviços de nuvem?
Não. DNS reverso é um recurso opt-in. Sem registros DNS reverso padrão são criados se você optar por não configurá-los.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>O que é o formato para o nome de domínio totalmente qualificado (FQDN)?
FQDNs são especificadas na ordem de frente e devem ser terminadas por um ponto (por exemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>O que acontece se as verificações de validação para o DNS reverso eu especificou falharem?
Onde a validação de DNS reverso verifica falhar, a operação de gerenciamento de serviço falhará. Corrija o valor DNS reverso conforme necessário e tente novamente.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Pode gerenciar o DNS reverso do meu site do Azure?
Não há suporte para o DNS reverso para sites do Azure. DNS reverso tem suporte para funções de PaaS do Azure e máquinas virtuais de IaaS.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>Pode configurar vários registros DNS reverso para meu serviço de nuvem?
Não. Azure oferece suporte a um único registro DNS reverso para cada serviço de nuvem do Azure. No entanto, cada serviço de nuvem do Azure pode ter seu próprio registro DNS reverso.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar emails para domínios externos da meus serviços de computação Azure?
Não. [Serviços de computação azure não dão suporte a enviar emails para domínios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
