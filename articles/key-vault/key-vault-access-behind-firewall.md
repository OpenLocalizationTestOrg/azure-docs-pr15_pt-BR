<properties
    pageTitle="Acessar o Cofre de chave atrás do firewall | Microsoft Azure"
    description="Saiba como acessar Cofre de chave de um aplicativo atrás de um firewall"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="accessing-key-vault-behind-firewall"></a>Acessando chave cofre atrás do firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-portshostsip-addresses-should-i-open-to-enable-access-to-key-vault"></a>P: meu aplicativo de cliente do cofre chave precisa ser atrás de um firewall, quais endereços de hosts/portas/IP devo abrir para habilitar o acesso às chave cofre?

Para acessar um cofre chave que seu aplicativo de cliente do cofre chave precisa ser capaz de acessar vários pontos de extremidade para diversas funcionalidades.

- Autenticação (via Active Directory do Azure)
- Gerenciamento de compartimento de chave (que inclui criar/leitura/atualização/excluir e também as políticas de acesso de configuração) por meio do Gerenciador de recursos do Azure
- Acessar e gerenciar objetos (chaves e segredos) armazenados no cofre chave em si, percorre o ponto de extremidade cofre chave específico (por exemplo, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Dependendo do seu ambiente e configuração, há algumas variações.   

## <a name="ports"></a>Portas

Todo o tráfego para compartimento chave para todas as 3 funções (acesso de plano de autenticação, gerenciamento e dados) apresenta HTTPS: porta 443. No entanto CRL, haverá ocasionalmente tráfego HTTP (porta 80). Clientes que oferecem suporte ao OCSP não devem alcançar CRL, mas ocasionalmente podem atingir [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Autenticação

Aplicativo de cliente do Cofre de chave precisa acessar pontos de extremidade do Active Directory do Azure para autenticação. O ponto de extremidade usado depende da configuração de locatário AAD e o tipo de capital – principal de usuário, principal do serviço e o tipo de conta, por exemplo, Account da Microsoft ou ID da organização.  

| Tipo de capital | Porta de ponto de extremidade: |
|----------------|---------------|
| Usuário usando Account da Microsoft<br> (por exemplouser@hotmail.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **China Azure:**<br> login.chinacloudapi.CN:443<br><br>**Azure governo dos EUA:**<br> us.microsoftonline.com:443 de logon<br><br>**Alemanha Azure:**<br> login.microsoftonline.de:443<br><br> e <br>login.Live.com:443   |
| Capital de usuário/serviço com ID da organização AAD (por exemplouser@contoso.com) | **Global:**<br> login.microsoftonline.com:443<br><br> **China Azure:**<br> login.chinacloudapi.CN:443<br><br>**Azure governo dos EUA:**<br> us.microsoftonline.com:443 de logon<br><br>**Alemanha Azure:**<br> login.microsoftonline.de:443 |
| Capital de usuário/serviço usando Org ID + ADFS ou outro ponto de extremidade federado (por exemplouser@contoso.com) | Todos os pontos de extremidade acima para ADFS além de ID da organização ou outros pontos de extremidade federados |

Há outros cenários complexos possíveis. Consulte [Fluxo do Azure Active Directory autenticação](/documentation/articles/active-directory-authentication-scenarios/), [Integração de aplicativos com o Active Directory do Azure](/documentation/articles/active-directory-integrating-applications/) e [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para obter informações adicionais.  

## <a name="key-vault-management"></a>Gerenciamento de chave cofre

Para o gerenciamento de compartimento de chave (CRUD e configuração de política de acesso), o aplicativo de cliente do cofre chave precisa acessar o ponto de extremidade do Gerenciador de recursos do Azure.  

| Tipo de operação | Porta de ponto de extremidade: |
|----------------|---------------|
| Operações de plano de controle de Cofre de chave<br> por meio do Gerenciador de recursos Azure | **Global:**<br> Management.Azure.com:443<br><br> **China Azure:**<br> Management.chinacloudapi.CN:443<br><br> **Azure governo dos EUA:**<br> Management.usgovcloudapi.NET:443<br><br> **Alemanha Azure:**<br> Management.microsoftazure.de:443 |
| Gráfico do Active Directory do Azure API | **Global:**<br> Graph.Windows.NET:443<br><br> **China Azure:**<br> Graph.chinacloudapi.CN:443<br><br> **Azure governo dos EUA:**<br> Graph.Windows.NET:443<br><br> **Alemanha Azure:**<br> Graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operações de chave cofre

Para todas as operações de criptografia e gerenciamento de objetos (chaves e segredos) do cofre chave, o cliente do cofre chave precisa acessar o ponto de extremidade do cofre chave. Dependendo da localização do seu Cofre de chave, o sufixo DNS do ponto de extremidade é diferente. O ponto de extremidade do cofre chave está no formato: < nome do cofre >. < região--sufixo dns específico-> conforme descrito na tabela a seguir.  

| Tipo de operação | Porta de ponto de extremidade: |
|----------------|---------------|
| Operações de chave cofre como operações de criptografia nas chaves, criado/leitura/atualização/excluir chaves e senhas, set/get marcas e outros atributos em objetos de chave cofre (chaves/segredos)     | **Global:**<br> &lt;nome do cofre&gt;. vault.azure.net:443<br><br> **China Azure:**<br> &lt;nome do cofre&gt;. vault.azure.cn:443<br><br> **Azure governo dos EUA:**<br> &lt;nome do cofre&gt;. vault.usgovcloudapi.net:443<br><br> **Alemanha Azure:**<br> &lt;nome do cofre&gt;. vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Intervalos de endereços IP

Serviço de chave cofre alternadamente usa outros recursos Azure como infraestrutura de PaaS, portanto, não é possível fornecer um intervalo específico de endereços IP que pontos de extremidade de serviço de chave cofre terão a qualquer momento. No entanto se seu firewall apenas dá suporte para endereço IP intervalos, consultem o documento de [Intervalos de IP do Microsoft Azure data center](https://www.microsoft.com/download/details.aspx?id=41653) .   Autenticação e identidade (Azure Active Directory), seu aplicativo deve ser capaz de se conectar a pontos de extremidade descrito em [autenticação e endereços de identidade](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Próximas etapas

- Se você tiver dúvidas sobre Cofre de chave, visite os [Fóruns de Cofre de chave do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
