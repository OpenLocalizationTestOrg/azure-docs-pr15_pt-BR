Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para começar a hospedagem de seu domínio, você precisa criar uma zona DNS. Qualquer registro DNS criado para um determinado domínio será dentro de uma zona DNS para o domínio. 

Por exemplo, o domínio "contoso.com" pode conter um número de registros DNS, como "mail.contoso.com" (para um servidor de email) e "www.contoso.com" (para um site da web). 


## <a name="names"></a>Sobre nomes de zona DNS
 
- O nome da zona deve ser exclusivo no grupo de recursos e a zona não deve existir. Caso contrário, a operação falhará.

- O mesmo nome de zona pode ser reutilizado em um grupo de recurso diferente ou uma assinatura diferente do Azure. 

- Onde várias zonas compartilham o mesmo nome, cada instância será atribuída endereços de servidor de nome diferente e pode ser delegada apenas uma instância do domínio pai. Para obter mais informações, consulte [representante um domínio ao Azure DNS](../articles/dns/dns-domain-delegation.md).