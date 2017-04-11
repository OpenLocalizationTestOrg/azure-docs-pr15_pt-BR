<properties 
    pageTitle="Configuração de segurança de divisão direta | Microsoft Azure" 
    description="Configurar x409 certificados de criptografia" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng" />


# <a name="split-merge-security-configuration"></a>Configuração de segurança de divisão direta  

Para usar o serviço de divisão/mala direta, você deve configurar corretamente segurança. O serviço é parte do recurso de dimensionamento Elástico do Microsoft Azure SQL Database. Para obter mais informações, consulte [elástica divisão de escala e mesclar Tutorial de serviço](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configurando certificados

Certificados são configurados de duas maneiras. 

1. [Para configurar o certificado SSL](#To-Configure-the-SSL#Certificate)
2. [Configurar certificados de cliente](#To-Configure-Client-Certificates) 

## <a name="to-obtain-certificates"></a>Para obter certificados

Podem ser obtidos certificados de autoridades de certificação pública (autoridades de certificação) ou do [Serviço de certificado do Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Esses são os métodos preferenciais para obter certificados.

Se essas opções não estiverem disponíveis, você pode gerar **certificados autoassinados**.
 
## <a name="tools-to-generate-certificates"></a>Ferramentas para gerar certificados

* [Makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Para executar as ferramentas

* Do desenvolvedor do Prompt de comando para Studios Visual, consulte [Prompt de comando do Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 

    Se tiver instalado, vá para:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obter o WDK do [Windows 8.1: baixar kits e ferramentas](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Para configurar o certificado SSL
É necessário um certificado SSL para criptografar a comunicação e autenticar o servidor. Escolha o mais aplicáveis dos três cenários abaixo e executar todas as suas etapas:

### <a name="create-a-new-self-signed-certificate"></a>Criar um novo certificado auto-assinado

1.    [Criar um certificado autoassinado](#Create-a-Self-Signed-Certificate)
2.    [Criar arquivo PFX para um certificado SSL autoassinado](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [Carregar um certificado SSL para o serviço em nuvem](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [Atualizar um certificado SSL no arquivo de configuração do serviço](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [Autoridade de certificação SSL de importação](#Import-SSL-Certification-Authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Usar um certificado existente do armazenamento de certificado
1. [Exportar um certificado SSL de armazenamento de certificado](#Export-SSL-Certificate-From-Certificate-Store)
2. [Carregar um certificado SSL para o serviço em nuvem](#Upload-SSL-Certificate-to-Cloud-Service)
3. [Atualizar um certificado SSL no arquivo de configuração do serviço](#Update-SSL-Certificate-in-Service-Configuration-File)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Para usar um certificado existente em um arquivo PFX

1. [Carregar um certificado SSL para o serviço em nuvem](#Upload-SSL-Certificate-to-Cloud-Service)
2. [Atualizar um certificado SSL no arquivo de configuração do serviço](#Update-SSL-Certificate-in-Service-Configuration-File)

## <a name="to-configure-client-certificates"></a>Configurar certificados de cliente
Certificados de cliente são necessárias para autenticar solicitações de serviço. Escolha o mais aplicáveis dos três cenários abaixo e executar todas as suas etapas:

### <a name="turn-off-client-certificates"></a>Desativar certificados de cliente
1.    [Desativar a autenticação baseada em certificado do cliente](#Turn-Off-Client-Certificate-Based-Authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Novos certificados autoassinados cliente da questão
1.    [Criar uma autoridade de certificação auto-assinado](#Create-a-Self-Signed-Certification-Authority)
2.    [Carregar certificado da CA para serviço em nuvem](#Upload-CA-Certificate-to-Cloud-Service)
3.    [Certificado de autoridade de certificação de atualização no arquivo de configuração do serviço](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Emitir certificados de cliente](#Issue-Client-Certificates)
5.    [Criar arquivos PFX para certificados de cliente](#Create-PFX-files-for-Client-Certificates)
6.    [Importar o certificado de cliente](#Import-Client-Certificate)
7.    [Copiar impressões digitais de certificado do cliente](#Copy-Client-Certificate-Thumbprints)
8.    [Configurar clientes permitidos no arquivo de configuração do serviço](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### <a name="use-existing-client-certificates"></a>Usar certificados de cliente existente
1.    [Localizar a chave pública de autoridade de certificação](#Find-CA-Public Key)
2.    [Carregar certificado da CA para serviço em nuvem](#Upload-CA-certificate-to-cloud-service)
3.    [Certificado de autoridade de certificação de atualização no arquivo de configuração do serviço](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Copiar impressões digitais de certificado do cliente](#Copy-Client-Certificate-Thumbprints)
5.    [Configurar clientes permitidos no arquivo de configuração do serviço](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [Configurar a verificação de revogação de certificado do cliente](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Endereços IP permitidos

Acesso a pontos de extremidade do serviço pode ser restrito a intervalos específicos de endereços IP.

## <a name="to-configure-encryption-for-the-store"></a>Configurar a criptografia para o repositório

É necessário um certificado para criptografar as credenciais armazenadas no repositório de metadados. Escolha o mais aplicáveis dos três cenários abaixo e executar todas as suas etapas:

### <a name="use-a-new-self-signed-certificate"></a>Usar um novo certificado auto-assinado

1.     [Criar um certificado autoassinado](#Create-a-Self-Signed-Certificate)
2.     [Criar arquivo PFX para o certificado de criptografia autoassinado](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [Carregar certificado de criptografia para serviço em nuvem](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [Certificado de criptografia de atualização no arquivo de configuração do serviço](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Usar um certificado existente do armazenamento de certificado

1.     [Exportar o certificado de criptografia do repositório de certificados](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [Carregar certificado de criptografia para serviço em nuvem](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [Certificado de criptografia de atualização no arquivo de configuração do serviço](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Usar um certificado existente em um arquivo PFX

1.     [Carregar certificado de criptografia para serviço em nuvem](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [Certificado de criptografia de atualização no arquivo de configuração do serviço](#Update-Encryption-Certificate-in-Service-Configuration-File)

## <a name="the-default-configuration"></a>A configuração padrão

A configuração padrão nega todo o acesso ao ponto de extremidade HTTP. Esta é a configuração recomendada, desde que as solicitações para esses pontos de extremidade podem carregar informações confidenciais, como credenciais de banco de dados.
A configuração padrão permite que todo o acesso ao ponto de extremidade HTTPS. Esta configuração pode ser mais restrita.

### <a name="changing-the-configuration"></a>Alterar a configuração

O grupo de regras de controle de acesso que se aplicam a e ponto de extremidade estiver configurado no **<EndpointAcls>** seção no **arquivo de configuração do serviço**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

As regras em um grupo de controle de acesso são configuradas em um <AccessControl name=""> seção do arquivo de configuração do serviço. 

O formato é explicado na documentação de listas de controle de acesso de rede.
Por exemplo, para permitir que apenas IPs no intervalo 100.100.0.0 para 100.100.255.255 para acessar o ponto de extremidade HTTPS, as regras teria esta aparência:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Negação de prevenção de serviço

Há dois mecanismos diferentes têm suportados para detectar e evitar ataques de negação de serviço:

*    Restringir o número de solicitações simultâneas por host remoto (desativado por padrão)
*    Restringir a taxa de acesso por host remoto (em por padrão)

Eles se baseiam os recursos ainda mais documentadas em segurança de IP dinâmico no IIS. Quando alterar essa configuração Lembre-se dos seguintes fatores:

* O comportamento de proxies e dispositivos de conversão de endereços de rede sobre as informações de host remoto
* Cada solicitação para qualquer recurso na função web é considerada (por exemplo, carregamento de scripts, imagens, etc)

## <a name="restricting-number-of-concurrent-accesses"></a>Restringindo o número de acessos simultâneos

As configurações que configurar esse comportamento são:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Alterar DynamicIpRestrictionDenyByConcurrentRequests como true para habilitar essa proteção.

## <a name="restricting-rate-of-access"></a>Restringindo a taxa de acesso

As configurações que configurar esse comportamento são:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Configurando a resposta a uma solicitação de negado

A seguinte configuração configura a resposta a uma solicitação de negado:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Consulte a documentação de segurança de IP dinâmico no IIS para outros valores com suporte.

## <a name="operations-for-configuring-service-certificates"></a>Operações de configuração de certificados de serviço
Este tópico é apenas para referência. Siga as etapas de configuração descritas:

* Configurar o certificado SSL
* Configurar certificados de cliente

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado
Execute:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Para personalizar:

*    -n com a URL do serviço. Caracteres curinga ("CN = * .cloudapp .net") e nomes alternativos ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") são compatíveis.
*    -e com a data de validade do certificado criar uma senha forte e especificá-lo quando solicitado.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Criar arquivo PFX de certificado SSL auto-assinado

Execute:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Digite a senha e, em seguida, exportar certificado com estas opções:
* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportar um certificado SSL de armazenamento de certificado

* Localizar certificado
* Clique em Ações -> todas as tarefas -> Exportar...
* Exportar certificado em um. Arquivo PFX com estas opções:
    * Sim, exportar a chave privada
    * Incluir todos os certificados no caminho de certificação, se possível * exportar todas as propriedades estendidas

## <a name="upload-ssl-certificate-to-cloud-service"></a>Carregar um certificado SSL no serviço em nuvem

Carregar certificado com o existente ou gerado. Arquivo PFX com o par de chaves SSL:

* Insira a senha protegendo as informações da chave privadas

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Atualizar um certificado SSL no arquivo de configuração do serviço

Atualize o valor da seguinte configuração no arquivo de configuração do serviço de impressão digital com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Autoridade de certificação SSL de importação

Siga estas etapas em todas as contas/máquina que irá se comunicar com o serviço:

* Clique duas vezes na. Arquivo CER no Windows Explorer
* Na caixa de diálogo do certificado, clique em Instalar certificado...
* Importar o certificado para o armazenamento de autoridades de certificação raiz confiável

## <a name="turn-off-client-certificate-based-authentication"></a>Desativar a autenticação baseada em certificado do cliente

Somente baseada em certificado autenticação de cliente é suportada e desativá-lo permitirá acesso público a pontos de extremidade do serviço, a menos que outros mecanismos estão no lugar (por exemplo, Microsoft Azure rede Virtual).

Altere essas configurações para false no arquivo de configuração do serviço para desativar o recurso:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Em seguida, copie a mesma impressão digital do certificado SSL na configuração de certificado de autoridade de certificação:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Criar uma autoridade de certificação auto-assinado
Execute as seguintes etapas para criar um certificado autoassinado para agir como uma autoridade de certificação:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Para personalizá-lo

*    -e com a data de expiração de certificação


## <a name="find-ca-public-key"></a>Localizar a chave pública de autoridade de certificação

Todos os certificados de cliente devem ter sido emitidos por uma autoridade de certificação confiável pelo serviço. Localize a chave pública à autoridade de certificação que emitiu o cliente certificados que vai ser usado para autenticação para carregá-lo ao serviço de nuvem.

Se o arquivo com a chave pública não estiver disponível, exportá-lo a partir do armazenamento de certificado:

* Localizar certificado
    * Procurar um certificado de cliente emitido pelo mesma autoridade de certificação
* Clique duas vezes no certificado.
* Selecione a guia caminho de certificação na caixa de diálogo certificado.
* Clique duas vezes na entrada de CA no caminho.
* Fazer anotações de propriedades do certificado.
* Feche a caixa de diálogo do **certificado** .
* Localizar certificado
    * Pesquisar a autoridade de certificação observada acima.
* Clique em Ações -> todas as tarefas -> Exportar...
* Exportar certificado em um. CER com estas opções:
    * **Não, não exportar a chave privada**
    * Inclua todos os certificados no caminho de certificação, se possível.
    * Exporte todas as propriedades estendidas.

## <a name="upload-ca-certificate-to-cloud-service"></a>Carregar certificado da CA no serviço em nuvem

Carregar certificado com o existente ou gerado. Arquivo CER com chave pública da CA.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Certificado de autoridade de certificação de atualização no arquivo de configuração do serviço

Atualize o valor da seguinte configuração no arquivo de configuração do serviço de impressão digital com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Atualize o valor da seguinte configuração com a mesma impressão digital:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Emitir certificados de cliente

Cada pessoa autorizada para acessar o serviço deve ter um certificado de cliente emitido para his/hers exclusivo use e deve escolher que HIS/hers proprietário senhas para proteger sua chave privada. 

As seguintes etapas devem ser executadas na mesma máquina onde o certificado auto-assinado da CA foi gerado e armazenado:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalizando:

* -n com uma ID para o cliente que será autenticada com este certificado
* -e com a data de expiração de certificado
* MyID.pvk e MyID.cer com nomes de arquivo exclusivo para este certificado de cliente

Esse comando solicitará uma senha ser criada e usada uma vez. Use uma senha forte.

## <a name="create-pfx-files-for-client-certificates"></a>Criar arquivos PFX para cliente certificados

Para cada certificado de cliente gerada, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizando:

    MyID.pvk and MyID.cer with the filename for the client certificate

Digite a senha e, em seguida, exportar certificado com estas opções:

* Sim, exportar a chave privada
* Exportar todas as propriedades estendidas
* A pessoa para quem está sendo emitido este certificado deve escolher a senha de exportação

## <a name="import-client-certificate"></a>Importar o certificado de cliente

Cada pessoa para quem um certificado de cliente foi emitido deve importar o par de chaves nas máquinas que ele usará para se comunicar com o serviço:

* Clique duas vezes na. Arquivo PFX no Windows Explorer
* Importar certificado em pessoal armazenar pelo menos esta opção:
    * Incluir todas as propriedades estendidas marcadas

## <a name="copy-client-certificate-thumbprints"></a>Copiar impressões digitais de certificado do cliente
Cada pessoa para quem um certificado de cliente foi emitido deve seguir estas etapas para obter a impressão digital do his/hers certificado que será adicionado ao arquivo de configuração de serviço:
* Executar certmgr.exe
* Selecione a guia pessoal
* Clique duas vezes no certificado do cliente para ser usado para autenticação
* Na caixa de diálogo certificado que é aberta, selecione a guia Detalhes
* Certifique-se de que mostrar está exibindo todos
* Selecione o campo denominado impressão digital na lista
* Copiar o valor da impressão digital **Excluir caracteres Unicode não visíveis na frente do primeiro dígito** excluir todos os espaços

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurar clientes de permitidos no arquivo de configuração do serviço

Atualize o valor da seguinte configuração no arquivo de configuração de serviço com uma lista separada por vírgulas das impressões digitais dos certificados do cliente permitidos para acessar o serviço:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Configurar a verificação de revogação de certificado do cliente

A configuração padrão não verifica com autoridade de certificação por status de revogação de certificado de cliente. Para ativar as verificações, se a autoridade de certificação que emitiu o cliente certificados suporta tais verificações, altere a seguinte configuração com um dos valores definidos na enumeração X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Criar arquivo PFX para certificados de criptografia autoassinados

Para um certificado de criptografia, execute:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizando:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Digite a senha e, em seguida, exportar certificado com estas opções:
*    Sim, exportar a chave privada
*    Exportar todas as propriedades estendidas
*    Você precisará da senha ao carregar o certificado para o serviço de nuvem.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportar o certificado de criptografia do repositório de certificados

*    Localizar certificado
*    Clique em Ações -> todas as tarefas -> Exportar...
*    Exportar certificado em um. Arquivo PFX com estas opções: 
  *    Sim, exportar a chave privada
  *    Incluir todos os certificados no caminho de certificação, se possível 
*    Exportar todas as propriedades estendidas

## <a name="upload-encryption-certificate-to-cloud-service"></a>Carregar certificado de criptografia no serviço em nuvem

Carregar certificado com o existente ou gerado. Arquivo PFX com o par de chaves de criptografia:

* Insira a senha protegendo as informações da chave privadas

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Certificado de criptografia de atualização no arquivo de configuração do serviço

Atualize o valor de impressão digital das configurações a seguir no arquivo de configuração de serviço com a impressão digital do certificado carregado para o serviço de nuvem:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Operações de certificado comuns

* Configurar o certificado SSL
* Configurar certificados de cliente

## <a name="find-certificate"></a>Localizar certificado

Siga estas etapas:

1. Execute mmc.exe.
2. Arquivo -> Adicionar/Remover Snap-in...
3. Selecione **certificados**.
4. Clique em **Adicionar**.
5. Escolha o local de armazenamento de certificado.
6. Clique em **Concluir**.
7. Clique em **Okey**.
8. Expanda **certificados**.
9. Expanda o nó de armazenamento de certificado.
10. Expanda o nó filho de certificado.
11. Selecione um certificado na lista.

## <a name="export-certificate"></a>Certificado de exportação
No **Assistente de exportação de certificado**:

1. Clique em **Avançar**.
2. Selecione **Sim**, **Exportar a chave privada**.
3. Clique em **Avançar**.
4. Selecione o formato de arquivo de saída desejado.
5. Marque as opções desejadas.
6. Verifique a **senha**.
7. Digite uma senha forte e confirmá-la.
8. Clique em **Avançar**.
9. Digite ou procure um nome de arquivo onde armazenar o certificado (use um. Extensão PFX).
10. Clique em **Avançar**.
11. Clique em **Concluir**.
12. Clique em **Okey**.

## <a name="import-certificate"></a>Certificado de importação

No Assistente de importação de certificado:

1. Selecione o local de armazenamento.

    * Selecione o **Usuário atual** se apenas processos em execução em usuário atual acessarão o serviço
    * Selecione **Máquina Local** se outros processos neste computador irá acessar o serviço
2. Clique em **Avançar**.
3. Se importar de um arquivo, confirme o caminho do arquivo.
4. Se importando um. Arquivo PFX:
    1.     Insira a senha protegendo a chave privada
    2.     Selecionar opções de importação
5.     Selecione certificados de "Local" no repositório a seguir
6.     Clique em **Procurar**.
7.     Selecione a loja desejada.
8.     Clique em **Concluir**.
       
    * Se o armazenamento de autoridade de certificação raiz confiável foi escolhido, clique em **Sim**.
9.     Clique em **Okey** em todas as janelas de diálogo.

## <a name="upload-certificate"></a>Carregar certificado

No [Portal do Azure](https://portal.azure.com/)

1. Selecione **Serviços de nuvem**.
2. Selecione o serviço de nuvem.
3. No menu superior, clique em **certificados**.
4. Na barra de ferramentas inferior, clique em **carregar**.
5. Selecione o arquivo de certificado.
6. Se é um. PFX arquivo, digite a senha para a chave privada.
7. Uma vez concluída, copie a impressão digital de certificado da nova entrada na lista.

## <a name="other-security-considerations"></a>Outras considerações de segurança
 
As configurações de SSL descritas neste documento criptografar a comunicação entre o serviço e seus clientes quando o ponto de extremidade HTTPS é usado. Isso é importante desde credenciais para acesso de banco de dados e potencialmente outras informações confidenciais contidos na comunicação. Observe, contudo, que o serviço persiste status interno, incluindo credenciais, em suas tabelas internas no banco de dados SQL do Microsoft Azure que você forneceu para armazenamento de metadados em sua assinatura do Microsoft Azure. Banco de dados foi definido como parte da seguinte configuração no seu arquivo de configuração de serviço (. Arquivo CSCFG): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Credenciais armazenadas no banco de dados são criptografadas. No entanto, como uma prática recomendada, verifique se funções da web e o operador de suas implantações de serviço são mantidas atualizadas e seguro como eles têm acessem o banco de dados de metadados e o certificado usado para criptografia e descriptografia de credenciais armazenadas. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
